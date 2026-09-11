# Product Requirements Document (PRD)

## Apa itu PRD
Product Requirements Document (PRD) adalah dokumen komprehensif yang menjadi panduan mutlak bagi **AI Agent** dalam membangun aplikasi atau proyek perangkat lunak ini. Dokumen ini menjelaskan secara rinci fungsionalitas, fitur, tujuan, dan batasan dari aplikasi yang akan dikembangkan.

Dalam pendekatan *Vibe Coding* dimana penulisan kode dan pengembangan dilakukan secara otonom atau semi-otonom oleh AI, PRD ini berfungsi sebagai instruksi utama (*master prompt*) dan sumber kebenaran tunggal (*single source of truth*). Dokumen ini mendefinisikan dengan jelas **apa** aplikasi yang harus dibangun oleh AI, **bagaimana perilaku yang diharapkan** dari aplikasi tersebut, serta **apa saja kriteria kesuksesannya** (*acceptance criteria*). Melalui dokumen ini, AI Agent dapat memahami *big picture* dan spesifikasi produk secara menyeluruh sebelum menyusun rancangan teknis (*System Design*) maupun mengeksekusi penulisan kode.

Secara rinci, sebuah dokumen PRD wajib memuat komponen-komponen berikut:
*   **Tujuan & Latar Belakang (*Objective & Background*):** Penjelasan mengenai masalah utama yang ingin dipecahkan, deskripsi target pengguna (*user personas*), dan alasan logis mengapa produk/fitur ini penting untuk dikembangkan.
*   **Alur Pengguna (*User Flow / User Journey*):** Narasi atau urutan langkah-langkah *end-to-end* yang menggambarkan cara pengguna berinteraksi dengan ekosistem (contoh: alur dari registrasi hingga menyelesaikan sebuah transaksi).
    *   **Kewajiban Visualisasi PlantUML:** Anda **WAJIB** membuat visualisasi alur pengalaman pengguna ini menggunakan PlantUML. Simpan kodenya di `global-docs/diagrams/user-journey.puml` lalu buat tautan rujukannya di sini.
*   **Interaksi Sistem Global (Use Case & Activity Diagram):** Khusus jika sistem ini memuat aplikasi bertipe *Web*, *Mobile*, atau *Desktop*, Anda **WAJIB** membuat *Use Case Diagram* atau *Activity Diagram* berbasis PlantUML. Jika aplikasi ini berupa *Game*, Anda **WAJIB** membuat *Macro State Diagram* (misal: *Menu State*, *Play State*) menggunakan PlantUML. Simpan kode diagram tersebut sebagai `.puml` di dalam folder `global-docs/diagrams/` secara eksplisit dan cantumkan tautannya di bagian ini.
*   **Kebutuhan Fungsional (*Functional Requirements*):** Daftar rinci berisi aksi-aksi dan fungsionalitas sistem yang wajib ada (contoh: "sistem harus dapat mengirimkan kode OTP via email", "pengguna dapat mem-filter data berdasarkan tanggal").
*   **Kebutuhan Non-Fungsional (*Non-Functional Requirements*):** Ekspektasi yang mengatur performa, keamanan, stabilitas, waktu respons sistem, hingga *support* *platform* (misalnya: *loading* halaman maksimal 2 detik).
*   **Kriteria Penerimaan (*Acceptance Criteria*):** Syarat dan batasan mutlak (termasuk skenario *edge cases*) yang harus terpenuhi agar sebuah fitur divalidasi dan dianggap "selesai" dikerjakan oleh AI Agent.
*   **Asumsi & Keterbatasan (*Assumptions & Constraints*):** Prediksi kondisi yang mendasari pengembangan (misal: "API pihak ketiga selalu tersedia") dan batasan sistem/bisnis yang mengikat jalannya proyek.
*   **Di Luar Cakupan (*Out of Scope*):** Daftar eksplisit mengenai fungsi atau fitur yang **tidak** akan dikerjakan pada fase/iterasi saat ini untuk menjaga agar fokus eksekusi AI Agent tidak menyimpang.
*   **Peta Jalan Fase (Milestone/Phase Breakdown):** Pembagian target rilis fitur ke dalam beberapa fase berurut (contoh: v1.0 MVP, v1.1 Lanjutan) agar AI Agent memahami prioritas eksekusi tiket di masa depan.

### Aturan Khusus Ekosistem Multi-Project (Multi-Node)
Jika proyek ini berkembang atau sejak awal dirancang berskala ekosistem (terdiri dari beberapa *node* terpisah, misalnya Frontend Web, Backend API, dan Mobile App), Anda (AI Agent) **WAJIB MUTLAK** merestrukturisasi isi dokumen PRD ini ke dalam dua tingkatan hierarki:
1. **Global Scope:** Berisi visi utama proyek, target *user personas*, alur pengguna lintas-sistem, dan asumsi tingkat ekosistem secara menyeluruh.
2. **Node-Specific Scope:** Pecah dan kelompokkan kebutuhan fungsional (*Functional Requirements*), batasan non-fungsional, dan kriteria penerimaan ke dalam sub-bab khusus untuk masing-masing *node* secara terisolasi.

## Cara Melakukan Generate PRD
Ceritakan konsep, ide dasar, dan keseluruhan alur aplikasi yang ingin dibangun secara garis besar. Setelah itu, instruksikan AI Agent untuk secara aktif menanyakan detail-detail apa saja yang masih dibutuhkan untuk memperjelas dan memperinci penjelasan Anda. Proses tanya-jawab ini dilakukan sampai AI memiliki konteks dan informasi yang cukup untuk menyusun draf PRD yang lengkap.

> **Kewajiban Alur (Pipeline Obligation):** Setelah dokumen PRD ini disahkan (selesai di-*generate*), AI Agent **WAJIB MUTLAK** diinstruksikan untuk melanjutkan proses penyusunan arsitektur teknis dengan merujuk pada dokumen `nodes/[nama-node]/docs/system-design.md` milik masing-masing *node*. Dilarang menulis kode sebelum System Design terbentuk!

## Anti Dispatch (macOS Dynamic Island & Workspace Orchestrator)

### 1. Tujuan & Latar Belakang (Objective & Background)
* **Masalah:** Saat menggunakan agen AI pada Antigravity IDE (berbasis Electron), pengembang kesulitan memantau status eksekusi agen yang sedang berjalan secara simultan (*parallel multi-agent / multi-worktree execution*) tanpa harus terus-menerus bolak-balik memeriksa jendela IDE. Selain itu, menjalankan beberapa cabang Git (*worktree*) dan menjaga isolasi memori/data-dir IDE membutuhkan konfigurasi CLI manual yang rentan kesalahan.
* **Solusi:** **Anti Dispatch** adalah aplikasi utilitas *native* macOS berupa panel melayang (*floating HUD / Dynamic Island*) yang menempel secara presisi di area notch MacBook (atau *floating capsule* pada Mac tanpa notch). Aplikasi ini menjadi pusat kendali visual (*glanceable mission control*) dan peluncur instan (*workspace orchestrator*) untuk mengelola, meluncurkan, dan memantau status eksekusi agen AI di Antigravity IDE secara 100% *pure GUI*.
* **Target Pengguna (*User Personas*):**
  * *AI-Assisted Software Engineer / Power User:* Pengembang yang mendelegasikan tugas kode ke beberapa agen AI sekaligus di berbagai *branch* atau *project*.
  * *Solo Developer:* Pengembang harian yang ingin memantau agen AI secara *glanceable* di sudut notch layar tanpa mengorbankan ruang kerja layar (*screen real estate*).

---

### 2. Alur Pengguna (User Flow / User Journey)
Visualisasi alur pengguna end-to-end telah dipetakan secara eksplisit pada diagram PlantUML:
👉 **Diagram Alur Pengguna:** [user-journey.puml](file:///Users/raka/Developer/repositories/projects/anti-dispatch-dir/anti-dispatch-ai-orchestrator/global-docs/diagrams/user-journey.puml)

* **Skenario 1 (Standby & Peluncuran Proyek Standar):**
  1. Notch menampilkan status *Standby* dengan tombol aksi cepat *"Open Project..."* dan *"Open New Branch..."*.
  2. Pengguna mengklik *"Open Project..."* -> Memilih folder repo melalui `NSOpenPanel` native.
  3. Background engine menghitung hash direktori untuk isolasi memori `~/.antigravity-multi/<hash>_<name>`, mengalokasikan port CDP loopback (rentang 9221–9229), dan meluncurkan Antigravity IDE.
  4. Notch otomatis bertransisi menjadi *Active Session HUD*.
* **Skenario 2 (Multi-Branch / Multi-Worktree Parallel Agent):**
  1. Pengguna mengklik *"Open New Branch..."* pada Notch HUD.
  2. Modal form `BranchCreatorSheet` muncul: Pengguna memilih repositori, mengetikkan nama cabang (misal `feat/oauth`), dan memilih cabang induk (`main`).
  3. Engine memanggil `GitWorktreeService` native untuk menjalankan `git worktree add`, mengalokasikan port CDP baru, dan meluncurkan instance Antigravity terisolasi.
  4. Notch menampilkan status multi-sesi dalam format *Glanceable Pill* (collapsed) dan *Accordion List* (expanded).
* **Skenario 3 (Interaksi Sesi & Window Focus):**
  1. Mengklik kartu sesi di Notch HUD langsung memfokuskan jendela IDE terkait (`NSRunningApplication.activate`).
  2. Mengklik tombol close ("x") menghentikan proses IDE dan membersihkan koneksi CDP.

---

### 3. Interaksi Sistem Global (Use Case)
Visualisasi aktor dan relasi use case sistem telah didokumentasikan pada:
👉 **Diagram Use Case:** [use-case.puml](file:///Users/raka/Developer/repositories/projects/anti-dispatch-dir/anti-dispatch-ai-orchestrator/global-docs/diagrams/use-case.puml)

---

### 4. Kebutuhan Fungsional (Functional Requirements)

#### A. Mesin Peluncur & Isolasi Data (`LauncherService`)
1. **Pendeteksi Binary IDE:** Menemukan path instalasi Antigravity IDE via `NSWorkspace.shared.urlForApplication(withBundleIdentifier:)` atau fallback ke `/Applications/Antigravity IDE.app`.
2. **Alokasi Port Dinamis:** Memindai rentang port loopback `127.0.0.1:9221-9229` menggunakan POSIX socket probe dan mengalokasikan port kosong pertama.
3. **Isolasi Memori & User Data:** Menghasilkan argumen `--user-data-dir="~/.antigravity-multi/<6-char-md5-hash>_<project>[_<branch>]"` untuk memastikan context window, cache, dan riwayat agen terisolasi sempurna.
4. **Peluncuran Subproses Terisolasi:** Menginjeksi argumen `--remote-debugging-port=<port>` dan `--user-data-dir=<dir>` melalui `NSWorkspace.OpenConfiguration` dengan `createsNewApplicationInstance = true`.

#### B. Manajemen Git Worktree Native (`GitWorktreeService`)
1. **Validasi Repositori:** Memverifikasi direktori target adalah repo Git valid via `/usr/bin/git rev-parse --is-inside-work-tree`.
2. **Deteksi Worktree Eksis:** Memeriksa apakah nama branch sudah terhubung ke worktree yang ada via `git worktree list --porcelain`.
3. **Pembuatan Worktree & Branch Baru:** Menjalankan `git worktree add` dengan sanitasi path aman (`[A-Za-z0-9.-]`) di direktori sejajar repositori induk (`<repo>-<sanitized-branch>`).

#### C. Mesin Monitoring CDP & Ekstraksi Status (`CDPService`)
1. **Port Scanner & Fingerprinting:** Melakukan probing HTTP `GET http://127.0.0.1:<port>/json/version` dan `GET /json/list` untuk memvalidasi hanya target Antigravity IDE (mengabaikan Electron app lain).
2. **WebSocket Client & Injeksi JS:** Membuka koneksi WebSocket ke `webSocketDebuggerUrl` dan mengeksekusi payload JavaScript mandiri melalui `Runtime.evaluate`.
3. **Pola Ekstraksi Multi-Tier:**
   - Layer 1: Container `[data-testid="agent-chat-container"]`, `.interactive-session`, `.agent-view`.
   - Layer 2: Status loading `.codicon-loading`, `.monaco-progress-container.active`, `[data-status="running"]`.
   - Layer 3: Heuristik teks status (`"worked for"`, `"completed"`, `"done"`) dan sub-steps tool call terbaru.
4. **Adaptive Polling:** Polling aktif **800ms** saat status `RUNNING`, melambat ke **3000ms** saat `IDLE`/`DONE`, dan berhenti (zero polling) saat tidak ada port aktif.

#### D. Antarmuka Notch HUD & Status Item (`AppKit + SwiftUI`)
1. **Floating Notch Panel:** Panel AppKit `NSPanel` berlevel `NSWindow.Level.floating`, borderless, non-activating, menempel di area notch MacBook (`NSScreen.safeAreaInsets.top > 0`).
2. **Fallback Floating Pill:** Otomatis bertransformasi menjadi kapsul melayang di bagian tengah atas layar jika layar tidak memiliki notch fisik (Mac mini, Mac Studio, display eksternal).
3. **Tampilan Mode Standby (`StandbyNotchView`):** Menampilkan status siap dengan tombol peluncur *"Open Project..."* dan *"Open New Branch..."*.
4. **Tampilan Single Session (`SingleSessionView`):** Menampilkan status ringkas (collapsed) dan rincian langkah kerja/file yang dianalisis (expanded on hover/click).
5. **Tampilan Multi-Session (`MultiSessionAccordionView`):** Menampilkan badge agregat (misal *"● 1 Running ● 1 Done"*) dengan accordion ekspansif untuk masing-masing cabang/proyek.
6. **Menu Bar Status Item:** Ikon status pada menu bar macOS sebagai titik akses sekunder.

---

### 5. Kebutuhan Non-Fungsional (Non-Functional Requirements)
1. **Performa & Konsumsi Daya:** Penggunaan CPU di latar belakang < 1.5% saat aktif memantau; konsumsi memori RAM < 45 MB.
2. **Keamanan Loopback:** Komunikasi CDP strictly dibatasi pada `127.0.0.1` / `::1` (localhost only). Tidak ada port atau server yang terbuka ke jaringan lokal/internet.
3. **Stabilitas & Resiliensi:** Jika koneksi CDP terputus atau IDE ditutup paksa, aplikasi tidak boleh *crash*, melainkan melakukan graceful teardown sesi.
4. **Target Platform:** macOS 14.0 (Sonoma) atau lebih tinggi (Swift 6 concurrency, modern SwiftUI `@Observable`, AppKit).

---

### 6. Kriteria Penerimaan (Acceptance Criteria)
- [ ] Pengguna dapat meluncurkan folder proyek lokal atau branch Git baru langsung dari antarmuka GUI Notch HUD tanpa membuka terminal.
- [ ] Argumen `--remote-debugging-port` dan `--user-data-dir` terinjeksi secara valid dan terbukti membuka instance terisolasi.
- [ ] Notch HUD mendeteksi perubahan status agen dari Antigravity IDE (Standby -> Running -> Done) secara real-time dengan latensi < 1 detik.
- [ ] Accordion multi-sesi dapat memantau setidaknya 3 instance paralel tanpa degradasi responsivitas UI.
- [ ] Mengklik sesi pada Notch HUD berhasil membawa jendela Antigravity IDE terkait ke layar depan.

---

### 7. Asumsi & Keterbatasan (Assumptions & Constraints)
* **Distribusi Non-App Store:** Aplikasi didistribusikan secara independen via Disk Image (`.dmg`) dengan auto-update **Sparkle 2 Framework** dan **Homebrew Cask** (`brew install --cask anti-notch`), ditandatangani dengan Apple Developer ID & Apple Notarized.
* **Ketergantungan CLI Git:** Sistem mengasumsikan binary `/usr/bin/git` (Apple Command Line Tools / Xcode Tools) tersedia di sistem pengguna.
* **Ketergantungan Antigravity IDE:** Antigravity IDE terinstal di mesin pengguna (`/Applications/Antigravity IDE.app` atau bundle ID terdaftar).

---

### 8. Di Luar Cakupan (Out of Scope)
* Menulis atau mengedit kode di dalam Antigravity secara langsung melalui Anti Dispatch (Anti Dispatch bertindak sebagai orkestrator peluncur & pemantau status pasif, bukan editor kode).
* Dukungan platform selain macOS (Windows / Linux berada di luar cakupan).
* Pengiriman data telemetri ke server cloud pihak ketiga (seluruh pemrosesan bersifat 100% offline & lokal).

---

### 9. Peta Jalan Fase (Milestone Breakdown)
* **Milestone 1 (v0.1 - Core Launcher Engine):** Implementasi `LauncherService`, `GitWorktreeService`, path hasher, dan alokasi port CDP dinamis.
* **Milestone 2 (v0.2 - CDP Protocol & State Extractor):** Implementasi `CDPService`, loopback scanner, WebSocket client, injeksi script DOM, dan adaptive polling.
* **Milestone 3 (v0.3 - Notch HUD & UI Components):** Implementasi `NSPanel` floating notch, deteksi safe area hardware, `StandbyNotchView`, `SingleSessionView`, `MultiSessionAccordionView`, dan modal `BranchCreatorSheet`.
* **Milestone 4 (v1.0 - Production Readiness & Polish):** Integrasi Menu Bar status item, aktivasi jendela instan PID, persistensi pengaturan `@AppStorage`, integrasi Sparkle 2 updater, dan packaging Homebrew Cask.
