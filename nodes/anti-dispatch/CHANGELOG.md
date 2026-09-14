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

