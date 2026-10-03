---
id: TICKET-21
title: Add Generate AI Orchestrator Feature with Watermark Verification
status: Done
priority: High
labels: [Feature, Orchestrator, Notch, MenuBar, Watermark]
---

# Deskripsi
Menambahkan fitur "Generate AI Orchestrator" pada Anti Dispatch yang memungkinkan pengguna membuat repositori orchestrator secara instan di parent folder proyek aktif dengan mengklon dari `https://github.com/rakafebriansy/ai-orchestrator-template`.

Fitur ini dilengkapi dengan mekanisme verifikasi watermark (`ORCHESTRATOR_WATERMARK.txt`) untuk memeriksa:
1. Apakah terdapat direktori dengan substring `orchestrator` di parent folder.
2. Apakah terdapat file watermark `ORCHESTRATOR_WATERMARK.txt`.
3. Apakah signature valid (`AI-ORCHESTRATOR-SIGNATURE: v1` dan issuer `rakafebriansy/ai-orchestrator-template`) serta apakah string ID mesin (6-karakter MD5 short hash dari path proyek) sudah terdaftar untuk proyek tersebut.
4. Mendukung multi-project dalam satu file watermark dan multi-machine ID per project untuk mendukung sinkronisasi lintas laptop via Git.

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Model `OrchestratorScanResult` dan `WatermarkDocument` dibuat untuk mengelola parsing, validasi signature, pengecekan registrasi, dan serialisasi file watermark.
- [x] Service actor `OrchestratorService` mengimplementasikan 3-tahap pemindaian (substring folder `orchestrator`, eksistensi watermark, dan verifikasi signature & machine ID).
- [x] `OrchestratorService` mampu mengklon template dari GitHub secara otonom, menghapus remote origin, mendaftarkan machine ID, dan mengungkap folder di Finder.
- [x] `NotchViewModel` mengintegrasikan `OrchestratorService` dan menyediakan aksi `generateOrchestrator(for:)` untuk sesi aktif maupun URL proyek.
- [x] Tombol "AI Orchestrator" ditambahkan pada `MultiSessionAccordionView` (header & baris sesi) dan `DynamicNotchRootView`.
- [x] Menu item "Generate AI Orchestrator..." ditambahkan pada `StatusBarController` di menu utama dan submenu per-sesi.
- [x] Seluruh unit test lulus 100% dengan tambahan pengujian parser watermark dan scanning `OrchestratorService`.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Core/Models/OrchestratorScanResult.swift`
- `Anti Dispatch/Anti Dispatch/Core/Services/OrchestratorService.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  1. Membuat model `OrchestratorScanResult` dan struct parser/serializer `WatermarkDocument` dengan header validasi `AI-ORCHESTRATOR-SIGNATURE: v1` dan issuer `rakafebriansy/ai-orchestrator-template`.
  2. Mengimplementasikan `OrchestratorService` (`actor`) yang menangani pemindaian parent folder, verifikasi signature watermark, registrasi string ID mesin, cloning Git dari GitHub / fallback lokal, serta pembersihan remote origin.
  3. Menghubungkan `OrchestratorService` ke `NotchViewModel` dengan metode `generateOrchestrator(for:)` yang memberikan notifikasi HUD dan membuka Finder.
  4. Menambahkan tombol antarmuka "AI Orchestrator" pada `MultiSessionAccordionView`, `DynamicNotchRootView`, dan menu item pada `StatusBarController`.
  5. Menambahkan unit test `testWatermarkDocumentParsingAndSerialization` dan `testOrchestratorServiceScanning` di `Anti_DispatchTests.swift` dengan 100% test passing.
  6. Mengompilasi dan mengemas versi rilis ke dalam DMG via `package_dmg.sh` dan memasangnya ke `/Applications/Anti Dispatch.app`.
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Core/Models/OrchestratorScanResult.swift`
  - `Anti Dispatch/Anti Dispatch/Core/Services/OrchestratorService.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
  - `ai-orchestrator-template/ORCHESTRATOR_WATERMARK.txt`
  - `anti-dispatch-ai-orchestrator/ORCHESTRATOR_WATERMARK.txt`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Menggunakan format baris teks `<project-name> => <machine-id-1>, <machine-id-2>` memungkinkan kolaborasi multi-laptop tanpa merusak git diff, sekaligus mempermudah deteksi cepat oleh Anti Dispatch.
