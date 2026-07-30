# Templates Directory (`global-docs/templates/`)

Direktori ini menampung seluruh *boilerplate* (templat cetak biru) dokumen yang menjadi standar pengoperasian dalam proyek ini. Penggunaan template ini **bersifat mutlak** demi menjaga konsistensi format dan struktur komunikasi tim.

## Daftar Template Tersedia
1. **`pull_request_template.md`**
   Standar penulisan deskripsi *Pull Request* (PR) di *repository*. Memiliki struktur pelaporan perubahan teknis dan mewajibkan tautan auto-closing tiket (misal: `Resolves TICKET-01`).
2. **`commit_message_template.md`**
   Standar penulisan *Commit Message* berformat Semantic (misal: `feat:`, `fix:`, `docs:`). Memastikan riwayat Git tetap rapi dan mudah di-*parse*.
3. **`bug_report_template.md`**
   Standar *markdown* untuk melaporkan kegagalan sistem ke dalam file tiket (berada di direktori `nodes/[nama-node]/tickets/`). Memuat paksaan analisis *Root Cause* dan daftar *Affected Files*.
4. **`api_documentation_template.md`**
   Standar dokumentasi arsitektur komunikasi (khusus aplikasi *backend*/*API*), memuat aturan struktur *Request*, *Response*, dan contoh JSON *payload*.
5. **`routing_template.md`**
   Peta navigasi rute halaman aplikasi (Web) maupun *endpoint* peladen (*Server*). Digunakan untuk melacak pergerakan antarmuka secara terpusat.
6. **`changelog_entry_template.md`**
   Standar penulisan entri log riwayat perubahan yang digunakan di dalam file `CHANGELOG.md` pada setiap *node*. Memastikan format tanggal, referensi *branch*, instruksi pengguna, serta detail file yang diubah tercatat seragam secara *reverse-chronological*.

## Kewajiban Penggunaan bagi AI Agent
Sistem *AI Orchestrator* telah mengikat file-file *guideline* untuk selalu merujuk ke templat ini. Anda dilarang mengarang format dokumen baru yang melenceng dari struktur di atas saat ditugaskan untuk menuliskan salah satu dari keenam jenis dokumen tersebut.

## Aturan Penggunaan Istilah "Orchestrator" (Git Actions)
Dalam segala aksi Git (commit, branch, PR) yang mengacu ke pembuatan/pengubahan template ini, perhatikan posisi repositori Anda:
- **Di Project/Node:** **DILARANG KERAS** menggunakan kata/istilah `orchestrator` dalam aksi Git.
- **Di Repositori Orchestrator:** **WAJIB** menyertakan kata/istilah `orchestrator` dalam setiap aksi Git (misal: pesan commit, penamaan branch).
