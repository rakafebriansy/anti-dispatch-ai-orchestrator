# AI Orchestrator Template

Selamat datang di ekosistem **AI Orchestrator**. Berbeda dengan sekadar *prompting* AI biasa untuk menulis kode, repositori ini adalah sebuah kerangka kerja (*framework*) rekayasa perangkat lunak yang dirancang agar AI Agent (seperti Cursor, GitHub Copilot Workspace, atau agen otonom lainnya) bekerja layaknya seorang *Software Engineer* terstruktur yang tidak mudah berhalusinasi, terhindar dari *looping error*, dan selalu merujuk pada dokumentasi mutlak (*Single Source of Truth*).

---

## 🚀 Cara Penggunaan

Proses pengembangan dengan Orchestrator ini dibagi menjadi dua fase Prompt.

### FASE 1: Startup Prompt (Inisialisasi Proyek)
Gunakan salah satu *prompt* di bawah ini hanya **satu kali** di awal proyek (tergantung apakah proyek Anda hanya satu aplikasi tunggal atau gabungan dari beberapa aplikasi/node). Tujuannya adalah menginisialisasi seluruh dokumen kosong di `docs/` dan menyiapkan pedoman proyek Anda.

#### Opsi A: Startup Prompt (Single-Project Environment)
Gunakan *prompt* ini jika Anda hanya ingin mengatur satu aplikasi/repositori (misal: hanya Frontend atau Fullstack Monorepo). Salin, isi bagian `[ DALAM KURUNG SIKU ]`, dan kirimkan utuh ke AI Agent:

```text
Saya ingin menginisialisasi Single-Project Environment menggunakan kerangka kerja AI Orchestrator ini.

Berikut adalah definisi Proyek Utama yang akan diatur:

A. INFORMASI PROYEK
1. Nama Sistem: [Nama aplikasi, misal: Personal Portfolio Website]
2. Ide/Konsep Utama: [Jelaskan fitur utama dari sistem ini]
3. Vibe/Estetika UI: [Misal: Modern, minimalis, dominasi warna gelap]
4. Batasan Sistem Utama: [Misal: Harus responsif, cepat, SEO-friendly]
5. Path Codebase: [Path absolut ke folder proyek, misal: `/Users/.../my-portfolio`]
6. Tech Stack: [Misal: Next.js, TailwindCSS]

INSTRUKSI STARTUP ANDA:
Berbekal informasi di atas, JANGAN MENULIS KODE APLIKASI SAMA SEKALI. Lakukan langkah-langkah otonom berikut secara berurutan:
1. Pahami struktur `ai-orchestrator-template` ini. Karena ini adalah lingkungan proyek tunggal, kita hanya akan menggunakan satu Node utama.
2. Evaluasi & Wawancara Pengguna: Jika deskripsi yang saya berikan di atas masih terlalu dangkal atau belum cukup untuk mengisi Dokumen Mandatory secara detail dan maksimal, Anda WAJIB BERHENTI mengeksekusi langkah selanjutnya. (Catatan: Yang tergolong Dokumen Mandatory adalah: `prd.md`, `design-system.md`, `system-design.md`, dan `development-planning.md`). Ajukan daftar pertanyaan kritis kepada saya terkait visi, batasan teknis, target pengguna, dan spesifikasi fungsionalitas. Ulangi proses tanya-jawab ini hingga Anda memiliki konteks yang sangat solid untuk mengisi keempat dokumen wajib tersebut secara mutlak.
3. Setelah informasi dirasa memadai, buatkan draf komprehensif untuk `global-docs/prd.md` dan `global-docs/design-system.md` berdasarkan spesifikasi proyek di atas.
4. Buatkan GitHub Project di awal untuk repositori ini. Pastikan GitHub Project tersebut dibuat di bawah kepemilikan (*belongs to*) *User* dan ditautkan (disambungkan) ke repositori ini.
5. Untuk inisialisasi Node proyek utama:
   a. Gandakan (copy) folder `nodes/_template/` menjadi `nodes/[nama-proyek]/`.
   b. Pindai (scan) source code asli dari Path Codebase yang diberikan untuk menganalisis pola arsitektur, legacy code, dan pustaka eksisting.
   c. Tuliskan hasil pindai dan batasan spesifik proyek ke dalam `nodes/[nama-proyek]/guidelines/project-context.md`.
   d. Buat `nodes/[nama-proyek]/docs/system-design.md`.
   e. Berdasarkan tipe aplikasi (Backend, Web, Mobile, Game), rancang seluruh arsitektur menggunakan PlantUML (ERD, Flowchart, State Diagram, User Journey, Use Case). Simpan kode arsitektur tersebut sebagai file-file `.puml` terpisah secara eksplisit di dalam folder `docs/diagrams/` (untuk Node) atau `global-docs/diagrams/` (untuk Global), lalu tautkan (link) file tersebut ke dalam `prd.md` dan `system-design.md` sesuai pedoman.
   f. Buat `nodes/[nama-proyek]/docs/development-planning.md` yang merancang daftar backlog tiket (TICKET-XX.md) yang harus dikerjakan di fase pertama.
   g. Tuliskan entri log inisialisasi awal ke dalam file `nodes/[nama-proyek]/CHANGELOG.md` menggunakan templat dari `global-docs/templates/changelog_entry_template.md` yang mencatat tanggal, status pembuatan node, dan ringkasan arsitektur dasar yang baru saja ditetapkan.

Setelah seluruh dokumen mandatory terlengkapi dan fase di atas selesai sempurna, berikan saya rangkuman singkat terkait struktur baru yang terbentuk dan tanyakan persetujuan saya sebelum kita masuk ke mode eksekusi tiket harian!

CATATAN PENTING UNTUK AI: JANGAN menghapus folder `nodes/_template/` setelah Anda menggandakannya. Folder tersebut harus tetap utuh dan tidak boleh disentuh sebagai cetak biru jika di masa depan kita beralih ke multi-proyek!
```

#### Opsi B: Startup Prompt (Multi-Project Environment)
Gunakan prompt ini jika ekosistem Anda terdiri dari beberapa aplikasi/node yang terpisah (misal: Frontend terpisah dari Backend). Salin, isi bagian [ DALAM KURUNG SIKU ], dan kirimkan utuh ke AI Agent:

```text
Saya ingin menginisialisasi Multi-Project Environment menggunakan kerangka kerja AI Orchestrator ini.

Berikut adalah definisi Ekosistem (Environment) dan Sub-Proyek (Nodes) yang akan diatur:

A. ENVIRONMENT (Global Scope)
1. Nama Sistem: [Nama sistem keseluruhan, misal: Smart E-Commerce Platform]
2. Ide/Konsep Utama: [Jelaskan fitur utama dari sistem ini]
3. Vibe/Estetika UI: [Misal: Modern, dominasi biru tua, minimalis]
4. Batasan Sistem Utama: [Misal: Harus secure, GDPR compliant]

B. NODES (Sub-Project Scope)
- Node 1: Frontend App
  - Path Codebase: [Path absolut ke folder frontend, misal: `/Users/.../my-frontend`]
  - Tech Stack: [Misal: Next.js, TailwindCSS]
- Node 2: Backend API
  - Path Codebase: [Path absolut ke folder backend, misal: `/Users/.../my-backend`]
  - Tech Stack: [Misal: NestJS, PostgreSQL]
(Tambahkan node lain jika ada)

INSTRUKSI STARTUP ANDA:
Berbekal informasi di atas, JANGAN MENULIS KODE APLIKASI SAMA SEKALI. Lakukan langkah-langkah otonom berikut secara berurutan:
1. Pahami struktur `ai-orchestrator-template` yang berbasis nodes ini.
2. Evaluasi & Wawancara Pengguna: Jika deskripsi yang saya berikan di atas masih terlalu dangkal atau belum cukup untuk mengisi Dokumen Mandatory secara detail dan maksimal, Anda WAJIB BERHENTI mengeksekusi langkah selanjutnya. (Catatan: Yang tergolong Dokumen Mandatory adalah: `prd.md`, `design-system.md`, `system-design.md`, dan `development-planning.md`). Ajukan daftar pertanyaan kritis kepada saya terkait visi, batasan teknis, target pengguna, dan spesifikasi fungsionalitas. Ulangi proses tanya-jawab ini hingga Anda memiliki konteks yang sangat solid untuk mengisi keempat dokumen wajib tersebut secara mutlak.
3. Setelah informasi dirasa memadai, buatkan draf komprehensif untuk `global-docs/prd.md` dan `global-docs/design-system.md` berdasarkan spesifikasi lingkungan (Environment) di atas.
4. Buatkan GitHub Project di awal untuk repositori ini. Pastikan GitHub Project tersebut dibuat di bawah kepemilikan (*belongs to*) *User* dan ditautkan (disambungkan) ke repositori ini.
5. Untuk SETIAP Node yang terdaftar di atas:
   a. Gandakan (copy) folder `nodes/_template/` menjadi `nodes/[nama-node]/`.
   b. Pindai (scan) source code asli dari Node tersebut di Path Codebase yang diberikan untuk menganalisis pola arsitektur, legacy code, dan pustaka eksisting.
   c. Tuliskan hasil pindai dan batasan spesifik node tersebut ke dalam `nodes/[nama-node]/guidelines/project-context.md`.
   d. Buat `nodes/[nama-node]/docs/system-design.md` khusus untuk node tersebut.
   e. Berdasarkan tipe aplikasi (Backend, Web, Mobile, Game), rancang seluruh arsitektur menggunakan PlantUML (ERD, Flowchart, State Diagram, User Journey, Use Case). Simpan kode arsitektur tersebut sebagai file-file `.puml` terpisah secara eksplisit di dalam folder `docs/diagrams/` spesifik milik node tersebut, lalu tautkan (link) file tersebut ke dalam prd.md and system-design.md sesuai pedoman.
   f. Buat `nodes/[nama-node]/docs/development-planning.md` yang merancang daftar backlog tiket (TICKET-XX.md) yang harus dikerjakan di fase pertama node ini.
   g. Tuliskan entri log inisialisasi awal ke dalam file `nodes/[nama-node]/CHANGELOG.md` menggunakan templat dari `global-docs/templates/changelog_entry_template.md` yang mencatat tanggal, status pembuatan node, dan ringkasan arsitektur dasar yang baru saja ditetapkan.

Setelah seluruh dokumen mandatory terlengkapi dan fase di atas selesai sempurna, berikan saya rangkuman singkat terkait struktur baru yang terbentuk dan tanyakan persetujuan saya sebelum kita masuk ke mode eksekusi tiket harian!

CATATAN PENTING UNTUK AI: JANGAN menghapus folder `nodes/_template/` setelah Anda menggandakannya. Folder tersebut harus tetap utuh dan tidak boleh disentuh sebagai cetak biru untuk penambahan node baru di masa depan!
```

### FASE 2: Execution Prompt (Pengerjaan Tugas)
Gunakan salah satu dari dua Execution Prompt di bawah ini sesuai dengan ruang lingkup tugas yang ingin Anda kerjakan.

#### Opsi A: Execution Prompt (Single-Node)
Gunakan prompt ini jika Anda hanya ingin fokus mengerjakan fitur di SATU proyek spesifik (misalnya hanya mengubah UI Frontend).

```text
Kamu WAJIB membaca `nodes/[NAMA_NODE_ANDA]/main.md` sebagai Master Entrypoint. Patuhi seluruh pedoman arsitektur dan larangan mutlak yang tertulis di dalamnya sebelum menulis satu baris kode pun.

Instruksi Tugas: [TULIS_INSTRUKSI_ATAU_ID_TIKET_DI_SINI]
```

#### Opsi B: Execution Prompt (Multi-Node / Lintas Proyek)
Gunakan *prompt* ini jika Anda memiliki tugas integrasi besar yang melibatkan banyak proyek sekaligus (misalnya menyambungkan API Backend ke Frontend).

```text
Tugas ini bersifat lintas-proyek (Multi-Node). Kamu WAJIB memindai direktori `nodes/` dan membaca file `main.md` dari masing-masing sub-proyek yang relevan. Pastikan integrasi antarsistem mematuhi pedoman global dan tidak merusak arsitektur.

Instruksi Tugas: [TULIS_INSTRUKSI_LINTAS_NODE_DI_SINI]
```

Dengan *prompt* super simpel yang hanya butuh 2 pengisian ini, AI akan senantiasa *context-aware*, disiplin, dan patuh pada pedoman tanpa Anda harus mengulang-ulang instruksi setiap kalinya.
