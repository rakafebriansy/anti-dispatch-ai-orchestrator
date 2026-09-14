# Modul 01.03: Launcher Service & Dynamic Port Scanner

Dokumen ini memandu pembuatan `LauncherService` — komponen actor yang bertugas mencari binary Antigravity IDE, memindai dan mengalokasikan port loopback CDP kosong (`9221-9229`), dan meluncurkan instance baru dengan konfigurasi terisolasi.

---

## 1. Konsep Dynamic Port Probing

Ketika pengguna meluncurkan instance Antigravity IDE baru, aplikasi memindai port `9221` sampai `9229`. Untuk mengetahui apakah port sedang dipakai tanpa mengganggu proses lain, kita membuat socket TCP sementara dan mencoba `bind` ke `127.0.0.1:<port>`. Jika `bind` sukses, port tersebut bebas dan siap dialokasikan.

---

## 2. Kode Swift Lengkap (`LauncherService.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Core/Services/LauncherService.swift`:

```swift
import Foundation
import AppKit

public actor LauncherService {
    
    private let gitService: GitWorktreeService
    
    public init(gitService: GitWorktreeService = GitWorktreeService()) {
        self.gitService = gitService
    }
    
    /// Mencari port bebas antara startPort dan endPort (default: 9221 - 9229)
    public func findAvailablePort(startPort: Int = 9221, endPort: Int = 9229) -> Int {
        for port in startPort...endPort {
            var addr = sockaddr_in()
            addr.sin_len = UInt8(MemoryLayout<sockaddr_in>.stride)
            addr.sin_family = sa_family_t(AF_INET)
            addr.sin_port = in_port_t(port).bigEndian
            addr.sin_addr.s_addr = inet_addr("127.0.0.1")
            
            let sock = socket(AF_INET, SOCK_STREAM, 0)
            if sock >= 0 {
                let bindResult = withUnsafePointer(to: &addr) {
                    $0.withMemoryRebound(to: sockaddr.self, capacity: 1) {
                        bind(sock, $0, socklen_t(MemoryLayout<sockaddr_in>.stride))
                    }
                }
                close(sock)
                if bindResult == 0 {
                    return port
                }
            }
        }
        return startPort // Fallback ke default jika seluruh rentang terikat
    }
    
    /// Menemukan URL binary Antigravity IDE di sistem macOS
    public func findAntigravityAppURL() -> URL? {
        if let appURL = NSWorkspace.shared.urlForApplication(withBundleIdentifier: "com.google.antigravity") {
            return appURL
        }
        if let appURL = NSWorkspace.shared.urlForApplication(withBundleIdentifier: "com.antigravity.ide") {
            return appURL
        }
        
        let defaultPath = URL(fileURLWithPath: "/Applications/Antigravity IDE.app")
        if FileManager.default.fileExists(atPath: defaultPath.path) {
            return defaultPath
        }
        
        return nil
    }
    
    /// Meluncurkan instance Antigravity IDE terisolasi
    public func launch(
        workspaceURL: URL,
        branch: String? = nil,
        customPort: Int? = nil
    ) async throws -> (pid: pid_t, allocatedPort: Int, dataDir: URL) {
        var targetWorkspaceURL = workspaceURL
        
        // 1. Jika ada branch yang diminta, siapkan worktree
        if let branch = branch, !branch.isEmpty {
            guard await gitService.isGitRepository(at: workspaceURL) else {
                throw NSError(domain: "LauncherError", code: 400, userInfo: [NSLocalizedDescriptionKey: "Directory is not a valid Git repository."])
            }
            targetWorkspaceURL = try await gitService.prepareWorktree(branch: branch, in: workspaceURL)
        }
        
        // 2. Tentukan direktori isolasi data
        let dataDir = PathHasher.resolveDataDirectory(for: targetWorkspaceURL, branch: branch)
        try FileManager.default.createDirectory(at: dataDir, withIntermediateDirectories: true)
        
        // 3. Alokasikan port bebas
        let allocatedPort = customPort ?? findAvailablePort()
        
        // 4. Temukan binary Antigravity IDE
        guard let appURL = findAntigravityAppURL() else {
            throw NSError(domain: "LauncherError", code: 404, userInfo: [NSLocalizedDescriptionKey: "Antigravity IDE.app was not found in the /Applications directory."])
        }
        
        // 5. Siapkan konfigurasi proses
        let config = NSWorkspace.OpenConfiguration()
        config.arguments = [
            targetWorkspaceURL.path,
            "--remote-debugging-port=\(allocatedPort)",
            "--user-data-dir=\(dataDir.path)"
        ]
        config.createsNewApplicationInstance = true
        
        // 6. Buka aplikasi dan ambil PID
        let runningApp = try await NSWorkspace.shared.openApplication(at: appURL, configuration: config)
        return (runningApp.processIdentifier, allocatedPort, dataDir)
    }
}
```

---

## 3. Poin Kunci
* `createsNewApplicationInstance = true`: Memastikan macOS membuka jendela instance mandiri terpisah dari instance Antigravity yang mungkin sudah aktif sebelumnya.
* Argumen CLI `--remote-debugging-port` membuka endpoint loopback CDP sehingga `CDPService` dapat langsung tersambung dan memantau status agen.
