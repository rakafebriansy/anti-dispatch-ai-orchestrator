# Security & Environment Constraints

Standar keamanan mutlak ini diperuntukkan guna mencegah kecerobohan kebocoran privasi (*credentials leakage*) atau penyalahgunaan penulisan kode oleh AI Agent.

## 1. Zero Hardcoded Secrets
Anda **DILARANG SECARA MUTLAK** untuk menyisipkan kunci kriptografi, kredensial peladen (*server credentials*), sandi koneksi pangkalan data (*database passwords*), atau *API Key* pihak ketiga (seperti OpenAI, Firebase, AWS, dll.) secara literal (sebagai teks tertanam) ke dalam struktur berkas kode sumber.
Seluruh variabel tersebut **WAJIB** digiring pemanggilannya menggunakan mekanisme *Environment Variables* (misalnya dengan skema rujukan `process.env.API_KEY` atau `Config.get('DATABASE_URL')`).

## 2. Kewajiban Pengabaian Git (Strict .gitignore)
Sebelum sistem orkestrasi Anda menginjeksikan atau menginisialisasi skema pengumpulan kredensial *Environment Variables* lokal (seperti penciptaan file `.env`), tugas pertama dan paling esensial Anda adalah menjamin bahwa rute penamaan file kredensial tersebut telah tercatat teguh di dalam daftar hirarki eksklusi kontrol versi `.gitignore`. Tidak ada toleransi bagi kebocoran kunci rahasia (*secret leak*) menuju repositori Git.

## 3. Kewajiban Sanitasi Input Dasar (Input Validation)
Saat membangun fitur antarmuka pemrograman terbuka (*public-facing API*) maupun interaksi *form inputs* dari sisi antarmuka klien pengguna:
- AI **DILARANG** mempercayai struktur *payload* mentah dari sisi klien secara naif.
- Anda wajib mensisipkan skema penyaringan (*sanitization*), filter regulasi regex, atau memanfaatkan perpustakaan *Data Validation Schema* baku untuk mencegat upaya penyusupan manipulatif seperti SQL Injection, manipulasi struktur NoSQL, hingga pancingan XSS (Cross-Site Scripting).
