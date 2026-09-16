# Modul 06.04: Integrasi Lengkap Anti Dispatch Dynamic Notch

Dokumen ini memandu implementasi tampilan utama terintegrasi (`DynamicNotchRootView.swift`) yang menyatukan arsitektur **Productive Island** dengan model status dan alur kerja **Anti Dispatch**.

---

## 1. Arsitektur Tampilan Terintegrasi

`DynamicNotchRootView` menggabungkan:
1. **Mode Compact (Menempel di Notch):**
   * **Left Lobe:** Menampilkan indikator status (Standby / Running), nama workspace, dan nama branch aktif.
   * **Center Gap:** Mengosongkan area lensa kamera (`Color.clear.frame(width: notch.width)`).
   * **Right Lobe:** Tombol *Open Project*, *New Branch*, atau animasi bar berdenyut saat agen AI aktif.
2. **Mode Expanded (Mengembang ke Bawah saat Hover/Tap):**
   * Menampilkan detail progres file analysis, sub-steps, tombol fokus jendela IDE, dan kontrol manajemen worktree.

---

## 2. Kode Swift Lengkap (`DynamicNotchRootView.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`:

```swift
import SwiftUI
import AppKit

public struct DynamicNotchRootView: View {
    public let notch: Notch
    @Bindable public var viewModel: NotchViewModel
    
    @State private var isHovering: Bool = false
    @State private var isPinned: Bool = false
    @State private var hoverTask: Task<Void, Never>?
    @State private var activityLevels: [CGFloat] = [0.2, 0.4, 0.7, 0.3, 0.5]
    
    private var isExpanded: Bool {
        isHovering || isPinned || viewModel.isShowingBranchModal
    }
    
    private var hasRunningAgent: Bool {
        viewModel.activeSessions.contains { $0.state == .running }
    }
    
    private var currentLobeWidth: CGFloat {
        isExpanded ? 190 : NotchMetrics.lobeWidth
    }
    
    private var currentWidth: CGFloat {
        notch.width + (2 * currentLobeWidth)
    }
    
    private var currentHeight: CGFloat {
        if !isExpanded {
            return notch.height
        }
        if viewModel.activeSessions.isEmpty {
            return NotchMetrics.expandedSingleHeight
        } else if viewModel.activeSessions.count == 1 {
            return NotchMetrics.expandedSingleHeight
        } else {
            return NotchMetrics.expandedMultiHeight
        }
    }
    
    public init(notch: Notch, viewModel: NotchViewModel) {
        self.notch = notch
        self.viewModel = viewModel
    }
    
    public var body: some View {
        VStack(spacing: 0) {
            ZStack(alignment: .top) {
                // 1. Lapisan Latar Belakang & Geometri Bezier
                IslandSurface(
                    radius: NotchMetrics.cornerRadius,
                    detached: !notch.hasPhysicalNotch,
                    isGlass: true,
                    hasBorder: true
                )
                
                // 2. Lapisan Konten (Compact vs Expanded)
                if isExpanded {
                    expandedContentView
                } else {
                    compactContentView
                }
            }
            .frame(width: currentWidth, height: currentHeight)
            .offset(y: NotchMetrics.dropOffset(hasNotch: notch.hasPhysicalNotch))
            .animation(.spring(response: 0.45, dampingFraction: 0.78), value: currentHeight)
            .animation(.spring(response: 0.45, dampingFraction: 0.78), value: currentWidth)
            .onHover(perform: handleHover)
            .onTapGesture {
                if !isExpanded {
                    hoverTask?.cancel()
                    isHovering = true
                }
            }
            .sheet(isPresented: $viewModel.isShowingBranchModal) {
                BranchCreatorSheet(viewModel: viewModel)
            }
            
            Spacer(minLength: 0)
        }
        .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .top)
        // Task Denyut Bar Aktivitas saat Agen Bekerja
        .task(id: hasRunningAgent) {
            guard hasRunningAgent else {
                activityLevels = [0.2, 0.2, 0.2, 0.2, 0.2]
                return
            }
            while !Task.isCancelled {
                activityLevels = (0..<5).map { _ in CGFloat.random(in: 0.2...1.0) }
                try? await Task.sleep(for: .milliseconds(140))
            }
        }
        // Task Polling Kursor untuk Linger Effect
        .task(id: isExpanded) {
            guard isExpanded else { return }
            var cursorExitTime: Date?
            
            while !Task.isCancelled {
                try? await Task.sleep(for: .milliseconds(100))
                
                if isMouseInside(width: currentWidth, height: currentHeight) {
                    cursorExitTime = nil
                    self.isHovering = true
                    continue
                }
                
                if cursorExitTime == nil {
                    cursorExitTime = Date()
                }
                
                if let exitTime = cursorExitTime,
                   Date().timeIntervalSince(exitTime) >= NotchMetrics.lingerDuration {
                    self.hoverTask?.cancel()
                    self.isHovering = false
                    cursorExitTime = nil
                }
            }
        }
    }
    
    // MARK: - Compact View (Menempel di Atas)
    
    @ViewBuilder
    private var compactContentView: some View {
        HStack(spacing: 0) {
            // Sayap Kiri
            leftCompactLobe
                .frame(width: currentLobeWidth)
            
            // Celah Kamera Fisik (Transparan)
            Color.clear
                .frame(width: notch.width)
            
            // Sayap Kanan
            rightCompactLobe
                .frame(width: currentLobeWidth)
        }
        .frame(height: notch.height)
        .foregroundStyle(.white)
        .clipped()
    }
    
    @ViewBuilder
    private var leftCompactLobe: some View {
        HStack(spacing: 6) {
            if let primary = viewModel.activeSessions.first {
                Circle()
                    .fill(statusColor(for: primary.state))
                    .frame(width: 7, height: 7)
                
                Text(primary.workspaceName)
                    .font(.system(size: 11.5, weight: .semibold))
                    .lineLimit(1)
                
                if let branch = primary.branchName {
                    Text("(\(branch))")
                        .font(.system(size: 10, weight: .regular, design: .monospaced))
                        .foregroundStyle(.secondary)
                        .lineLimit(1)
                }
            } else {
                Circle()
                    .fill(Color.secondary.opacity(0.6))
                    .frame(width: 7, height: 7)
                
                Text("Standby")
                    .font(.system(size: 11.5, weight: .medium))
                    .foregroundStyle(.secondary)
            }
            Spacer(minLength: 0)
        }
        .padding(.horizontal, 10)
    }
    
    @ViewBuilder
    private var rightCompactLobe: some View {
        HStack(spacing: 8) {
            Spacer(minLength: 0)
            
            if hasRunningAgent {
                ActivityBars(levels: activityLevels, color: .cyan)
            } else if viewModel.activeSessions.isEmpty {
                Button {
                    viewModel.promptOpenProject()
                } label: {
                    HStack(spacing: 3) {
                        Image(systemName: "folder.badge.plus")
                        Text("Open")
                    }
                    .font(.system(size: 10.5, weight: .semibold))
                    .padding(.horizontal, 6)
                    .padding(.vertical, 2.5)
                    .background(Color.white.opacity(0.1))
                    .cornerRadius(5)
                }
                .buttonStyle(.plain)
                
                Button {
                    viewModel.isShowingBranchModal = true
                } label: {
                    Image(systemName: "arrow.triangle.branch")
                        .font(.system(size: 10.5, weight: .semibold))
                        .padding(3.5)
                        .background(Color.cyan.opacity(0.2))
                        .foregroundStyle(Color.cyan)
                        .cornerRadius(5)
                }
                .buttonStyle(.plain)
            } else {
                Text("\(viewModel.activeSessions.count) session")
                    .font(.system(size: 10.5, weight: .medium, design: .monospaced))
                    .foregroundStyle(.secondary)
            }
        }
        .padding(.horizontal, 10)
    }
    
    // MARK: - Expanded View (Saat Terbuka)
    
    @ViewBuilder
    private var expandedContentView: some View {
        VStack(spacing: 8) {
            if viewModel.activeSessions.isEmpty {
                standbyExpandedView
            } else if viewModel.activeSessions.count == 1, let session = viewModel.activeSessions.first {
                SingleSessionView(session: session, viewModel: viewModel)
            } else {
                MultiSessionAccordionView(viewModel: viewModel)
            }
        }
        .padding(.top, notch.height + 2)
        .padding(.horizontal, 14)
        .padding(.bottom, 8)
        .transition(.opacity)
    }
    
    @ViewBuilder
    private var standbyExpandedView: some View {
        HStack(spacing: 12) {
            VStack(alignment: .leading, spacing: 2) {
                Text("Antigravity Workspace")
                    .font(.system(size: 12, weight: .semibold))
                Text("No active agents. Launch a project or create a worktree.")
                    .font(.system(size: 10.5))
                    .foregroundStyle(.secondary)
            }
            
            Spacer()
            
            HStack(spacing: 6) {
                Button {
                    viewModel.promptOpenProject()
                } label: {
                    HStack(spacing: 4) {
                        Image(systemName: "folder.badge.plus")
                        Text("Open Project...")
                    }
                    .font(.system(size: 11, weight: .semibold))
                    .padding(.horizontal, 8)
                    .padding(.vertical, 4)
                    .background(Color.white.opacity(0.1))
                    .cornerRadius(6)
                }
                .buttonStyle(.plain)
                
                Button {
                    viewModel.isShowingBranchModal = true
                } label: {
                    HStack(spacing: 4) {
                        Image(systemName: "arrow.triangle.branch")
                        Text("New Branch...")
                    }
                    .font(.system(size: 11, weight: .semibold))
                    .padding(.horizontal, 8)
                    .padding(.vertical, 4)
                    .background(Color.cyan.opacity(0.2))
                    .foregroundStyle(Color.cyan)
                    .cornerRadius(6)
                }
                .buttonStyle(.plain)
            }
        }
    }
    
    // MARK: - Helper Methods
    
    private func handleHover(_ isEntering: Bool) {
        guard isEntering else { return }
        hoverTask?.cancel()
        hoverTask = Task {
            try? await Task.sleep(for: NotchMetrics.hoverDelay)
            if !Task.isCancelled {
                self.isHovering = true
            }
        }
    }
    
    private func isMouseInside(width: CGFloat, height: CGFloat) -> Bool {
        let mouse = NSEvent.mouseLocation
        let screenFrame = notch.screen.frame
        let halfWidth = (width / 2.0) + 8.0
        let isInsideX = abs(mouse.x - notch.centerX) <= halfWidth
        let isInsideY = mouse.y >= (screenFrame.maxY - height - 8.0)
        return isInsideX && isInsideY
    }
    
    private func statusColor(for state: AgentExecutionState) -> Color {
        switch state {
        case .running: return Color.cyan
        case .done: return Color.green
        case .idle: return Color.gray
        case .standby: return Color.secondary
        case .disconnected: return Color.red
        }
    }
}
```

---

## 3. Langkah Pengujian di Xcode
1. Tambahkan berkas pendukung baru:
   * `Presentation/Notch/Models/Notch.swift` (dari Modul 06.01)
   * `Presentation/Notch/Models/NotchMetrics.swift` (dari Modul 06.01)
   * `Presentation/Notch/Views/Components/IslandShape.swift` (dari Modul 06.02)
   * `Presentation/Notch/Views/Components/IslandSurface.swift` (dari Modul 06.02)
   * `Presentation/Notch/Views/Components/ActivityBars.swift` (dari Modul 06.03)
   * `Presentation/Notch/Views/DynamicNotchRootView.swift` (dari Modul 06.04)
2. Perbarui `NotchPanelController.swift` dengan kode dari Modul 06.01.
3. Jalankan aplikasi dengan tombol **`⌘ + R`**.
4. Arahkan kursor ke notch untuk melihat efek spring morphing ekspansi ala Productive Island.
