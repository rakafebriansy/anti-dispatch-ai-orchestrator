---
id: TICKET-18
title: Open Worktree Existing Branch Support and Mode Switching
status: Done
priority: High
labels: [Frontend, Feature, Git, Worktree]
---

# Deskripsi
Mengubah fitur "New Worktree" menjadi "Open Worktree" pada dynamic notch dan status bar menu. Memungkinkan pengguna untuk membuka worktree dari branch yang sudah ada (`Open Existing`) maupun membuat branch baru dari base branch yang ditentukan (`Create New`).

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Mendukung dua mode kerja pada Worktree modal: "Open Existing" dan "Create New".
- [x] Mode "Open Existing" menampilkan dropdown branch yang tersedia untuk langsung dibuka tanpa memaksa pembuatan branch baru.
- [x] Mode "Create New" mempertahankan alur pembuatan branch baru dari base branch.
- [x] `GitWorktreeService` mendukung branch lokal maupun remote ref tracking secara otomatis saat penyiapan worktree.
- [x] Label UI pada accordion header, dynamic notch, dan status bar diperbarui dari "New Worktree" menjadi "Open Worktree".
- [x] Unit test memverifikasi definisi `WorktreeMode` serta helper method branch pada ViewModel.
- [x] Seluruh test suite lulus 100% via `xcodebuild test`.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Presentation/Modals/BranchCreatorSheet.swift`
- `Anti Dispatch/Anti Dispatch/Core/Services/GitWorktreeService.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH mengeksekusi tiket ini.*

- **Langkah Teknis Tereksekusi:**
  1. Mengimplementasikan `WorktreeMode` enum (`openExisting`, `createNew`) dan state selector interaktif pada `BranchCreatorSheet.swift`.
  2. Merancang layout mode `Open Existing` dengan dropdown menu branch yang ada dan tombol aksi `"Open Worktree"`.
  3. Merancang layout mode `Create New` dengan field branch baru, base branch selector, dan tombol aksi `"Create & Open"`.
  4. Memperluas verifikasi ref pada `GitWorktreeService.prepareWorktree` agar mendeteksi referensi lokal (`refs/heads/`) maupun remote tracking (`refs/remotes/origin/`).
  5. Menyelaraskan teks tombol pada `MultiSessionAccordionView.swift`, `DynamicNotchRootView.swift`, dan `StatusBarController.swift` menjadi `"Open Worktree"`.
  6. Menambahkan unit test baru pada `Anti_DispatchTests.swift` untuk memverifikasi `WorktreeMode` dan helper branch `NotchViewModel`.
  7. Memvalidasi seluruh skema pengujian dengan `xcodebuild test` (100% pass, 0 failure).
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Presentation/Modals/BranchCreatorSheet.swift`
  - `Anti Dispatch/Anti Dispatch/Core/Services/GitWorktreeService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Pemilihan branch yang sudah ada langsung diteruskan ke `prepareWorktree` tanpa side-effect pembuatan branch cabang redundan.
  - Sesuai Zero-Comment Policy, kode Swift ditulis bersih tanpa komentar inline.
