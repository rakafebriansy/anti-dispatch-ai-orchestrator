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

**INSTRUKSI LANJUTAN UNTUK AI:**
Jika Anda sudah menyerap informasi dari dokumen-dokumen *Wajib* di atas, silakan analisis instruksi spesifik pengguna (atau buka tiket yang ditugaskan di direktori `tickets/`) dan mulailah bekerja!

> 📝 **KEWAJIBAN PENCATATAN (LOGGING OBLIGATION):**
> Setiap kali Anda selesai mengeksekusi sebuah tiket atau instruksi tugas, Anda **WAJIB MUTLAK** mencatat ringkasan perubahan (*changelog*) yang Anda lakukan ke dalam file `CHANGELOG.md` di node ini sebelum mengakhiri sesi. Jangan sampai lupa!