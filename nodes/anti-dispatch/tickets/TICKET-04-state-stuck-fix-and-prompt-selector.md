---
id: TICKET-04
title: Fix Stuck RUNNING State and Simplify Decision Prompt Selector
status: Done
priority: High
labels: [Bug, CDP, State Management, Agent Panel]
---

# Deskripsi
Mengatasi masalah status agen yang tetap terkunci di status RUNNING/Working meskipun agen telah selesai menghasilkan respons final (seperti pembuatan rencana implementasi), serta menyederhanakan mekanisme ekstraksi label prompt keputusan dari 14 selector generik menjadi 1 selector spesifik standar Antigravity (`span.font-medium.text-foreground`).

## Acceptance Criteria (Kriteria Penerimaan)
*Daftar spesifikasi mutlak (checklists) yang harus terpenuhi agar tiket ini sah dianggap berstatus selesai (Definition of Done).*
- [x] Selector `hasSpinner`, `hasActiveTaskElement`, dan `hasStatusBarRunning` dibatasi lingkup query-nya ke `agentPanel` dan selector internal Monaco Editor yang permanen (`.monaco-progressbar`, `[role="progressbar"]`, dll.) dihapus.
- [x] Deteksi respons final asisten (`latestResponse`) diekstrak sebelum evaluasi running state, dan ditambahkan guard `hasDefinitelyFinished = latestResponse && !hasStopButton` agar state segera beralih ke `DONE` saat tidak ada tombol Stop aktif.
- [x] Accordion step yang telah lalu tidak lagi memicu `hasActiveStepRunning` jika `latestResponse` sudah tersedia (`!latestResponse && steps.some(...)`).
- [x] `promptSelectors` yang berlebihan (14 selector fallback) digantikan dengan pemindaian tunggal spesifik pada `span.font-medium.text-foreground` atau turunannya di dalam `agentPanel`.
- [x] Skrip fallback di `CDPService.swift` disinkronkan secara identik dengan `extract_state.js`.
- [x] Seluruh unit tests pada `Anti_DispatchTests.swift` lulus tanpa kegagalan.

## Target Lingkup File (Affected Files)
*Daftar path file yang diinstruksikan atau berpotensi diubah sebagai referensi utama eksekusi AI.*
- `Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Core/Services/CDPService.swift`
- `Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH mengeksekusi tiket ini.*

- **Langkah Teknis Tereksekusi:**
  1. Mengisolasi pemindaian elemen aktif (`hasSpinner`, `hasActiveTaskElement`, `hasStatusBarRunning`) agar hanya mencari di dalam kontainer `agentPanel` daripada `document` global, serta menyingkirkan selector UI internal Monaco editor.
  2. Memindahkan urutan ekstraksi `latestResponse` ke atas sebelum evaluasi step verbs dan running state, serta menambahkan guard `hasDefinitelyFinished` yang menonaktifkan `isRunning` jika ada respons final tanpa tombol stop.
  3. Memperbaiki `hasActiveStepRunning` dengan guard `!latestResponse` agar accordion step historis tidak memicu flag aktif.
  4. Mengganti 14 selector fallback decision prompt menjadi satu selector spesifik `span.font-medium.text-foreground`.
  5. Menyelaraskan seluruh logika JS pada inline fallback `CDPService.swift`.
  6. Menambahkan unit test `testStateResetsToDoneWhenResponseCompleteAndNoStopButton` pada `Anti_DispatchTests.swift` dan memverifikasi kelulusan seluruh 14 test suite.
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti DispatchTests/Anti_DispatchTests.swift`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Mengeliminasi selector generik pada level document mencegah false-positive dari buffer terminal VS Code yang terbuka di bawah atau elemen progress bar monaco editor yang selalu ada di DOM.
