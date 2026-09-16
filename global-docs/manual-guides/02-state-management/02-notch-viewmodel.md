# Modul 02.02: Notch ViewModel

Dokumen ini memandu pembuatan `NotchViewModel` — komponen sentral `@Observable` berbasis `@MainActor` yang menghubungkan Background Actor Services (`LauncherService`, `CDPService`, `GitWorktreeService`) ke antarmuka SwiftUI.

---

## 1. Peran `NotchViewModel`
1. **Pusat State Sesi:** Menyimpan daftar `activeSessions: [WorkspaceSession]`.
2. **Loop Pemantau Adaptif:** Menjalankan `Task` latar belakang yang terus memindai status CDP di setiap port sesi aktif.
3. **Eksekutor Aksi Pengguna:** Menyediakan metode `openProject()`, `launchNewBranch()`, `focusSession()`, dan `closeSession()`.

---

## 2. Kode Swift Lengkap (`NotchViewModel.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`:

```swift
import Foundation
import SwiftUI
import AppKit

@Observable
@MainActor
public final class NotchViewModel {
    
    // MARK: - Published State
    public var activeSessions: [WorkspaceSession] = []
    public var isExpanded: Bool = false
    public var isShowingBranchModal: Bool = false
    public var selectedSessionForModal: WorkspaceSession? = nil
    public var errorMessage: String? = nil
    
    public var hasRunningAgent: Bool {
        activeSessions.contains { $0.state == .running }
    }
    
    // MARK: - Services
    private let launcherService: LauncherService
    private let cdpService: CDPService
    private let gitService: GitWorktreeService
    
    private var monitoringTask: Task<Void, Never>? = nil
    
    public init(
        launcherService: LauncherService = LauncherService(),
        cdpService: CDPService = CDPService(),
        gitService: GitWorktreeService = GitWorktreeService()
    ) {
        self.launcherService = launcherService
        self.cdpService = cdpService
        self.gitService = gitService
        
        startMonitoringLoop()
    }
    
    // MARK: - Monitoring Loop
    
    public func startMonitoringLoop() {
        monitoringTask?.cancel()
        
        monitoringTask = Task { [weak self] in
            while !Task.isCancelled {
                guard let self = self else { break }
                
                await self.pollActiveSessions()
                
                // Adaptive delay: 800ms jika ada running, 3000ms jika idle/standby
                let hasRunning = self.activeSessions.contains { $0.state == .running }
                let delayMs = hasRunning ? AppPreferences.pollingIntervalRunningMs : AppPreferences.pollingIntervalIdleMs
                
                try? await Task.sleep(nanoseconds: UInt64(delayMs) * 1_000_000)
            }
        }
    }
    
    private func pollActiveSessions() async {
        // 1. Probing port rentang 9221-9229 untuk menemukan instance yang mungkin diluncurkan di luar app
        let startPort = AppPreferences.cdpPortRangeStart
        let endPort = AppPreferences.cdpPortRangeEnd
        
        for port in startPort...endPort {
            let (isValid, wsURL) = await cdpService.probePort(port: port)
            
            if isValid, let wsURL = wsURL {
                await cdpService.connectWebSocket(port: port, wsURL: wsURL)
                
                if let extracted = await cdpService.evaluateState(port: port) {
                    updateOrInsertSession(port: port, extracted: extracted)
                }
            } else {
                // Port mati -> Jika ada sesi yang tercatat di port ini, hapus sesi
                removeSession(forPort: port)
            }
        }
    }
    
    private func updateOrInsertSession(port: Int, extracted: CDPExtractedState) {
        let stateEnum = AgentExecutionState(rawValue: extracted.state) ?? .idle
        let name = extracted.workspaceName ?? "Workspace"
        
        if let index = activeSessions.firstIndex(where: { $0.cdpPort == port }) {
            activeSessions[index].state = stateEnum
            activeSessions[index].headline = extracted.headline
            activeSessions[index].subSteps = extracted.subSteps
            activeSessions[index].updatedAt = Date()
        } else {
            let newSession = WorkspaceSession(
                workspaceName: name,
                workspaceURL: URL(fileURLWithPath: "~/"),
                pid: 0,
                cdpPort: port,
                state: stateEnum,
                headline: extracted.headline,
                subSteps: extracted.subSteps
            )
            activeSessions.append(newSession)
        }
    }
    
    private func removeSession(forPort port: Int) {
        if let index = activeSessions.firstIndex(where: { $0.cdpPort == port }) {
            activeSessions.remove(at: index)
        }
    }
    
    // MARK: - User Actions
    
    /// Buka dialog native untuk memilih folder proyek
    public func promptOpenProject() {
        let panel = NSOpenPanel()
        panel.canChooseFiles = false
        panel.canChooseDirectories = true
        panel.allowsMultipleSelection = false
        panel.prompt = "Open in Antigravity"
        
        if panel.runModal() == .OK, let selectedURL = panel.url {
            Task {
                await launchProject(at: selectedURL)
            }
        }
    }
    
    public func launchProject(at url: URL, branch: String? = nil) async {
        do {
            let (pid, allocatedPort, _) = try await launcherService.launch(workspaceURL: url, branch: branch)
            
            let session = WorkspaceSession(
                workspaceName: url.lastPathComponent,
                workspaceURL: url,
                branchName: branch,
                pid: pid,
                cdpPort: allocatedPort,
                state: .standby
            )
            activeSessions.append(session)
        } catch {
            self.errorMessage = error.localizedDescription
        }
    }
    
    /// Membawa jendela Antigravity IDE sesi ini ke layar depan
    public func focusSession(_ session: WorkspaceSession) {
        if session.pid > 0, let app = NSRunningApplication(processIdentifier: session.pid) {
            if #available(macOS 14.0, *) {
                app.activate()
            } else {
                app.activate(options: [.activateIgnoringOtherApps])
            }
        }
    }
    
    /// Menutup instance Antigravity IDE sesi ini
    public func closeSession(_ session: WorkspaceSession) {
        if session.pid > 0, let app = NSRunningApplication(processIdentifier: session.pid) {
            app.terminate()
        }
        removeSession(forPort: session.cdpPort)
        Task {
            await cdpService.disconnectWebSocket(port: session.cdpPort)
        }
    }
}
```

---

## 3. Poin Kunci
* Seluruh mutasi `activeSessions` terjadi di `@MainActor`, menjamin tidak ada glitch atau race condition pada rendering SwiftUI.
* Fitur auto-discovery mendeteksi sesi baru maupun sesi yang ditutup secara real-time.
* Siklus hidup `monitoringTask` aman 100% dari *data race* (tanpa memerlukan `unsafe` atau `deinit`) karena menggunakan `[weak self]` di dalam loop `Task`. Ketika `NotchViewModel` dilepas dari memori, `guard let self = self else { break }` otomatis menghentikan loop pemantau secara alami.
