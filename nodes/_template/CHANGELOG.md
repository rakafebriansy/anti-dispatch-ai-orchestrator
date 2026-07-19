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

Setiap penambahan log versi terbaru **WAJIB MUTLAK** diletakkan di baris **PALING ATAS** daftar, tepat di bawah judul "Log Perubahan" (urutan *descending* / *reverse-chronological*). AI **DILARANG KERAS** menambahkan log baru di baris terbawah. Anda **WAJIB** mencatat SEMUA kategori perubahan secara disiplin, bukan hanya modifikasi kode (`Implementation`). Demi kenyamanan membaca (*readability*), hindari penggunaan format tabel. Gunakan format daftar berstruktur (*structured list*) di bawah ini (Anda juga dapat melihat templatnya di `../../global-docs/templates/changelog_entry_template.md`):

### [YYYY-MM-DD HH:MM] - [Kategori Utama]
> **Branch:** `branch-name` | **Repo:** `https://github.com/...`
- **Instruksi User:** "[Kutipan asli atau intisari perintah dari pengguna yang memicu aksi ini]"
- **Perubahan:** [Detail penjelasan spesifik. Khusus kategori Implementation wajib sisipkan tag seperti `[Added]` di awal teks]
- **Path File:** `path/ke/file.ext`

> **Catatan Penting Konteks Instruksi:**
> Jika pesan pengguna yang memicu aksi ini hanyalah pesan penerus seperti *"Continue"* atau *"Lanjutkan"* (misalnya karena terputus gangguan jaringan atau batas limit token), AI Agent **DILARANG** mencatat kata "Continue" mentah-mentah. AI Agent wajib menganalisis riwayat *chat* dan menyisipkan intisari instruksi asli yang sebenarnya sedang dieksekusi.

**Contoh Penulisan:**

### [2026-06-16 00:35] - Implementation
> **Branch:** `feature/auth` | **Repo:** `https://github.com/org/repo`
- **Instruksi User:** "Tolong buatkan fitur login lengkap dengan validasi email pada form"
- **Perubahan:** `[Added]` Membuat fitur login baru dan penambahan validasi email pada form.
- **Path File:** `app/lib/auth/login.dart`

### [2026-06-16 01:00] - Guideline: Coding Standard
> **Branch:** `main` | **Repo:** `https://github.com/org/repo`
- **Instruksi User:** "Tulis aturan bahwa semua variabel dart wajib memakai camelCase"
- **Perubahan:** Menambahkan aturan penulisan variabel camelCase untuk bahasa spesifik.
- **Path File:** `../../global-guidelines/coding.md`

## Log Perubahan (_Judul Proyek_)

*(⚠️ PERHATIAN AI AGENT: TAMBAHKAN ENTRI LOG BARU ANDA TEPAT DI BAWAH BARIS INI. JANGAN DI PALING BAWAH DOKUMEN!)*

### [2026-07-19 22:43] - Guideline: Master Entrypoint & SOP
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "masukkan aturan ke @[/Users/rakafebriansyahputra/Developer/repositories/boilerplates/ai-orchestrator-template/nodes/_template/main.md] dan refine lah main md buatlah lebih terstruktur, dan refine lagi terkait hierarki supaya tidak tumpang tindih. berlakukan aturan ke seluruh file di orchestrator template"
- **Perubahan:** Restrukturisasi total `main.md` menjadi hierarki A-E yang lebih jelas, menambahkan Protokol Pemulihan Konteks (Bagian B), memindahkan aturan Git & Inisiatif Liar ke file pedoman global (`version-control.md` & `error-handling.md`), serta menambahkan keterangan SSoT pada `global-guidelines/README.md`.
- **Path File:** `nodes/_template/main.md`, `global-guidelines/error-handling.md`, `global-guidelines/version-control.md`, `global-guidelines/README.md`

### [2026-07-02 16:58] - Guideline: Main SOP
> **Branch:** `main` | **Repo:** `ai-orchestrator-template`
- **Instruksi User:** "harus tetap auto commit dan setiap auto commit harus berdasarkan ticket, jika instruksi tak memiliki ticket maka harus dibuatkan ticket! tambahkan di @[nodes/_template/main.md]"
- **Perubahan:** Menambahkan poin ke-7 pada SOP di `main.md` yang mewajibkan auto-commit berbasis tiket. Jika tidak ada tiket, AI wajib membuatkannya terlebih dahulu.
- **Path File:** `nodes/_template/main.md`

