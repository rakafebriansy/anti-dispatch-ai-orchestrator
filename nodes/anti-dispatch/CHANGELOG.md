# CHANGELOG

Changelog berfungsi sebagai catatan riwayat perubahan untuk proyek ini. Tujuan dari changelog ini adalah untuk melacak semua modifikasi, penambahan, dan perbaikan yang dilakukan secara terstruktur agar mudah dipahami oleh seluruh tim.

## Kategori Perubahan

Setiap entri log perubahan harus dikategorikan berdasarkan jenis file atau area yang diubah. Kategori-kategori tersebut terbagi menjadi:

*   **Guideline: <judul>**: Perubahan atau penambahan pada dokumen panduan (guideline).
*   **PRD**: Perubahan pada dokumen Product Requirements Document (PRD).
*   **Design System**: Pembaruan atau modifikasi yang berkaitan dengan Design System.
*   **System Design**: Perubahan pada arsitektur atau dokumen System Design.
*   **Development Planning: <nomor>**: Pembaruan yang terkait dengan perencanaan pengembangan (Development Planning).
*   **Ticket: <nomor>**: Perbaikan atau penambahan fitur yang merujuk pada tiket tertentu.
*   **Prototype: <judul>**: Pembaruan atau pembuatan prototipe desain/aplikasi.
*   **Implementation**: Implementasi coding pada folder yang dituju (harus menyertakan penjelasan detail mengenai apa saja yang diubah di dalam folder project).

Khusus untuk kategori **Implementation**, penjelasan pada kolom "Perubahan" **wajib** mencantumkan salah satu tag status spesifik berikut di awal kalimatnya:
*   `[Added]`: Penambahan fitur, dokumen, atau konfigurasi baru.
*   `[Changed]`: Modifikasi atau penyesuaian pada fungsionalitas, logika, atau dokumen yang sudah ada.
*   `[Fixed]`: Perbaikan atas suatu kelemahan, galat (*bug*), atau kesalahan *syntax*.
*   `[Removed]`: Penghapusan fitur, pedoman, atau kode usang (*deprecated*).

## Format Changelog

Setiap penambahan log versi terbaru **WAJIB MUTLAK** diletakkan di baris **PALING ATAS** daftar, tepat di bawah judul "Log Perubahan" (urutan *descending* / *reverse-chronological*). AI **DILARANG KERAS** menambahkan log baru di baris terbawah. Anda **WAJIB** mencatat SEMUA kategori perubahan secara disiplin, bukan hanya modifikasi kode (`Implementation`).

> ⚠️ **Satu-satunya Format yang Sah (Single Source of Truth):**
> AI Agent dilarang mengarang format changelog sendiri. Anda **WAJIB MUTLAK** menyalin dan mematuhi struktur baku yang terdapat pada berkas referensi berikut:
> `../../global-docs/templates/changelog_entry_template.md`

## Log Perubahan (Anti Dispatch)

*(⚠️ PERHATIAN AI AGENT: TAMBAHKAN ENTRI LOG BARU ANDA TEPAT DI BAWAH BARIS INI. JANGAN DI PALING BAWAH DOKUMEN!)*

### [2026-09-16 12:36:00] - Implementation: Early Accessibility Permission Request & Dynamic Notch Permission View
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "buatlah meminta accessibility diminta di awal banget, jika tidak di acc maka menu di notch terbuka ganti ke informasi "harus terima aksesibilitas" atau semacamnya"
- **Perubahan:** `[Added]` Menambahkan permintaan izin Aksesibilitas (`AXIsProcessTrustedWithOptions` dengan `kAXTrustedCheckOptionPrompt`) sejak aplikasi pertama kali diluncurkan (`init` pada `NotchViewModel`). `[Added]` Menambahkan antarmuka responsif `accessibilityRequiredView` di dalam notch terbuka saat izin aksesibilitas belum disetujui, lengkap dengan status peringatan bergaya glassmorphic oranye, tombol navigasi langsung ke `System Settings > Privacy & Security > Accessibility`, dan tombol "Periksa Ulang". `[Added]` Menambahkan pemantauan status izin secara reaktif pada loop polling latar belakang sehingga UI notch langsung bertransisi ke tampilan workspace aktif begitu toggle diizinkan pengguna. `[Added]` Menambahkan unit test `testAccessibilityPermissionCheck` pada `Anti_DispatchTests.swift`.
- **Path File:** `Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`, `Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`, `Anti DispatchTests/Anti_DispatchTests.swift`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-16 08:06:00] - Implementation: Worktree Nomenclature, Inline Creator Container & Decision HUD Polish
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "revisi bagi fitur new branch. 1. ganti "branch" jadi "worktree" 2. di modal Decision Required, terdapat bg dengan opacity rendah yang shapenya sharp (tidak rounded), bisakah dihapus? 3. di modal Decision Required belum ada hovering style pada dua buttonya 4. apa maksud 1 dan Yes, allow this time...? berikan informasi questionnya bukan informasi di buttonnya seperti Yes allow, misal "find . -maxdepth 4 -not -path '*/.*'" 5. ganti modal launch new branch instance menjadi diletakkan di dalam notch terbuka, di atas semua project. namun style dari container dan kontennya dibuat sangat berbeda supaya user tidak menganggapnya sebagai bagian task"
- **Perubahan:** `[Changed]` Mengganti seluruh terminologi UI "branch" menjadi "worktree", menempatkan `WorktreeCreatorInlineView` secara langsung di bagian atas daftar project di dalam notch terbuka dengan kontainer bergaya linear gradient cyan/blue border khusus yang kontras dari task row (menghapus format modal sheet), memoles `DecisionNotificationHUDController` dengan menghilangkan latar window persegi tajam (`hasShadow = false`, `NSColor.clear`), menambahkan state hover interaktif (`DecisionHUDButton`) dengan glow emas, serta memperbarui logika ekstraksi `extract_state.js` agar secara cerdas menyaring tombol angka/allow dan memprioritaskan argumen perintah terminal (`code`/`pre`) atau pertanyaan konfirmasi asli agen.
- **Path File:** `Anti Dispatch/Presentation/Modals/BranchCreatorSheet.swift`, `Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`, `Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`, `Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`, `Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`, `Anti Dispatch/Resources/Scripts/extract_state.js`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-16 07:46:00] - Implementation: Session Close Modal Width Optimization (50% Notch Width)
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "modal close workspace section terlalu panjang, buat panjangnya 50% notch terbuka saja"
- **Perubahan:** `[Changed]` Menyesuaikan lebar kartu dialog konfirmasi penutupan sesi workspace pada `MultiSessionAccordionView` menjadi fixed 320pt (~50% dari total lebar container notch saat terbuka penuh) lengkap dengan drop shadow elegan, memastikan tampilan dialog lebih proporsional, terpusat, dan tidak merentang selebar seluruh notch.
- **Path File:** `Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-16 07:45:00] - Implementation: Unified Clickable Hit Area for Session Accordion Toggle
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "untuk buka accordion bukan cuma dengan klik namanya, tapi panahnya juga bisa. jadi dari panah -> circle -> label kalau bisa dibungkus dalam 1 stack lalu clickable"
- **Perubahan:** `[Changed]` Membungkus elemen ikon panah chevron (`chevron.right`), status circle, label nama workspace, dan tag nama branch ke dalam satu kesatuan `HStack` dengan `contentShape(Rectangle())` serta frame dan padding yang konsisten pada `SessionRowView`. Seluruh area dari panah hingga label kini menjadi satu target sentuh/klik yang responsif untuk membuka dan menutup accordion sesi.
- **Path File:** `Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-16 07:41:00] - Implementation: Notch Debounce Boundary Check & Post-Dismiss Cooldown
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "sudah oke, namun setelah project terpilih sepertinya anda membuka notch lagi, tidak usah, biarkan tertutup terus sampai user hover lagi ke notch nya" / "setelah select project, masih buka lalu tutup lagi. coba cari penyebabnya"
- **Perubahan:** `[Fixed]` Mengatasi galat open-close jitter pada notch saat modal `NSOpenPanel` ditutup dengan menambahkan validasi koordinat mouse nyata (`isMouseInside`) pada `handleHover` serta cooldown 800ms (`suppressHoverUntil`) untuk mengabaikan event `mouseEntered` palsu dari sistem macOS pasca penutupan modal. Notch kini tetap berada dalam kondisi compact/tertutup secara stabil hingga pengguna dengan sengaja mengarahkan kembali kursor ke area notch fisik.
- **Path File:** `Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-16 07:33:00] - Implementation: Jump to Project Button Styling & Smooth Notch Collapse Flow
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "1. style jump to project sama seperti style badge status Idle, ganti supaya lebih terlihat seperti clickable button 2. apakah memang harus dihapus 100% untuk notch nya saat membuka container, tidak bisakah notch nya minimize dulu baru terbuka containernya, lalu jika container ditutup baru di maximize lagi"
- **Perubahan:** `[Changed]` Mengganti style teks aksi pada `SessionRowView` dengan komponen interaktif `JumpToProjectButton` (dilengkapi border cyan stroke, background tint, semi-bold typography, dan hover highlight) serta memodifikasi `promptOpenProject()` dan `promptSelectRepo()` agar notch melakukan animasi spring minimize (collapse ke ukuran compact) terlebih dahulu (~550ms) sebelum menampilkan `NSOpenPanel.runModal()` alih-alih menghapus (`orderOut`) jendela notch dari layar.
- **Path File:** `Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`, `Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`, `Anti Dispatch/Presentation/Modals/BranchCreatorSheet.swift`, `Anti Dispatch/App/AppDelegate.swift`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-16 06:45:00] - Implementation: Floating Decision Notification HUD & Compact Notch Status Badges
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "pada antigravity akan selalu ada masa dimana ia menunggu input user untuk membuat decision. jika ada, maka buatlah pop up di layar (bukan di notch, supaya user melihat) yang memberitahu bahwa ada kebutuhan input user, terdapat dua button, kiri: abaikan, kanan: jump to app... di notch tertutup ubahlah menjadi angka saja yang bersusun horizontal..."
- **Perubahan:** `[Added]` Membuat controller dan view `DecisionNotificationHUDController` (`NSPanel` level `.floating`) yang memunculkan notifikasi pop-up di layar saat agen membutuhkan input pengguna lengkap dengan tombol 'Ignore' dan 'Jump to app'. Mengubah representasi visual notch saat tertutup (compact) menjadi badge angka horizontal berkode warna: biru (processing/running) & kuning (waiting for input) di lobe kiri, serta abu-abu (idle/standby) & hijau (done) di lobe kanan.
- **Path File:** `Anti Dispatch/Presentation/Notifications/DecisionNotificationHUDController.swift`, `Anti Dispatch/Presentation/Notch/Views/DynamicNotchRootView.swift`, `Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 20:02:00] - Guideline: Equatable Task ID & hasRunningAgent Property Resolution
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "Instance method 'task(id:name:priority:file:line:_:)' requires that 'Binding<Subject>' conform to 'Equatable'"
- **Perubahan:** `[Fixed]` Menambahkan computed property `hasRunningAgent: Bool` pada `NotchViewModel` di `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md` dan menambahkan property helper lokal `hasRunningAgent` pada `DynamicNotchRootView` di `global-docs/manual-guides/06-productive-island-notch-morphing/04-integrasi-anti-dispatch-dynamic-notch.md` untuk mencegah macro `@Bindable` mengonversi properti yang belum didefinisikan menjadi dynamic `Binding<Subject>` saat dipassing ke `.task(id:)`.
- **Path File:** `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md`, `global-docs/manual-guides/06-productive-island-notch-morphing/04-integrasi-anti-dispatch-dynamic-notch.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 19:59:00] - Guideline: NSColor.clear Ambiguity Resolution
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "Cannot infer contextual base in reference to member 'clear'"
- **Perubahan:** `[Fixed]` Memperbarui penugasan warna latar belakang jendela `backgroundColor` pada `global-docs/manual-guides/06-productive-island-notch-morphing/01-notch-geometry-dan-hardware-detection.md` dan `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md` dari `.clear` menjadi `NSColor.clear` eksplisit untuk menyelesaikan ambiguitas tipe antara `NSColor.clear` (AppKit) dan `Color.clear` (SwiftUI).
- **Path File:** `global-docs/manual-guides/06-productive-island-notch-morphing/01-notch-geometry-dan-hardware-detection.md`, `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 19:58:00] - Guideline: NSWindow.Level Explicit Type Qualification
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "Cannot infer contextual base in reference to member 'statusBar'"
- **Perubahan:** `[Fixed]` Memperbarui penugasan level jendela pada `global-docs/manual-guides/06-productive-island-notch-morphing/01-notch-geometry-dan-hardware-detection.md` dan `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md` dari `.statusBar` menjadi `NSWindow.Level.statusBar` eksplisit untuk mengatasi kegagalan inferensi tipe kontekstual pada kompiler Swift.
- **Path File:** `global-docs/manual-guides/06-productive-island-notch-morphing/01-notch-geometry-dan-hardware-detection.md`, `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 17:15:00] - Guideline: Productive Island Dynamic Notch Morphing Architecture (Module 06)
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "Notch dari Anti Dispatch samakan dengan yang ada di productive-island. jangan langsung update swift. buatlah manual guide 06 di manual-guides"
- **Perubahan:** `[Added]` Menyusun rangkaian dokumen panduan implementasi baru (Modul 06) di `global-docs/manual-guides/06-productive-island-notch-morphing/` yang mengadaptasi arsitektur notch mutakhir dari `Productive Island`. Mencakup: deteksi geometri notch presisi via `auxiliaryTopLeftArea` / `auxiliaryTopRightArea` dan kanvas statis `NSPanel` (`01-notch-geometry-dan-hardware-detection.md`), bentuk bezier adaptif dengan lengkungan kurva kuadratik (*outward curls*) dan background Jet-Black Glassmorphism (`02-island-shape-surface-dan-fluid-morphing.md`), tata letak 3 slot Lobe dengan celah transparan kamera dan engine kursor linger 1.8 detik (`03-lobe-slot-architecture-dan-hover-engine.md`), serta kode terintegrasi penuh `DynamicNotchRootView.swift` untuk Anti Dispatch (`04-integrasi-anti-dispatch-dynamic-notch.md`).
- **Path File:** `global-docs/manual-guides/06-productive-island-notch-morphing/01-notch-geometry-dan-hardware-detection.md`, `global-docs/manual-guides/06-productive-island-notch-morphing/02-island-shape-surface-dan-fluid-morphing.md`, `global-docs/manual-guides/06-productive-island-notch-morphing/03-lobe-slot-architecture-dan-hover-engine.md`, `global-docs/manual-guides/06-productive-island-notch-morphing/04-integrasi-anti-dispatch-dynamic-notch.md`, `global-docs/manual-guides/README.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 15:48:00] - Guideline: NotchNook Jet-Black Dynamic Island Styling
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "StandbyNotchView.swift tidak seperti notchnook, malah ada di pojok kanan bawah screen saya"
- **Perubahan:** `[Changed]` Memperbarui gaya visual pada `global-docs/manual-guides/03-ui-presentation/02-ui-standby-view.md` dengan menambahkan latar belakang berlapis Jet-Black (`Color.black.opacity(0.95)` + `ultraThinMaterial`) dan *curvature radius* bawah 16pt agar menyatu secara alami (*seamless bezel blend*) dengan notch fisik hardware MacBook ala NotchNook / Dynamic Island.
- **Path File:** `global-docs/manual-guides/03-ui-presentation/02-ui-standby-view.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 15:46:00] - Guideline: Robust Physical Notch Positioning and Display Geometry
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "StandbyNotchView.swift tidak seperti notchnook, malah ada di pojok kanan bawah screen saya"
- **Perubahan:** `[Fixed]` Memperbaiki logika penentuan posisi pada `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md` dengan memprioritaskan layar yang memiliki notch fisik (`safeAreaInsets.top > 0`), memperbaiki perhitungan koordinat `minX` & `maxY`, menyelaraskan konten SwiftUI ke atas (`alignment: .top`), dan mengatur level panel ke `.statusBar` agar jendela HUD selalu menempel presisi di notch MacBook.
- **Path File:** `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 15:34:00] - Guideline: Unit Testing Guide English Translation
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "global-docs/manual-guides/05-testing/01-unit-testing-swift-testing.md ubah ke inggris teksnya"
- **Perubahan:** `[Changed]` Menerjemahkan seluruh teks dokumentasi dan deskripsi `@Test` pada `global-docs/manual-guides/05-testing/01-unit-testing-swift-testing.md` ke dalam bahasa Inggris standar.
- **Path File:** `global-docs/manual-guides/05-testing/01-unit-testing-swift-testing.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 14:32:00] - Guideline: AppUpdater File Separation and Menu Bar Integration
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "di global-docs/manual-guides/04-system-integration/03-auto-updater-sparkle-dan-packaging.md appdelegate kan sudah ada global-docs/manual-guides/04-system-integration/02-window-focus-dan-lifecycle.md"
- **Perubahan:** `[Changed]` Memperjelas penempatan berkas `AppUpdater.swift` sebagai service terpisah di `Anti Dispatch/Core/Services/AppUpdater.swift` pada `global-docs/manual-guides/04-system-integration/03-auto-updater-sparkle-dan-packaging.md` dan menambahkan contoh integrasi tombol 'Check for Updates...' pada `StatusBarController.swift` agar tidak menimpa atau membingungkan pembaca terkait peran `AppDelegate.swift`.
- **Path File:** `global-docs/manual-guides/04-system-integration/03-auto-updater-sparkle-dan-packaging.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 12:41:00] - Guideline: StatusBarController MainActor Concurrency Isolation
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "jelaskan secara rinci global-docs/manual-guides/04-system-integration/01-menu-bar-status-bar-controller.md"
- **Perubahan:** `[Fixed]` Menambahkan anotasi `@MainActor` pada deklarasi kelas `StatusBarController` di `global-docs/manual-guides/04-system-integration/01-menu-bar-status-bar-controller.md` untuk menjamin interaksi dengan `NSStatusBar`, `NSMenu`, dan `NotchViewModel` sepenuhnya aman dan terisolasi di Main Thread sesuai standar Swift 6.
- **Path File:** `global-docs/manual-guides/04-system-integration/01-menu-bar-status-bar-controller.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 12:36:00] - Guideline: NotchPanelController MainActor Isolation & Protocol Cleanup
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "Type 'NotchPanelController' does not conform to protocol 'ObservableObject'"
- **Perubahan:** `[Fixed]` Memperbarui deklarasi kelas `NotchPanelController` di `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md` dengan menghapus konformansi usang `ObservableObject` dan menambahkan anotasi `@MainActor`, memastikan seluruh manipulasi jendela `NSPanel`, `NSScreen`, dan `NSHostingView` terisolasi secara aman di Main Thread tanpa memicu galat sintesis protokol.
- **Path File:** `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-15 08:03:00] - Guideline: NSRunningApplication Deprecation Fix for macOS 14+
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "'activateIgnoringOtherApps' was deprecated in macOS 14.0: ignoringOtherApps is deprecated in macOS 14 and will have no effect."
- **Perubahan:** `[Fixed]` Memperbarui metode `focusSession` pada `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md` dengan menambahkan pemeriksaan ketersediaan `#available(macOS 14.0, *)` untuk memanggil `app.activate()` tanpa parameter opsi usang (`.activateIgnoringOtherApps`), menghilangkan peringatan deprecation pada macOS 14 (Sonoma) dan versi lebih baru.
- **Path File:** `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-14 22:42:00] - Guideline: Swift 6 Concurrency Safe Task Lifecycle via Weak Self
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "kenapa harus di unsafe untuk nonisolated(unsafe) private var monitoringTask: Task<Void, Never>? = nil? tidak adakah cara yang "safe"? -> kerjakan solusi safe 1"
- **Perubahan:** `[Changed]` Mengadopsi pola 100% *safe concurrency* pada `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md` dengan menghapus deklarasi `deinit` dan kata kunci `nonisolated(unsafe)`. Siklus hidup `monitoringTask` kini mengandalkan penangkapan `[weak self]` dan evaluasi `guard let self = self else { break }` yang mematikan loop pemantauan secara otomatis saat objek dilepas dari memori.
- **Path File:** `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-14 22:36:00] - Guideline: Swift 6 Concurrency NotchViewModel Deinit Task Safety
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "Main actor-isolated property 'monitoringTask' can not be referenced from a nonisolated context; monitoringTask?.cancel()"
- **Perubahan:** `[Fixed]` Memperbarui deklarasi `monitoringTask` pada `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md` dengan menambahkan atribut `nonisolated(unsafe)` agar `Task` handle dapat dibatalkan (`cancel()`) di dalam `deinit` tanpa melanggar aturan isolasi Swift 6 Concurrency.
- **Path File:** `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-14 21:34:00] - Guideline: Multi-Tier Bundle Script Path Resolution
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "tapi scriptnya di Resources/Scripts/here"
- **Perubahan:** `[Changed]` Memperbarui initializer `CDPService.swift` di `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md` untuk mendukung pemuatan path bertingkat (`subdirectory: "Resources/Scripts"`, `subdirectory: "Scripts"`, dan root bundle `Bundle.main.url(forResource:withExtension:)`) agar kompatibel dengan penambahan folder di Xcode baik sebagai Folder Reference maupun Group.
- **Path File:** `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-14 09:05:00] - Guideline: CDPExtractedState Non-Optional Array Decoding Fix
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "self.subSteps = try container.decodeIfPresent([String].self, forKey: .subSteps) ?? [] Left side of nil coalescing operator '??' has non-optional type '[String]', so the right side is never used"
- **Perubahan:** `[Fixed]` Memperbarui decoding `subSteps` di `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md` menjadi `try container.decode([String].self, forKey: .subSteps)` untuk menghilangkan redundansi operator nil-coalescing (`??`) pada tipe non-optional array.
- **Path File:** `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-14 08:58:00] - Guideline: Swift 6 Strict Concurrency Fix (CDPExtractedState nonisolated Codable)
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "Main actor-isolated conformance of 'CDPExtractedState' to 'Decodable' cannot be used in actor-isolated context; this is an error in the Swift 6 language mode"
- **Perubahan:** `[Fixed]` Memperbarui modul panduan `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md` dengan menambahkan implementasi eksplisit `nonisolated init(from: Decoder)` dan `nonisolated func encode(to: Encoder)` pada `CDPExtractedState` agar dapat didecode secara bebas dari background actor `CDPService` tanpa terhalang isolasi `@MainActor` pada Swift 6.
- **Path File:** `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-14 07:38:00] - Guideline: CDP JavaScript Payload Modularization
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "tidak bisakah file script dipisah saja supaya clean di 04-cdp-service-dan-websocket-client.md"
- **Perubahan:** `[Changed]` Memperbarui modul panduan `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md` dengan memisahkan payload JavaScript DOM extractor ke dalam file resource mandiri `Anti Dispatch/Resources/Scripts/extract_state.js`, serta merestrukturisasi implementasi `CDPService.swift` agar memuat script dari App Bundle dengan fallback inline yang aman untuk unit testing.
- **Path File:** `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-14 07:25:00] - Guideline: Synchronize Template Updates & Standardize Knowledge Base (LEARN.md)
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "ada beberapa perubahan/update pada template, implementasikan perubahan template orchestrator @[/Users/raka/Developer/repositories/projects/anti-dispatch-dir/ai-orchestrator-template] ke @[/Users/raka/Developer/repositories/projects/anti-dispatch-dir/anti-dispatch-ai-orchestrator] tanpa merusak kemajuan yang sudah dibangun"
- **Perubahan:** `[Added]` Menyinkronkan pembaruan dari `ai-orchestrator-template` (commit `411f24f`): menambahkan FASE 4 Implementation Q&A Prompt pada `README.md`, menambahkan referensi kondisional Q&A pada `nodes/_template/main.md` dan `nodes/anti-dispatch/main.md`, serta menstandarisasi dokumen `global-docs/LEARN.md` dengan 4 hukum protokol operasional AI, taksonomi 10 kategori utama, boilerplate baku, dan mengintegrasikan seluruh materi tanya-jawab pembelajaran Modul 00 s/d 01.03 ke dalam format entri `[QA-YYYYMMDD-XX]` terindeks rapi.
- **Path File:** `README.md`, `nodes/_template/main.md`, `nodes/anti-dispatch/main.md`, `global-docs/LEARN.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-13 23:28:00] - Implementation: Swift 6 Concurrency Fix (PathHasher nonisolated)
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "Main actor-isolated static method 'resolveDataDirectory(for:branch:customRoot:)' cannot be called from outside of the actor; this is an error in the Swift 6 language mode"
- **Perubahan:** `[Fixed]` Menambahkan konformansi `Sendable` dan penanda `nonisolated` pada `PathHasher` dan seluruh metode statisnya agar dapat dipanggil secara bebas oleh background actor `LauncherService` tanpa terhalang isolasi default `@MainActor` pada Swift 6.
- **Path File:** `global-docs/manual-guides/01-core-engine/01-path-hasher-dan-direktori-isolasi.md`, `Anti Dispatch/Utilities/PathHasher.swift`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-13 22:34:00] - Guideline: Manual Guides Error Localization
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "ubah ke full inggris error ra global-docs/manual-guides"
- **Perubahan:** `[Changed]` Memperbarui seluruh pesan galat (*raw error descriptions*) pada modul `global-docs/manual-guides/01-core-engine/03-launcher-service-dan-port-scanner.md` dari bahasa Indonesia menjadi bahasa Inggris baku (`"Directory is not a valid Git repository."` dan `"Antigravity IDE.app was not found in the /Applications directory."`).
- **Path File:** `global-docs/manual-guides/01-core-engine/03-launcher-service-dan-port-scanner.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-13 20:09:00] - Guideline: Knowledge Base Update (Bind vs Connect & POSIX 0 Return Code)
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "update global-docs/LEARN.md"
- **Perubahan:** `[Added]` Memperbarui `global-docs/LEARN.md` dengan menambahkan penjelasan mengenai perbedaan konseptual antara mengklaim port (`bind`) vs menghubungi server (`connect`), simulasi perulangan port probing langkah-demi-langkah, dan filosofi sistem operasi di balik penetapan angka `0` sebagai representasi sukses (*Zero Errors*) vs *Boolean true/false* di bahasa tingkat tinggi.
- **Path File:** `global-docs/LEARN.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-13 20:04:00] - Guideline: Knowledge Base Update (POSIX & Socket Probing)
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "update global-docs/LEARN.md"
- **Perubahan:** `[Added]` Memperbarui `global-docs/LEARN.md` dengan menambahkan materi tanya-jawab mendalam seputar konsep dasar POSIX & POSIX Call, perbedaan filosofi arsitektur Windows vs POSIX (Win32, WSL, Winsock), parameter asli (C/POSIX signature) dari `socket()`, `bind()`, dan `close()`, mekanisme pointer tingkat rendah di Swift (`withUnsafePointer`, `withMemoryRebound(to:capacity:)`), serta logika socket probing dan fallback port.
- **Path File:** `global-docs/LEARN.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-13 19:36:00] - Guideline: Knowledge Base & Learning Q&A (LEARN.md)
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "simpan semua penjelasanmu di markdown LEARN.md di global-docs dari awal interaksi kita di 00-overview sampai 01-core-engine/03-launcher-service-dan-port-scanner.md . tetap buat dalam format tanya jawab sesuai aslinya"
- **Perubahan:** `[Added]` Membuat dokumen repositori pembelajaran komprehensif `global-docs/LEARN.md` yang merangkum seluruh sesi tanya-jawab teknis, bedah baris kode, konsep memori buffer, POSIX socket networking, subprocess execution, dan arsitektur concurrency dari modul `00-overview` hingga `01-03`.
- **Path File:** `global-docs/LEARN.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-11 14:02:00] - Guideline: Manual Development Guides
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "saya ingin membuat dengan manual, buatlah manual guides di global-docs/ folder manual-guides/ berisi markdown berisi tutorial pembuatan yang sudah dipecah pecah jadi beberapa markdown, didalamnya lengkap berisi kode dan penjelasan bagi saya. buatkanlah dari paling pertama hingga paling akhir."
- **Perubahan:** `[Added]` Menyusun rangkaian dokumen panduan implementasi manual (Manual Development Guides) secara modular di folder `global-docs/manual-guides/`. Mencakup 6 modul utama: Overview Arsitektur & Xcode Setup (`00-overview/`), Core Engine Actor Concurrency (`01-core-engine/`), State Management & ViewModels (`02-state-management/`), UI Presentation Notch HUD & Components (`03-ui-presentation/`), System Integration & Sparkle Auto-updater (`04-system-integration/`), dan Unit Testing dengan Swift Testing (`05-testing/`) lengkap dengan kode Swift siap pakai.
- **Path File:** `global-docs/manual-guides/README.md`, `global-docs/manual-guides/00-overview/01-arsitektur-dan-alur-kerja.md`, `global-docs/manual-guides/00-overview/02-persiapan-project-xcode.md`, `global-docs/manual-guides/01-core-engine/01-path-hasher-dan-direktori-isolasi.md`, `global-docs/manual-guides/01-core-engine/02-git-worktree-service.md`, `global-docs/manual-guides/01-core-engine/03-launcher-service-dan-port-scanner.md`, `global-docs/manual-guides/01-core-engine/04-cdp-service-dan-websocket-client.md`, `global-docs/manual-guides/02-state-management/01-data-models-dan-entities.md`, `global-docs/manual-guides/02-state-management/02-notch-viewmodel.md`, `global-docs/manual-guides/03-ui-presentation/01-notch-panel-controller.md`, `global-docs/manual-guides/03-ui-presentation/02-ui-standby-view.md`, `global-docs/manual-guides/03-ui-presentation/03-ui-single-session-view.md`, `global-docs/manual-guides/03-ui-presentation/04-ui-multi-session-accordion.md`, `global-docs/manual-guides/03-ui-presentation/05-modal-branch-creator-sheet.md`, `global-docs/manual-guides/04-system-integration/01-menu-bar-status-bar-controller.md`, `global-docs/manual-guides/04-system-integration/02-window-focus-dan-lifecycle.md`, `global-docs/manual-guides/04-system-integration/03-auto-updater-sparkle-dan-packaging.md`, `global-docs/manual-guides/05-testing/01-unit-testing-swift-testing.md`, `nodes/anti-dispatch/CHANGELOG.md`

### [2026-09-11 13:25:00] - Implementation: Inisialisasi Node & Fondasi Arsitektur Anti Dispatch
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `https://github.com/rakafebriansy/anti-dispatch-ai-orchestrator.git`
- **Konteks:** "Saya ingin menginisialisasi Single-Project Environment menggunakan kerangka kerja AI Orchestrator ini. Nama Sistem: Anti Dispatch, Tech Stack: Swift (macOS Utility)"
- **Perubahan:** `[Added]` Inisialisasi Node `anti-dispatch` sebagai single-project environment berbasis MODE 2 (Prompt-Driven). Menyusun PRD dan Design System global untuk macOS Dynamic Island & Workspace Orchestrator. Membuat seluruh diagram PlantUML (User Journey, Use Case, Flowchart Polling CDP, State Diagram Siklus Hidup Agen, ERD Model Sesi). Menyusun System Design (Clean Swift + Actor Concurrency), Project Context Guidelines, dan Development Planning Roadmap (Fase 1–4).
- **Path File:** `global-docs/prd.md`, `global-docs/design-system.md`, `global-docs/diagrams/user-journey.puml`, `global-docs/diagrams/use-case.puml`, `nodes/anti-dispatch/main.md`, `nodes/anti-dispatch/guidelines/project-context.md`, `nodes/anti-dispatch/docs/system-design.md`, `nodes/anti-dispatch/docs/diagrams/flowchart.puml`, `nodes/anti-dispatch/docs/diagrams/state-diagram.puml`, `nodes/anti-dispatch/docs/diagrams/erd.puml`, `nodes/anti-dispatch/docs/development-planning.md`, `nodes/anti-dispatch/CHANGELOG.md`


### [2026-09-07 17:47:00] - Guideline: Database & Datetime Storage Standard
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Konteks:** "tambahkan panduan di orchestrator, untuk menyimpan datetime di aplikasi database buatlah dua opsi, yakni menggunakan epoch time millis (konversi jam saat ini ke epoch time) dan menyimpan timestamp + timezone utc di database (konversi jam saat ini di timezone user ke utc, baru di insert)"
- **Perubahan:** `[Added]` Menambahkan pedoman universal `database.md` yang menetapkan standar penyimpanan datetime di basis data dengan dua opsi baku (Opsi 1: Epoch Time Milliseconds / `BIGINT` dan Opsi 2: Timestamp with Timezone UTC / `TIMESTAMPTZ` / ISO-8601 UTC) beserta aturan konversi, tipe data, skenario penggunaan, dan matriks perbandingan. Memperbarui `global-guidelines/README.md`, `nodes/_template/main.md`, dan `nodes/_template/docs/system-design.md` dengan cross-reference ke pedoman baru tersebut.
- **Path File:** `global-guidelines/database.md`, `global-guidelines/README.md`, `nodes/_template/main.md`, `nodes/_template/docs/system-design.md`, `nodes/_template/CHANGELOG.md`

### [2026-09-02 08:14:00] - Guideline: Safe File Operations Policy
> **Trigger:** Prompt Driven | **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Konteks:** "saya seringkali kehilangan kesempatan me-review ketika mengiyakan ai agent untuk menggunakan: 1. scripts (perubahan massal) dengan js, python, dll 2. sed -i ... 3. git checkout <file> 4. git restore <file> berikan batasan untuk penggunaan hal tersebut"
- **Perubahan:** `[Added]` Menambahkan pedoman `safe-file-operations.md` yang melarang operasi file destruktif (mass change scripts, in-place edit seperti `sed -i`, `git checkout <file>`, `git restore <file>`, `git clean`, `git stash drop`, `write_to_file` overwrite) serta mewajibkan alternatif reversible. Memperbarui `version-control.md`, `error-handling.md`, dan `nodes/_template/main.md` dengan cross-reference ke pedoman baru tersebut.
- **Path File:** `global-guidelines/safe-file-operations.md`, `global-guidelines/error-handling.md`, `global-guidelines/version-control.md`, `nodes/_template/main.md`, `nodes/_template/CHANGELOG.md`

### [2026-07-30 13:10] - Guideline: Comprehensive Audit Resolution
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "periksalah seluruh ai-orchestrator-template untuk potensi halu atau inefisiensi oleh AI"
- **Perubahan:** `[Fixed]` Menyelesaikan 16 temuan audit mencakup perbaikan halusinasi (H-01 s/d H-05), optimasi token (I-01 s/d I-05), penjernihan kontradiksi (A-01 s/d A-04), dan penutupan celah kepatuhan (C-01 s/d C-02, M-01 s/d M-03).
- **Path File:** `global-docs/prd.md`, `global-docs/design-system.md`, `global-docs/templates/README.md`, `global-guidelines/coding.md`, `global-guidelines/testing.md`, `global-guidelines/ui-and-assets.md`, `global-guidelines/version-control.md`, `nodes/_template/CHANGELOG.md`, `nodes/_template/main.md`, `nodes/_template/retrospectives/README.md`, `nodes/_template/docs/diagrams/README.md`

### [2026-07-20 18:48] - Enhancement: Node-Level Graphify Integration
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "ubahlah pendekatan graphify, jangan dibuat di directory orchestrator melainkan dibuat di directory milik project/node... mintalah cek terlebih dahulu apakah ada graphify di dalam project/node... sebelum melakukan eksekusi/scanning manual"
- **Perubahan:** Mengubah referensi `graphify build` dan `graphify update` pada root `README.md` dan `nodes/_template/main.md` agar menargetkan direktori spesifik *Path Codebase*. Menambahkan langkah wajib baru di SOP `main.md` (Poin E.2) untuk mengecek ketersediaan `.graphify` demi menghemat *token* sebelum pemindaian manual.
- **Path File:** `README.md`, `nodes/_template/main.md`

### [2026-07-19 22:43] - Guideline: Master Entrypoint & SOP
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "masukkan aturan ke `nodes/_template/main.md` dan refine lah main md buatlah lebih terstruktur, dan refine lagi terkait hierarki supaya tidak tumpang tindih. berlakukan aturan ke seluruh file di orchestrator template"
- **Perubahan:** Restrukturisasi total `main.md` menjadi hierarki A-E yang lebih jelas, menambahkan Protokol Pemulihan Konteks (Bagian B), memindahkan aturan Git & Inisiatif Liar ke file pedoman global (`version-control.md` & `error-handling.md`), serta menambahkan keterangan SSoT pada `global-guidelines/README.md`.
- **Path File:** `nodes/_template/main.md`, `global-guidelines/error-handling.md`, `global-guidelines/version-control.md`, `global-guidelines/README.md`

### [2026-07-02 16:58] - Guideline: Main SOP
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "harus tetap auto commit dan setiap auto commit harus berdasarkan ticket, jika instruksi tak memiliki ticket maka harus dibuatkan ticket! tambahkan di @[nodes/_template/main.md]"
- **Perubahan:** `[SUPERSEDED]` Menambahkan poin ke-7 pada SOP di `main.md` yang mewajibkan auto-commit berbasis tiket. *(Catatan: Aturan ini telah digugurkan. Berdasarkan revisi terbaru di `version-control.md`, AI dilarang keras melakukan auto-commit).*
- **Path File:** `nodes/_template/main.md`

