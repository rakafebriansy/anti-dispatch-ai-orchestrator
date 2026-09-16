# Modul 06.01: Deteksi Geometri Hardware Notch & NSPanel Canvas

Dokumen ini memandu implementasi sistem pendeteksi dimensi fisik notch MacBook (`Notch` Geometry) dan arsitektur jendela kanvas tetap (*Single-Sized Fixed Canvas NSPanel*) yang diadaptasi dari arsitektur **Productive Island**.

---

## 1. Masalah pada Pendekatan Dinamis Konvensional vs Solusi Productive Island

Pada pendekatan konvensional, setiap kali UI berganti dari mode *compact* ke *expanded*, ukuran `NSPanel` diubah secara dinamis menggunakan `panel.setFrame(newRect, display: true, animate: false)`. Hal ini menyebabkan beberapa masalah krusial:
1. **AppKit Window Jitter & Frame Lag:** Mengubah ukuran `NSPanel` melalui AppKit memicu penataan ulang jendela OS (*window server re-anchoring*) yang menyebabkan efek patah-patah (*jitter*).
2. **Kekacauan Koordinat Multi-Display:** Pada layar eksternal atau konfigurasi multi-monitor, perhitungan koordinat `targetY = screenFrame.maxY - height` dapat melempar jendela ke pojok layar jika terjadi selisih ukuran frame.
3. **Kamera Fisik Tertutup:** Elemen teks sering kali berada persis di bawah modul kamera MacBook.

### Solusi Arsitektur Productive Island:
* **Kanvas Tetap Transparan (*Fixed Transparent Canvas*):** `NSPanel` dibuat berukuran penuh (cukup besar untuk menampung panel terbesar yang mungkin muncul) dan ditempatkan statis tepat di atas notch.
* **SwiftUI Native Spring Morphing:** Seluruh animasi ekspansi/kolaps, perubahan lebar (*width*), dan tinggi (*height*) dikontrol 100% oleh engine layout SwiftUI dengan kurva pegas (*spring physics*), bukan oleh `NSPanel.setFrame`.

```
+-------------------------------------------------------------+
|               NSPanel Canvas (Transparan & Statis)          |
|                                                             |
|           +-----[ Notch Hardware MacBook ]-----+            |
|           |                                    |            |
|      +----+------------------------------------+----+       |
|      |    SwiftUI IslandSurface & IslandShape       |       |
|      |    (Animasi Spring Mengembang/Menyusut)      |       |
|      +----------------------------------------------+       |
|                                                             |
+-------------------------------------------------------------+
```

---

## 2. Struktur Geometri Notch (`Notch.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/Models/Notch.swift` (atau kelompok berkas Presentasi):

```swift
import AppKit

/// Merepresentasikan dimensi dan posisi presisi dari hardware notch pada layar MacBook.
/// Secara otomatis beralih ke mode "Detached Pill" pada layar eksternal tanpa notch fisik.
public struct Notch: Sendable {
    public let width: CGFloat
    public let height: CGFloat
    public let centerX: CGFloat   // Titik tengah horizontal dalam koordinat layar
    public let screen: NSScreen
    public let hasPhysicalNotch: Bool

    public init(screen: NSScreen) {
        self.screen = screen
        let frame = screen.frame
        
        // Memeriksa area menu bar kiri dan kanan yang mengapit notch fisik MacBook
        if let leftArea = screen.auxiliaryTopLeftArea,
           let rightArea = screen.auxiliaryTopRightArea,
           screen.safeAreaInsets.top > 0 {
            self.width = rightArea.minX - leftArea.maxX
            self.height = screen.safeAreaInsets.top
            self.centerX = leftArea.maxX + (self.width / 2.0)
            self.hasPhysicalNotch = true
        } else {
            // Fallback untuk layar eksternal tanpa notch fisik (Floating Pill)
            self.width = 180
            self.height = 32
            self.centerX = frame.midX
            self.hasPhysicalNotch = false
        }
    }
}
```

---

## 3. Metrik & Ukuran Kanvas (`NotchMetrics.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/Models/NotchMetrics.swift`:

```swift
import Foundation

public enum NotchMetrics {
    // Lebar sayap kiri & kanan (lobe) saat mode standby/compact
    public static let lobeWidth: CGFloat = 135
    
    // Tinggi panel saat expanded
    public static let compactHeight: CGFloat = 34
    public static let expandedSingleHeight: CGFloat = 88
    public static let expandedMultiHeight: CGFloat = 140
    public static let maxPanelHeight: CGFloat = 260
    
    // Radius kelengkungan sudut
    public static let cornerRadius: CGFloat = 16
    
    // Offset jatuh ke bawah jika berada di layar eksternal (detached pill)
    public static func dropOffset(hasNotch: Bool) -> CGFloat {
        hasNotch ? 0 : 6
    }
    
    // Durasi linger (tetap terbuka selama X detik setelah kursor keluar)
    public static let lingerDuration: TimeInterval = 1.8
    public static let hoverDelay: Duration = .milliseconds(120)

    /// Ukuran kanvas NSPanel statis yang cukup besar untuk menampung animasi terbesar
    public static func panelSize(notch: Notch) -> CGSize {
        let totalWidth = notch.width + (2 * lobeWidth) + 80
        let totalHeight = maxPanelHeight + 40
        return CGSize(width: totalWidth, height: totalHeight)
    }
}
```

---

## 4. AppKit Panel Controller (`NotchPanelController.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/NotchPanelController.swift`:

```swift
import AppKit
import SwiftUI

public final class NotchPanel: NSPanel {
    override public var canBecomeKey: Bool { true }
    override public var canBecomeMain: Bool { false }
}

@MainActor
public final class NotchPanelController {
    private var panel: NotchPanel?
    private let viewModel: NotchViewModel
    
    public init(viewModel: NotchViewModel) {
        self.viewModel = viewModel
        setupPanel()
    }
    
    public func setupPanel() {
        // Cari layar yang memiliki notch fisik terlebih dahulu, fallback ke layar utama
        guard let screen = NSScreen.screens.first(where: { $0.safeAreaInsets.top > 0 })
              ?? NSScreen.screens.first
              ?? NSScreen.main else {
            return
        }
        
        let notch = Notch(screen: screen)
        let canvasSize = NotchMetrics.panelSize(notch: notch)
        
        // Posisikan kanvas statis di bagian paling atas tengah layar
        let origin = NSPoint(
            x: notch.centerX - (canvasSize.width / 2.0),
            y: screen.frame.maxY - canvasSize.height
        )
        
        let notchPanel = NotchPanel(
            contentRect: NSRect(origin: origin, size: canvasSize),
            styleMask: [.borderless, .nonactivatingPanel],
            backing: .buffered,
            defer: false
        )
        
        notchPanel.level = NSWindow.Level.statusBar
        notchPanel.collectionBehavior = [.canJoinAllSpaces, .stationary, .fullScreenAuxiliary]
        notchPanel.backgroundColor = NSColor.clear
        notchPanel.isOpaque = false
        notchPanel.hasShadow = false
        notchPanel.isMovable = false
        notchPanel.hidesOnDeactivate = false
        
        let rootView = DynamicNotchRootView(notch: notch, viewModel: viewModel)
        notchPanel.contentView = NSHostingView(rootView: rootView)
        
        self.panel = notchPanel
        notchPanel.orderFrontRegardless()
    }
    
    public func updatePosition() {
        guard let screen = NSScreen.screens.first(where: { $0.safeAreaInsets.top > 0 })
              ?? NSScreen.screens.first
              ?? NSScreen.main,
              let panel = panel else {
            return
        }
        
        let notch = Notch(screen: screen)
        let canvasSize = NotchMetrics.panelSize(notch: notch)
        let origin = NSPoint(
            x: notch.centerX - (canvasSize.width / 2.0),
            y: screen.frame.maxY - canvasSize.height
        )
        
        panel.setFrame(NSRect(origin: origin, size: canvasSize), display: true, animate: false)
    }
}
```

---

## 5. Ringkasan Keunggulan
* `screen.auxiliaryTopLeftArea` dan `screen.auxiliaryTopRightArea` memberikan lebar pixel fisik notch yang 100% akurat pada seluruh lini MacBook Air / Pro (M1, M2, M3, M4).
* `NSPanel` tidak pernah di-resize saat animasi berlangsung, menghilangkan seluruh potensi galat AppKit frame clamping dan pergeseran jendela ke pojok layar.
