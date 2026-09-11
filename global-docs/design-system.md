# Design System

## Apa itu Design System
Design System adalah kumpulan terpusat yang berisi komponen visual, pedoman desain (seperti palet warna, tipografi, dan *spacing*), serta standar pola interaksi UI/UX yang dapat digunakan kembali (*reusable*). Dokumen ini bertujuan untuk memastikan konsistensi tampilan dan pengalaman pengguna di seluruh bagian aplikasi.

Dalam pendekatan *Vibe Coding* yang dikerjakan oleh AI Agent, Design System berfungsi sebagai pedoman gaya (*styling ground truth*). Saat AI meng-*generate* antarmuka (UI) atau komponen baru, AI akan merujuk secara ketat pada aturan-aturan di dokumen ini. Hal ini memastikan bahwa kode UI yang dihasilkan (misalnya kode Flutter atau CSS) akan secara otomatis selaras dengan identitas visual, *branding*, dan tema aplikasi, sehingga terhindar dari ketidakkonsistenan desain.

Secara rinci, sebuah dokumen Design System wajib memuat komponen-komponen berikut:
*   **Identitas Merek (*Brand Identity*):** Penjelasan mengenai filosofi desain, *vibe* atau nuansa aplikasi (contoh: *playful*, *minimalist*, korporat), serta *guideline* penggunaan aset merek.
*   **Palet Warna (*Color Palette*):** Daftar lengkap warna yang akan diaplikasikan, mencakup peran *Primary*, *Secondary*, *Background*, *Surface*, *Error*, *Success*, dan *Warning*. Wajib menyertakan kode warnanya (*Hex/RGB*) serta adaptasi untuk *Dark Mode* dan *Light Mode*.
*   **Tipografi (*Typography*):** Spesifikasi hierarki teks secara terstruktur, meliputi jenis huruf (*font family*), ukuran (*font sizes*), ketebalan (*font weights*), tingkat jarak huruf (*letter spacing*), dan tinggi baris (*line heights*) untuk elemen *Heading 1-6*, *Body*, *Subtitle*, *Caption*, dan teks pada tombol.
*   **Sistem *Spacing*, Tata Letak, & Breakpoints (*Spacing & Layout System*):** Skala kelipatan angka standar untuk penentuan jarak *margin*, *padding*, sistem *grid*, penentuan titik batas responsif (*responsive breakpoints* untuk *mobile*, *tablet*, *desktop*), serta spesifikasi sudut elemen (*border radius*).
*   **Aset & Ikonografi (*Iconography & Assets*):** Spesifikasi gaya visual ikon yang digunakan (seperti *outlined*, *filled*, atau *two-tone*), rasio ukuran standar ikon, dan acuan tata letak ilustrasi.
*   **Komponen UI Utama (*UI Components Library*):** Rincian dan spesifikasi desain untuk setiap elemen *reusable* (seperti *buttons*, *text fields*, *dropdowns*, *cards*, *modals*, *bottom sheets*, dll.) beserta semua kemungkinan statusnya (*default*, *hover*, *pressed*, *disabled*, *focus*, *error*).
*   **Animasi & Interaksi (*Micro-interactions/Animations*):** Standar perilaku gerakan antarmuka, mencakup durasi waktu (*duration*), kurva transisi (*easing curve*), dan *feedback* visual terhadap aksi dari pengguna.
*   **Standar Aksesibilitas (a11y):** Persyaratan minimum aksesibilitas yang harus ditaati (seperti *color contrast ratio*, *focus states* yang jelas, dan ketersediaan label pembaca layar/*screen reader*).

### Aturan Khusus Ekosistem Multi-Project (Multi-Node)
Jika ekosistem ini terdiri dari berbagai *platform* atau aplikasi yang memiliki interaksi (*Multi-Node*), strukturkan Design System ini menjadi dua tingkatan:
1. **Global Tokens:** Aturan fundamental (warna, tipografi utama, aset jenama/logo) yang wajib dipatuhi secara universal oleh seluruh *node* demi konsistensi *brand*.
2. **Node-Specific Components:** Bagilah daftar komponen UI spesifik ke dalam sub-bab masing-masing *node* (misalnya: sub-bab "Komponen Web-Admin" terpisah dari sub-bab "Komponen Mobile-App") guna mencegah kontaminasi rancangan dan kerancuan (*ambiguity*) ketika AI melakukan koding antarmuka di node tertentu.

## Cara Melakukan Generate Design System
Jelaskan "rasa" (*vibe*), tema, gaya visual (*modern*, minimalis, *playful*, dsb), serta preferensi estetika dari aplikasi Anda kepada AI Agent. Kemudian, mintalah AI Agent untuk mengajukan pertanyaan-pertanyaan spesifik terkait aset visual (seperti warna primer/sekunder, bentuk tombol, *dark/light mode*, tipografi) guna memperinci gambaran desain Anda. Berdasarkan interaksi tersebut, AI akan merumuskan dan menyusun pedoman komponen UI yang baku.

> **Kewajiban Uji Validasi HTML:** Desain yang dirumuskan di dokumen ini **TIDAK BOLEH** langsung dikoding ke dalam *framework* asli (Flutter, Next.js, SwiftUI, dll.). AI Agent **WAJIB** membuat sketsa purwarupanya terlebih dahulu dalam format HTML sederhana di direktori `nodes/[nama-node]/prototypes/` dari proyek yang bersangkutan.
## Anti Dispatch (macOS Dynamic Island & Workspace Orchestrator)

### 1. Identitas Merek & Filosofi Desain (Brand Identity)
* **Filosofi Desain:** Mengikuti pedoman **Apple Human Interface Guidelines (HIG)** untuk aplikasi utilitas desktop macOS modern. Panel berwujud panel melayang semi-transparan (*floating HUD / Dynamic Island*) yang terintegrasi secara mulus dengan kontur hardware MacBook Notch.
* **Karakter Visual:** *Sleek, Glanceable, Unobtrusive, Reactive, Precise*.
* **Aksen Utama:** *Electric Cyan Blue* yang memancarkan kesan AI-native, cepat, dan presisi tinggi.

---

### 2. Palet Warna (Color Palette)

Aplikasi mengutamakan *Dark-first Material Aesthetic* yang menyatu dengan latar belakang bezel layar Apple:

| Peran Warna | Hex / Nilai Warna (Dark Mode) | Hex / Nilai Warna (Light Mode) | Keterangan & Penggunaan |
|---|---|---|---|
| **Primary Accent (Cyan Blue)** | `#00D2FF` / `Color.cyan` | `#007AFF` / `Color.blue` | Aksen tombol aktif, indikator status `RUNNING`, dan glow status |
| **Success / Done (Emerald)** | `#30D158` / `Color.green` | `#34C759` | Indikator status agen selesai (`DONE`), konfirmasi commit |
| **Warning (Amber)** | `#FF9F0A` / `Color.orange` | `#FF9500` | Indikator polling lambat / retry koneksi |
| **Error / Alert (Coral Red)** | `#FF453A` / `Color.red` | `#FF3B30` | Indikator gagal koneksi CDP / port crash |
| **HUD Background** | `rgba(22, 22, 24, 0.82)` | `rgba(246, 246, 246, 0.88)` | Dilapisi efek `NSVisualEffectView` (`.ultraThinMaterial`) |
| **Surface Card / Row** | `rgba(255, 255, 255, 0.07)` | `rgba(0, 0, 0, 0.05)` | Background kartu sesi pada mode ekspansi accordion |
| **Border / Divider** | `rgba(255, 255, 255, 0.12)` | `rgba(0, 0, 0, 0.10)` | Garis batas tipis 0.5pt untuk kontur panel & kartu |
| **Text Primary** | `#FFFFFF` | `#1D1D1F` | Judul sesi, nama project, teks utama |
| **Text Secondary** | `rgba(255, 255, 255, 0.65)` | `rgba(0, 0, 0, 0.60)` | Sub-steps detail, file path, metadata durasi |
| **Text Tertiary / Muted** | `rgba(255, 255, 255, 0.40)` | `rgba(0, 0, 0, 0.35)` | Nomor port, placeholder, label non-aktif |

---

### 3. Tipografi (Typography)

Menggunakan font sistem native Apple: **SF Pro** untuk antarmuka umum dan **SF Mono** untuk file path dan log terminal.

| Elemen Teks | Font Family | Ukuran (pt) | Weight | Line Height | Tracking |
|---|---|---|---|---|---|
| **HUD Header / Title** | `SF Pro` | `13pt` | `Semibold` | `16pt` | `-0.01em` |
| **Project / Branch Label** | `SF Pro` | `12pt` | `Medium` | `15pt` | `0` |
| **Body / Status Headline** | `SF Pro` | `11.5pt` | `Regular` | `14pt` | `0` |
| **File Path / Tool Detail** | `SF Mono` | `10.5pt` | `Regular` | `13pt` | `0` |
| **Badge / Port Tag** | `SF Mono` | `9.5pt` | `Medium` | `11pt` | `+0.02em` |
| **Button Text** | `SF Pro` | `11pt` | `Semibold` | `13pt` | `0` |

---

### 4. Sistem Spacing, Sudut & Geometri (Spacing & Layout System)

* **Grid Standar:** Kelipatan 4pt (`4pt`, `8pt`, `12pt`, `16pt`, `20pt`, `24pt`).
* **Padding Kontainer:**
  * Collapsed Notch Pill: Horizontal `12pt`, Vertical `4pt`.
  * Expanded Notch Panel: Horizontal `16pt`, Vertical `14pt`.
  * Accordion Session Row: Horizontal `12pt`, Vertical `8pt`.
* **Border Radius:**
  * Notch Panel Atas: `0pt` (Menempel rata pada bezel fisik display).
  * Notch Panel Bawah: `16pt` (Melengkung presisi mengikuti kurva notch MacBook).
  * Floating Capsule Pill (Fallback Layar Non-Notch): `18pt` (Kapsul penuh).
  * Kartu Sesi & Form Modal: `10pt`.
  * Tombol Aksi: `6pt`.

---

### 5. Ikonografi & Aset Visual (Iconography)

* **Ikonografi Native:** Seluruh ikon menggunakan **SF Symbols 5+** dengan bobot `.medium` atau `.semibold`.
* **Daftar Simbol Utama:**
  * Status Running: `circle.fill` dengan efek cyan pulsing animation.
  * Status Done: `checkmark.circle.fill` (hijau emerald).
  * Status Standby: `circle.dashed` / `power`.
  * Open Project: `folder.badge.plus`.
  * Open Branch: `arrow.triangle.branch`.
  * Focus Window / Jump: `arrow.up.forward.app`.
  * Close Session: `xmark`.
  * Menu Bar Extra: `cpu.fill` / `point.topleft.filled.down.to.point.bottomright.curvepath`.

---

### 6. Spesifikasi Komponen UI Utama (UI Component Library)

#### A. `StandbyNotchView` (Tampilan Nol Sesi Aktif)
* **Karakter:** Lebar kompak (~300pt), tinggi mengikuti area notch (~32pt).
* **Konten:** Label *"Antigravity Standby"* di sisi kiri, 2 tombol aksi kompak (*"Open Project..."* dan *"New Branch..."*) dengan background `rgba(255,255,255,0.1)`.

#### B. `SingleSessionView` (1 Sesi Proyek Berjalan)
* **Collapsed Mode:** Kapsul mini (~240pt) menampilkan dot status berdenyut (Cyan) + `ProjectName: Thinking... (5s)`.
* **Expanded Mode:** Panel melebar (~420pt x 180pt) menampilkan nama proyek, status badge (`[RUNNING]`), daftar 3-5 file yang sedang dianalisis secara live, durasi berjalan, serta tombol navigasi cepat.

#### C. `MultiSessionAccordionView` (Multi-Worktree & Multi-Project)
* **Collapsed Mode:** Kapsul mini menampilkan ringkasan agregat (`● 2 Running  ● 1 Done`).
* **Expanded Mode:** List vertikal accordion. Setiap row proyek dapat diekspansi untuk melihat sub-steps, memiliki tombol lompat ke jendela IDE terkait (`Jump to Window`), dan tombol tutup sesi.

#### D. `BranchCreatorSheet` (Modal Pembuatan Worktree & Branch)
* **Form:** Sheet modal dengan pemilih repo aktif, input nama branch (`TextField`), base branch selector (`main`/`master`), indikator deteksi worktree eksis, dan tombol aksi *"Launch Branch Instance"*.

---

### 7. Animasi & Interaksi Mikro (Micro-interactions)

* **Animasi Ekspansi Notch:** Transisi dinamis menggunakan spring kurva native:
  ```swift
  .spring(response: 0.36, dampingFraction: 0.82, blendDuration: 0.2)
  ```
* **Pulsing Status Glow:** Efek bayangan berpendar lembut pada dot status cyan saat agen sedang menganalisis/menulis:
  ```swift
  .shadow(color: Color.cyan.opacity(isThinking ? 0.6 : 0.0), radius: 6, x: 0, y: 0)
  ```
* **Hover Interaction:** Kartu sesi dan tombol mengalami perubahan opasitas latar dari `0.07` ke `0.15` secara instan dengan durasi 120ms.

---

### 8. Kewajiban Prototipe HTML
Sesuai aturan orkestrator, purwarupa tampilan interaktif disimpan pada direktori `nodes/anti-dispatch/prototypes/` dalam format HTML/CSS modern sebelum diimplementasikan ke SwiftUI.

