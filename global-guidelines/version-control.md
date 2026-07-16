# Mandatory Changelog & Version Control Policy

## Wajib Mencatat Setiap Perubahan (Semua Jenis File)
Setiap kali Anda memproduksi baris kode baru, merevisi *bug*, atau melakukan pengaturan konfigurasi apa pun atas instruksi pengguna di suatu *node*, Anda **WAJIB** mendokumentasikan catatan perubahan tersebut secara rinci ke dalam berkas `CHANGELOG.md` milik *node* yang bersangkutan. **Pencatatan ini TIDAK HANYA berlaku untuk perubahan kode (Implementation), melainkan MUTLAK untuk segala bentuk perubahan: pembuatan/pembaruan file `README.md`, dokumen pedoman (`guidelines/`), dokumen desain sistem, pembuatan/pembaruan tiket, dan lain-lain. Semua harus tercatat!** Tidak boleh ada satu pun perubahan yang terlewat dari pencatatan.

## Penambahan Secara Reverse-Chronological (Wajib Descending)
Setiap penambahan log riwayat versi terbaru **WAJIB** diletakkan di bagian **PALING ATAS** dari daftar pembaruan (urutan *descending*, tepat di bawah *header* utama dokumen *changelog*). AI **DILARANG KERAS** menambahkan log di baris terbawah. Dengan demikian, versi terbaru selalu menduduki urutan pertama dan riwayat versi yang lebih lama perlahan terdorong ke bawah.

## Kewajiban Informasi Branch & VCS
Setiap entri pembaruan yang dicatatkan wajib memuat informasi mengenai nama *branch Git* yang sedang digarap, beserta tautan (*link*) menuju repositori *Version Control System* (VCS) yang bersangkutan.

## Aturan Penamaan Branch
1. **Pada Direktori Project/Node:** Anda **DILARANG KERAS** menggunakan format penamaan *branch* dengan ID tiket kapital seperti `feat(TICKET-XX)`. Penamaan *branch* untuk pengembangan kode aplikasi di dalam *node* diharuskan menggunakan deskripsi fitur secara langsung, contoh: `feat(feature-name)`.
2. **Pada Repositori Orchestrator:** Khusus untuk repositori ekosistem Orchestrator (template yang sudah di-*startup*), penamaan *branch* **WAJIB MUTLAK** menggunakan ID tiket dengan huruf kecil, contoh: `feat(ticket-xx)` atau gabungan tiket jika ada banyak tugas seperti `feat(ticket-xx,ticket-yy)`.

## Prosedur Konfirmasi Pembuatan Branch (Branch Switching)
Setiap kali Anda menerima tugas/instruksi eksekusi baru, Anda **WAJIB** mengevaluasi apakah tugas tersebut berada pada domain fitur yang berbeda atau memiliki konteks yang berbeda (misalnya: *hotfix*, penulisan *testing*, *refactor*, dsb.) dari *branch* yang saat ini sedang aktif.
Jika konteks atau domain fiturnya dirasa berbeda, Anda **WAJIB** secara otomatis menanyakan dan meminta persetujuan pengguna untuk membuat serta berpindah ke *branch* baru (contoh: `git checkout -b type/nama-branch`) SEBELUM Anda mulai mengeksekusi modifikasi kode apa pun.

## Kewajiban Commit & Mekanisme Rollback
1. **Wajib Commit Setiap Selesai Pencatatan Changelog:** Setiap kali Anda selesai mengeksekusi sebuah tiket/tugas dan telah mencatat riwayat pembaruan ke dalam file `CHANGELOG.md`, Anda **WAJIB MUTLAK** langsung melakukan *git commit*. Jangan pernah menumpuk banyak perubahan dari berbagai tugas ke dalam satu *commit* besar. Hal ini bertujuan agar setiap tiket atau tugas terisolasi secara aman dan rapi dalam satu *commit history* yang bersih.
2. **Mekanisme Rollback (Pembatalan Perubahan):** Berkat kewajiban *commit* per-tugas di atas, jika di kemudian hari terjadi *error* fatal, fitur merusak sistem, atau pengguna meminta Anda membatalkan *task* terakhir, Anda **WAJIB** menerapkan mekanisme *rollback* Git (kembali ke versi stabil sebelumnya). Gunakan perintah seperti `git reset --hard HEAD~1` (atau nama *commit hash* terkait). Anda **WAJIB meminta konfirmasi persetujuan pengguna** terlebih dahulu sebelum mengeksekusi *rollback* destruktif semacam ini.

## Format Log Pembaruan di Respons
Setelah Anda menuntaskan sebuah tugas atau instruksi, Anda wajib menyertakan log pembaruan di bagian akhir respons Anda. Hindari penggunaan format tabel; gunakan format daftar berstruktur (*structured list*) yang rapi berikut ini guna memaksimalkan keterbacaan (*readability*) dan kemudahan pengarsipan tiket internal. **Pastikan juga Anda selalu mengutip intisari instruksi/perintah awal dari pengguna**:

### [YYYY-MM-DD HH:MM] - [Kategori Utama]
> **Branch:** `branch-name` | **Repo:** `https://github.com/...`
- **Instruksi User:** "[Teks atau intisari perintah yang diberikan oleh pengguna]"
- **Perubahan:** [Detail penjelasan perubahan spesifik. Khusus kategori Implementation sisipkan tag seperti `[Added]` di awal teks]
- **Path File:** `path/ke/file.ext`

> **Catatan Penting Konteks Instruksi:**
> Jika pesan pengguna hanyalah instruksi penyambung seperti *"Continue"* atau *"Lanjutkan"*, AI Agent **DILARANG** menelan mentah-mentah kata tersebut ke dalam kolom log. AI Agent wajib merujuk kembali ke percakapan sebelumnya dan mengekstrak perintah asli yang sedang diselesaikan agar konteks riwayat perubahan tetap utuh.

## Ticket-Driven Development Workflow
Infrastruktur proyek AI Orchestrator ini menganut sistem manajemen tugas *offline* terpusat berbasis tiket di direktori `tickets/` yang berada di dalam masing-masing *node*. Selaku AI Agent, Anda dituntut mematuhi protokol berikut selama fase pengerjaan kode:
1.  **Rujuk pada Tiket:** Jangan mengeksekusi logika secara membabi buta tanpa arah. Pertama-tama, Anda **WAJIB membaca panduan struktural tiket** pada file referensi **`nodes/_template/tickets/README.md`** (atau `README.md` lokal di *node* Anda). Jika diperintahkan **membuat tiket baru**, Anda **wajib menyalin mentah-mentah format Boilerplate** dari file tersebut. Setelah memahaminya, barulah buka file tiket spesifik yang relevan (contoh: `nodes/[nama-node]/tickets/TICKET-01-login.md`). Apabila tiket tersebut berisi laporan masalah, tiket **WAJIB** dikonstruksikan menggunakan standar `global-docs/templates/bug_report_template.md`.
2.  **Pengujian Kode (Testing):** Setelah Anda menyelesaikan perombakan logika/kode pada tiket, Anda **WAJIB LANGSUNG** melakukan uji coba fungsional (testing) untuk mendeteksi *error* kompilasi atau galat logika.
3.  **Pemutakhiran Status & Checklist:** Saat Anda mulai menggarap sebuah tugas, Anda berhak mengubah properti *frontmatter* `status: Todo` menjadi `status: In Progress` pada file tiket lokal. Jika seluruh kriteria pengerjaan dan pengujian telah sukses secara tuntas, Anda **WAJIB LANGSUNG** mengubahnya menjadi `status: Done` dan menandai secara mutlak (*mencentang*) seluruh *checkbox* (`- [x]`) di bagian *Acceptance Criteria* tiket tersebut.
4.  **Kewajiban Pengisian Log AI:** Anda diwajibkan menjabarkan secara rinci jejak teknis, modifikasi, dan pertimbangan arsitektural di bawah seksi `AI Execution Log & Output` pada dasar file tiket terkait agar transparansi keputusan terjamin.
5.  **Tautan Changelog:** Pastikan Anda menyertakan ID referensi tiket (misal: "Referensi: TICKET-01") pada detail penulisan log saat melaporkan kemajuan pembaruan di layar obrolan maupun di dalam arsip `CHANGELOG.md` lokal.

## Pull Request (PR) Submission Policy
Apabila siklus pengembangan Anda melibatkan pembaruan kode lintas cabang (misalnya transisi dari cabang `feature/` ke `main`), Anda (AI Agent) **DILARANG KERAS** membiarkan deskripsi *Pull Request* kosong atau diisi secara sembarangan. Anda dituntut untuk senantiasa mematuhi pedoman pengajuan PR berikut:
1.  **Wajib Menggunakan Templat Referensi:** Segera setelah Anda siap mengajukan permintaan penggabungan kode (*merge request*), Anda **WAJIB** menyalin secara utuh struktur *boilerplate* yang tersimpan di **`global-docs/templates/pull_request_template.md`** sebagai format dasar pengisian deskripsi PR.
2.  **Kewajiban Auto-Closing Tiket:** Di dalam tubuh deskripsi PR tersebut, Anda **DIWAJIBKAN SECARA MUTLAK** untuk menautkan kata kunci penutup tiket dengan sintaksis `Resolves [TICKET-ID]` (contoh: `Resolves TICKET-01`). Sintaksis sakral ini berfungsi ganda sebagai pemicu pemindahan kartu (*card movement trigger*) di papan visual GitHub Projects sehingga tetap tersinkronisasi 100% dengan status penyelesaian tiket di dalam kerangka kerja lokal kita.

## Standar Pesan Commit (Semantic Commits)
Setiap kali Anda menjejakkan perubahan ke dalam riwayat repositori (*git commit*), Anda **WAJIB** menyelaraskan penulisan pesannya (*commit message*) dengan standar baku yang tersimpan di dalam **`global-docs/templates/commit_message_template.md`**. Jangan pernah menulis pesan *commit* yang sembarangan, tidak deskriptif, atau mengabaikan format struktur label *Semantic Commit* (seperti `feat:`, `fix:`, `chore:`, dsb.).
