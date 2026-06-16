# Product Requirements Document (PRD)

## Apa itu PRD
Product Requirements Document (PRD) adalah dokumen komprehensif yang menjadi panduan mutlak bagi **AI Agent** dalam membangun aplikasi atau proyek perangkat lunak ini. Dokumen ini menjelaskan secara rinci fungsionalitas, fitur, tujuan, dan batasan dari aplikasi yang akan dikembangkan.

Dalam pendekatan *Vibe Coding* dimana penulisan kode dan pengembangan dilakukan secara otonom atau semi-otonom oleh AI, PRD ini berfungsi sebagai instruksi utama (*master prompt*) dan sumber kebenaran tunggal (*single source of truth*). Dokumen ini mendefinisikan dengan jelas **apa** aplikasi yang harus dibangun oleh AI, **bagaimana perilaku yang diharapkan** dari aplikasi tersebut, serta **apa saja kriteria kesuksesannya** (*acceptance criteria*). Melalui dokumen ini, AI Agent dapat memahami *big picture* dan spesifikasi produk secara menyeluruh sebelum menyusun rancangan teknis (*System Design*) maupun mengeksekusi penulisan kode.

Secara rinci, sebuah dokumen PRD wajib memuat komponen-komponen berikut:
*   **Tujuan & Latar Belakang (*Objective & Background*):** Penjelasan mengenai masalah utama yang ingin dipecahkan, deskripsi target pengguna (*user personas*), dan alasan logis mengapa produk/fitur ini penting untuk dikembangkan.
*   **Alur Pengguna (*User Flow / User Journey*):** Narasi atau urutan langkah-langkah *end-to-end* yang menggambarkan cara pengguna berinteraksi dengan ekosistem (contoh: alur dari registrasi hingga menyelesaikan sebuah transaksi).
    *   **Kewajiban Visualisasi Mermaid:** Anda **WAJIB** menyertakan visualisasi alur pengalaman pengguna ini menggunakan sintaks `journey` atau `flowchart` dari MermaidJS.
*   **Interaksi Sistem Global (Use Case & Activity Diagram):** Khusus jika sistem ini memuat aplikasi bertipe *Web*, *Mobile*, atau *Desktop*, Anda **WAJIB** menyertakan *Use Case Diagram* atau *Activity Diagram* berbasis MermaidJS (`flowchart` atau `stateDiagram`) yang menggambarkan operasi aktor lintas sistem. Jika aplikasi ini berupa *Game*, Anda **WAJIB** membuat *Macro State Diagram* (misal: *Menu State*, *Play State*) menggunakan `stateDiagram-v2` dari MermaidJS.
*   **Kebutuhan Fungsional (*Functional Requirements*):** Daftar rinci berisi aksi-aksi dan fungsionalitas sistem yang wajib ada (contoh: "sistem harus dapat mengirimkan kode OTP via email", "pengguna dapat mem-filter data berdasarkan tanggal").
*   **Kebutuhan Non-Fungsional (*Non-Functional Requirements*):** Ekspektasi yang mengatur performa, keamanan, stabilitas, waktu respons sistem, hingga *support* *platform* (misalnya: *loading* halaman maksimal 2 detik).
*   **Kriteria Penerimaan (*Acceptance Criteria*):** Syarat dan batasan mutlak (termasuk skenario *edge cases*) yang harus terpenuhi agar sebuah fitur divalidasi dan dianggap "selesai" dikerjakan oleh AI Agent.
*   **Asumsi & Keterbatasan (*Assumptions & Constraints*):** Prediksi kondisi yang mendasari pengembangan (misal: "API pihak ketiga selalu tersedia") dan batasan sistem/bisnis yang mengikat jalannya proyek.
*   **Di Luar Cakupan (*Out of Scope*):** Daftar eksplisit mengenai fungsi atau fitur yang **tidak** akan dikerjakan pada fase/iterasi saat ini untuk menjaga agar fokus eksekusi AI Agent tidak menyimpang.
*   **Peta Jalan Fase (Milestone/Phase Breakdown):** Pembagian target rilis fitur ke dalam beberapa fase berurut (contoh: v1.0 MVP, v1.1 Lanjutan) agar AI Agent memahami prioritas eksekusi tiket di masa depan.

## Cara Melakukan Generate PRD
Ceritakan konsep, ide dasar, dan keseluruhan alur aplikasi yang ingin dibangun secara garis besar. Setelah itu, instruksikan AI Agent untuk secara aktif menanyakan detail-detail apa saja yang masih dibutuhkan untuk memperjelas dan memperinci penjelasan Anda. Proses tanya-jawab ini dilakukan sampai AI memiliki konteks dan informasi yang cukup untuk menyusun draf PRD yang lengkap.

> **Kewajiban Alur (Pipeline Obligation):** Setelah dokumen PRD ini disahkan (selesai di-*generate*), AI Agent **WAJIB MUTLAK** diinstruksikan untuk melanjutkan proses penyusunan arsitektur teknis dengan merujuk pada dokumen `nodes/[nama-node]/docs/system-design.md` milik masing-masing *node*. Dilarang menulis kode sebelum System Design terbentuk!

## (_Judul Proyek_)

*(Isi dari Product Requirements Document spesifik untuk aplikasi ini akan ditambahkan di bawah batas ini)*
