---
id: TICKET-22
title: Fix DONE State Assistant Content Scoping and Notch AI Orchestrator UI Cleanup
status: Done
priority: High
labels: [Core, CDP, State-Machine, UI, Notch, Bug, Refactor]
---

# Deskripsi
Memperbaiki bug di mana workspace yang sudah berstatus selesai (`DONE`) keliru terdeteksi sebagai `WORKING` (`RUNNING`) akibat parser DOM langkah agen (`steps`) mengekstrak teks subjudul markdown balasan asisten (seperti `Testing, Build & Sync:`) yang berawalan kata aksi aktif, serta membersihkan tombol "AI Orchestrator" dari Notch UI agar aksesibilitasnya terfokus eksklusif pada macOS Status Menu Bar.

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Menambahkan filter `isInsideAssistantOrUserInputContent` pada `extract_state.js` dan fallback `CDPService.swift` untuk mengabaikan teks balasan asisten (`.leading-relaxed`, `div.select-text`, `markdown`, `prose`) dan input pengguna (`user-input`, `.whitespace-pre-wrap`) dari pengumpulan langkah agen (`steps`) dan pemindaian fallback `hasActiveThinkingOrWorking`.
- [x] Menghapus tombol "AI Orchestrator" dari header `MultiSessionAccordionView.swift` dan baris sesi `SessionRowView`.
- [x] Menghapus tombol "AI Orchestrator..." dari `standbyExpandedView` pada `DynamicNotchRootView.swift`.
- [x] Memastikan item menu "Generate AI Orchestrator..." pada `StatusBarController.swift` di Menu Bar tetap berfungsi optimal.
- [x] Menambahkan unit test `testDoneStatePreservedWhenAssistantMarkdownContainsActionHeadings` pada `Anti_DispatchTests.swift` dengan 100% test lulus.
- [x] Memverifikasi secara langsung via CDP live runtime pada instance Antigravity IDE bahwa status `DONE` tidak lagi terdistorsi oleh teks balasan asisten.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output

- **Langkah Teknis Tereksekusi:**
  1. Melakukan live CDP diagnostic extraction pada port 9222 dan menemukan akar masalah deterministik: subjudul tebal balasan asisten `"Testing, Build & Sync:"` terbaca oleh pengumpul `steps` karena berawalan kata `"Test"`, yang kemudian dicocokkan oleh `hasActiveThinkingOrWorking` sebagai `lower.startsWith('testing')` sehingga menghasilkan false positive `isRunning = true`.
  2. Mengimplementasikan fungsi `isInsideAssistantOrUserInputContent(el)` pada `extract_state.js` dan `CDPService.swift` `fallbackScript`, mengecualikan elemen DOM di dalam container balasan asisten dan input pengguna dari `allButtons`, `elements`, dan `hasActiveThinkingOrWorking`.
  3. Memverifikasi secara live dengan skrip CDP bahwa subjudul balasan asisten berhasil dieliminasi dari `stepHits` dan `subSteps`.
  4. Menghapus tombol "AI Orchestrator" dari `MultiSessionAccordionView.swift` (header & session row) dan `DynamicNotchRootView.swift` (`standbyExpandedView`).
  5. Menambahkan unit test `testDoneStatePreservedWhenAssistantMarkdownContainsActionHeadings` di `Anti_DispatchTests.swift`.
  6. Menjalankan `xcodebuild test` dengan hasil 51/51 tests lulus 100%.
  7. Memaketkan rilis aplikasi via `package_dmg.sh` ke `/Applications/Anti Dispatch.app`.

- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Isolasi parsing teks DOM menggunakan class boundary (`.leading-relaxed`, `div.select-text`, `[class*="markdown"]`, `div[class*="prose"]`) menjamin konten percakapan umum maupun teknis dari AI tidak akan pernah mengotori machine state detection agen.
