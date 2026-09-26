---
id: TICKET-16
title: Decision Prompt Command Extraction & Top Option Agree Hint
status: Done
priority: High
labels: [Frontend, CDP, macOS, SwiftUI, Bugfix, UX]
---

# Deskripsi
Memperbaiki anomali pada popup/modal Decision Required HUD di mana teks penjelasan/deskripsi yang diekstraksi selalu keliru berupa teks opsi teratas (misalnya "Allow this time"), padahal yang seharusnya muncul adalah deskripsi perintah terminal sesungguhnya (misalnya "npx graphify update") atau teks pertanyaan izin yang spesifik. Selain itu, menambahkan catatan/hint informatif di bawah teks deskripsi bahwa mengklik tombol Agree akan secara otomatis menerima/memilih opsi teratas (misalnya "Allow this time") guna memberikan kejelasan saat dialog memuat banyak opsi keputusan.

## Acceptance Criteria (Kriteria Penerimaan)
*Daftar spesifikasi mutlak (checklists) yang harus terpenuhi agar tiket ini sah dianggap berstatus selesai (Definition of Done).*
- [x] Memperbaiki ekstraksi DOM pada `extract_state.js` dan fallback `CDPService.swift` agar memisahkan antara opsi radio/checkbox dengan perintah aksi/pertanyaan izin, memprioritaskan blok kode perintah terminal (`code`, `pre`, `command-line`, `terminal`, `npx`, `git`, dll.) sebagai `decisionPrompt`.
- [x] Mengekstrak teks opsi teratas/rekomendasi (`topOption`) dari kontainer keputusan aktif dan menyertakannya pada payload JSON ekstraksi CDP.
- [x] Menambahkan properti `topOption: String?` pada model `CDPExtractedState` dan `WorkspaceSession` serta menyelaraskan sinkronisasi datanya di `NotchViewModel.swift`.
- [x] Memperbarui antarmuka `DecisionNotificationHUDView` dan kalkulasi window pada `DecisionNotificationHUDController.swift` dengan micro-note `Agree selects top option: "<topOption>"` serta styling ikon SF Symbol `info.circle`.
- [x] Mencegah status stuck di `WAITING` setelah pertanyaan `ask_question` dijawab dengan menambahkan helper `isSupersededInConversation` (memeriksa posisi dokumen terhadap respon asisten berikutnya) dan memfilter ringkasan kartu pertanyaan statis.
- [x] Memperbarui purwarupa visual HTML `decision-modal-agree-button.html` di direktori `prototypes/` dengan contoh perintah `npx graphify update` dan micro-note opsi teratas.
- [x] Menambahkan unit test regresi di `Anti_DispatchTests.swift` (`testCDPExtractedStateWithTopOptionDecoding`, `testDecisionHUDViewWithTopOption`, `testSupersededQuestionStateHandling`) serta memastikan seluruh 41 unit dan UI tests lulus 100%.

## Target Lingkup File (Affected Files)
*Daftar path file yang diinstruksikan atau berpotensi diubah sebagai referensi utama eksekusi AI.*
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti Dispatch/Core/Models/WorkspaceSession.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/prototypes/decision-modal-agree-button.html`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH mengeksekusi tiket ini.*

- **Langkah Teknis Tereksekusi:**
  1. Menganalisis akar masalah DOM scanning di mana elemen opsi radio (`[role="radio"]`, `<label>`) dengan class `font-medium text-foreground` yang diawali kata "Allow " secara keliru tertangkap oleh `questionEls` sebagai `bestQuestionEl`, menutupi blok kode perintah (`<code>npx graphify update</code>`).
  2. Memperbarui `extract_state.js` dan fallback static script di `CDPService.swift` dengan pemindai opsi `isOptionText`, ekstraksi terpisah untuk `topOption` (memprioritaskan opsi berlabel `(recommended)` atau opsi pertama), serta memprioritaskan blok perintah `code`, `pre`, `[class*="command"]`, `[class*="terminal"]` ke dalam `decisionPrompt`.
  3. Mengatasi anomali status stuck di `WAITING` setelah pertanyaan chat/tool dilewati dengan menambahkan fungsi `isSupersededInConversation` yang memvalidasi bahwa kartu keputusan tidak memiliki respon asisten atau langkah baru setelahnya, serta membatasi pemindaian inline hanya pada turn paling akhir (`targetTurn`).
  4. Memperbarui model `CDPExtractedState` (Codable encoding/decoding) dan `WorkspaceSession` dengan field `topOption: String?` berkemampuan backward-compatible default value.
  5. Menyelaraskan `updateOrInsertSession` pada `NotchViewModel.swift` agar memetakan `extracted.topOption` ke sesi yang sedang aktif maupun sesi baru.
  6. Menambahkan rendering catatan mikro `Agree selects top option: "\(optionText)"` di bawah prompt/command pada `DecisionNotificationHUDView` dan menyesuaikan tinggi frame window menjadi 160pt pada `DecisionNotificationHUDController.swift`.
  7. Memperbarui purwarupa visual `decision-modal-agree-button.html` di `nodes/anti-dispatch/prototypes/` dengan styling `.top-option-hint` dan command `npx graphify update`.
  8. Menambahkan unit test `testCDPExtractedStateWithTopOptionDecoding`, `testDecisionHUDViewWithTopOption`, dan `testSupersededQuestionStateHandling` pada `Anti_DispatchTests.swift`, memvalidasi eksekusi `xcodebuild test` (41 unit & UI tests lulus 100%).
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti Dispatch/Core/Models/WorkspaceSession.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/prototypes/decision-modal-agree-button.html`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Pemisahan data antara deskripsi aksi (`decisionPrompt`) dan pilihan aksi default (`topOption`) menjamin teks penjelasan di HUD selalu menampilkan perintah/pertanyaan teknis asli tanpa terdistorsi oleh teks label radio button.
  - Pengecekan posisi dokumen `isSupersededInConversation` memastikan histori kartu pertanyaan/tool yang sudah selesai tidak lagi memicu status `WAITING` saat percakapan berlanjut.
