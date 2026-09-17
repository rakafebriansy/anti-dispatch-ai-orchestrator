---
id: TICKET-08
title: Decision Required HUD Bug Fixes and Status Bar UX Overhaul
status: Done
priority: High
labels: [UI, UX, Bug, Feature, Notch, MenuBar, macOS]
---

# Deskripsi
Perbaikan bug visual dan interaktivitas pada Decision Required Notification HUD (ghost container background, hover animation flickering, click unresponsiveness, dan regresi realtime detection dari TICKET-07), serta penambahan dukungan custom status bar icon dan perombakan total visual hierarchy menu status bar macOS menjadi interaktif dan user-friendly.

## Acceptance Criteria (Kriteria Penerimaan)
- [x] Background container dengan opacity kecil (ghost box) di belakang Decision Required modal berhasil dihilangkan melalui layer transparency AppKit hosting view.
- [x] Animasi hover pada tombol "Jump to App" di Decision Required modal tidak lagi blinking/flickering, menyala halus saat di-hover dan hilang saat hover out.
- [x] Tombol "Jump to App" responsif dalam 1 kali klik tanpa memblokir Main Thread (offloading AXUIElement & AppleScript ke background task).
- [x] Regresi realtime Decision HUD dari TICKET-07 terselesaikan dengan immediate HUD update pada state waiting dan fast polling saat session waiting.
- [x] Status bar controller mendukung pemuatan custom template icon `StatusBarIcon` dari asset bundle dengan fallback SF Symbol.
- [x] Menu status bar dirombak dengan hierarchy modern: header badge summary, status workspaces berikon warna, submenu detail & action per workspace, quick actions, dan toggle controls.
- [x] Seluruh unit test suite (18 Unit Tests + 4 UI Launch Tests) lulus 100%.

## Target Lingkup File (Affected Files)
- `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`

---

## AI Execution Log & Output

- **Langkah Teknis Tereksekusi:**
  1. Mengonfigurasi `NSHostingView` layer di `DecisionNotificationHUDController.swift` dengan `wantsLayer = true` dan `backgroundColor = .clear` untuk menghilangkan artifact persegi/ghost opacity di luar kurva modal.
  2. Memperbaiki `DecisionHUDButton` hover animation: mengganti closure `withAnimation` dengan view-level `.animation(.easeInOut(duration: 0.18), value: isHovered)`, menambahkan `.contentShape(Rectangle())` untuk stabilitas hit-test boundary, dan menyesuaikan dynamic glow shadow saat hover.
  3. Memperbaiki `focusSession()` di `NotchViewModel.swift` dengan memindahkan evaluasi AppleScript dan AXUIElement window raising ke `Task.detached(priority: .userInitiated)` serta menerapkan `.activate(options: [.activateIgnoringOtherApps, .activateAllWindows])` instan di Main Thread.
  4. Menyelaraskan lifecycle dismiss HUD sebelum window activation pada `handleSessionAction()` sehingga window target langsung naik ke foreground.
  5. Memperbaiki responsivitas realtime Decision HUD dengan memperluas kondisi `hasActive` ke state `.waiting` pada monitoring loop, serta memicu `decisionHUD?.update()` secara instan setiap kali session state berubah ke `.waiting` atau session ditambahkan/dihapus.
  6. Menambahkan mekanisme pemuatan custom icon `NSImage(named: "StatusBarIcon")` dengan `isTemplate = true` di `StatusBarController.swift`.
  7. Merombak struktur `NSMenu` status bar dengan summary header dinamik, section "ACTIVE WORKSPACES" berindikator status berwarna (`●`, `⚠️`, `✓`, `○`), submenu interaktif per sesi, quick actions dengan ikon native SF Symbols, dan state switch.
  8. Menjalankan `xcodebuild test` (18 Unit Tests + 4 UI Tests passed).
  9. Menjalankan sinkronisasi `graphify update`.

- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti Dispatch/Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/tickets/TICKET-08-decision-hud-and-statusbar-ux.md`
  - `anti-dispatch-ai-orchestrator/nodes/anti-dispatch/CHANGELOG.md`

- **Catatan & Keputusan Arsitektural:**
  - Menjaga isolasi MainActor vs background execution: pemanggilan AX UI dan AppleScript `System Events` selalu berpotensi memblokir Main RunLoop hingga 1-2 detik jika target aplikasi sedang loading. Offloading ke detached user-initiated task menjamin UI responsif seketika.
