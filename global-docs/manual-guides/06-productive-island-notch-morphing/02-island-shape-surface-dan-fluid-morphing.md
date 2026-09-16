# Modul 06.02: IslandShape Custom Bezier & Jet-Black Surface

Dokumen ini memandu pembuatan bentuk geometri dinamis (`IslandShape`) dengan kurva bezier organik di sudut atas (*outward curls*) dan permukaan visual *Jet-Black Glassmorphism* (`IslandSurface`) yang identik dengan desain **Productive Island** dan **Dynamic Island Apple**.

---

## 1. Anatomi Bentuk Bezier Organik (`IslandShape`)

Ketika antarmuka notch mengembang (*expand*) melebihi lebar notch fisik MacBook, tepi atas kiri dan kanan tidak boleh berupa siku tajam 90 derajat. Agar terlihat seolah-olah "tertarik keluar dari notch", sudut atas dibuat memiliki lengkungan kurva kuadratik (*quadratic bezier outward curl*):

```
       Outward Curl                          Outward Curl
         (Left)                                 (Right)
        ╭──────[  Notch Hardware MacBook  ]──────╮
       │                                         │
       │                                         │
       ╰─────────────────────────────────────────╯
    Rounded Bottom-Left                       Rounded Bottom-Right
       (Arc 90°-180°)                             (Arc 0°-90°)
```

* **Mode Layar Notch Fisik (`detached = false`):**
  * Sudut atas kiri & kanan: Melengkung keluar (*outward curl*) menyambung ke bezel atas.
  * Sudut bawah kiri & kanan: Melengkung ke dalam (*smooth continuous arc radius 16pt*).
* **Mode Layar Eksternal (`detached = true`):**
  * Bentuk kapsul melayang penuh (*stadium capsule / 4 rounded corners*).

---

## 2. Kode Swift Lengkap (`IslandShape.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/Views/Components/IslandShape.swift`:

```swift
import SwiftUI

/// Bentuk geometri notch adaptif.
/// Menghasilkan lengkungan organik di sudut atas saat menempel di bezel MacBook,
/// atau kapsul melayang sempurna saat berada di monitor eksternal.
public struct IslandShape: InsettableShape {
    public var radius: CGFloat
    public var detached: Bool
    public var inset: CGFloat
    
    public init(radius: CGFloat = 16, detached: Bool = false, inset: CGFloat = 0) {
        self.radius = radius
        self.detached = detached
        self.inset = inset
    }
    
    public func inset(by amount: CGFloat) -> IslandShape {
        var copy = self
        copy.inset += amount
        return copy
    }
    
    public func path(in rect: CGRect) -> Path {
        let r = rect.insetBy(dx: inset, dy: inset)
        
        // Skenario 1: Floating Capsule pada layar eksternal (Detached)
        if detached {
            return Path(roundedRect: r, cornerRadius: min(radius, r.height / 2.0), style: .continuous)
        }
        
        // Skenario 2: Organic Bezel Notch pada layar MacBook
        let curl: CGFloat = min(8, radius / 2.0)
        let cornerRadius = min(radius, r.height / 2.0)
        var path = Path()
        
        // 1. Mulai dari sudut kiri atas (sedikit di luar bounding box untuk outward curl)
        path.move(to: CGPoint(x: r.minX - curl, y: r.minY))
        
        // 2. Outward Bezier Curl kiri atas
        path.addQuadCurve(
            to: CGPoint(x: r.minX, y: r.minY + curl),
            control: CGPoint(x: r.minX, y: r.minY)
        )
        
        // 3. Garis lurus ke bawah menuju radius sudut kiri bawah
        path.addLine(to: CGPoint(x: r.minX, y: r.maxY - cornerRadius))
        
        // 4. Lengkungan sudut kiri bawah (Arc 180° -> 90°)
        path.addArc(
            center: CGPoint(x: r.minX + cornerRadius, y: r.maxY - cornerRadius),
            radius: cornerRadius,
            startAngle: .degrees(180),
            endAngle: .degrees(90),
            clockwise: true
        )
        
        // 5. Garis horizontal bawah
        path.addLine(to: CGPoint(x: r.maxX - cornerRadius, y: r.maxY))
        
        // 6. Lengkungan sudut kanan bawah (Arc 90° -> 0°)
        path.addArc(
            center: CGPoint(x: r.maxX - cornerRadius, y: r.maxY - cornerRadius),
            radius: cornerRadius,
            startAngle: .degrees(90),
            endAngle: .degrees(0),
            clockwise: true
        )
        
        // 7. Garis lurus ke atas menuju curl kanan atas
        path.addLine(to: CGPoint(x: r.maxX, y: r.minY + curl))
        
        // 8. Outward Bezier Curl kanan atas
        path.addQuadCurve(
            to: CGPoint(x: r.maxX + curl, y: r.minY),
            control: CGPoint(x: r.maxX, y: r.minY)
        )
        
        path.closeSubpath()
        return path
    }
}
```

---

## 3. Komponen Permukaan Visual (`IslandSurface.swift`)

Permukaan visual notch memadukan warna *Jet-Black* solid dengan lapisan *Ultra Thin Material* untuk menghasilkan kedalaman visual premium yang tidak tembus cahaya secara berlebihan:

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/Views/Components/IslandSurface.swift`:

```swift
import SwiftUI

/// Lapisan background dan border yang menyatu sempurna dengan warna bezel hitam MacBook
public struct IslandSurface: View {
    public var radius: CGFloat
    public var detached: Bool
    public var isGlass: Bool
    public var hasBorder: Bool
    
    public init(
        radius: CGFloat = 16,
        detached: Bool = false,
        isGlass: Bool = true,
        hasBorder: Bool = true
    ) {
        self.radius = radius
        self.detached = detached
        self.isGlass = isGlass
        self.hasBorder = hasBorder
    }
    
    public var body: some View {
        let shape = IslandShape(radius: radius, detached: detached)
        
        ZStack {
            if isGlass {
                shape
                    .fill(.ultraThinMaterial)
                    .environment(\.colorScheme, .dark)
                
                // Lapisan Jet-Black pekat agar tidak terlihat transparan abu-abu di atas wallpaper terang
                shape
                    .fill(Color.black.opacity(0.88))
            } else {
                shape
                    .fill(Color.black)
            }
            
            // Border aksen tipis (Apple HIG hairline border)
            if hasBorder {
                shape
                    .strokeBorder(Color.white.opacity(0.15), lineWidth: 0.75)
            }
        }
        .shadow(color: Color.black.opacity(detached ? 0.35 : 0), radius: 10, y: 4)
    }
}
```

---

## 4. Karakteristik Kurva Animasi Pegas (*Spring Physics*)
Animasi morphing notch menggunakan formula *interactive spring* dengan parameter:
* `response: 0.45`: Kecepatan respons perubahan dimensi (terasa instan dan responsif).
* `dampingFraction: 0.78`: Mencegah osilasi membal berlebihan (*no rubber-banding*), memberikan kesan solid dan presisi.

```swift
.animation(.spring(response: 0.45, dampingFraction: 0.78), value: currentHeight)
.animation(.spring(response: 0.45, dampingFraction: 0.78), value: currentWidth)
```
