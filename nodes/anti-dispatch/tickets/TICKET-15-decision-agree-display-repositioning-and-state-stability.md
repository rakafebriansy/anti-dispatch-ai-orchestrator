---
id: TICKET-15
title: "[BUGFIX] Decision HUD Agree Button CDP Automation, External Display Dynamic Repositioning, Modal Gap Calibration, and Agent State Flapping Stability"
status: Done
priority: High
labels: [Bugfix, CDP Automation, Multi-Monitor, UI HUD, State Machine, Zero-Comment]
---

# Deskripsi
Memperbaiki empat permasalahan krusial pada Anti Dispatch:
1. **CDP Agree Button Automation**: Tombol "Agree" pada modal *Decision Required HUD* belum berhasil melakukan konfirmasi/persetujuan otomatis ke Antigravity karena tombol keputusan berada di dalam *modal/dialog container* atau *Radix UI radiogroup* dengan event listener pointer yang membutuhkan sekuens interaksi lengkap (`pointerdown` -> `mousedown` -> `focus` -> `pointerup` -> `mouseup` -> `click`).
2. **Multi-Monitor / Studio Display Reconnection Positioning**: Saat layar eksternal (Studio Display / monitor resolusi tinggi) dikoneksikan, panel notch menempel di tengah layar karena koordinat `origin.y` sebelumnya terkunci pada resolusi MacBook internal dan tidak merespons perubahan geometri layar macOS.
3. **Modal Gap Calibration**: Jarak visual (*gap*) antara modal *Decision Required HUD* dan modal *Done HUD* saat muncul bersamaan terlalu renggang akibat akumulasi *transparent padding* dari kedua window panel.
4. **Agent State Flapping (Working -> Done -> Working)**: Transisi status agen tidak stabil di mana agen yang sedang mengeksekusi *tool/step* sementara didefinisikan sebagai `Done` karena adanya partial response markdown text di antara step eksekusi, lalu kembali menjadi `Working`.

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Skrip `agree_decision.js` diperbarui dengan traversal ke parent dialog/modal, sekuens lengkap `PointerEvent` dan `MouseEvent`, seleksi otomatis radio option `(recommended)`, pengecualian kata kunci negatif (*skip, cancel, deny*), dan daftar *allow keywords* yang komprehensif.
- [x] Skrip `extract_state.js` diperbaiki agar tidak membatalkan `isRunning` hanya karena keberadaan `latestResponse` parsial, memperluas pencarian stop button ke seluruh `document`, dan memprioritaskan active step.
- [x] `NotchPanelController.swift` mengobservasi `NSApplication.didChangeScreenParametersNotification` dan langsung memanggil `updateFrame(animated: false)` saat monitor eksternal tersambung/terputus atau resolusi layar berubah.
- [x] `DecisionNotificationHUDController.swift` dan `DoneNotificationHUDController.swift` mengobservasi perubahan parameter layar dan mereposisi window secara adaptif.
- [x] Jarak frame `DoneNotificationHUDController.calculateFrame` dikalibrasi (`startY = decisionFrame.minY + 6`) sehingga jarak visual efektif antar modal tepat 10pt.
- [x] `CDPService.swift` memiliki fallback script raw string (`#""" ... """#`) yang sinkron dengan `extract_state.js` dan `agree_decision.js`.
- [x] `NotchViewModel.swift` memastikan koneksi WebSocket siap sebelum mengeksekusi `agreeDecision` dan memberikan jeda 200ms sebelum *polling state* pasca-konfirmasi.
- [x] Rangkaian unit test (`testRunningStatePrecedenceWithActiveSteps`, `testMultiMonitorNotchMetricsCalculation`, `testDoneNotificationHUDFrameCalculationWithDecisionHUD`) ditambahkan dan seluruh 38 unit & UI tests lulus 100% (`xcodebuild test`).
- [x] Zero-Comment Policy dipatuhi secara ketat tanpa komentar baru pada source code Swift / JS.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/agree_decision.js`
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/NotchPanelController.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DoneNotificationHUDController.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/tickets/TICKET-15-decision-agree-display-repositioning-and-state-stability.md`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`

---

## AI Execution Log & Output

- **Langkah Teknis Tereksekusi:**
  1. Memodifikasi `agree_decision.js` untuk mendeteksi `role="dialog"`, form, dan card container; menyimulasikan event pointer & mouse terpadu; memilih radio button rekomendasi secara otomatis; dan mengklik tombol aksi persetujuan.
  2. Memperbaiki logika state classification pada `extract_state.js` dengan menghapus negasi `!hasDefinitelyFinished` dari `isRunning`, memindai tombol stop di seluruh document, dan mengekstrak active step headline dengan akurat.
  3. Menambahkan listener `NSApplication.didChangeScreenParametersNotification` pada `NotchPanelController`, `DecisionNotificationHUDController`, dan `DoneNotificationHUDController` untuk menjamin panel selalu melekat di tepi atas layar pada setiap konfigurasi monitor.
  4. Menyelaraskan formula penumpukan Done HUD di bawah Decision HUD pada `DoneNotificationHUDController.calculateFrame` untuk mengeliminasi padding transparan berlebih sehingga gap visual tepat 10pt.
  5. Menyelaraskan raw string multiline literals pada `CDPService.swift` serta menambahkan unit test pendukung di `Anti_DispatchTests.swift`.
  6. Menjalankan pengujian `xcodebuild test` dan memastikan 38 pengujian lulus tanpa kegagalan.
  7. Menjalankan sinkronisasi knowledge graph via `graphify update`.
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/agree_decision.js`
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/NotchPanelController.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DoneNotificationHUDController.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/tickets/TICKET-15-decision-agree-display-repositioning-and-state-stability.md`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`
- **Catatan & Keputusan Arsitektural:**
  - Pada lingkungan multi-monitor macOS, saat resolusi layar berubah atau monitor utama berpindah, notifikasi sistem `NSApplication.didChangeScreenParametersNotification` dipancarkan di Main Thread. Penanganan langsung pada observer memastikan reposisi frame instan tanpa *stuttering* atau posisi melayang di tengah layar.
  - Untuk event interaksi di VSCode/Antigravity webview DOM, penekanan tombol modern membutuhkan `PointerEvent` di samping `MouseEvent` standar agar synthetic click dikenali oleh framework UI internal (React / Radix UI).
