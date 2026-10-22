---
id: TICKET-23
title: Orchestrator Placement Options and Project Directory Picker
status: Done
priority: High
labels: [macOS, MenuBar, Orchestrator, Feature]
---

# Deskripsi
Menambahkan fleksibilitas pada fitur pembuatan AI Orchestrator di Anti Dispatch:
1. Saat mengarahkan kursor ke project aktif pada Menu Bar dan memilih "Generate AI Orchestrator", sediakan 2 opsi penempatan:
   - Sibling / Di Sampingnya: Dibuat di direktori parent dari project target (`../<projectKey>-ai-orchestrator`).
   - Nested / Di Dalamnya: Dibuat di dalam direktori project target (`<project>/<projectKey>-ai-orchestrator`).
2. Pada item menu bar Quick Actions ("Generate AI Orchestrator..."), saat diklik akan membuka dialog native `NSOpenPanel` agar pengguna dapat memilih direktori project apa pun di disk, lalu secara otomatis membuat AI Orchestrator di sampingnya (`beside`).

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Model `OrchestratorPlacement` (`beside`, `inside`) diimplementasikan dengan kalkulasi path target yang presisi.
- [x] Fungsi `OrchestratorService.sanitizeProjectKey(_:)` membersihkan kata `dir` dan `org` beserta pemisahnya (contoh: `kasir-jagat-dir` $\rightarrow$ `kasir-jagat`, `anti-dispatch-dir` $\rightarrow$ `anti-dispatch`).
- [x] `OrchestratorService` memvalidasi dan memindai orchestrator yang sudah terdaftar maupun membuat orchestrator baru sesuai dengan penempatan `.beside` atau `.inside` dengan nama yang telah disanitasi.
- [x] Submenu penempatan ("Beside Project (Sibling)" dan "Inside Project (Nested)") tersedia saat hover ke project di Menu Bar, dengan fallback dialog konfirmasi alert jika item induk diklik.
- [x] Item menu bar Quick Action "Generate AI Orchestrator..." membuka `NSOpenPanel` untuk memilih project directory dan otomatis menghasilkan orchestrator di samping direktori project terpilih.
- [x] Unit test komprehensif ditambahkan ke `Anti_DispatchTests.swift` dan seluruh test suite lulus 100% via `xcodebuild test` (55 unit tests + 4 UI tests).

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Core/Models/OrchestratorScanResult.swift`
- `Anti Dispatch/Anti Dispatch/Core/Services/OrchestratorService.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH mengeksekusi tiket ini.*

- **Langkah Teknis Tereksekusi:**
  1. Menambahkan enum `OrchestratorPlacement` dengan case `beside` dan `inside`, display name, dan kalkulasi target URL pada `OrchestratorScanResult.swift`.
  2. Mengimplementasikan fungsi statis `OrchestratorService.sanitizeProjectKey(_:)` untuk menghapus kata `dir` dan `org` dari nama direktori proyek.
  3. Memperbarui `OrchestratorService.swift` method `scan(projectURL:placement:)` dan `generateOrchestrator(for:placement:)` serta `targetURL` agar mendukung pemindaian serta inisialisasi orchestrator baik di level parent sibling maupun nested child dengan nama yang telah disanitasi.
  4. Memperbarui `NotchViewModel.swift` dengan parameter `placement` pada method `generateOrchestrator` dan menambahkan helper `promptGenerateOrchestrator(placement:)` yang menampilkan `NSOpenPanel` dengan proteksi level `.screenSaver` dan `isSelectorOpen`.
  5. Menambahkan submenu dua opsi penempatan pada item sesi `StatusBarController.swift` ("Beside Project (Sibling)" dan "Inside Project (Nested)"), serta menghubungkan klik Quick Action menubar ke `promptGenerateOrchestrator(placement: .beside)`.
  6. Menulis unit test baru `testSanitizeProjectKeyRemovesDirAndOrg`, `testOrchestratorPlacementWithDirAndOrgFolderNames`, `testOrchestratorPlacementOptionsAndResolution`, dan `testOrchestratorServiceScanningPlacements` pada `Anti_DispatchTests.swift`.
  7. Memvalidasi 100% kelulusan test suite melalui `xcodebuild test` (55 unit tests + 4 UI tests lulus).
  8. Memaketkan Release build via `./package_dmg.sh`, memperbarui instalasi aplikasi di `/Applications/Anti Dispatch.app`, dan me-restart proses aplikasi agar pembaruan langsung aktif di sistem.
  9. Menjalankan `graphify update` pada root codebase `Anti Dispatch`.

- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Core/Models/OrchestratorScanResult.swift`
  - `Anti Dispatch/Anti Dispatch/Core/Services/OrchestratorService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/tickets/TICKET-23-orchestrator-placement-and-picker.md`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`

- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Menggunakan native AppKit `NSOpenPanel` dengan modal blocking yang aman diaktifkan ke foreground, konsisten dengan pemilih folder proyek `promptOpenProject()`.
  - Menggabungkan submenu hover pada menu bar untuk kenyamanan akses cepat dengan modal alert fallback saat item parent diklik, meminimalkan friksi pengguna.
