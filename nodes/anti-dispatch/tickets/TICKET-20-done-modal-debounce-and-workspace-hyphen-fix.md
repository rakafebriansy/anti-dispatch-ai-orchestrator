---
id: TICKET-20
title: Fix Task Completed Modal Spam and Hyphenated Workspace Name Truncation
status: Done
priority: High
labels: [Bug, CDP, Notch, Notifications, State]
---

# Deskripsi
Terdapat dua isu pada Anti Dispatch:
1. Saat agen sedang berstatus working lalu pengguna mengetik di input chat, modal notifikasi "Task Completed" muncul berulang kali seperti spam kartu akibat fluktuasi transisi status RUNNING <-> DONE tanpa mekanisme debounce, dedup, dan proteksi window tunggal.
2. Label nama workspace pada notch untuk folder yang mengandung tanda hubung (seperti `anti-dispatch-dir`) terkadang terpotong menjadi hanya `anti` karena regex pemecah judul window VS Code memecah tanda hubung tanpa memeriksa spasi pembatas.

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Regex pemecah segmen judul window pada `extract_state.js` dan `CDPService.swift` `fallbackScript` hanya memecah pada pemisah yang dikelilingi spasi (`\s+[-—–]\s+`), menjaga nama folder bertanda hubung seperti `anti-dispatch-dir`.
- [x] `NotchViewModel` mengimplementasikan mekanisme penyembuhan nama workspace dengan memprioritaskan nama folder dari `resolvedURL` saat nama yang diekstraksi merupakan substring terpotong.
- [x] `NotchViewModel` mengimplementasikan debounce 2.5 detik untuk status `DONE` sebelum memicu notifikasi `doneHUD`.
- [x] `NotchViewModel` mengimplementasikan fingerprint per-turn (`latestResponse` + jumlah step) dan cooldown 10 detik untuk mencegah duplikasi notifikasi pada turn yang sama.
- [x] `DoneNotificationHUDController` menerapkan aturan satu panel per port (`single-panel-per-port`), menggantikan entri aktif jika ada notifikasi baru untuk port yang sama.
- [x] Seluruh unit test lulus 100% dengan tambahan pengujian regresi untuk nama bertanda hubung dan single-panel HUD.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DoneNotificationHUDController.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  1. Mengubah regex `title.split(/\s*[-—]\s*/)` menjadi `title.split(/\s+[-—–]\s+/)` pada `extract_state.js` dan `CDPService.swift` `fallbackScript` agar tanda hubung di dalam nama proyek/folder tidak terpecah menjadi segmen terpisah.
  2. Menambahkan `handleDoneStateTransition` dengan debounce asynchronous 2.5 detik, fingerprint hash berbasis summary + step count, cooldown 10 detik, dan reset fingerprint saat status `RUNNING` stabil (>= 2 siklus polling).
  3. Memperbarui `DoneNotificationHUDController.showNotification` untuk menutup panel aktif yang memiliki `cdpPort` yang sama sebelum menampilkan notifikasi baru.
  4. Menambahkan logika rekonsiliasi nama folder di `NotchViewModel` yang menggantikan nama parsial dengan `validURL.lastPathComponent`.
  5. Menambahkan unit test `testTitleSplitPreservesHyphenatedProjectNames`, `testDoneHUDControllerSinglePanelPerPort`, dan `testWorkspaceSessionDisplayNamePreservesHyphenatedFolder` pada `Anti_DispatchTests.swift` dengan hasil 100% test lulus.
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DoneNotificationHUDController.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Mengisolasi debounce dan dedup di layer `NotchViewModel` menjaga kesucian parsing DOM CDP `extract_state.js` tanpa menambah kompleksitas stateful di JavaScript injection.
