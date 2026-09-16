# Modul 06.03: Lobe Slot Architecture & Cursor Linger Engine

Dokumen ini memandu pembuatan tata letak sayap modular (*Lobe Slot Architecture*) dan engine pelacak kursor cerdas (*Cursor Linger & Polling Engine*) yang diadopsi dari **Productive Island**.

---

## 1. Konsep Lobe Slot Architecture (Compact Mode)

Pada mode *compact* (saat notch merapat di atas), tata letak dibagi menjadi **tiga slot terpisah**:

```
+-------------------+-----------------------------+-------------------+
|     LEFT LOBE     |      CENTER CAMERA GAP      |    RIGHT LOBE     |
| (Width: 135-150pt)|   (Width: notch.width pt)   | (Width: 135-150pt)|
+-------------------+-----------------------------+-------------------+
|  ● Workspace Name |    [ Lensa Kamera Mac ]     | ⚡ Open / Branch   |
|    (branch-main)  |     (Area Transparan)       |   Live Status     |
+-------------------+-----------------------------+-------------------+
```

* **Left Lobe (Sayap Kiri):** Menampilkan status sesi aktif, nama workspace, dan badge branch.
* **Center Camera Gap:** Area kosong transparan (`Color.clear.frame(width: notch.width)`). Hal ini menjamin tidak ada tombol atau teks yang terhalang/tertutup oleh modul lensa kamera MacBook.
* **Right Lobe (Sayap Kanan):** Menampilkan tombol tindakan instan (*Quick Action Buttons*) atau visualizer aktivitas agen AI.

---

## 2. Masalah `onHover` Bawaan vs Solusi Cursor Linger Polling

### Keterbatasan `onHover` Standar di SwiftUI:
Saat pengguna mengarahkan kursor (*hover*) ke notch, tampilan mengembang ke bawah (*spring expand*). Selama transisi animasi frame, event `onHover(false)` sering terpicu secara keliru karena frame bergerak menjauhi kursor. Akibatnya, jendela langsung menutup sendiri (*flickering / jitter*).

### Solusi Productive Island Engine:
1. **Debounced Hover In:** Menunggu jeda singkat (`hoverDelay: 120ms`) sebelum membuka agar geseran kursor yang tidak sengaja tidak langsung memicu ekspansi.
2. **Instant Click Open:** Sentuhan klik mouse (`onTapGesture`) langsung membuka notch tanpa jeda.
3. **Cursor Polling Loop (`NSEvent.mouseLocation`):** Saat panel dalam kondisi terbuka (*expanded*), task asinkron memeriksa posisi kursor setiap 100ms.
4. **Linger Timer (1.8 detik):** Ketika kursor meninggalkan area panel, sistem menunggu selama 1.8 detik sebelum menutup. Jika kursor kembali masuk ke area panel dalam rentang waktu tersebut, penutupan dibatalkan.

---

## 3. Komponen Animated Activity Bars (`ActivityBars.swift`)

Visualizer 5 batang animasi untuk menandakan agen AI sedang bekerja atau standby:

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/Views/Components/ActivityBars.swift`:

```swift
import SwiftUI

/// 5 Bar aktivitas live yang berdenyut saat agen AI aktif menganalisis kode
public struct ActivityBars: View {
    public var levels: [CGFloat]  // Rentang 0...1 (5 bar)
    public var color: Color
    
    public init(levels: [CGFloat] = [0.2, 0.4, 0.8, 0.5, 0.3], color: Color = .cyan) {
        self.levels = levels
        self.color = color
    }
    
    public var body: some View {
        HStack(alignment: .center, spacing: 2.5) {
            ForEach(0..<5, id: \.self) { i in
                RoundedRectangle(cornerRadius: 1)
                    .fill(color)
                    .frame(
                        width: 2.5,
                        height: max(2, 12 * (levels.indices.contains(i) ? levels[i] : 0.2))
                    )
            }
        }
        .frame(height: 12)
        .animation(.spring(response: 0.18, dampingFraction: 0.6), value: levels)
    }
}
```

---

## 4. Logika Polling Kursor & Deteksi Batas Layar

Berikut adalah pola pengecekan koordinat global AppKit terhadap geometri notch:

```swift
/// Memeriksa apakah kursor mouse saat ini berada di dalam area notch
private func isMouseInsideNotch(notch: Notch, currentWidth: CGFloat, currentHeight: CGFloat) -> Bool {
    let mouse = NSEvent.mouseLocation
    let screenFrame = notch.screen.frame
    
    // Berikan toleransi margin 8pt di sekeliling panel
    let halfWidth = (currentWidth / 2.0) + 8.0
    let isInsideX = abs(mouse.x - notch.centerX) <= halfWidth
    let isInsideY = mouse.y >= (screenFrame.maxY - currentHeight - 8.0)
    
    return isInsideX && isInsideY
}
```

Task pemantau siklus hidup hover:

```swift
.task(id: isExpanded) {
    guard isExpanded else { return }
    var cursorExitTime: Date?
    
    while !Task.isCancelled {
        try? await Task.sleep(for: .milliseconds(100))
        
        if isMouseInsideNotch(notch: notch, currentWidth: currentWidth, currentHeight: currentHeight) {
            cursorExitTime = nil
            self.isHovering = true
            continue
        }
        
        // Kursor berada di luar area
        if cursorExitTime == nil {
            cursorExitTime = Date()
        }
        
        // Cek apakah sudah melebihi durasi linger (1.8s)
        if let exitTime = cursorExitTime,
           Date().timeIntervalSince(exitTime) >= NotchMetrics.lingerDuration {
            self.hoverTask?.cancel()
            self.isHovering = false
            cursorExitTime = nil
        }
    }
}
```
