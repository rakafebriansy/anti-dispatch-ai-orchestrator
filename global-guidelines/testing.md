# Testing, Sandboxing, & Pre-flight Standards

## Isolasi Pengujian & Sandboxing
Apabila Anda perlu menguji coba simulasi perilaku algoritma, atau menampilkan pratinjau (*preview*) mandiri dari sebuah modul komponen yang terisolasi:
1.  Buatlah lingkungan *sandbox* atau direktori pengujian coba-coba di tingkat akar proyek (misalnya: membuat direktori `preview/` atau `sandbox/`).
2.  Jangan pernah mencampurkan skrip simulasi pengujian atau kode coba-coba ini secara hierarkis ke dalam kerangka produksi (*production source code*) dari aplikasi utama.
3.  **Strict Gitignore Policy:** Direktori *sandbox* atau *preview* sementara tersebut **WAJIB** dideklarasikan ke dalam berkas `.gitignore`. Artefak kode eksperimental ini dilarang keras ikut terbawa ke dalam rekam jejak repositori *Version Control* (commit) maupun bocor ke lingkup rilis (*production environment*).

## Mandatory Pre-Flight Testing & Linting
Sebelum menyatakan sebuah modul telah selesai atau siap diserahkan kepada pengguna untuk ditinjau:
1.  Anda **WAJIB** memastikan bahwa proses pemeriksaan prasyarat kompilasi (*build checking*), eksekusi *linter*, dan validasi ketepatan referensi atau tipe (*type checker*) telah dieksekusi.
2.  **Verifikasi Log Terminal:** Pastikan dengan mutlak tidak ditemukan adanya galat (*compile errors*), perselisihan tipe (*type mismatches*), dependensi yang putus (*missing imports*), maupun peringatan krusial (*runtime warnings/errors*) pada instrumen *console* atau *log* log sistem.
3.  **Validasi Realitas Eksekusi:** Hindari sifat berasumsi bahwa kode akan otomatis berfungsi mulus sesaat setelah ditulis. Selalu yakinkan bahwa aliran instruksi komputasi berjalan selaras, presisi, dan sesuai dengan ekspektasi atau spesifikasi batas parameter awal yang ditugaskan.
4.  **Persetujuan Eksekusi Pengujian Manual Berdurasi Panjang:** Apabila Anda (sebagai AI Agent) berniat untuk melakukan metode pengujian manual yang spesifik, berat, dan memakan waktu panjang (seperti menjalankan *emulator* perangkat keras pada proyek *mobile*, atau menginisiasi otomatisasi peramban seperti *Chromium* pada proyek *web*), Anda **WAJIB meminta izin secara eksplisit terlebih dahulu** kepada pengguna sebelum melancarkan aksi tersebut. Hal ini mutlak diberlakukan guna mencegah pemborosan durasi dan kuota konsumsi *token* pengguna.

## Aturan Universal Pengujian (Universal Testing Policy)

Terlepas dari spesifikasi teknis platform (Web, API, Mobile), AI Agent wajib mematuhi seluruh doktrin pengujian di bawah ini:

### 1. Strategi Piramida Testing (Testing Pyramid)
- **Unit Test:** Ini adalah fondasi utama. Anda **WAJIB** membuat *unit test* terisolasi untuk **setiap fungsi logika bisnis (*business logic*) inti** yang Anda tulis. Dilarang meninggalkan fungsi inti tanpa pengujian.
- **Integration Test:** Uji interaksi antar-modul (contoh: *Controller* berinteraksi dengan *Service* dan *Database*).
- **End-to-End (E2E) Test:** Simulasi alur penggunaan aplikasi secara menyeluruh layaknya pengguna asli.

### 2. Standar Penamaan & Organisasi File
- Anda wajib mengikuti konvensi penamaan *file test* yang umum digunakan oleh *framework* target (misal: `*.test.ts`, `*_test.dart`, `test_*.py`).
- File pengujian tidak boleh berserakan. Tempatkan secara terpusat di dalam direktori spesifik (misal: `tests/` atau `__tests__/`) atau berdampingan persis (*co-located*) dengan *file source code* aslinya jika pola kerangka kerjanya mewajibkan hal tersebut.

### 3. Pencegahan Regresi (Regression Prevention)
Setiap kali Anda ditugaskan untuk memperbaiki *bug*, selain memperbaiki *source code*, Anda **DIWAJIBKAN MUTLAK** untuk menulis minimal 1 (satu) *unit test* baru yang secara spesifik mensimulasikan kondisi terjadinya *bug* tersebut. Ini adalah bukti matematis bahwa *bug* tersebut tidak akan bisa lolos dan muncul kembali di kemudian hari.

### 4. Isolasi Data Pengujian (Data Independence)
Skrip pengujian yang Anda rancang **DILARANG KERAS** memanggil atau bergantung pada koneksi *database* produksi (*production database*) atau *database staging* jarak jauh. Anda wajib menggunakan fitur pemalsuan data (*Mocking*), kelas *Fixtures*, atau metode peniruan entitas (*Stubs*) untuk menjamin bahwa tes berjalan terisolasi, mandiri, cepat, dan *idempotent* (tidak menimbulkan efek samping).

### 5. Integrasi Pipeline Otomatis
Seluruh pengujian yang Anda buat harus didesain sedemikian rupa agar kompatibel untuk dijalankan secara senyap (*headless*) dan otomatis di dalam *pipeline* CI/CD. Anda tidak diperkenankan menyerahkan sebuah Pull Request (PR) jika *test suite* yang Anda bangun masih membuang kode galat (*error/fail*). 

### 6. Rujukan Silang saat Tes Gagal Berulang
Sesuai dengan pedoman di `error-handling.md`, jika eksekusi tes Anda selalu gagal atau buntu (*stuck*) walau sudah dicoba berulang kali (mencapai batas *Max Retry Rule*), Anda **WAJIB** mengeksekusi dua prosedur darurat:
1. Menghentikan eksperimen paksa dan segera melapor kepada pengguna.
2. Mencatat kebingungan dan jalan buntu teknis tersebut ke dalam file `nodes/[nama-node]/retrospectives/RETROSPECTIVE.md`.

### 7. Persyaratan Cakupan Pengujian 100% (100% Coverage Requirement)
Setiap penambahan atau modifikasi *source code* **WAJIB** memenuhi standar **100% *test coverage*** (meliputi *statements*, *branches*, *functions*, dan *lines*). Tidak boleh ada satupun baris kode atau cabang logika yang terlewat dari validasi pengujian. Jika coverage kurang dari 100%, kode tidak boleh dilanjutkan ke tahap berikutnya.

### 8. Perintah Eksekusi Pengujian Spesifik Teknologi (Tech-Specific Testing Commands)
Berikut adalah panduan perintah standar eksekusi pengujian beserta inspeksi *coverage* berdasarkan ekosistem teknologi yang digunakan. Saat diminta untuk melakukan tes, selalu sertakan parameter *coverage* untuk memvalidasi syarat 100% coverage:

- **Node.js (Jest / Vitest / TypeScript):**
  - Eksekusi Test: `npm run test` atau `npx jest` / `npx vitest`
  - Eksekusi Test dengan Coverage (100%): `npm run test:cov` atau `npx jest --coverage` / `npx vitest run --coverage`
- **Go (Golang):**
  - Eksekusi Test: `go test ./...`
  - Eksekusi Test dengan Coverage (100%): `go test ./... -coverprofile=coverage.out && go tool cover -func=coverage.out`
- **Python (Pytest):**
  - Eksekusi Test: `pytest`
  - Eksekusi Test dengan Coverage (100%): `pytest --cov=. --cov-report=term-missing`
- **Dart (Flutter):**
  - Eksekusi Test: `flutter test`
  - Eksekusi Test dengan Coverage (100%): `flutter test --coverage`
- **Rust:**
  - Eksekusi Test: `cargo test`
  - Eksekusi Test dengan Coverage (100%): `cargo tarpaulin --ignore-tests`
- **Java / Kotlin (Gradle / Maven dengan Jacoco):**
  - Eksekusi Test (Gradle): `./gradlew test jacocoTestReport`
  - Eksekusi Test (Maven): `mvn clean test jacoco:report`

### 9. Pembersihan Artefak Pengujian (Test Artifact Cleanup)
Anda **WAJIB** selalu memastikan bahwa setiap file hasil *build* atau file ter-generate (*generated files*) lainnya yang berasal dari sisa hasil pengujian (*testing*) segera dihapus apabila ada. Jangan biarkan file sementara dari pengujian ini mengotori repositori.
