---
id: TICKET-11
title: "[BUG] State Done vs Working Misclassification & Untitled Workspace Resolution"
status: Done
priority: High
labels: [Bug, Core, CDP, State Management]
---

# Laporan Bug

## Deskripsi Masalah
1. State agen yang telah selesai mengeksekusi tugas (menampilkan ringkasan status sinkronisasi, Zero-Comment Policy, tiket retrospektif, dan pembaruan CHANGELOG) masih diklasifikasikan sebagai `RUNNING` (Working).
2. Workspace yang berada dalam kondisi Untitled Workspace dan membuka view "Code Search" dilabeli nama `"Code Search"` alih-alih folder repositori proyek aslinya (`"ff-prod"`).

## Langkah Reproduksi (Steps to Reproduce)
1. Jalankan proses AI pada Antigravity hingga selesai menghasilkan teks output final.
2. Amati status pada Anti Dispatch Notch: status tetap "Working" alih-alih "Completed" / "Done".
3. Buka tab atau view "Code Search" pada window Antigravity dengan workspace untitled.
4. Amati label workspace pada Anti Dispatch Notch: workspace dilabeli "Code Search".

## Ekspektasi (Expected Behavior)
1. Ketika respons AI telah tuntas dan tombol Stop tidak ada di DOM, state harus bertransisi ke `DONE` (`"Completed"`).
2. Ketika workspace untitled membuka view editor/search, nama workspace harus me-resolve ke nama folder proyek asli (`"ff-prod"`).

## Kondisi Aktual (Actual Behavior)
1. Sisa label accordion (`"Working..."`) dan selektor spinner yang terlalu luas memicu `isRunning = true`, menimpa evaluasi `latestResponse` dan mengunci status di `RUNNING`.
2. Parsing title regex naif mengekstrak `"Code Search"`, yang lolos dari filter generic karena tidak mengandung kata `"untitled"`, sehingga tidak memicu fallback ke path folder.

---

## Acceptance Criteria (Kriteria Penyelesaian Bug)
- [x] State agen yang selesai mengeksekusi instruksi dan memiliki respons lengkap terklasifikasi sebagai `DONE` (`"Completed"`).
- [x] Selektor spinner dibersihkan dari Monaco progressbar dan elemen statis, serta `hasDefinitelyFinished` guard mengabaikan accordion step lampau.
- [x] Parsing multi-segmen title dan filter nama generic (`"Code Search"`, `"Search"`, dll.) diterapkan pada `extract_state.js`, `CDPService.swift`, dan `NotchViewModel.swift`.
- [x] Workspace untitled dengan view Code Search me-resolve ke nama folder proyek (`"ff-prod"`).
- [x] Unit test baru (`testCompletedTurnWithSyncTextClassifiedAsDone` dan `testUntitledWorkspaceWithCodeSearchTitleResolvesToProjectFolder`) ditambahkan dan lulus 100%.
- [x] Zero-Comment Policy dipatuhi tanpa komentar baru pada kode.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- `anti-dispatch-ai-orchestrator/global-docs/LEARN.md`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`

---

## AI Execution Log & Output

- **Penyebab Akar (Root Cause Analysis):**
  1. `extract_state.js` mengevaluasi `isRunning` mendahului `latestResponse` dan menganggap label teks accordion lampau (`"Working..."`, `"Planning..."`, `"Thinking..."`) sebagai status aktif via `isLastStepActive`.
  2. Selektor `visibleSpinners` memuat class luas (`.monaco-progress-container.active`, `[class*="loading"]`) yang selalu aktif di VS Code.
  3. Parsing `document.title` Antigravity (`"Code Search - Untitled (Workspace) - Antigravity"`) mengekstrak segmen pertama (`"Code Search"`), dan pengecekan generic `isUntitledOrGeneric` gagal mendeteksi view non-untitled sehingga fallback ke folder path dilewati.
- **Langkah Perbaikan:**
  1. Menambahkan guard `hasDefinitelyFinished = !!latestResponse && !hasStopButton && !hasExplicitDecisionModal` yang langsung memprioritaskan state `DONE`.
  2. Membatasi pencarian spinner hanya di dalam `targetTurn` dengan selektor ikon berputar aktif (`.codicon-modifier-spin`, `.animate-spin`), menyingkirkan elemen Monaco progressbar.
  3. Memperbaiki parser title untuk mengambil segmen workspace nama (penultimate) dan menambahkan daftar view generic (`code search`, `search`, `explorer`, `settings`, `welcome`, dll.) pada `extract_state.js`, `CDPService.swift`, dan `NotchViewModel.swift`.
  4. Menambahkan unit test regression di `Anti_DispatchTests.swift` dan memverifikasi seluruh test suite lulus 100%.
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
  - `anti-dispatch-ai-orchestrator/global-docs/LEARN.md`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/tickets/TICKET-11-state-done-detection-and-workspace-naming.md`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`
