# AI Orchestrator: Master Entrypoint

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

---

**STANDARD OPERATING PROCEDURE (SOP) EKSEKUSI AI:**
Anda **DIWAJIBKAN SECARA MUTLAK** untuk mematuhi alur kerja berikut tanpa terkecuali setiap kali menerima *Execution Prompt* atau penugasan:

1. **Pembuatan Tiket Baru (Jika Diinstruksikan):** Jika pengguna meminta Anda membuat tiket tugas baru, Anda **WAJIB MUTLAK** menyalin utuh struktur `Boilerplate (Templat)` dari `tickets/README.md`. DILARANG mengarang format *markdown* sendiri atau mengabaikan templat tersebut.
2. **Pengerjaan & Pengujian Kode:** Selesaikan instruksi pengguna secara tuntas, lalu Anda **WAJIB LANGSUNG** melakukan *testing* (pengujian) untuk memastikan fungsionalitas berjalan normal atau tidak ada *error*.
3. **Penyelesaian Tiket (Wajib Checklist):** Setelah selesai mengerjakan tugas yang merujuk pada suatu tiket (`TICKET-*.md`), Anda **WAJIB LANGSUNG** kembali ke file tiket tersebut, mengubah *status* menjadi `Done`, dan **MENCENTANG** semua kotak `[ ]` menjadi `[x]` pada bagian `Acceptance Criteria`, serta mengisi `AI Execution Log & Output`.
4. **Pencatatan Changelog (Wajib Descending & Menyeluruh):** 
   - Anda **WAJIB MUTLAK** mencatat riwayat pembaruan ke dalam file `CHANGELOG.md` lokal node.
   - Catatan baru **WAJIB** disisipkan di baris **PALING ATAS** daftar (*descending* / *reverse-chronological*). AI **DILARANG KERAS** menaruhnya di urutan terbawah!
   - Anda **WAJIB** mencatat **SEMUA** jenis kategori perubahan, **BUKAN HANYA** modifikasi kode (`Implementation`). Pembaruan pedoman (`guidelines`), teks `README.md`, pembuatan file tiket, dan modifikasi arsitektur semuanya wajib dicatat!