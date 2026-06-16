# Deployment & Release Standard (Universal)

Panduan ini mengatur standar perilisan dan *deployment* untuk seluruh spektrum jenis perangkat lunak. Selaku AI Agent, Anda wajib menyesuaikan daftar periksa (*checklist*) pengiriman (*delivery*) ini sesuai dengan platform target yang sedang dibangun.

## 1. Persiapan Pra-Rilis (Pre-Deployment Checklist)
Terlepas dari jenis aplikasinya (Web, Mobile, Game, atau API), fase ini wajib dilalui secara absolut:
1.  **Environment Audit:** Verifikasi bahwa seluruh URL pengembangan (*localhost*, *staging API*) telah dipindahkan atau dikonfigurasi menunjuk ke URL *Production*.
2.  **Kewajiban Uji Kelulusan (Testing Pass):** Anda (AI Agent) **DILARANG KERAS** mengeksekusi skrip *deployment* apabila *test suite* (kumpulan pengujian) aplikasi belum mencapai status hijau (*pass*). Wajib ikuti seluruh prasyarat yang tertera pada pedoman `testing.md` sebelum perilisan.
3.  **Secret Management:** Pastikan tidak ada kredensial, *API key*, atau kata sandi yang ter-*hardcode*. Seluruhnya harus merujuk pada *Environment Variables* lingkungan produksi.
4.  **Build Optimization:** Lakukan *build* dengan konfigurasi khusus rilis (*release/production flag*) untuk memastikan pengecilan ukuran (*minification*), *tree-shaking*, dan pembuangan *debug logs* berjalan aktif.
5.  **Version Bump:** Pastikan nomor versi di *package manager* (seperti `package.json`, `pubspec.yaml`, `build.gradle`) telah ditingkatkan secara terstruktur sesuai format *Semantic Versioning* (SemVer).

## 2. Standar Spesifik Berdasarkan Platform Target

### A. Web Application & API Server (Backend)
- **Containerization (Opsional namun Sangat Disarankan):** Bungkus aplikasi dalam *Docker container* untuk menjamin konsistensi di *environment* peladen (*server*). Pastikan konfigurasi `Dockerfile` menggunakan arsitektur *multi-stage build* untuk menekan ukuran (*size*) rilis final.
- **Reverse Proxy & SSL:** Pastikan aplikasi berjalan di balik *reverse proxy* standar industri (seperti Nginx/Caddy) dengan penyandian SSL terkonfigurasi.
- **Port Binding:** Pastikan aplikasi mengikat port yang disuplai oleh lingkungan peladen melalui variabel dinamis (misal: `process.env.PORT`), BUKAN port *hardcoded*.

### B. Mobile Application (iOS & Android)
- **Code Signing:** Pastikan *Keystore* (Android) dan *Provisioning Profile/Certificates* (iOS) yang divalidasi adalah sertifikat kelas distribusi (*Distribution Certificates*), bukan *Development*.
- **Permission Sanitization:** Lakukan penyisiran ulang terhadap pengaturan *manifest* (seperti `AndroidManifest.xml` atau `Info.plist`). Hapus perizinan akses (*permissions*) yang diaktifkan hanya demi kepentingan *debugging*.
- **App Bundling:** Hasilkan format distribusi termodern. Untuk platform Android, Anda wajib memprioritaskan format *Android App Bundle* (`.aab`) ketimbang `.apk` mentah.

### C. Game Development
- **Asset Stripping:** Pastikan mode pembangunan (*build mode*) terkonfigurasi untuk membuang aset-aset yang tidak direferensikan dalam siklus akhir layar permainan (*unused assets stripping*).
- **Architecture Slicing:** Apabila mengeksekusi pembangunan kompilasi (*build compile*), konfigurasikan arsitektur sistem (contoh: ARM64) agar presisi dengan arsitektur OS perangkat target sasaran.