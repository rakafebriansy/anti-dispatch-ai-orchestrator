---
id: TICKET-01
title: Manual Development Guides
status: Done
priority: High
labels: [Documentation, Guide, Swift, Architecture]
---

# Deskripsi
Penyusunan modul panduan manual (*manual guides*) yang dipecah menjadi beberapa dokumen markdown di direktori `global-docs/manual-guides/` untuk memandu pengembang membangun aplikasi Anti Dispatch secara manual dari awal hingga akhir lengkap dengan kode Swift dan penjelasannya.

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Struktur direktori `global-docs/manual-guides/` terpecah modular berdasarkan layer/topik (Overview, Core Engine, State Management, UI Presentation, System Integration, Testing).
- [x] Menyediakan kode Swift lengkap dan siap pakai pada setiap tutorial tanpa placeholder kode yang terputus.
- [x] Panduan mencakup integrasi Actor Concurrency, AppKit `NSPanel` floating notch, protokol loopback CDP, model sesi, dan integrasi Sparkle 2 updater.
- [x] Indeks `README.md` pada direktori `manual-guides/` menautkan seluruh file panduan dengan rapi.

## Target Lingkup File (Affected Files)
- `global-docs/manual-guides/README.md`
- `global-docs/manual-guides/00-overview/01-arsitektur-dan-alur-kerja.md`
- `global-docs/manual-guides/00-overview/02-persiapan-project-xcode.md`
- `global-docs/manual-guides/01-core-engine/01-path-hasher-dan-direktori-isolasi.md`
- `global-docs/manual-guides/01-core-engine/02-git-worktree-service.md`
- `global-docs/manual-guides/01-core-engine/03-launcher-service-dan-port-scanner.md`
- `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md`
- `global-docs/manual-guides/02-state-management/01-data-models-dan-entities.md`
- `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md`
- `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md`
- `global-docs/manual-guides/03-ui-presentation/02-ui-standby-view.md`
- `global-docs/manual-guides/03-ui-presentation/03-ui-single-session-view.md`
- `global-docs/manual-guides/03-ui-presentation/04-ui-multi-session-accordion.md`
- `global-docs/manual-guides/03-ui-presentation/05-modal-branch-creator-sheet.md`
- `global-docs/manual-guides/04-system-integration/01-menu-bar-status-bar-controller.md`
- `global-docs/manual-guides/04-system-integration/02-window-focus-dan-lifecycle.md`
- `global-docs/manual-guides/04-system-integration/03-auto-updater-sparkle-dan-packaging.md`
- `global-docs/manual-guides/05-testing/01-unit-testing-swift-testing.md`
- `nodes/anti-dispatch/CHANGELOG.md`

---

## AI Execution Log & Output
- **Langkah Teknis Tereksekusi:**
  1. Membuat indeks panduan utama di `global-docs/manual-guides/README.md`.
  2. Menyusun modul gambaran arsitektur sistem (`00-01`) dan konfigurasi Xcode project (`00-02`).
  3. Menyusun modul background core engine: `PathHasher.swift` (`01-01`), `GitWorktreeService.swift` (`01-02`), `LauncherService.swift` (`01-03`), dan `CDPService.swift` (`01-04`).
  4. Menyusun modul model data `WorkspaceSession.swift` (`02-01`) dan state coordinator `@Observable @MainActor NotchViewModel.swift` (`02-02`).
  5. Menyusun modul UI floating notch `NotchPanelController.swift` (`03-01`), `StandbyNotchView.swift` (`03-02`), `SingleSessionView.swift` (`03-03`), `MultiSessionAccordionView.swift` (`03-04`), dan `BranchCreatorSheet.swift` (`03-05`).
  6. Menyusun modul integrasi sistem `StatusBarController.swift` (`04-01`), `AppDelegate.swift` (`04-02`), dan integrasi Sparkle 2 / Homebrew packaging (`04-03`).
  7. Menyusun panduan unit testing modern menggunakan `Swift Testing` framework (`05-01`).
  8. Mencatat entri pembaruan ke `CHANGELOG.md`.

- **Keputusan Arsitektur:**
  - Kode Swift disusun modular dan mandiri dengan pembagian file yang siap di-*copy-paste* atau diimplementasikan satu per satu ke Xcode target `Anti Dispatch`.
  - Mengedepankan Swift 6 Strict Concurrency dengan pembagian actor yang jelas antara I/O subprocess, network socket, dan MainActor UI.
