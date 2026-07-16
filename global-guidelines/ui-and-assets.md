# User Interface (UI) & Asset Integration Standards

*(Pedoman teknis ini berlaku krusial untuk fase pengerjaan atas entitas proyek apa pun yang mengelola subsistem antarmuka visual)*

## Kewajiban Penggunaan Prototipe (Pre-Coding)
Sebelum Anda (AI Agent) secara langsung menulis *source code* antarmuka (*UI*) menggunakan *framework* utama aplikasi (seperti Flutter, SwiftUI, Next.js, dsb.), Anda **DIWAJIBKAN** merancang sketsa draf visualnya di dalam direktori spesifik *node* yaitu `nodes/[nama-node]/prototypes/`. 
Anda **WAJIB MUTLAK** membaca dan mematuhi seluruh arsitektur *Rapid HTML-based Previewing* dan *Single-File Policy* yang tertulis di dalam file referensi **`nodes/[nama-node]/prototypes/README.md`** sebelum menyusun kode UI apa pun.
## Standar Konversi Aset Visual ke Kode Implementatif
Setiap kali mengeksekusi konversi dari objek aset visual mentah (contoh: templat *raw* SVG, berkas HTML orisinal, XML tata letak struktural) menjadi *codebase* berbasis *framework* fungsional:
1.  Patuhi secara saksama kaidah penamaan konvensi internal dari target *framework* tersebut. Misalnya, atribut kode yang semula berbasis sintaks *kebab-case* **wajib dikonversi** ke dalam format sintaksis *camelCase* apabila tata kelola bahasa memandatkannya.
2.  Gugurkan segala *value default* dari sumber asli visual yang sudah tidak relevan di lingkungan eksekusi, format ulang penulisan sintaksis pelapisan desain (*styling parameters*), serta tertibkan penutup *tag* agar sepenuhnya memenuhi prasyarat kompiler atau penata dokumen terkait.

## Prosedur Autorisasi Sumber Aset Lintas Domain
1.  Di saat lapisan visual dituntut untuk merender media statis pendukung (*placeholder image/stream resource*) langsung dari sumber infrastruktur (*domain*) pihak luar secara jarak jauh (*remote fetching*):
2.  Aturan tata batas mutlak mewajibkan Anda untuk meregistrasikan alamat domain eksternal secara sadar ke dalam konfigurasi daftar aman (*whitelist* / *safe origins setting*) kerangka kerja proyek, guna mencegah terblokirnya materi media akibat *policy restriction* seperti batasan CORS atau *invalid internal runtime exceptions*.

## Metrik Skalabilitas Satuan Antarmuka (Aksesibilitas Mutlak)
1.  **Tinggalkan Absolutisme Unit Piksel:** Sangat dihindari dalam spesifikasi pengembangan menggunakan pendefinisian jarak absolut dan kaku melalui piksel (`px`) untuk menetapkan hierarki ukuran area pandang (*margins/spacing*), parameter kerangka blok, maupun topografi *font* tulisan secara *hardcoded*.
2.  **Inisiatif Satuan Dimensi Responsif:** Sistem harus dibangun dengan berorientasi pada nilai porsi rasio proporsional—seperti `rem`, `em`, atau persentase basis (`%`). Regulasi ini esensial bukan sebatas aspek *layouting responsif*, melainkan agar aplikasi secara mendasar mampu bersanding mulus terhadap adaptasi pengaturan aksesibilitas skala *font default* secara sistemik di OS (*Operating System*) atau peramban dari sisi preferensi klien akhir. Pengecualian hanya dimaklumi bagi properti atribut kaku yang mensyaratkan tipe *pure integers* dan tidak menoleransi masukan string kalkulatif.

## Kepatuhan Aksesibilitas (Accessibility Compliance)
Jika *project/node* yang sedang dikerjakan merupakan aplikasi antarmuka pengguna seperti **Web (Frontend)** atau **Mobile App**, Anda **WAJIB** memastikan bahwa hasil pekerjaan memenuhi standar aksesibilitas (*accessibility* / a11y).
1. **Adaptasi Berdasarkan Teknologi:** Pendekatan aksesibilitas harus disesuaikan dengan jenis teknologi yang digunakan. Jika menggunakan teknologi *native*, manfaatkan kapabilitas dan API aksesibilitas bawaan dari *platform native* tersebut secara maksimal.
2. **Prosedur Implementasi & Konfirmasi:** Anda harus mendaftar dan memberikan seluruh opsi fitur aksesibilitas yang relevan dan dapat diimplementasikan sesuai dengan *stack* teknologi yang dipakai. Setelah memberikan daftar opsi tersebut, **wajib** tanyakan kepada *developer* (pengguna) mengenai fitur mana saja yang ingin diimplementasikan.
