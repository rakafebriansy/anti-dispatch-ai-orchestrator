# Development Planning (Roadmap)

## Apa itu Development Planning
*Development Planning* adalah dokumen peta jalan (*roadmap*) strategis yang menjembatani dokumen makro (seperti PRD dan System Design) dengan tugas-tugas mikro (berupa Tiket). Dokumen ini berfungsi untuk memecah keseluruhan ruang lingkup proyek ke dalam beberapa fase pengerjaan (*Milestones*) yang dapat dikelola secara bertahap.

Dalam pendekatan *Vibe Coding* dengan AI Agent, dokumen ini sangat krusial sebagai "Gudang Antrean Tiket" (*Ticket Backlog*). Daripada meminta pengguna membuat instruksi tiket secara manual satu per satu, AI Agent akan secara proaktif mendaftarkan seluruh kerangka tiket yang dibutuhkan di dalam dokumen ini. 

## Aturan Sinkronisasi Tiket
1.  **Dilarang Mengarang Tiket (Khusus MODE 1):** Pada Mode 1, AI Agent **DILARANG KERAS** mengarang, mencetuskan, atau membuat file tiket baru di direktori `tickets/` jika tiket tersebut belum tercatat sebagai target *backlog* di dalam file `development-planning.md` ini. Namun pada **MODE 2**, aturan ini dibatalkan dan AI justru diwajibkan membuat tiket retrospektif setelah menyelesaikan setiap *prompt* dari *developer*.
2.  **Transisi Status:** Jika sebuah tiket dari dokumen ini telah dipindahkan wujudnya menjadi file *markdown* di folder `tickets/`, AI wajib kembali ke dokumen ini dan menandai tiket tersebut sebagai `[CREATED]` atau mencoretnya.
3.  **Kebijakan UI Slicing & Prototyping:** Khusus untuk tiket yang berkaitan dengan perancangan antarmuka (*UI Slicing*), AI Agent **WAJIB** menanyakan terlebih dahulu kepada pengguna apakah desainnya sudah ada atau perlu di-*generate* via HTML melalui direktori `prototypes/`. Apabila diputuskan menggunakan mekanisme prototipe, maka tiket untuk perancangan prototipe **WAJIB dipisahkan** dari tiket implementasi ke dalam *codebase* proyek.

## Struktur Rencana Pengembangan

Setiap fase wajib diuraikan dengan mencantumkan gol utama (*objective*) dan daftar antrean tiket yang akan dibuat ke depannya.

### Fase 1: [Nama Fase, misal: Inisialisasi & Autentikasi]
- **Tujuan:** [Jelaskan target pencapaian di fase ini]
- **Daftar Tiket (Backlog):**
  - `[ ]` **TICKET-01:** [Judul/Deskripsi Singkat]
  - `[ ]` **TICKET-02:** [Judul/Deskripsi Singkat]

### Fase 2: [Nama Fase]
- **Tujuan:** [Jelaskan target pencapaian di fase ini]
- **Daftar Tiket (Backlog):**
  - `[ ]` **TICKET-03:** [Judul/Deskripsi Singkat]

---

## (_Judul Proyek_)

*(Peta jalan pengembangan dan rincian backlog tiket spesifik untuk proyek ini akan disusun oleh AI Agent di bawah batas ini)*
