# Development Planning (Roadmap)

## Apa itu Development Planning
*Development Planning* adalah dokumen peta jalan (*roadmap*) strategis yang menjembatani dokumen makro (seperti PRD dan System Design) dengan tugas-tugas mikro (berupa Tiket). Dokumen ini berfungsi untuk memecah keseluruhan ruang lingkup proyek ke dalam beberapa fase pengerjaan (*Milestones*) yang dapat dikelola secara bertahap.

Dalam pendekatan *Vibe Coding* dengan AI Agent, dokumen ini sangat krusial sebagai "Gudang Antrean Tiket" (*Ticket Backlog*). Daripada meminta pengguna membuat instruksi tiket secara manual satu per satu, AI Agent akan secara proaktif mendaftarkan seluruh kerangka tiket yang dibutuhkan di dalam dokumen ini. 

## Aturan Sinkronisasi Tiket
1.  **Dilarang Mengarang Tiket (Khusus MODE 1):** Pada Mode 1, AI Agent **DILARANG KERAS** mengarang, mencetuskan, atau membuat file tiket baru di direktori `tickets/` jika tiket tersebut belum tercatat sebagai target *backlog* di dalam file `development-planning.md` ini. Namun pada **MODE 2**, aturan ini dibatalkan dan AI justru diwajibkan membuat tiket retrospektif setelah menyelesaikan setiap *prompt* dari *developer*.
2.  **Transisi Status:** Jika sebuah tiket dari dokumen ini telah dipindahkan wujudnya menjadi file *markdown* di folder `tickets/`, AI wajib kembali ke dokumen ini dan menandai tiket tersebut sebagai `[CREATED]` atau mencoretnya.
3.  **Kebijakan UI Slicing & Prototyping:** Khusus untuk tiket yang berkaitan dengan perancangan antarmuka (*UI Slicing*), AI Agent **WAJIB** menanyakan terlebih dahulu kepada pengguna apakah desainnya sudah ada atau perlu di-*generate* via HTML melalui direktori `prototypes/`. Apabila diputuskan menggunakan mekanisme prototipe, maka tiket untuk perancangan prototipe **WAJIB dipisahkan** dari tiket implementasi ke dalam *codebase* proyek.

## Struktur Rencana Pengembangan

Setiap fase wajib diuraikan dengan mencantumkan gol utama (*objective*) dan daftar antrean tiket yang akan dibuat ke depannya.

### Fase 1: [Nama Fase, misal: Inisialisasi & Autentikasi]
- **Tujuan:** [Jelaskan target pencapaian di fase ini]
- **Daftar Tiket (Backlog):**
  - `[ ]` **TICKET-01:** [Judul/Deskripsi Singkat]
  - `[ ]` **TICKET-02:** [Judul/Deskripsi Singkat]

### Fase 2: [Nama Fase]
- **Tujuan:** [Jelaskan target pencapaian di fase ini]
- **Daftar Tiket (Backlog):**
  - `[ ]` **TICKET-03:** [Judul/Deskripsi Singkat]

---

## Anti Dispatch (Roadmap & Ticket Backlog)

> 💡 **Catatan Mode Operasional (MODE 2):** Karena node ini beroperasi dalam **MODE 2 (Prompt-Driven)**, daftar tiket di bawah berfungsi sebagai panduan acuan arsitektur (*roadmap reference*). AI Agent akan mengeksekusi instruksi per-langkah dari *developer* dan membuat tiket retrospektif sesuai instruksi yang diselesaikan.

---

### Fase 1: Core Launcher & Git Worktree Engine
- **Tujuan:** Membangun fondasi orkestrasi peluncuran instance Antigravity IDE, perhitungan isolasi memori `~/.antigravity-multi`, alokasi port CDP loopback (9221–9229), dan otomatisasi Git worktree native via Swift concurrency.
- **Daftar Tiket (Backlog):**
  - `[ ]` **TICKET-01:** Implementasi `PathHasher` & Alokator Direktori Isolasi `~/.antigravity-multi/<hash>_<name>`.
  - `[ ]` **TICKET-02:** Implementasi `GitWorktreeService` (Subprocess `/usr/bin/git` wrapper: rev-parse, worktree list, worktree add).
  - `[ ]` **TICKET-03:** Implementasi `LauncherService` & Dynamic Port Scanner (`127.0.0.1:9221-9229`) via POSIX bind probe.

---

### Fase 2: CDP Protocol & State Extractor Engine
- **Tujuan:** Membangun modul pemantau latar belakang yang memverifikasi identitas target Antigravity IDE via HTTP `/json/version`, menghubungkan WebSocket CDP, dan mengekstraksi status live DOM secara adaptif.
- **Daftar Tiket (Backlog):**
  - `[ ]` **TICKET-04:** Implementasi HTTP Port Probing & Target Fingerprinting Validator (`/json/version` & `/json/list`).
  - `[ ]` **TICKET-05:** Implementasi WebSocket CDP Client & Eksekusi Script DOM Injeksi (`Runtime.evaluate`).
  - `[ ]` **TICKET-06:** Implementasi Adaptive Polling Scheduler (800ms saat `RUNNING`, 3000ms saat `IDLE`/`DONE`).

---

### Fase 3: Notch HUD & Floating Interface
- **Tujuan:** Merancang antarmuka visual native berbasis AppKit `NSPanel` floating yang terpasang di notch MacBook (atau floating capsule pill) dengan komponen SwiftUI reaktif.
- **Daftar Tiket (Backlog):**
  - `[ ]` **TICKET-07:** Implementasi `NotchPanel: NSPanel` (borderless, floating level, non-activating, safeAreaInsets detection).
  - `[ ]` **TICKET-08:** Implementasi `StandbyNotchView` & Aksi Cepat Native Folder Picker (`NSOpenPanel`).
  - `[ ]` **TICKET-09:** Implementasi `SingleSessionView` (Collapsed capsule & Expanded glanceable live feed).
  - `[ ]` **TICKET-10:** Implementasi `MultiSessionAccordionView` & Modal Sheet `BranchCreatorSheet`.

---

### Fase 4: System Integration, Menu Bar & Polish
- **Tujuan:** Menyempurnakan integrasi sistem macOS, menu bar status item, aktivasi fokus window via PID, dan auto-update.
- **Daftar Tiket (Backlog):**
  - `[ ]` **TICKET-11:** Implementasi Menu Bar `StatusBarController` (`NSStatusItem`) sebagai titik akses sekunder.
  - `[ ]` **TICKET-12:** Implementasi Window Focus Controller (`NSRunningApplication.activate`).
  - `[ ]` **TICKET-13:** Integrasi Sparkle 2 Auto-Updater Framework & Persistensi Pengaturan `@AppStorage`.

