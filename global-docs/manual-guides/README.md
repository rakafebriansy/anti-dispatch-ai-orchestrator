# Anti Dispatch - Manual Development Guides

Selamat datang di panduan teknis implementasi mandiri (*step-by-step manual development guide*) untuk **Anti Dispatch** — aplikasi utilitas native macOS berbasis panel melayang (*floating HUD / Dynamic Island*) yang berfungsi sebagai pusat kendali visual (*mission control*) sekaligus peluncur instan (*workspace orchestrator*) untuk mengelola dan memantau status eksekusi agen AI pada **Antigravity IDE**.

Panduan ini disusun secara modular dari level fondasi arsitektur hingga integrasi sistem tingkat lanjut, lengkap dengan kode Swift siap pakai dan penjelasan teknis yang komprehensif.

---

## 🗺️ Peta Panduan (Table of Contents)

### 00. Overview & Fondasi Proyek
* [01 - Arsitektur & Alur Kerja Sistem](./00-overview/01-arsitektur-dan-alur-kerja.md)
  * Memahami arsitektur Clean Swift + Actor Concurrency, interaksi loopback CDP Electron, isolasi data memori, dan siklus hidup state agen.
* [02 - Persiapan Proyek Xcode & Konfigurasi](./00-overview/02-persiapan-project-xcode.md)
  * Pengaturan target macOS 14.0+ Sonoma, penonaktifan App Sandbox untuk akses subprocess & window level, Info.plist, dan pengaturan Swift Concurrency.

### 01. Core & Background Engine
* [01 - Path Hasher & Direktori Isolasi Data](./01-core-engine/01-path-hasher-dan-direktori-isolasi.md)
  * Pembuatan modul `PathHasher.swift` menggunakan `CryptoKit.Insecure.MD5` untuk menghitung path unik `~/.antigravity-multi/<hash>_<name>`.
* [02 - Git Worktree Service Native](./01-core-engine/02-git-worktree-service.md)
  * Pembuatan actor `GitWorktreeService.swift` sebagai wrapper subprocess `/usr/bin/git` (validasi repo, deteksi worktree eksis, pembuatan worktree terisolasi, dan sanitasi nama branch).
* [03 - Launcher Service & Dynamic Port Scanner](./01-core-engine/03-launcher-service-dan-port-scanner.md)
  * Pembuatan actor `LauncherService.swift` untuk menemukan binary Antigravity IDE via `NSWorkspace`, dynamic socket probe rentang port `9221-9229`, dan peluncuran instance terisolasi.
* [04 - CDP Service & WebSocket Client](./01-core-engine/04-cdp-service-dan-websocket-client.md)
  * Pembuatan actor `CDPService.swift` untuk HTTP probing `/json/version`, koneksi WebSocket CDP, injeksi resilient JS DOM evaluator (`Runtime.evaluate`), dan adaptive polling timer (800ms / 3000ms).

### 02. State Management & ViewModels
* [01 - Data Models & Entities](./02-state-management/01-data-models-dan-entities.md)
  * Pendefinisian model data `WorkspaceSession.swift`, enum `AgentExecutionState.swift`, dan `AppPreferences.swift`.
* [02 - Notch ViewModel](./02-state-management/02-notch-viewmodel.md)
  * Pembuatan `@Observable @MainActor NotchViewModel.swift` yang mengorkestrasi active sessions, menerima live state stream dari `CDPService`, serta mengontrol peluncuran dan penutupan sesi.

### 03. UI Presentation & Floating Window
* [01 - Notch Panel & AppKit Controller](./03-ui-presentation/01-notch-panel-controller.md)
  * Pembuatan custom AppKit `NotchPanel.swift` (`NSPanel` borderless, non-activating, floating level) dan `NotchPanelController.swift` dengan deteksi hardware notch (`safeAreaInsets.top`) dan fallback floating capsule pill.
* [02 - Komponen UI: Standby View](./03-ui-presentation/02-ui-standby-view.md)
  * Pembuatan `StandbyNotchView.swift` saat nol sesi aktif, lengkap dengan tombol picker folder `NSOpenPanel` dan pembuat branch.
* [03 - Komponen UI: Single Session View](./03-ui-presentation/03-ui-single-session-view.md)
  * Pembuatan `SingleSessionView.swift` untuk mode collapsed pill status dan expanded panel aktivitas live file analysis.
* [04 - Komponen UI: Multi-Session Accordion](./03-ui-presentation/04-ui-multi-session-accordion.md)
  * Pembuatan `MultiSessionAccordionView.swift` untuk memantau multi-branch/multi-project secara paralel, tombol fokus jendela IDE, dan close session.
* [05 - Modal UI: Branch Creator Sheet](./03-ui-presentation/05-modal-branch-creator-sheet.md)
  * Pembuatan form modal popover `BranchCreatorSheet.swift` untuk membuat cabang Git baru dan meluncurkan instance worktree seketika.

### 04. Integrasi Sistem & Distribusi
* [01 - Menu Bar Status Bar Controller](./04-system-integration/01-menu-bar-status-bar-controller.md)
  * Pembuatan `StatusBarController.swift` menggunakan `NSStatusItem` sebagai titik akses sekunder di Menu Bar macOS.
* [02 - Window Focus & App Lifecycle](./04-system-integration/02-window-focus-dan-lifecycle.md)
  * Konfigurasi `AppDelegate.swift`, manajemen siklus hidup aplikasi, dan aktivasi jendela IDE instan via PID (`NSRunningApplication.activate`).
* [03 - Auto-Updater Sparkle & Distribusi](./04-system-integration/03-auto-updater-sparkle-dan-packaging.md)
  * Integrasi Sparkle 2 framework via SPM, konfigurasi signing Apple Developer ID, pembuatan file DMG, dan formula Homebrew Cask.

### 05. Testing & Verifikasi
* [01 - Unit Testing dengan Swift Testing](./05-testing/01-unit-testing-swift-testing.md)
  * Penulisan test suite modern menggunakan `Swift Testing` (`import Testing` / `@Test`) untuk menguji logika path hasher, git parser, port probing, dan CDP parser.

---

## 💡 Rekomendasi Urutan Pengerjaan
Mulailah secara berurutan dari **Bagian 00**, lalu selesaikan **Bagian 01 (Core Engine)** sebelum beralih ke **Bagian 02 & 03 (UI & Presentation)**. Hal ini memastikan seluruh fondasi data dan background actor telah siap sebelum dihubungkan ke antarmuka SwiftUI.
