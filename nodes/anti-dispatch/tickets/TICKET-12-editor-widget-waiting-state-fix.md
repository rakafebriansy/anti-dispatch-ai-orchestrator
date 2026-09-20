---
id: TICKET-12
title: "[BUG] Monaco Editor Find Widget False Positive WAITING State Fix"
status: Done
priority: High
labels: [Bug, Core, CDP, State Management]
---

# Laporan Bug

## Deskripsi Masalah
Terjadi bug di mana workspace Antigravity (seperti `/Users/raka/Developer/repositories/projects/wealthy-people-org/omen-dir/omen`) selalu terdeteksi dan terkunci pada status `WAITING` ("Agent needs permission to proceed.") tanpa pembaruan apa pun, meskipun agen sedang aktif bekerja (`RUNNING`) atau telah selesai (`DONE`).

## Langkah Reproduksi (Steps to Reproduce)
1. Buka workspace Antigravity (misalnya `omen`).
2. Tekan `Cmd+F` atau `Ctrl+F` di salah satu tab editor (misal: `page.tsx`) sehingga widget pencarian editor (`.find-widget.visible`) terbuka di DOM.
3. Jalankan prompt tugas pada Antigravity Agent.
4. Amati status pada Anti Dispatch Notch: status selalu terkunci di `WAITING` ("Agent needs permission to proceed.") dan tidak pernah berubah ke `Working` atau `Completed`.

## Ekspektasi (Expected Behavior)
1. Widget internal editor seperti Find/Replace Monaco tidak boleh dianggap sebagai modal keputusan persetujuan agen.
2. Ketika agen sedang menjalankan tugas, status harus terdeteksi secara akurat sebagai `RUNNING` (Working) dan setelah tuntas berganti ke `DONE` (Completed).

## Kondisi Aktual (Actual Behavior)
1. Monaco find widget memiliki atribut `role="dialog"` dan class `editor-widget find-widget visible` serta memuat toggle ber-`role="checkbox"` (`.monaco-custom-toggle`).
2. Query `activeModals` menangkap dialog editor ini dan memicu `hasExplicitDecisionModal = true`, yang secara mutlak membajak status menjadi `WAITING` dengan fallback prompt `"Agent needs permission to proceed."`.

---

## Acceptance Criteria (Kriteria Penyelesaian Bug)
- [x] Helper `isMonacoEditorWidget` menyaring kontainer dan widget internal Monaco Editor (`.monaco-editor`, `.find-widget`, `.replace-widget`, `.overlayWidgets`, `.quick-input-widget`, `.suggest-widget`).
- [x] `isDecisionButton` dan `isSemanticRadioOrCheckbox` mengabaikan tombol dan toggle bawaan Monaco (`.monaco-custom-toggle`).
- [x] `activeModals`, `permissionCards`, dan `potentialCards` mengecualikan dialog internal editor dan hanya memicu `hasExplicitDecisionModal` jika modal tersebut memuat tombol keputusan atau berada dalam konteks pertanyaan agen yang valid.
- [x] Evaluasi langsung pada instance `omen` berhasil bertransisi secara akurat ke status aktual (`RUNNING` / `DONE`).
- [x] Unit test baru (`testMonacoFindWidgetDoesNotTriggerWaitingState` dan `testGenuineDecisionModalClassification`) ditambahkan dan lulus 100%.
- [x] Zero-Comment Policy dipatuhi tanpa komentar baru pada kode sumber.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/tickets/TICKET-12-editor-widget-waiting-state-fix.md`
- `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`

---

## AI Execution Log & Output

- **Penyebab Akar (Root Cause Analysis):**
  1. `extract_state.js` memindai seluruh elemen global dengan query `[role="dialog"]` dan `[role="radiogroup"]` tanpa mengecualikan kontainer internal Monaco Editor.
  2. Saat pengguna membuka fitur pencarian editor (`.find-widget`), dialog Monaco tersebut memuat toggle filter (regex, whole-word, case-sensitive) beratribut `role="checkbox"`.
  3. Hal ini menyebabkan `hasRadios` dan `hasExplicitDecisionModal` bernilai `true`, membajak status eksekusi menjadi `WAITING` secara permanen dan mencegah pembaruan ke `RUNNING` atau `DONE`.
- **Langkah Perbaikan:**
  1. Menambahkan helper `isMonacoEditorWidget` pada `extract_state.js` dan inline `fallbackScript` di `CDPService.swift`.
  2. Memfilter `activeModals`, `modalBtns`, `modalRadios`, `permissionCards`, dan `potentialCards` agar mengabaikan seluruh elemen editor Monaco.
  3. Memperketat validasi `hasRadios` agar hanya mengaktifkan `hasExplicitDecisionModal` jika modal berada di dalam kontainer agen atau memiliki elemen judul pertanyaan yang valid.
  4. Menambahkan unit test `testMonacoFindWidgetDoesNotTriggerWaitingState` dan `testGenuineDecisionModalClassification` pada `Anti_DispatchTests.swift`.
  5. Menjalankan `xcodebuild test` dan memastikan 25 unit & UI test lulus 100%.
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/tickets/TICKET-12-editor-widget-waiting-state-fix.md`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`
