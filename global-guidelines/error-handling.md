# Error Handling & Anti-Hallucination Policy

Dokumen ini adalah barikade utama untuk mencegah perilaku halusinasi, asumsi keliru, atau percobaan *debugging* secara "membabi buta" oleh AI Agent.

## Aturan "Stop-and-Ask" (Anti-Looping)
1. **Batas Percobaan Maksimal (Max Retry Rule):** Apabila Anda mencoba memperbaiki sebuah kegagalan (contoh: *build error*, peringatan linting berulang, kegagalan *test case*) dan gagal menyelesaikannya dalam **2 kali iterasi penyelesaian**, Anda **DIWAJIBKAN MUTLAK** untuk berhenti meraba-raba (*blind guessing*).
2. **Kewajiban Melapor:** Setelah menyentuh batas percobaan tersebut, Anda harus secara proaktif melaporkan kegagalan tersebut kepada pengguna. Paparkan *log error* terakhir secara utuh, sampaikan hipotesis mengapa hal ini sangat persisten, dan mintalah panduan lebih spesifik.
3. **Kewajiban Pencatatan Retrospektif:** Selain melapor kepada pengguna, Anda **WAJIB** mencatatkan detail kegagalan persisten ini ke dalam log pembelajaran mandiri di `nodes/[nama-node]/retrospectives/RETROSPECTIVE.md` (mengikuti pedoman di folder tersebut). Ini untuk memastikan jejak masalah terekam spesifik untuk *node* ini.

## Larangan Mengasumsikan Keberhasilan
1. **Wajib Memvalidasi Eksekusi:** Anda **DILARANG KERAS** melontarkan klaim "Kode sudah berhasil diperbaiki" atau "Seharusnya sudah berfungsi" secara teoretis tanpa eksekusi bukti konkret.
2. Jika Anda memodifikasi kode fungsional, Anda harus menjalankan validasi di lapis bawah secara mandiri (misalnya dengan menjalankan terminal linting, mengeksekusi kompilasi lokal, atau menjalankan skrip *test case* yang relevan). Hanya ketika indikator terminal mencetak status bersih (*pass/success*), barulah Anda boleh mengonfirmasi ke pengguna.

## Verifikasi Eksistensi File (No Blind Imports)
1. **Dilarang Menebak Path:** Sebelum Anda (AI Agent) merujuk (*import/require*), mengedit secara *inline*, atau membongkar struktur file apa pun yang belum pernah Anda sentuh di sesi ini, Anda **WAJIB** melakukan pengecekan awal.
2. Gunakan perintah pemindaian terminal seperti `ls`, pemanggilan fungsi pelacakan direktori, atau penelusuran *grep* untuk memvalidasi bahwa lokasi direktori atau struktur berkas (*file/folder*) yang akan Anda kerjakan benar-benar eksis secara fisik di sistem.
