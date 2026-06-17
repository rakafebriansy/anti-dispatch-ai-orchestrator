# Code Quality & Formatting Standard

## ZERO-COMMENT POLICY
Anda **DILARANG KERAS** menambahkan komentar apa pun di dalam *source code* yang Anda hasilkan (seperti `// ini fungsi untuk...` atau `/* penjelasan */`). Kode harus sangat bersih, jelas, dan bisa menjelaskan dirinya sendiri (*self-documenting code*). Jika Anda perlu menjelaskan logika atau alur kode, berikan penjelasan tersebut secara naratif di dalam teks Markdown (di luar blok kode).

## Modularitas & Linter
1.  **Linter & Formatter:** Pastikan konfigurasi linter (misalnya: ESLint, Pylint, SwiftLint) dan *formatter* (misalnya: Prettier, Black, Gofmt) yang Anda berikan tidak saling bentrok. Selalu gunakan konfigurasi standar yang direkomendasikan secara global.
2.  **Modular & DRY (Don't Repeat Yourself):** Jangan menulis kode yang berulang. Pisahkan logika ke dalam fungsi utilitas, layanan (*services*), modul, atau *class* yang dapat digunakan kembali (*reusable*).
3.  **Kewajiban Membaca Konteks (Context Reading Policy):** Sebelum Anda menyisipkan sebaris kode baru atau mengedit fungsi spesifik di dalam berkas yang sudah ada, Anda **WAJIB SECARA MUTLAK** untuk memindai/membaca keseluruhan isi dokumen tersebut terlebih dahulu. Pahami pola eksistingnya, konvensi penamaan lokalnya, dan logika di sekitarnya. Jangan langsung menyuntikkan kode buta yang merusak harmoni *file*!

## Penjelasan Skrip CLI
1.  Jika Anda memberikan perintah terminal/CLI (seperti eksekusi skrip, instalasi dependensi, atau *build*), jelaskan secara ringkas fungsi dari setiap *flag* atau argumen yang digunakan di luar blok kode agar mudah dipahami.

## Hindari Hardcoded Default Values
1.  **DILARANG memberikan *default value* di dalam definisi inti:** Jangan pernah menetapkan nilai bawaan (*default value*) secara *hardcode* di dalam definisi parameter komponen, fungsi, atau kelas utama.
2.  Segala bentuk *default value* atau nilai awal harus diinjeksi atau diberikan secara eksplisit melalui argumen/parameter saat fungsi atau komponen tersebut dipanggil (diinisialisasi), untuk memaksimalkan penggunaan ulang (*reusability*) di skenario yang berbeda.

## Dilarang Mem-Bypass Arsitektur (No Hacks)
1.  **DILARANG KERAS menggunakan *inline styles* atau jalan pintas (*shortcuts*):** Anda dilarang menggunakan pendekatan pintas (seperti *inline styles* pada UI atau *hardcode* modifikasi lokal) sekadar untuk mengakali *bug* atau kegagalan konfigurasi spesifik.
2.  **Perbaiki Akar Masalah (*Root Cause*):** Jika ada konfigurasi atau sistem penataan yang gagal teraplikasikan, Anda wajib menelusuri dan memperbaiki akar masalahnya hingga ke file pengaturan utama atau arsitektur dasarnya. Jangan gunakan *hack* lokal sebagai solusi.

## Visualisasi Dokumentasi Berbasis Teks (PlantUML)
Sistem dokumentasi arsitektur di ekosistem ini **DILARANG KERAS** menggunakan lampiran gambar statis eksternal (`.png`, `.jpg`) untuk menggambarkan alur, struktur basis data, atau bagan interaksi.
1.  **Pemisahan File PlantUML:** Segala bentuk visualisasi (seperti Flowchart, ERD, Use Case, State Diagram, atau User Journey) **WAJIB MUTLAK** ditulis menggunakan tata bahasa [PlantUML](https://plantuml.com/) dan disimpan sebagai file berekstensi `.puml` terpisah secara eksplisit di dalam direktori `docs/diagrams/` (untuk lingkup spesifik node) atau `global-docs/diagrams/` (untuk lingkup global ekosistem). Jangan meletakkannya di root direktori node.
2.  **Rujukan (*Linking*):** Di dalam dokumen Markdown (seperti `prd.md` atau `system-design.md`), Anda **DILARANG** menulis blok kode ````plantuml````. Anda hanya diizinkan untuk membuat rujukan atau tautan Markdown menuju file `.puml` tersebut (contoh: `[Lihat Flowchart Game Loop](./diagrams/flowchart.puml)`).
3.  **Kemudahan Modifikasi (Text-Searchable):** Ini bertujuan agar AI Agent dapat melakukan pencarian teks, dan pengguna manusia dapat melihat diagram dengan mudah menggunakan ekstensi PlantUML di *code editor* (VS Code) tanpa merusak atau memperberat pembacaan file Markdown.

## Standarisasi Templat Pengembangan
Saat membangun fungsi-fungsi fundamental tertentu, Anda diwajibkan menyusun dokumentasinya menggunakan struktur *boilerplate* yang telah disediakan:
1.  **Dokumentasi API (`global-docs/templates/api_documentation_template.md`):** Khusus apabila Anda sedang merancang aplikasi yang bersifat API (seperti *backend server* atau integrasi *endpoint* murni), semua struktur URL dan *payload* wajib didokumentasikan menggunakan templat tersebut. *(Peringatan: Gunakan templat ini HANYA pada proyek berbasis API)*.
2.  **Peta Perutean (`global-docs/templates/routing_template.md`):** Segala bentuk tata letak lalu lintas halaman antarmuka (untuk Web/Frontend) atau rute lalu lintas API (untuk Backend) wajib dipetakan kelebarannya secara terpusat menggunakan standar templat *routing* ini guna mencegah rute yatim-piatu (*orphan routes*).
## Strict Directory & Documentation Boundaries

### Pemisahan Kode dan Dokumentasi
1.  Anda **DILARANG KERAS** meletakkan file dokumentasi (seperti berkas PRD, System Design, atau Markdown penjelas) berserakan di dalam direktori *source code* inti aplikasi (seperti di dalam `src/`, `app/`, `lib/`, atau `core/`).
2.  Sistem **AI Orchestrator** secara eksplisit dirancang agar **tidak tertanam (*embedded*) di dalam folder aplikasi utama**, melainkan berdiri sendiri di luarnya. Hal ini bertujuan agar sistem orkestrasi ini dapat dengan mudah dipasang, dipindahkan, atau digunakan pada proyek baru maupun proyek lama (*legacy*) tanpa menimbulkan konflik hierarki.

### Standar Struktur Tingkat Akar (*Root-Level*)
Gunakan struktur direktori terpisah berikut sebagai acuan logika pemisahan ruang kerja. Selalu tuliskan *path* berkas secara akurat di awal setiap blok kode yang Anda instruksikan:

```text
📁 [Root Environment]
├── 📁 frontend-app/               <-- Source code aplikasi asli Frontend
├── 📁 backend-api/                <-- Source code aplikasi asli Backend
│
└── 📁 ai-orchestrator-template/   <-- Lingkungan mandiri pusat kendali AI Multi-Project
    ├── 📄 README.md               <-- Global Startup Prompt
    ├── 📁 global-docs/            <-- Pusat dokumen fondasi (PRD Utama, Design System).
    │   └── 📁 diagrams/           <-- Pusat file arsitektur global (.puml).
    ├── 📁 global-guidelines/      <-- Aturan mutlak lintas-node (security, testing, version-control).
    │
    └── 📁 nodes/                  <-- Pusat komando sub-proyek
        └── 📁 _template/          <-- Draf kosong yang akan digandakan untuk setiap proyek
            ├── 📄 main.md         <-- Entrypoint harian KHUSUS untuk node ini
            ├── 📄 CHANGELOG.md    <-- Arsip historis pembaruan node ini
            ├── 📁 docs/           <-- System Design spesifik & Development Planning.
            │   └── 📁 diagrams/   <-- Diagram spesifik arsitektur node (.puml).
            ├── 📁 guidelines/     <-- Aturan lokal & hasil scan legacy codebase.
            ├── 📁 prototypes/     <-- Area sketsa HTML statis sandbox UI.
            ├── 📁 retrospectives/ <-- Pusat pembelajaran AI untuk kegagalan node ini.
            └── 📁 tickets/        <-- Manajemen task/isu offline node ini.
```

> **Catatan Penting Terkait Direktori Proyek:**
> Isi dan struktur aplikasi asli (seperti `frontend-app/` atau `backend-api/`) di luar template ini tidak diatur secara ketat. Aturan mutlak pada pedoman ini hanyalah menegakkan **pemisahan letak lingkungan secara fisik** antara lingkup *source code* aplikasi Anda dengan direktori `ai-orchestrator-template/`.
