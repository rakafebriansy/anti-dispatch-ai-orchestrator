---
id: TICKET-19
title: Fix Workspace Naming and False-Positive Editor State Filtering
status: Done
priority: High
labels: [Core, CDP, Bug]
---

# Deskripsi
Memperbaiki dua anomali pada sistem pemantauan sesi:
1. Penamaan workspace pada folder non-git yang sebelumnya memilih subdirektori child secara acak non-deterministik alih-alih folder root yang dibuka pengguna.
2. Status 'Working' atau 'Waiting' yang terpicu secara false-positive saat idle akibat elemen editor/webview atau file teks terbuka yang mengandung kata kunci perintah dipindai sebagai aktivitas agen.

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Ekstraksi `workspaceName` memprioritaskan `folderFromPath` dan `envFolderName` dari konfigurasi workbench VS Code dibandingkan `document.title` parsial.
- [x] Metode `findWorkspaceIdentityRoot` pada `GitWorktreeService` menjaga identitas folder parent yang dibuka pengguna tanpa mengarahkan ke child git root secara tidak diinginkan.
- [x] Helper `isInsideEditorContent` menyaring seluruh elemen dalam `.monaco-editor`, `.editor-instance`, webview, dan `.part.editor` dari deteksi status, tombol stop, spinner, dan prompt keputusan.
- [x] Pemindaian fallback `hasActiveThinkingOrWorking` dibatasi pada kontainer `agentPanel` dengan filter editor.
- [x] 100% unit test dan UI test lulus tanpa regresi.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti Dispatch/Core/Services/GitWorktreeService.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  1. Menambahkan helper `isInsideEditorContent` dan memperbarui `extract_state.js` serta `fallbackScript` pada `CDPService.swift` untuk memblokir kontaminasi deteksi dari editor panel dan webview.
  2. Memprioritaskan `folderFromPath` dan `envFolderName` pada ekstraksi nama workspace di `extract_state.js`.
  3. Mengimplementasikan `findWorkspaceIdentityRoot(at:)` dan pengurutan deterministik subdirektori pada `GitWorktreeService.swift`.
  4. Menyelaraskan resolusi nama sesi pada `NotchViewModel.swift`.
  5. Menambahkan unit test baru pada `Anti_DispatchTests.swift` dan memvalidasi seluruh test suite dengan `xcodebuild test` (100% pass).
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti Dispatch/Core/Services/GitWorktreeService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Isolasi antara `findWorkspaceIdentityRoot` (identitas workspace) dan `findGitRoot` (pemeriksaan branch pada folder wrapper) memastikan konsistensi penamaan UI sekaligus menjaga kapabilitas deteksi branch git.
