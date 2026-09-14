# Modul 00.02: Persiapan Proyek Xcode & Konfigurasi

Dokumen ini memandu konfigurasi proyek Xcode untuk **Anti Dispatch** agar siap mengintegrasikan API tingkat rendah macOS (`NSPanel`, `Process`, `NSWorkspace`, `CryptoKit`, `SwiftUI`).

---

## 1. Konfigurasi Dasar Target Xcode

1. **Target OS Minimum:**
   - Buka project di Xcode (`Anti Dispatch.xcodeproj`).
   - Pilih Target **Anti Dispatch** -> Tab **General**.
   - Set **Minimum Deployments:** `macOS 14.0` (Sonoma) atau lebih tinggi.

2. **Pengaturan Swift Language & Concurrency:**
   - Masuk ke tab **Build Settings**.
   - Cari **Swift Language Version:** Pastikan memilih `Swift 5` atau `Swift 6`.
   - Cari **Strict Concurrency Checking:** Set ke `Complete` (untuk memastikan kepatuhan isolasi Data-Race Safety pada `actor` dan `@MainActor`).

---

## 2. Pengaturan Entitlements & App Sandbox (PENTING)

Anti Dispatch bertindak sebagai utilitas pengontrol sistem yang memerlukan:
- Menjalankan subprocess `/usr/bin/git`.
- Membuka koneksi socket TCP loopback lokal (`127.0.0.1:9221-9229`).
- Membuat jendela melayang tingkat tinggi (`NSWindow.Level.floating`) di atas aplikasi full-screen.
- Mengirim sinyal aktivasi jendela via `NSRunningApplication.activate`.

> ⚠️ **Catatan Penting:** Batasan *App Sandbox* di macOS memblokir eksekusi subprocess acak dan komunikasi CDP lokal. Oleh karena itu, aplikasi ini didistribusikan secara independen (*Direct Distribution DMG + Sparkle 2*) di luar Mac App Store.

### Langkah Konfigurasi:
1. Pilih Target **Anti Dispatch** -> Tab **Signing & Capabilities**.
2. Jika ada capability **App Sandbox**, klik tombol **Delete / Hapus (x)** pada App Sandbox.
3. Pastikan **Hardened Runtime** tetap aktif (diperlukan untuk proses *Apple Notarization*).
4. **Perilaku Jaringan (Network):**
   - Saat **App Sandbox dihapus**, macOS secara otomatis **mengizinkan semua koneksi jaringan keluar (*Outgoing Network Connections*)**, termasuk HTTP, WebSocket, dan TCP socket loopback (`127.0.0.1:9221-9229`), sehingga Anda **tidak perlu** mencentang izin jaringan tambahan.
   - Pada panel **Hardened Runtime**, Anda dapat membiarkan opsi default (seluruh checkbox boleh tidak dicentang, atau centang *Allow Execution of JIT-compiled Code* jika diperlukan).

---

## 3. Konfigurasi `Info.plist` (Tab Info di Xcode)

Di Xcode modern, Anda dapat mengaturnya langsung melalui UI Target Settings:
1. Pilih Target **Anti Dispatch** -> Tab **Info**.
2. Pada bagian **Custom macOS Application Target Properties**, klik tombol **(+)** untuk menambahkan baris baru:

| Nama Properti di UI Xcode | Raw XML Key | Tipe | Nilai (Value) | Keterangan |
|---|---|---|---|---|
| **`Application is agent (UIElement)`** | `LSUIElement` | `Boolean` | `YES` | Menyembunyikan ikon app di Dock (berjalan sebagai Menu Bar & Notch Utility) |
| **`Copyright (human-readable)`** | `NSHumanReadableCopyright` | `String` | `Copyright © 2026 Anti Dispatch.` | Informasi hak cipta aplikasi |

Atau jika membuka file `Info.plist` secara langsung dalam format XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <!-- Mencegah aplikasi menampilkan icon di Dock standar jika ingin murni berjalan sebagai Menu Bar & Notch Utility -->
    <key>LSUIElement</key>
    <true/>
    
    <!-- Deskripsi izin sistem -->
    <key>NSHumanReadableCopyright</key>
    <string>Copyright © 2026 Anti Dispatch. All rights reserved.</string>
</dict>
</plist>
```

> **Catatan `LSUIElement`:**
> Jika `<key>LSUIElement</key><true/>` diaktifkan, aplikasi akan berjalan sebagai *agent app* (tanpa ikon Dock), melainkan hanya muncul di Menu Bar dan Floating Notch HUD.

---

## 4. Struktur Folder yang Disarankan di Xcode

Buat kelompok folder (*groups*) di dalam project navigator Xcode seperti berikut:

```
Anti Dispatch/
├── App/
├── Core/
│   ├── Services/
│   ├── Models/
│   └── Protocols/
├── Presentation/
│   ├── Notch/
│   │   ├── Views/
│   │   └── ViewModels/
│   ├── Modals/
│   └── MenuBar/
├── Utilities/
└── Resources/
```
