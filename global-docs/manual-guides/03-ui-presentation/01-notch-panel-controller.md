# Modul 03.01: Notch Panel & AppKit Controller

Dokumen ini memandu pembuatan `NotchPanel` (subclass dari AppKit `NSPanel`) dan `NotchPanelController` yang bertugas meletakkan jendela di posisi notch fisik MacBook atau mengubahnya menjadi floating capsule pada layar standar.

---

## 1. Spesifikasi Teknis Jendela Notch
* **Tipe Jendela:** `NSPanel`
* **Level Window:** `NSWindow.Level.floating` / `.statusBar` (berada di atas window lain dan aplikasi fullscreen).
* **StyleMask:** `[.borderless, .nonactivatingPanel]` (tidak mencuri fokus ketikan user).
* **CollectionBehavior:** `[.canJoinAllSpaces, .fullScreenAuxiliary]`.
* **Transparansi:** `isOpaque = false`, `backgroundColor = .clear`.

---

## 2. Kode Swift Lengkap (`NotchPanelController.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/NotchPanelController.swift`:

```swift
import AppKit
import SwiftUI

public final class NotchPanel: NSPanel {
    override public var canBecomeKey: Bool { false }
    override public var canBecomeMain: Bool { false }
}

public final class NotchPanelController: ObservableObject {
    
    private var panel: NotchPanel?
    private let viewModel: NotchViewModel
    
    public init(viewModel: NotchViewModel) {
        self.viewModel = viewModel
        setupPanel()
    }
    
    public func setupPanel() {
        let notchPanel = NotchPanel(
            contentRect: NSRect(x: 0, y: 0, width: 340, height: 36),
            styleMask: [.borderless, .nonactivatingPanel],
            backing: .buffered,
            defer: false
        )
        
        notchPanel.level = .floating
        notchPanel.collectionBehavior = [.canJoinAllSpaces, .fullScreenAuxiliary]
        notchPanel.isOpaque = false
        notchPanel.backgroundColor = .clear
        notchPanel.hasShadow = true
        
        // Host SwiftUI Root View
        let rootView = NotchContainerView(viewModel: viewModel)
        notchPanel.contentView = NSHostingView(rootView: rootView)
        
        self.panel = notchPanel
        updatePosition()
        
        notchPanel.orderFrontRegardless()
    }
    
    /// Mengatur posisi panel menempel di Notch MacBook atau menjadi Capsule Pill
    public func updatePosition(width: CGFloat = 340, height: CGFloat = 36) {
        guard let screen = NSScreen.main, let panel = panel else { return }
        
        let screenFrame = screen.frame
        let hasPhysicalNotch = screen.safeAreaInsets.top > 0
        
        let targetX = screenFrame.midX - (width / 2.0)
        let targetY: CGFloat
        
        if hasPhysicalNotch {
            // Menempel rata di bagian paling atas display
            targetY = screenFrame.maxY - height
        } else {
            // Floating Pill dengan sedikit jarak dari atas menu bar
            targetY = screenFrame.maxY - height - 6
        }
        
        let targetRect = NSRect(x: targetX, y: targetY, width: width, height: height)
        panel.setFrame(targetRect, display: true, animate: false)
    }
}

/// Kontainer SwiftUI yang memuat seluruh tampilan Notch
public struct NotchContainerView: View {
    @Bindable var viewModel: NotchViewModel
    
    public var body: some View {
        Group {
            if viewModel.activeSessions.isEmpty {
                StandbyNotchView(viewModel: viewModel)
            } else if viewModel.activeSessions.count == 1, let first = viewModel.activeSessions.first {
                SingleSessionView(session: first, viewModel: viewModel)
            } else {
                MultiSessionAccordionView(viewModel: viewModel)
            }
        }
        .frame(maxWidth: .infinity, maxHeight: .infinity)
    }
}
```

---

## 3. Penjelasan Desain
* Penggunaan `canBecomeKey = false` dan `canBecomeMain = false` memastikan bahwa ketika HUD memperbarui status atau di-hover, kursor teks di editor kode pengembang tidak terputus.
