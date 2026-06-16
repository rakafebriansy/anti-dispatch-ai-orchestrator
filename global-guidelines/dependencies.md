# Dependency & Package Management Policy

Dokumen ini mengatur cara pandang AI Agent saat berinteraksi dengan perpustakaan pihak ketiga (*third-party libraries/dependencies*) agar ukuran aplikasi tidak membengkak (*bloat*) tanpa arah jelas.

## 1. Larangan Penambahan Library Tanpa Eksekusi Validasi Justifikasi
Anda **DILARANG** menginisialisasi proses instalasi atau menginjeksikan perpustakaan dependensi eksternal mana pun secara otonom tanpa justifikasi yang kokoh. 
Apabila Anda merasa bahwa solusi dari sebuah fitur mensyaratkan instalasi paket baru (seperti instalasi melalui mekanisme *NPM*, *Composer*, *Pub*, *Pip*):
- Anda **WAJIB** menyampaikan argumentasi mengapa penambahan paket ini jauh lebih menguntungkan ketimbang menulis algoritma mandiri (*native logic*).
- Prioritaskan pemakaian paket utilitas standar yang telah menjadi konvensi global *(well-maintained & widely-used)* daripada memilih paket *niche* yang tidak dikenal.
- Jika pengguna ragu, mintalah izin terlebih dahulu!

## 2. Integritas Lockfile
Sistem pengelolaan versi absolut (*Lockfile*) dari setiap *package manager* (contoh: berkas hierarkis `package-lock.json`, `pubspec.lock`, `Gemfile.lock`, atau `yarn.lock`) adalah **dokumen yang pantang dirusak**.
Anda (AI Agent) dilarang merekomendasikan pengguna atau mengambil kendali paksa untuk menghapus berkas-berkas pengunci ini sekadar sebagai jalan pintas darurat (*quick hack*) saat menghadapi konflik ketidakcocokan dependensi instalasi (*resolution conflict*). Perbaiki di akar berkas inisialisasi utamanya (contoh: `package.json`), bukan dengan melenyapkan sistem sejarah kunci *lockfile*-nya.

## 3. Kesesuaian Platform
Sebelum menyertakan dan menginstal suatu paket antarmuka sistem:
Anda wajib memverifikasi dukungan kompatibilitas silang (*cross-platform compatibility*) dari paket tersebut, serta menyesuaikannya secara spesifik dengan dokumen pedoman arsitektur lokal yang digariskan di dalam `nodes/[nama-node]/docs/system-design.md`. (Sebagai ilustrasi: Jangan memasang paket khusus utilitas DOM Web pada proyek sasaran berspesifikasi *Flutter Desktop/Mobile* murni tanpa kompatibilitas *Web*).
