# Internationalization (i18n) Standard

Saat merancang arsitektur terjemahan atau lokalisasi (i18n) untuk proyek perangkat lunak apa pun:
1.  Gunakan struktur berkas kamus/translasi (misalnya: JSON, YAML, atau XML) yang dipecah berdasarkan *namespace* atau area fitur aplikasi (contoh: `common.json`, `auth.json`, `settings.json`), dan dilarang menggabungkannya dalam satu berkas raksasa tunggal.
2.  Terapkan penamaan kunci bahasa (*translation keys*) menggunakan format *snake_case* atau *camelCase* secara konsisten di seluruh bagian proyek.
3.  Pastikan sistem lokalisasi yang diterapkan mendukung mekanisme penentuan bahasa cadangan (*fallback language*), yang pada praktiknya umumnya merujuk ke bahasa Inggris ('en') jika bahasa target tidak ditemukan.
