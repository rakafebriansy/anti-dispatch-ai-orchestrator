---
id: TICKET-17
title: WebSocket Connection Refresh per Project
status: Done
priority: High
labels: [Frontend, CDP, macOS, SwiftUI, Bugfix, UX]
---

# Deskripsi
Mengimplementasikan fitur manual refresh koneksi WebSocket CDP per sesi workspace/project pada notch bar Anti Dispatch. Fitur ini dirancang untuk mengatasi masalah koneksi zombie atau stale WebSocket yang terjadi secara berkala di mana state berpindah lambat (2-3 detik delay) atau status langsung terdeteksi "working" saat membuka project baru padahal belum ada aktivitas. Tombol refresh memutuskan koneksi WebSocket lama secara paksa, melakukan re-probe port `/json/list`, menyambungkan ulang WebSocket dengan URL fresh, dan segera memicu evaluasi state terbaru dengan indikator visual progress spinner.

## Acceptance Criteria (Kriteria Penerimaan)
*Daftar spesifikasi mutlak (checklists) yang harus terpenuhi agar tiket ini sah dianggap berstatus selesai (Definition of Done).*
- [x] Menambahkan method `refreshConnection(port: Int) async -> Bool` pada actor `CDPService.swift` yang memutus task WebSocket lama, menunggu hingga termination selesai, melakukan probing ulang port, menghubungkan ulang WebSocket target, dan mengevaluasi status terbaru.
- [x] Menambahkan tracking state `refreshingPorts: Set<Int>`, accessor `isRefreshingPort(_ port: Int) -> Bool`, dan method `refreshSession(_ session: WorkspaceSession) async` pada `@MainActor NotchViewModel.swift` untuk mengelola siklus refresh dan memicu `pollActiveSessions(forceFullScan: true)`.
- [x] Menambahkan komponen `RefreshSessionButton` pada `MultiSessionAccordionView.swift` yang ditempatkan secara konsisten di antara status pill dan tombol × close pada setiap `SessionRowView`.
- [x] Memberikan feedback visual interaktif selama refresh pada `SessionRowView`: status pill menampilkan teks `"Reconnecting..."` dengan warna cyan, tombol Jump dan Close dinonaktifkan/dimmed, serta tombol refresh menampilkan progress spinner mini.
- [x] Menambahkan tombol refresh koneksi WebSocket dengan status spinner pada `SingleSessionView.swift`.
- [x] Membuat purwarupa interaktif `websocket-refresh-button.html` pada direktori `prototypes/` yang memvisualisasikan penempatan tombol dan transisi state normal vs refreshing.
- [x] Menambahkan unit test regresi pada `Anti_DispatchTests.swift` (`testRefreshConnectionOnInactivePortReturnsFalse`, `testIsRefreshingPortReflectsLifecycle`, `testRefreshSessionUpdatesActiveSessionState`) dan memastikan seluruh suite test lulus 100%.

## Target Lingkup File (Affected Files)
*Daftar path file yang diinstruksikan atau berpotensi diubah sebagai referensi utama eksekusi AI.*
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/SingleSessionView.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/prototypes/websocket-refresh-button.html`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH mengeksekusi tiket ini.*

- **Langkah Teknis Tereksekusi:**
  1. Menganalisis masalah koneksi zombie pada `webSocketTasks` di `CDPService` di mana status `isConnected` bernilai `true` karena state task masih `.running` padahal transport underlying terhambat atau membawa cache evaluasi lama.
  2. Mengimplementasikan method `refreshConnection(port:) async -> Bool` pada actor `CDPService` yang secara eksplisit memanggil `disconnectWebSocket(port:)`, memberi delay 100ms untuk pelepasan soket, memanggil `probePort(port:)`, membuat koneksi WebSocket baru, dan mengevaluasi state awal.
  3. Memperbarui `NotchViewModel.swift` dengan property `@ObservationIgnored` atau terobservasi `refreshingPorts: Set<Int>`, helper method `isRefreshingPort(_:)`, serta method `refreshSession(_:) async` yang membungkus pembaruan state port dengan `defer` block dan memicu `pollActiveSessions(forceFullScan: true)`.
  4. Membuat komponen `RefreshSessionButton` dan mengintegrasikannya ke `SessionRowView` pada `MultiSessionAccordionView.swift`, diposisikan di antara status pill dan tombol × close session.
  5. Menambahkan state visual saat refresh berlangsung: status pill menampilkan `"Reconnecting..."` berwarna cyan, tombol Jump dan Close dinonaktifkan, serta tombol refresh menampilkan `ProgressView` berukuran mini.
  6. Memperbarui `SingleSessionView.swift` dengan tombol refresh `arrow.clockwise` sebelum tombol jump to project.
  7. Membuat purwarupa interaktif `websocket-refresh-button.html` pada direktori node `prototypes/` untuk memvalidasi posisi dan interaksi visual button.
  8. Menambahkan 3 unit test komprehensif pada `Anti_DispatchTests.swift` (`testRefreshConnectionOnInactivePortReturnsFalse`, `testIsRefreshingPortReflectsLifecycle`, `testRefreshSessionUpdatesActiveSessionState`).
  9. Menjalankan test suite menggunakan `xcodebuild test` dan memvalidasi bahwa seluruh unit test serta UI test lulus 100%.
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/SingleSessionView.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/prototypes/websocket-refresh-button.html`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Isolasi siklus refresh di dalam `NotchViewModel` via actor `CDPService` menjamin thread-safety tanpa memodifikasi interval background polling otomatis.
  - Penempatan tombol refresh di samping status pill dan tombol close memberikan akses instan tanpa mengganggu hierarki visual bar atau nama workspace.
