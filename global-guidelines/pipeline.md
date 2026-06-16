# Continuous Integration & Delivery (CI/CD) Standard

Saat menyusun otomasi *pipeline* CI/CD (seperti GitHub Actions, GitLab CI, Bitbucket Pipelines, dsb.):
1.  Pastikan *pipeline* terpicu (*triggered*) secara otomatis setiap kali terdapat aksi `Push` atau pembuatan `Pull Request` ke *branch* repositori utama (misalnya: `main`, `master`, atau `develop`).
2.  Urutan pekerjaan (*jobs*) wajib mematuhi alur verifikasi logis: `Install Dependencies` -> `Check Formatting` -> `Linting / Static Analysis` -> `Build` -> `Run Tests`.
3.  Terapkan mekanisme penyimpanan sementara (*caching*) untuk lapisan dependensi (misal: *node_modules*, berkas *vendor*, *gradle cache*) guna memastikan *pipeline* berjalan secara optimal dan jauh lebih cepat pada eksekusi berikutnya.
