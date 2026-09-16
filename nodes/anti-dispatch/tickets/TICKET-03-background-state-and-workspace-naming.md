---
id: TICKET-03
title: Background Process State Persistence & Untitled Workspace Resolution
status: Done
priority: High
labels: [Core, CDP, State Management, UI, Bug]
---

# Deskripsi
Memperbaiki klasifikasi state agen agar background process (seperti `npm install`, testing, background command, dan subprocess) tetap dipertahankan pada status Processing / Working (`RUNNING`) dan tidak prematur terdefinisi sebagai `DONE`. Selain itu, menyelesaikan masalah workspace berlabel "Untitled (Workspace)" saat folder ditambahkan ke VS Code/Antigravity tanpa nama workspace kustom, dengan mengekstrak nama folder aslinya baik dari parameter environment, Web configuration, DOM explorer, maupun resolusi URL subprocess agar antarmuka notch dan modal Decision Required selalu menampilkan nama folder asli yang bersih.

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Background process dan active operations (seperti `npm install`, `testing`, `building`, `executing`, command in progress) dideteksi secara andal dan mempertahankan status `RUNNING`.
- [x] Respon teks asisten tidak secara prematur mengubah status ke `DONE` selama proses latar belakang atau langkah aktif masih berjalan.
- [x] Workspace dengan label "Untitled (Workspace)" atau "Workspace" secara cerdas mengekstrak nama folder aslinya dari `envParams.workspace.folders`, `folderUri`, DOM Explorer, atau path filesystem.
- [x] `DecisionNotificationHUDView` dan `NotchViewModel` menampilkan nama folder asli saat workspace tidak memiliki judul khusus.
- [x] Label prompt pertanyaan aksi manusia (contoh: `"Allow syncing and committing TICKET-29 in orchestrator?"`) diekstrak secara akurat dan diprioritaskan penuh di atas blok kode perintah terminal di dalamnya (`Run git pull...`).
- [x] Seluruh unit test pada `Anti_DispatchTests` berhasil 100% tanpa galat.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti Dispatch/Core/Models/WorkspaceSession.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/SingleSessionView.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH mengeksekusi tiket ini.*

- **Langkah Teknis Tereksekusi:**
  1. Memperbarui `extract_state.js` dan fallback script di `CDPService.swift` dengan deteksi elemen task aktif, background process chips, dan kata kerja progresif (`running`, `installing`, `testing`, `building`, `executing`, `thinking`, `working`) yang belum selesai, sehingga `isRunning` bernilai `true` dan mencegah transisi prematur ke `DONE`.
  2. Menambahkan ekstraksi nama folder dari `meta[name="vscode-workbench-environment-params"]`, `vscode-workbench-web-configuration`, DOM Explorer, dan sanitasi path untuk menggantikan string `"Untitled (Workspace)"`.
  3. Memperbarui `NotchViewModel.swift` agar secara dinamis memetakan nama workspace ke `lastPathComponent` URL folder jika nama dari CDP berupa `"Untitled (Workspace)"` atau generic `"Workspace"`.
  4. Menjadikan `workspaceName` mutable pada `WorkspaceSession.swift`, menambahkan `displayName` property & auto-sanitization pada `WorkspaceSession.init`, serta mengintegrasikan `session.displayName` ke seluruh UI (`MultiSessionAccordionView`, `SingleSessionView`, `StatusBarController`, dan `DecisionNotificationHUDView`).
  5. Memperbaiki ekstraksi label prompt keputusan di `extract_state.js` dan `CDPService.swift` dengan memindai elemen label pertanyaan aksi secara spesifik (`span.text-foreground`, `span.font-medium`, selector dialog title, serta penelusuran hierarki kartu kontainer dari tombol interaktif) yang memuat kalimat pertanyaan (berakhiran `?` atau diawali frasa aksi `Allow `, `Confirm `, `Do you `, `Would you `), memastikan label prompt aksi seperti `"Allow syncing and committing TICKET-29 in orchestrator?"` selalu diprioritaskan di atas blok perintah terminal di dalamnya (`Run git pull...`).
  6. Menulis unit test baru `testRunningStateWithBackgroundProcess`, `testDecisionHUDDisplaysRealFolderName`, `testWorkspaceSessionDisplayNameResolution`, `testDecisionPromptQuestionTextPreservation`, dan `testActionApprovalPromptTextPrecedenceOverTerminalCommand` pada `Anti_DispatchTests.swift`, serta memvalidasi 100% test pass via `xcodebuild test` (13/13 lulus).
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti Dispatch/Core/Models/WorkspaceSession.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/SingleSessionView.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Mengisolasi pemulihan nama folder pada lapisan ekstraksi CDP, model `WorkspaceSession`, dan seluruh lapisan presentation untuk menjamin konsistensi tampilan dari baris accordion notch hingga modal dialog.
  - Memastikan prioritas ekstraksi prompt DOM membedakan secara tegas antara label pertanyaan aksi manusia vs cuplikan kode terminal, dengan memprioritaskan label manusia.
