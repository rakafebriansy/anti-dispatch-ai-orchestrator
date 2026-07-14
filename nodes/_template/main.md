# AI Orchestrator: Master Entrypoint

> 🟢 **STATUS MODE SAAT INI:** [PILIH: MODE 1 / MODE 2]

> ⚠️ **PERHATIAN UNTUK AI AGENT:** 
> Ini adalah dokumen pertama yang WAJIB Anda baca setiap kali memulai sesi baru atau menerima *Execution Prompt*. Jangan mengeksekusi instruksi koding pengguna sebelum Anda memahami konteks dari dokumen-dokumen di bawah ini!

Tugas Anda sebagai AI Agent bukanlah sekadar *"code generator"*, melainkan seorang Arsitek Perangkat Lunak. Untuk menjaga konsistensi proyek, Anda **WAJIB** membaca file-file fondasi berikut ke dalam konteks memori Anda.

> 🛑 **BATASAN RUANG LINGKUP (ANTI-CROSS CONTAMINATION):**
> Node ini terisolasi! Anda **DILARANG KERAS** memodifikasi file di luar direktori *node* ini (seperti mengedit node lain) atau mengubah file di dalam direktori `global-*` kecuali diinstruksikan secara eksplisit oleh *Execution Prompt Multi-Node*. Fokuslah hanya pada *path* lokal di dalam node ini.

## 1. Dokumen Fondasi (Wajib Dibaca Seluruhnya)
File-file ini adalah nyawa dari ekosistem proyek ini. Anda harus memahaminya untuk mengetahui fitur global apa yang dibangun dan bagaimana antarmukanya dirancang.
1. `../../global-docs/prd.md` (Spesifikasi fitur dan alur pengguna global)
2. `docs/system-design.md` (Arsitektur teknis spesifik node ini)
3. `../../global-docs/design-system.md` (Aturan UI/UX, warna, tipografi global)
4. `docs/development-planning.md` (Peta jalan spesifik node ini)
5. `CHANGELOG.md` (Untuk mengetahui progres terakhir di node ini)

## 2. Pedoman Mutlak (Wajib Dibaca Seluruhnya)
Hukum besi operasional Anda. Pelanggaran terhadap pedoman ini akan merusak integritas sistem.
1. `../../global-guidelines/coding.md` (Aturan kualitas kode, modularitas, larangan *hacks*)
2. `../../global-guidelines/version-control.md` (Protokol Git dan alur kerja berbasis Tiket)
3. `../../global-guidelines/error-handling.md` (Aturan *Stop-and-Ask* saat Anda terjebak *error*)
4. `../../global-guidelines/security.md` (Larangan *hardcode API keys*)
5. `guidelines/project-context.md` (Aturan khusus & hasil pemindaian sistem dari node ini)

## 3. Dokumen Kondisional (Baca Saat Dibutuhkan Saja)
Jangan buang token Anda untuk membaca file ini jika instruksi pengguna tidak berkaitan dengannya.
*   **Akan mendeploy aplikasi atau mengkonfigurasi CI/CD?** Baca `../../global-guidelines/deployment.md` dan `../../global-guidelines/pipeline.md`.
*   **Akan menulis unit test?** Baca `../../global-guidelines/testing.md`.
*   **Akan membuat/mengelola dependensi?** Baca `../../global-guidelines/dependencies.md`.
*   **Akan merancang UI, mengelola aset, atau menambahkan bahasa?** Baca `../../global-guidelines/ui-and-assets.md` dan `../../global-guidelines/localization.md`.
*   **Akan membuat sketsa prototipe tampilan baru?** Baca `prototypes/README.md`.
*   **Akan mengambil, membaca, atau membuat tiket tugas?** Baca `tickets/README.md`.
*   **Terjebak error yang sama berkali-kali?** Baca `retrospectives/RETROSPECTIVE.md` untuk melihat apakah AI sebelumnya pernah memecahkan masalah ini di node ini.
*   **Ditugaskan membuat tiket Bug?** Baca `../../global-docs/templates/bug_report_template.md`.
*   **Ditugaskan membuat deskripsi PR/Commit?** Baca `../../global-docs/templates/pull_request_template.md` & `../../global-docs/templates/commit_message_template.md`.

## 4. Aturan Mode Operasional
Ekosistem ini beroperasi dalam salah satu dari dua mode. Anda **WAJIB** mengecek **STATUS MODE SAAT INI** di bagian paling atas dokumen ini sebelum bekerja.

- **MODE 1 (Autonomous Planning):** Digerakkan oleh *roadmap*. Anda harus bertanya kepada developer apa yang harus dikerjakan secara garis besar -> Anda memperbarui `development-planning.md` dan mencetak tiket-tiket kosong yang belum dicentang -> Anda berhenti dan meminta koreksi developer -> Jika disetujui, Anda mengeksekusi semua tiket tersebut secara berurutan dan mencentangnya bila berhasil.
- **MODE 2 (Prompt-Driven):** Digerakkan oleh instruksi per-langkah dari developer. Developer memberi *prompt* instruksi -> Anda mengeksekusi kode -> Setelah selesai, Anda secara otomatis dan retrospektif membuat tiket baru untuk instruksi tersebut dan langsung mencentangnya sendiri.

---

**STANDARD OPERATING PROCEDURE (SOP) EKSEKUSI AI:**
Anda **DIWAJIBKAN SECARA MUTLAK** untuk mematuhi alur kerja berikut tanpa terkecuali setiap kali menerima *Execution Prompt* atau penugasan:

1. **Membaca Changelog:** Anda **WAJIB SELALU** membaca `CHANGELOG.md` terlebih dahulu untuk memahami konteks dan progres terakhir sebelum melakukan eksekusi apa pun.
2. **Berhenti Berinisiatif Liar (Stop & Ask):** Anda **DILARANG KERAS** mengambil inisiatif liar atau menebak-nebak jika ada informasi yang kurang atau kebingungan dalam mengambil keputusan. Anda **WAJIB BERHENTI** dan bertanya kepada pengguna melalui *prompt*. Inisiatif yang salah dan merugikan sangat dikutuk!
3. **Pembuatan Tiket Baru (Tergantung Mode):** Jika Anda berada di **MODE 1**, tiket dibuat di awal sebelum eksekusi berdasarkan `development-planning.md`. Jika Anda berada di **MODE 2**, tiket dibuat di akhir eksekusi sebagai rekam jejak (*retrospective*). Anda **WAJIB MUTLAK** menyalin utuh struktur `Boilerplate (Templat)` dari `tickets/README.md`. DILARANG mengarang format *markdown* sendiri.
4. **Pengerjaan & Pengujian Kode:** Selesaikan instruksi pengguna secara tuntas berdasarkan tiket (MODE 1) atau berdasarkan *prompt* (MODE 2), lalu Anda **WAJIB LANGSUNG** melakukan *testing* (pengujian) untuk memastikan fungsionalitas berjalan normal atau tidak ada *error*.
5. **Penyelesaian Tiket (Wajib Checklist) & Sinkronisasi:** Setelah selesai mengerjakan tugas, Anda **WAJIB LANGSUNG** memperbarui file tiket tersebut, mengubah *status* menjadi `Done`, dan **MENCENTANG** semua kotak `[ ]` menjadi `[x]` pada bagian `Acceptance Criteria`, serta mengisi `AI Execution Log & Output`. Selain itu, Anda **WAJIB** men-sinkronkan *tickets* di folder tiket lokal node terkait dengan GitHub Projects milik pengguna.
6. **Pencatatan Changelog (Wajib Descending & Menyeluruh):** 
   - Anda **WAJIB MUTLAK** mencatat riwayat pembaruan ke dalam file `CHANGELOG.md` lokal node.
   - Catatan baru **WAJIB** disisipkan di baris **PALING ATAS** daftar (*descending* / *reverse-chronological*). AI **DILARANG KERAS** menaruhnya di urutan terbawah!
   - Anda **WAJIB** mencatat **SEMUA** jenis kategori perubahan, **BUKAN HANYA** modifikasi kode (`Implementation`). Pembaruan pedoman (`guidelines`), teks `README.md`, pembuatan file tiket, dan modifikasi arsitektur semuanya wajib dicatat!
7. **Kewajiban Auto-Commit Berbasis Tiket:** Anda **WAJIB MUTLAK** melakukan *auto-commit* ke Version Control System (Git) setiap kali selesai mengeksekusi instruksi dan mencatat Changelog. Setiap *auto-commit* **WAJIB** merujuk pada nama/ID tiket. Pada **MODE 1**, referensikan tiket yang sedang dikerjakan. Pada **MODE 2**, referensikan tiket baru yang baru saja Anda buat secara retrospektif di langkah sebelumnya.
8. **Sinkronisasi Konteks (Kondisional):** JIKA di dalam root repositori ini terdapat direktori tersembunyi `.graphify`, maka setelah tugas selesai dan di-commit, kamu WAJIB menjalankan perintah `graphify update` di terminal. Jika folder tersebut tidak ada, maka abaikan langkah ini sepenuhnya.