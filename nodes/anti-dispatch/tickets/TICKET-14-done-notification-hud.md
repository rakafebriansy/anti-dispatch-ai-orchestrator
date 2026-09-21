---
id: TICKET-14
title: "[FEATURE] Done Notification Floating HUD Modal with Auto-Dismiss and Smart Stacking"
status: Done
priority: High
labels: [Feature, Core, UI, Notification HUD, Stacking]
---

# Deskripsi
Menambahkan notifikasi melayang terpisah (**Done Notification HUD**) berukuran lebih ringkas (310pt x 68pt) beraksen hijau emerald (`#30D158`) yang muncul saat agen pada suatu repositori/workspace telah menyelesaikan tugasnya (`state == .done`). Notifikasi ini memiliki durasi tampil otomatis selama 3.0 detik, menampilkan nama folder/workspace dan ringkasan pekerjaan, dapat ditutup secara instan dengan klik, tersusun vertikal ke bawah (*stacking*) jika ada beberapa notifikasi Done bersamaan, serta secara otomatis berposisi di bawah modal **Decision Required** dengan jarak (*gap*) konsisten sebesar 10pt bila keduanya aktif bersamaan.

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Struktur data `DoneNotificationItem` dibuat untuk merepresentasikan notifikasi penyelesaian tugas per sesi kerja.
- [x] Komponen tampilan `DoneNotificationHUDView` dirancang ringkas (310pt x 68pt) dengan aksen hijau emerald, badge direktori monospaced, ringkasan tugas 2 baris, progress countdown bar 3.0 detik, dan gestur tap-to-dismiss.
- [x] Kontroller `DoneNotificationHUDController` mengelola siklus hidup, auto-dismiss 3.0 detik berbasis task async, dan penghitungan posisi frame (*coordinate math*) dengan orientasi macOS origin (0,0) di kiri bawah.
- [x] Penumpukan vertikal (*smart stacking*) ke arah bawah dengan gap 10pt terimplementasi jika beberapa workspace selesai dalam waktu berdekatan.
- [x] Integrasi posisi adaptif di bawah modal Decision Required: bila modal Decision Required aktif, modal Done pertama muncul tepat 10pt di bawahnya; bila tidak aktif, modal Done muncul di sudut kanan atas layar (24pt margin).
- [x] Deteksi transisi status agen di `NotchViewModel.swift` hanya memicu notifikasi saat terjadi transisi dari `.running` atau `.waiting` menuju `.done`.
- [x] Binding otomatis `decisionHUD.onVisibilityChanged` ke `doneHUD.setDecisionHUDFrame(...)` untuk reposisi mulus saat modal Decision Required muncul/hilang.
- [x] Purwarupa interaktif visual `done-notification-modal.html` dibuat dan divalidasi pada direktori `prototypes/`.
- [x] Unit test komprehensif (`testDoneNotificationItemCreation`, `testDoneNotificationHUDFrameCalculationWithoutDecisionHUD`, `testDoneNotificationHUDFrameCalculationWithDecisionHUD`, `testDoneNotificationHUDViewDismissCallback`) ditambahkan ke `Anti_DispatchTests.swift`.
- [x] Zero-Comment Policy dipatuhi secara ketat tanpa komentar baru pada source code Swift / JS.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DoneNotificationHUDController.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/prototypes/done-notification-modal.html`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/tickets/TICKET-14-done-notification-hud.md`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`

---

## AI Execution Log & Output

- **Langkah Teknis Tereksekusi:**
  1. Merancang dan menguji purwarupa interaktif `done-notification-modal.html` yang mensimulasikan modal Done tunggal, multiple stacking, dan koordinasi jarak di bawah modal Decision Required.
  2. Mengimplementasikan `DoneNotificationItem`, `DoneNotificationHUDController`, dan `DoneNotificationHUDView` di `DoneNotificationHUDController.swift` dengan sistem penentuan posisi window koordinat macOS, animasi masuk/keluar, auto-dismiss 3 detik, dan progress bar.
  3. Memperbarui `DecisionNotificationHUDController.swift` dengan properti `currentWindowFrame: NSRect?` dan callback `onVisibilityChanged: (@MainActor () -> Void)?`.
  4. Menghubungkan `doneHUD` pada `NotchViewModel.swift`, memantau transisi state `previousSessionStates` untuk mendeteksi `stateEnum == .done`, dan mengaitkan perubahan visibilitas Decision HUD.
  5. Menambahkan rangkaian unit test verifikasi model, kalkulasi koordinat, dan interaksi dismiss di `Anti_DispatchTests.swift`.
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DoneNotificationHUDController.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/prototypes/done-notification-modal.html`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/tickets/TICKET-14-done-notification-hud.md`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`
- **Catatan & Keputusan Arsitektural:**
  - Mengingat sistem koordinat layar macOS memiliki titik origin (0,0) di kiri bawah, kalkulasi penumpukan ke arah bawah dihitung dengan formula `y = startY - (i + 1) * cardHeight - i * gap`.
  - Jika modal Decision Required sedang terlihat, `startY` secara dinamis disetel ke `decisionFrame.minY - gap` sehingga gap antar jenis modal dan antar sesama modal Done konsisten sebesar 10pt.
