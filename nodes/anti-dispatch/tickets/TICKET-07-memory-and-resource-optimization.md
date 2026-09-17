---
id: TICKET-07
title: Optimasi Memori, CPU, dan Pengendalian Background Process
status: Done
priority: High
labels: [Performance, Memory, Concurrency, Refactor, Core]
---

# Deskripsi
Audit komprehensif terhadap Anti Dispatch macOS menu bar notch app menemukan beberapa area pemborosan memori dan CPU:
1. Akumulasi `Task` asinkron pencarian *git branch* yang berulang pada polling cycle tanpa pembatalan (*unbounded concurrent task spawning*).
2. Spawning proses sistem `lsof` dan `ps` berulang setiap siklus *polling* untuk *resolve workspace URL* tanpa mekanisme *caching*.
3. Penggunaan `URLSession.shared` default yang mempertahankan cache koneksi/respons HTTP memori.
4. Duplikasi memori string JavaScript *fallback extractor* (~370 baris) pada setiap instance `CDPService`.
5. Probing port TCP range 9221–9229 yang konstan setiap detik untuk port non-terkoneksi.
6. Pemborosan render animasi equalizer 140ms saat UI notch dalam keadaan tertutup (*compact*).
7. `latestResponse` string yang tidak terbatasi ukurannya saat menerima output panjang dari CDP.

Tiket ini mengimplementasikan rangkaian optimasi performa dan efisiensi memori untuk menjaga footprint aplikasi tetap berada pada kisaran optimal (<30MB RAM & <0.5% idle CPU).

## Acceptance Criteria (Kriteria Penerimaan)
*Daftar spesifikasi mutlak (checklists) yang harus terpenuhi agar tiket ini sah dianggap berstatus selesai (Definition of Done).*
- [x] Spawning `gitService.findGitRoot` dibatasi dengan `pendingBranchTasks` dictionary per-port dan dibatalkan sebelum membuat Task baru.
- [x] `resolveWorkspaceURLForPort` dilengkapi dengan `resolvedWorkspaceCache` berbasis TTL 10 detik.
- [x] Instance `CDPService` menggunakan konfigurasi `URLSession(configuration: .ephemeral)` untuk menonaktifkan caching memori & disk.
- [x] String fallback extraction script dikonversi menjadi `private static let fallbackScript` pada `CDPService`.
- [x] Port yang belum terhubung di-probe dengan throttled interval (setiap siklus ke-3).
- [x] Animasi equalizer audio bar di-gate hanya saat `hasRunningAgent && isContainerExpanded` dan interval dinaikkan ke 280ms.
- [x] Interval hover tracking pada notch view dinaikkan dari 80ms ke 150ms.
- [x] Nilai `latestResponse` dibatasi maksimal 500 karakter.
- [x] Seluruh *unit test* dan *UI test* lolos tanpa error.

## Target Lingkup File (Affected Files)
*Daftar path file yang diinstruksikan atau berpotensi diubah sebagai referensi utama eksekusi AI.*
- `Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`
- `Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH mengeksekusi tiket ini.*

- **Langkah Teknis Tereksekusi:**
  1. Mengaudit penggunaan memori, potensi leak, dan efisiensi background process, serta menyusun dokumen analisis dan *implementation plan*.
  2. Mengubah `CDPService.swift` untuk mengadopsi `URLSession(configuration: .ephemeral)` dan memindahkan fallback JS string ke `private static let fallbackScript`.
  3. Menambahkan `pendingBranchTasks`, `resolvedWorkspaceCache` (TTL 10s), `probeTickCount` (throttled 3-cycle probe), dan pemotongan `latestResponse` ke 500 karakter pada `NotchViewModel.swift`.
  4. Memperbarui `DynamicNotchRootView.swift` untuk meng-gate animasi waveform equalizer saat notch dalam kondisi terbuka (`isContainerExpanded`) serta mengatur polling hover ke 150ms.
  5. Menambahkan unit test baru pada `Anti_DispatchTests.swift` untuk validasi `latestResponse` truncation dan custom script init.
  6. Menjalankan seluruh test suite menggunakan `xcodebuild test` dan memverifikasi kelulusan 100%.

- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Menggunakan struktur cache non-blocking berbasis timestamp `Date` di MainActor `NotchViewModel` untuk menghindari overhead pembuatan aktor terpisah atau locking primitives.
  - Memastikan seluruh pembatalan `Task` asinkron Git branch dilakukan secara aman saat session dihapus melalui `removeSession(forPort:)`.
