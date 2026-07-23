# Peta Perutean (Routing/Navigation Map)

*Dokumen ini diperuntukkan sebagai peta sentral pemetaan URL atau navigasi layar aplikasi. Digunakan untuk mendokumentasikan rute web (seperti React/Next.js router) maupun arsitektur routing API (seperti Express/Laravel router).*

## Base Configuration
- **Base URL API:** `https://api.domain.com/v1`
- **Base URL Web:** `https://app.domain.com/`

---

## Daftar Rute (Route Registry)

### Modul: [Nama Modul, misal: Authentication]

| Path / Route | Method | Controller/View/Handler | Middleware/Guards | Deskripsi |
| :--- | :---: | :--- | :--- | :--- |
| `/login` | `GET` | `LoginScreen` | `GuestGuard` | Menampilkan halaman form masuk |
| `/api/login` | `POST` | `AuthController.login` | `RateLimiter` | Endpoint validasi kredensial pengguna |
| `/dashboard` | `GET` | `DashboardView` | `AuthGuard` | Menampilkan dasbor (wajib login) |

### Modul: [Nama Modul, misal: User Management]

| Path / Route | Method | Controller/View/Handler | Middleware/Guards | Deskripsi |
| :--- | :---: | :--- | :--- | :--- |
| `/users` | `GET` | `UserController.index` | `AuthGuard, AdminRole` | Mendapatkan daftar seluruh pengguna |
| `/users/:id`| `GET` | `UserController.show` | `AuthGuard` | Mendapatkan detail satu pengguna spesifik |

<!-- Catatan Git: Saat melakukan commit/PR untuk dokumen ini, patuhi aturan istilah "orchestrator" (Dilarang di Project/Node, Wajib di Orchestrator). -->
