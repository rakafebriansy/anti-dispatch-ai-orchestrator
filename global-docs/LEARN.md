# Knowledge Base & Implementation Q&A Repository (`LEARN.md`)

Dokumen ini adalah repositori terpusat (*Single Source of Truth*) untuk menyimpan seluruh tanya-jawab (*Question & Answer*) seputar implementasi teknis, keputusan arsitektural, dan logika *codebase* yang ditanyakan oleh pengguna (*User*).

Berbeda dengan log retrospektif error (`RETROSPECTIVE.md`), dokumen ini khusus mendokumentasikan pemahaman konseptual dan teknis yang diperoleh dari proses tanya-jawab eksplisit antara pengguna dan AI Agent.

---

## 🛑 Protokol Operasional AI Agent (Wajib Dipatuhi)

Setiap kali AI Agent menerima instruksi tanya-jawab implementasi (*Implementation Q&A Prompt*), agen **WAJIB MUTLAK** mematuhi 4 hukum operasional berikut:

### 1. Hanya Menjawab Pertanyaan Eksplisit (Strictly Explicit Q&A Only)
- AI Agent **DILARANG KERAS** membuat penjelasan, rangkuman, atau inisiatif analisis secara otomatis tanpa pertanyaan eksplisit yang diajukan oleh pengguna.
- Hanya jelaskan poin-poin pertanyaan yang secara nyata tertulis pada daftar pertanyaan pengguna (contoh: *1. Pertanyaan A, 2. Pertanyaan B*).

### 2. Berakar pada Codebase & Orchestrator (No Hallucinations / Full Grounding)
- Seluruh jawaban **WAJIB** berakar pada analisis kode sumber (*source code*) nyata, konfigurasi, dan dokumen orchestrator (`global-docs/`, `global-guidelines/`, `nodes/[nama-node]/`).
- Sertakan path file yang valid (dan nomor baris jika relevan) sebagai bukti konkret implementasi.
- Jelaskan **alasan/rasional (*why*)** di balik keputusan arsitektur, bukan hanya sekadar mendeskripsikan ulang baris kode (*what*).

### 3. Output Ganda (Dual-Output: Chat Sidebar + LEARN.md)
- AI Agent **WAJIB** mengetikkan jawaban lengkap, terstruktur, dan mudah dipahami langsung pada **chat sidebar** percakapan.
- Pada saat yang bersamaan, AI Agent **WAJIB** menyalin dan merekam sesi tanya-jawab tersebut ke bagian [Arsip Log Tanya-Jawab](#-arsip-log-tanya-jawab) di dalam file ini menggunakan [Format Entri Baku](#-format-entri-baku-boilerplate) serta memperbarui [Indeks Kategori](#-indeks-kategori--daftar-isi).

### 4. Disiplin Taksonomi & Kategorisasi
- AI Agent **WAJIB** mengklasifikasikan setiap entri Q&A ke dalam **Kategori Utama** dan menyematkan **Tags** yang relevan agar memudahkan pengguna dalam membaca dan mencari di kemudian hari.

---

## 🏷️ Taksonomi & Sistem Kategorisasi

Untuk menjaga keteraturan dan kemudahan pencarian (*searchability*), gunakan standar taksonomi berikut saat mencatat entri baru:

### 1. Format ID Entri
Gunakan format penomoran: `[QA-YYYYMMDD-XX]`
- `YYYYMMDD`: Tahun, Bulan, Tanggal pencatatan (contoh: `20260913`).
- `XX`: Nomor urut pada hari tersebut (contoh: `01`, `02`).

### 2. Daftar Kategori Utama (Pilih Salah Satu)
| Kategori Utama | Cakupan / Domain |
| :--- | :--- |
| **`Architecture & Pattern`** | Struktur folder, modularitas, alur sistem, design patterns, separation of concerns. |
| **`State Management & Data Flow`** | Alur data, global store (Zustand/Redux), server state (React Query), props/event flow. |
| **`Database & Data Modeling`** | Skema tabel/koleksi, ORM (Prisma/TypeORM/Mongoose), migrasi, indexing, query optimization. |
| **`API & Network Integration`** | REST endpoints, GraphQL, WebSocket, gRPC, format request/response, error handling network. |
| **`UI/UX & Design System`** | Komponen visual, Tailwind/CSS, konsistensi tema, responsivitas, aksesibilitas (a11y). |
| **`Security & Authentication`** | JWT, sesi, OAuth, RBAC/Permissions, hashing, sanitasi input, proteksi CORS/CSRF. |
| **`Testing & Quality Assurance`** | Strategi unit test, integration test, E2E, mock data, coverage, assertions. |
| **`Build, Tooling & DevOps`** | Konfigurasi bundler (Vite/Webpack), Docker, CI/CD, script npm, environment variables. |
| **`Business Logic & Domain Rules`** | Logika perhitungan, validasi transaksi, aturan proses bisnis spesifik aplikasi. |
| **`Orchestrator & Workflow`** | Aturan template, manajemen tiket, SOP guidelines, mekanisme multi-node. |

### 3. Konvensi Tagging
Gunakan format `#kebab-case` untuk tag spesifik. Contoh:
- `#jwt-auth` `#zustand` `#prisma-relations` `#optimistic-update` `#tailwind-v4` `#rbac-middleware`

---

## 📋 Format Entri Baku (Boilerplate)

AI Agent **WAJIB** menyalin struktur *markdown* berikut saat menambahkan rekaman tanya-jawab baru ke dalam file ini:

```markdown
### [QA-YYYYMMDD-XX] <Judul Singkat Representatif Terkait Topik Pertanyaan>
- **Tanggal**: YYYY-MM-DD HH:mm
- **Scope / Target Node**: `[Global / Nama Node / Path Codebase]`
- **Kategori**: `[Pilih salah satu dari Kategori Utama di atas]`
- **Tags**: `#tag1 #tag2 #tag3`
- **File Referensi**:
  - `path/to/relevant-file-1.ext` (L10-L45)
  - `path/to/relevant-file-2.ext`

#### ❓ Pertanyaan Pengguna
1. **[Tulis ulang pertanyaan 1 secara presisi]**
2. **[Tulis ulang pertanyaan 2 secara presisi]**

#### 💡 Jawaban & Penjelasan Implementasi

##### 1. [Judul Poin Jawaban 1]
- **Ringkasan Inti**: [Penjelasan singkat 1-2 kalimat]
- **Detail Implementasi & Logika**:
  [Penjelasan komprehensif alur kerja kode]
- **Rujukan Kode Sumber**:
  ```[language]
  // Cuplikan kode atau referensi fungsi/kelas yang relevan
  ```
- **Rasional & Keputusan Teknis**:
  [Mengapa pendekatan ini yang dipilih, pertimbangan trade-off, atau kesesuaian dengan pedoman]

##### 2. [Judul Poin Jawaban 2]
- **Ringkasan Inti**: [Penjelasan singkat 1-2 kalimat]
- **Detail Implementasi & Logika**:
  [Penjelasan komprehensif alur kerja kode]
- **Rujukan Kode Sumber**:
  ```[language]
  // Cuplikan kode atau referensi fungsi/kelas yang relevan
  ```
- **Rasional & Keputusan Teknis**:
  [Mengapa pendekatan ini yang dipilih, pertimbangan trade-off, atau kesesuaian dengan pedoman]

---
```

---

## 🗂️ Indeks Kategori & Daftar Isi

*AI Agent WAJIB memperbarui tautan indeks di bawah ini setiap kali menambahkan entri baru (urutkan dari yang terbaru / descending).*

- **Architecture & Pattern**
  - `[QA-20260912-01]` [GitWorktreeService, Subprocess CLI, dan POSIX Exit Codes](#qa-20260912-01-gitworktreeservice-subprocess-cli-dan-posix-exit-codes)
  - `[QA-20260911-02]` [PathHasher, Data Buffer, MD5 Mapping, dan Direktori Isolasi](#qa-20260911-02-pathhasher-data-buffer-md5-mapping-dan-direktori-isolasi)
- **State Management & Data Flow**
  - `[QA-20260918-01]` [Penyebab State Waiting Dianggap Done pada Modal Izin Antigravity ("Allow testing proxy endpoint...")](#qa-20260918-01-penyebab-state-waiting-dianggap-done-pada-modal-izin-antigravity-allow-testing-proxy-endpoint)
- **Database & Data Modeling**
  - *(Belum ada entri)*
- **API & Network Integration**
  - `[QA-20260913-01]` [LauncherService, POSIX Socket Probing, dan Memory Layout Pointers](#qa-20260913-01-launcherservice-posix-socket-probing-dan-memory-layout-pointers)
- **UI/UX & Design System**
  - *(Belum ada entri)*
- **Security & Authentication**
  - *(Belum ada entri)*
- **Testing & Quality Assurance**
  - *(Belum ada entri)*
- **Build, Tooling & DevOps**
  - `[QA-20260911-01]` [Overview Arsitektur, Swift Versioning, Xcode Hardened Runtime, dan Distribusi DMG](#qa-20260911-01-overview-arsitektur-swift-versioning-xcode-hardened-runtime-dan-distribusi-dmg)
- **Business Logic & Domain Rules**
  - *(Belum ada entri)*
- **Orchestrator & Workflow**
  - *(Belum ada entri)*

---

## 📚 Arsip Log Tanya-Jawab

### [QA-20260918-01] Penyebab State Waiting Dianggap Done pada Modal Izin Antigravity ("Allow testing proxy endpoint...")
- **Tanggal**: 2026-09-18 14:30
- **Scope / Target Node**: `nodes/anti-dispatch` (`Anti Dispatch/Resources/Scripts/extract_state.js`, `Anti Dispatch/Core/Services/CDPService.swift`)
- **Kategori**: `State Management & Data Flow`
- **Tags**: `#cdp-state-extraction #waiting-state #decision-prompt #extract-state #dom-heuristics #regex-matching #antigravity-permissions`
- **File Referensi**:
  - `Anti Dispatch/Resources/Scripts/extract_state.js` (L206-L333)
  - `Anti Dispatch/Core/Services/CDPService.swift` (L285-L415)
  - `Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift` (L252-L368)

#### ❓ Pertanyaan Pengguna
1. **Kenapa masih ada waiting dianggap done contohnya saat "Allow testing proxy endpoint on fashionforth.id?" di `/Users/raka/Developer/repositories/projects/fashionforth-org/ff-prod`? Caritahu penyebabnya!**
2. **Kenapa saat agen sedang Working... (contohnya saat menjalankan Goal / subagent: "Navigate to https://fashionforth.id and inspect the console logs...") malah dianggap WAITING (Decision Required)? Caritahu penyebabnya!**

#### 💡 Jawaban & Penjelasan Implementasi

##### 1. Masalah Kasus 1: State Waiting Dianggap DONE (Waiting -> Done Misclassification)
- **Akar Masalah 1.1 (Evaluasi Regex/Substring Tombol Keputusan):** Fungsi `isDecisionButton` (L206–L218) menggunakan matching kaku `txt.startsWith(k + ' ')` yang gagal ketika teks tombol memiliki tanda koma (misal: `"Yes, allow this time"`), variasi domain dinamis (`"Allow on fashionforth.id"`), atau kata kunci tindakan lain (`"Trust"`, `"Accept"`, `"Run"`).
- **Akar Masalah 1.2 (Scope Seleksi Modal Terlalu Sempit):** `activeModals` dan `permissionCards` (L223–L243) hanya memindai selector class tertentu, mengabaikan kartu izin proxy inline atau custom widget dengan class Tailwind generik (`border rounded-xl bg-card p-4`).
- **Akar Masalah 1.3 (Isolasi Scope ke targetTurn):** `targetTurn` hanya memeriksa bubble chat terakhir (`.scroll-mt-4`), mengabaikan widget izin yang berada di luar chat turn (seperti di docked footer atau portal container).
- **Akar Masalah 1.4 (Radio/Checkbox Semantik ARIA Tidak Terdeteksi):** Pilihan radio modern dirender sebagai `<button role="radio">` dan bukan native `<input type="radio">`, sehingga selector `input[type="radio"]` menghasilkan panjang `0`.
- **Akar Masalah 1.5 (State Fallthrough Prematur ke DONE):** Saat agen berhenti menunggu klik user, spinner mati (`hasSpinner = false`) dan tombol Stop hilang (`hasStopButton = false`). Karena `isWaitingForInput` gagal terdeteksi (`false`), percabangan langsung jatuh ke `else if (latestResponse || steps.some(...))` yang bernilai `true` karena riwayat chat asisten, sehingga status diset menjadi `"DONE"`.

##### 2. Masalah Kasus 2: State Working/Running Dianggap WAITING (Working -> Waiting False Positive)
- **Akar Masalah 2.1 (Pemeriksaan activeModals Tanpa Validasi isDecisionButton):**
  Pada `extract_state.js` (L223–L232), ketika Antigravity membuka panel/dialog subagent, Goal view, atau editor tool yang memiliki atribut `[role="dialog"]` atau `.interactive-modal`, script hanya mengecek apakah ada tombol apa pun di dalamnya (`modalBtns.length > 0`). Script **TIDAK** memverifikasi apakah tombol-tombol tersebut adalah tombol izin (`isDecisionButton`). Akibatnya, tombol biasa seperti "Close", "Stop", atau "Inspect" di dalam dialog Goal langsung menjadikan panel tersebut sebagai `decisionContainer` aktif.
- **Akar Masalah 2.2 (False Positive Keyword Tombol pada targetTurn):**
  Daftar `decisionKeywords` memuat kata-kata umum seperti `'submit'`, `'skip'`, `'proceed'`, dan `'no'`. Tombol "Submit" pada area chat input (`<button aria-label="Submit">`), tombol "Skip" pada langkah Goal, atau elemen dengan `aria-label="No logs"` secara keliru memicu `isDecisionButton = true` pada `targetTurn`.
- **Akar Masalah 2.3 (Hierarki Precedence State yang Terbalik / Pembajakan Status RUNNING):**
  Pada L315 dan L324:
  ```javascript
  const isRunning = !isWaitingForInput && (hasStopButton || hasSpinner || hasActiveTaskElement || hasStatusBarRunning || isLastStepActive);

  if (isWaitingForInput) {
      state = "WAITING";
  } else if (isRunning) {
      state = "RUNNING";
  }
  ```
  Evaluasi `if (isWaitingForInput)` diletakkan **SEBELUM** `isRunning`, dan variabel `isRunning` dipaksa bernilai `false` jika `isWaitingForInput` bernilai `true` (`!isWaitingForInput`).
  Akibatnya, meskipun agen sedang aktif bekerja (spinner berputar, `hasSpinner = true`, dan tombol Stop aktif `hasStopButton = true`), keberadaan false positive `isWaitingForInput` langsung membajak (*override*) status eksekusi menjadi `WAITING`.
- **Akar Masalah 2.4 (Ekstraksi Paragraf Goal sebagai decisionPrompt):**
  Ketika `decisionContainer` keliru terpilih sebagai `targetTurn`, parser `promptEl` (L265) mencari elemen `strong`, `h1-h4`, atau `span.font-medium` yang mencocokkan tag `<strong>Goal</strong>`. Karena teks `"Goal"` terlalu pendek (< 5 karakter), fallback `lines.find(l => l.length > 5...)` (L277) mengambil baris paragraf pertama di bawah Goal (`"Navigate to https://fashionforth.id and inspect the console logs..."`) dan menetapkannya sebagai `decisionPrompt`. Hal inilah yang memicu munculnya modal HUD "Decision Required" berisi instruksi Goal lengkap.

##### 3. Rujukan Kode Sumber & Bukti Teknis
```javascript
// Anti Dispatch/Resources/Scripts/extract_state.js: L223-L232
const activeModals = Array.from(document.querySelectorAll('[role="dialog"], [role="radiogroup"], .interactive-modal...')).filter(isVisible);
let decisionContainer = activeModals.length > 0 ? activeModals[activeModals.length - 1] : null;

if (decisionContainer) {
    // BUG: Hanya mengecek keberadaan tombol sembarang tanpa memvalidasi isDecisionButton!
    const modalBtns = Array.from(decisionContainer.querySelectorAll('button')).filter(b => isVisible(b) && !b.disabled);
    const modalRadios = Array.from(decisionContainer.querySelectorAll('input[type="radio"]:not(:disabled), input[type="checkbox"]:not(:disabled)')).filter(isVisible);
    if (modalBtns.length === 0 && modalRadios.length === 0) {
        decisionContainer = null;
    }
}

// Anti Dispatch/Resources/Scripts/extract_state.js: L315-L327
// BUG: isWaitingForInput membajak isRunning secara absolut
const isRunning = !isWaitingForInput && (hasStopButton || hasSpinner || hasActiveTaskElement || hasStatusBarRunning || isLastStepActive);

let state = "IDLE";
if (isWaitingForInput) {
    state = "WAITING";
} else if (isRunning) {
    state = "RUNNING";
}
```

---

### [QA-20260913-01] LauncherService, POSIX Socket Probing, dan Memory Layout Pointers
- **Tanggal**: 2026-09-13 19:30
- **Scope / Target Node**: `nodes/anti-dispatch` (`Anti Dispatch/Services/LauncherService.swift`)
- **Kategori**: `API & Network Integration`
- **Tags**: `#posix-socket #socket-probing #port-scanner #sockaddr-in #big-endian #with-unsafe-pointer #memory-rebound #darwin-c`
- **File Referensi**:
  - `Anti Dispatch/Services/LauncherService.swift`
  - `global-docs/manual-guides/01-core-engine/03-launcher-service-dan-port-scanner.md`

#### ❓ Pertanyaan Pengguna
1. **Apa fungsi `sockaddr_in()` dan bedanya dengan `sockaddr`, `sockaddr_un`, atau `sockaddr_dl`?**
2. **Apa fungsi `stride` di Swift `MemoryLayout`?**
3. **Apa fungsi `sin_len`, kenapa diisi `UInt8`, dan apa itu `UInt8`?**
4. **Apa itu `MemoryLayout` dan kenapa mengambil `MemoryLayout<sockaddr_in>.stride`?**
5. **Apa itu `sin_family`, `sa_family_t`, dan `AF_INET`?**
6. **Apa itu `sin_port`, `in_port_t`, dan kenapa WAJIB `.bigEndian`?**
7. **Apa itu `addr.sin_addr.s_addr`, `inet_addr`, dan kenapa `127.0.0.1`?**
8. **Kenapa `sin_len` harus diisi manual, tidakkah bisa otomatis mengambil stride miliknya?**
9. **Tabel Akronim Lengkap Istilah Jaringan Tingkat Rendah POSIX?**
10. **Apa itu POSIX dan POSIX Call secara sederhana?**
11. **Kenapa Windows tidak ikut standar POSIX?**
12. **Apa parameter asli (C/POSIX Signature) dari `socket()`, `bind()`, dan `close()`?**
13. **Jelaskan fungsi variabel `sock`, kenapa diisi `socket(AF_INET, SOCK_STREAM, 0)`, dan kenapa dicek `sock >= 0`?**
14. **Apa itu `bindResult`, apa itu `withUnsafePointer`, dan kenapa butuh closure pointer?**
15. **Apa arti `$0`, apa fungsi `withMemoryRebound(to:capacity:)`, dan kenapa butuh `sockaddr.self` kapasitas 1?**
16. **Apa fungsi `bind()`, apa itu `socklen_t`, dan kenapa panjangnya memakai `MemoryLayout<sockaddr_in>.stride`?**
17. **Jelaskan fungsi `close(sock)`!**
18. **Apa fungsi `if bindResult == 0 { return port }`, arti masuk/gagal di `if`, dan kenapa fallback ke `startPort`?**
19. **Jadi tujuan perulangan dalam `for` adalah di `bind()`, yang mana dengan `bind()` kita bisa terhubung ke port yang ingin dicek dan mengetahui apakah sudah ada proses atau belum?**
20. **Kenapa diterima/sukses menghasilkan `0` dan bukan `1`?**

#### 💡 Jawaban & Penjelasan Implementasi

##### 1. Perbedaan Keluarga Struct Socket Address POSIX
- **Ringkasan Inti**: `sockaddr` adalah struct generik, sedangkan `sockaddr_in` adalah spesifik IPv4.
- **Detail Implementasi & Logika**:
  - `sockaddr`: Struct dasar untuk parameter fungsi kernel C (`bind()`, `connect()`).
  - `sockaddr_in`: Struct khusus alamat jaringan **IPv4** (memuat port `sin_port` dan alamat IP 32-bit `sin_addr`).
  - `sockaddr_un`: Struct khusus komunikasi antar-proses lokal (IPC) berbasis file path di disk (`/var/run/docker.sock`).
  - `sockaddr_dl`: Struct khusus perangkat keras Link Layer (membaca MAC Address kartu jaringan).
  - `sockaddr_in6`: Struct khusus alamat jaringan **IPv6** (128-bit).

##### 2. Fungsi `stride` pada Swift `MemoryLayout`
- **Ringkasan Inti**: `stride` mengukur jarak byte antar-elemen memori termasuk padding alignment OS.
- **Detail Implementasi & Logika**:
  - `size`: Jumlah byte murni isi data.
  - `stride`: Jarak byte dari awal elemen pertama ke elemen berikutnya di RAM (termasuk *memory alignment / padding* penggenap).
  - Digunakan saat mengirim panjang buffer struct ke fungsi kernel C agar kernel membaca seluruh alokasi memori secara utuh.

##### 3. Fungsi `sin_len`, `UInt8`, dan Inisialisasi Manual
- **Ringkasan Inti**: `sin_len` adalah field panjang struct dalam byte pada kernel turunan BSD (macOS Darwin).
- **Detail Implementasi & Logika**:
  - `UInt8` adalah *Unsigned 8-bit Integer* (bilangan bulat positif 0–255, memakan 1 byte memori).
  - Field `sin_len` pada header C kernel Darwin bertipe `__uint8_t`, sehingga Swift mewajibkan konversi tipe eksplisit ke `UInt8`.

##### 4. Refleksi Memori Dinamis via `MemoryLayout<sockaddr_in>.stride`
- **Ringkasan Inti**: Mengukur ukuran fisik struct di RAM pada CPU Mac saat ini secara dinamis.
- **Detail Implementasi & Logika**:
  - Menjamin ukuran struct dihitung 100% presisi dan kompatibel baik di Mac Apple Silicon (ARM64) maupun Intel (x86_64).

##### 5. Penjelasan `sin_family`, `sa_family_t`, dan `AF_INET`
- **Ringkasan Inti**: `sin_family` menentukan keluarga protokol alamat jaringan.
- **Detail Implementasi & Logika**:
  - `AF_INET`: *Address Family: Internet* (konstanta integer C penanda protokol **IPv4**).
  - `sa_family_t`: Tipe data integer C untuk field famili alamat.

##### 6. Penjelasan `sin_port`, `in_port_t`, dan Kewajiban `.bigEndian`
- **Ringkasan Inti**: CPU Mac menggunakan Little-Endian, sedangkan jaringan TCP/IP mewajibkan Big-Endian (Network Byte Order).
- **Detail Implementasi & Logika**:
  - `sin_port`: Field nomor port TCP (misal `9221`).
  - `in_port_t`: Tipe integer 16-bit (`UInt16`, 0–65.535) khusus port jaringan.
  - Port 9221 (`0x2405`) jika tidak di-`.bigEndian` akan terbaca terbalik oleh kernel macOS sebagai port **`1316`** (`0x0524`).

##### 7. Penjelasan `addr.sin_addr.s_addr`, `inet_addr`, dan `127.0.0.1`
- **Ringkasan Inti**: Mengonversi teks string IP menjadi biner 32-bit integer Network Byte Order pada loopback localhost.
- **Detail Implementasi & Logika**:
  - `sin_addr`: Sub-struct penampung alamat IP.
  - `s_addr`: Integer 32-bit di dalam `sin_addr` penyimpan biner IPv4.
  - `inet_addr("127.0.0.1")`: Fungsi pengubah string teks `"127.0.0.1"` menjadi biner 32-bit `0x7F000001`.
  - Menggunakan `127.0.0.1` menjamin socket probing hanya memeriksa proses lokal Mac dan **tidak pernah memancarkan paket ke jaringan eksternal/internet**.

##### 8. Solusi Ekstensi Inisialisasi Otomatis `sockaddr_in`
- **Ringkasan Inti**: Menggunakan extension Swift untuk mengotomatiskan pengisian struct C yang berulang.
- **Rujukan Kode Sumber**:
  ```swift
  extension sockaddr_in {
      init(port: Int, ip: String = "127.0.0.1") {
          self.init()
          self.sin_len = UInt8(MemoryLayout<sockaddr_in>.stride)
          self.sin_family = sa_family_t(AF_INET)
          self.sin_port = in_port_t(port).bigEndian
          self.sin_addr.s_addr = inet_addr(ip)
      }
  }
  ```

##### 9. Tabel Akronim Lengkap Istilah Jaringan POSIX
| Singkatan / Istilah | Kepanjangan Asli (Bahasa Inggris) | Arti / Penjelasan Singkat |
|---|---|---|
| **`addr`** | *Address* | Alamat |
| **`sockaddr`** | *Socket Address* | Struct umum alamat soket jaringan Unix |
| **`sockaddr_in`** | *Socket Address Internet* | Struct alamat soket khusus IPv4 |
| **`sockaddr_un`** | *Socket Address Unix* | Struct alamat soket lokal berkas (*Unix Domain*) |
| **`sockaddr_dl`** | *Socket Address Data Link* | Struct alamat hardware layer 2 (MAC Address) |
| **`sin_len`** | *Socket Internet Length* | Panjang ukuran byte struct `sockaddr_in` |
| **`sin_family`** | *Socket Internet Family* | Famili protokol jaringan (IPv4/IPv6/Unix) |
| **`sin_port`** | *Socket Internet Port* | Nomor port TCP/UDP |
| **`sin_addr`** | *Socket Internet Address* | Sub-struct alamat IP |
| **`s_addr`** | *Socket Address* | Nilai integer biner 32-bit dari IPv4 |
| **`sa_family_t`** | *Socket Address Family Type* | Tipe data integer untuk jenis famili protokol |
| **`in_port_t`** | *Internet Port Type* | Tipe data integer 16-bit untuk nomor port |
| **`AF_INET`** | *Address Family: Internet* | Konstanta penanda protokol IPv4 |
| **`inet_addr`** | *Internet Address* | Fungsi pengubah teks IP jadi biner jaringan |
| **`SOCK_STREAM`** | *Socket Stream* | Soket berbasis aliran data andal (**TCP**) |
| **`SOCK_DGRAM`** | *Socket Datagram* | Soket berbasis paket tanpa koneksi (**UDP**) |
| **`UInt8`** | *Unsigned Integer 8-bit* | Angka bulat positif 1 byte (0–255) |
| **`UInt16`** | *Unsigned Integer 16-bit* | Angka bulat positif 2 byte (0–65.535) |
| **`POSIX`** | *Portable Operating System Interface* | Standar antarmuka sistem operasi Unix/BSD/macOS |

##### 10. Konsep Dasar POSIX dan POSIX System Call
- **Ringkasan Inti**: POSIX adalah standar antarmuka sistem operasi Unix/macOS; POSIX Call adalah fungsi C tingkat rendah untuk memanggil kernel secara langsung dengan performa ultra-cepat dalam mikrodetik.
- **Detail Implementasi & Logika**:
  ```
  Aplikasi Swift (User Space)
              │
              ▼ (Memanggil POSIX Call)
  POSIX APIs (socket(), bind(), open(), read())
              │
              ▼
  Kernel macOS Darwin (System / Kernel Space)
              │
              ▼
  Hardware Komputer (Network Card / SSD / RAM)
  ```

##### 11. Perbedaan Filosofi Sistem Windows vs POSIX
- **Ringkasan Inti**: POSIX menganut filosofi *"Everything is a file"*, sedangkan Windows NT menganut *"Everything is an Object / Handle"*.
- **Detail Implementasi & Logika**:
  - macOS/Linux menggunakan POSIX System Call asli (`open`, `close`, `socket`, `bind`).
  - Windows menggunakan Win32 API (`CreateFileA`, `CloseHandle`, Winsock `closesocket`).
  - Windows kini menyediakan WSL (Linux kernel via hypervisor) dan Winsock2 untuk kompatibilitas.

##### 12. Signature C Asli untuk `socket()`, `bind()`, dan `close()`
- **Rujukan Kode Sumber**:
  ```c
  #include <sys/socket.h>
  #include <unistd.h>

  int socket(int domain, int type, int protocol);
  int bind(int socket, const struct sockaddr *address, socklen_t address_len);
  int close(int fildes);
  ```

##### 13. Fungsi Variabel `sock`, `AF_INET + SOCK_STREAM`, dan Guard `sock >= 0`
- **Ringkasan Inti**: `sock` menyimpan File Descriptor integer non-negatif (`>= 0`) saat soket TCP IPv4 berhasil dibuat oleh kernel.
- **Detail Implementasi & Logika**:
  - `AF_INET` = IPv4, `SOCK_STREAM` = TCP stream, `0` = default protocol (`IPPROTO_TCP`).
  - Jika kernel gagal mengalokasikan socket (RAM habis/descriptor penuh), `socket()` mengembalikan `-1`. `guard sock >= 0` mencegah akses memori berbahaya.

##### 14. Mekanisme `bindResult`, `withUnsafePointer`, dan Scoped Pointer Safety
- **Ringkasan Inti**: Menjembatani model memory-safety Swift dengan pointer C mentah (`&addr`) secara aman dalam blok closure.
- **Detail Implementasi & Logika**:
  - `withUnsafePointer(to: &addr)` meminjamkan pointer fisik `addr` hanya selama closure berjalan.
  - Mencegah bahaya *Dangling Pointer* dan *Memory Leak*.

##### 15. Penjelasan `$0`, `withMemoryRebound(to:capacity:)`, dan Type Casting Memori
- **Ringkasan Inti**: Menafsirkan ulang pointer struct `sockaddr_in` menjadi pointer generik `sockaddr` sesuai ekspektasi fungsi `bind()`.
- **Detail Implementasi & Logika**:
  - `$0` adalah `UnsafePointer<sockaddr_in>`.
  - `withMemoryRebound` memenuhi *Strict Aliasing Rule* kompiler Swift dengan memberi tahu kompiler untuk membaca ulang memori tersebut sebagai `sockaddr` dengan kapasitas 1 struct.

##### 16. Fungsi `bind()`, `socklen_t`, dan Alokasi Buffer Stride
- **Ringkasan Inti**: `bind()` mengikat socket ke alamat & port tertentu.
- **Detail Implementasi & Logika**:
  - Parameter `socklen_t(MemoryLayout<sockaddr_in>.stride)` memberi tahu kernel ukuran byte valid dari struct agar kernel membaca memori dengan batas presisi.

##### 17. Fungsi `close(sock)` dan Siklus Hidup Socket Descriptor
- **Ringkasan Inti**: Menutup descriptor socket segera setelah tes probing selesai.
- **Detail Implementasi & Logika**:
  - Membebaskan kuota file descriptor kernel OS.
  - Melepaskan port kembali ke status kosong agar instance Antigravity IDE dapat menggunakannya.
  - Mencegah kebocoran memori / *resource leak*.

##### 18. Logika Evaluasi `bindResult == 0` dan Fallback ke `startPort`
- **Ringkasan Inti**: Jika `bindResult == 0`, port kosong dan fungsi langsung me-return port tersebut. Jika `-1`, lanjut loop. Jika semua penuh, fallback ke `startPort`.
- **Detail Implementasi & Logika**:
  - Mengembalikan nilai valid secara defensif untuk mencegah crash runtime.

##### 19. Perbedaan Konseptual Mengklaim (`bind`) vs Menghubungi (`connect`)
- **Ringkasan Inti**: `bind()` bertindak sebagai pemesan lapak port di kernel OS (mendeteksi port kosong), bukan `connect()` yang bertindak sebagai tamu pengetuk server.
- **Detail Implementasi & Logika**:
  ```
  [Loop 1: Port 9221]
    ├─► socket() ──► Buat jalur socket baru
    ├─► bind(port 9221) ──► Kernel: "Ditolak! (-1) Port 9221 sudah dipakai Sesi 1."
    ├─► close(sock)
    └─► Gagal if (bindResult == 0). Lanjut ke loop berikutnya...

  [Loop 2: Port 9222]
    ├─► socket() ──► Buat jalur socket baru
    ├─► bind(port 9222) ──► Kernel: "Diterima! (0) Port 9222 masih kosong."
    ├─► close(sock) ──► Segera dilepaskan kembali agar bersih
    └─► Masuk if (bindResult == 0) ──► return 9222 (SELESAI!)
  ```

##### 20. Filosofi Sistem Operasi POSIX di Balik Return Code `0`
- **Ringkasan Inti**: Angka `0` melambangkan kondisi *Zero Errors* (nol masalah) pada standar UNIX/POSIX.
- **Detail Implementasi & Logika**:
  - Sukses itu mutlak (hanya butuh 1 kode: `0`).
  - Gagal memiliki banyak kemungkinan alasan/error code (`EADDRINUSE`, `EACCES`, `EINVAL`).
  - Standar: `bind() == 0`, `close() == 0`, `exit(0)`, `git status == 0`.

---

### [QA-20260912-01] GitWorktreeService, Subprocess CLI, dan POSIX Exit Codes
- **Tanggal**: 2026-09-12 10:15
- **Scope / Target Node**: `nodes/anti-dispatch` (`Anti Dispatch/Services/GitWorktreeService.swift`)
- **Kategori**: `Architecture & Pattern`
- **Tags**: `#git-worktree #actor-concurrency #process-pipe #posix-exit-codes #subprocess #command-line #porcelain`
- **File Referensi**:
  - `Anti Dispatch/Services/GitWorktreeService.swift`
  - `global-docs/manual-guides/01-core-engine/02-git-worktree-service.md`

#### ❓ Pertanyaan Pengguna
1. **Jelaskan kode `GitWorktreeService.swift` baris per baris!**
2. **Jika sebuah class tidak diberikan eksplisit `public init() {}`, apakah tidak bisa diinstansiasi?**
3. **Apa itu `rev-parse` dan `--is-inside-work-tree`?**
4. **Apakah pada Git jika me-return `0` itu aman dan bukan `1`?**
5. **Apa itu error code 128 pada Git?**
6. **Jelaskan kegunaan `Process` dan `Pipe` secara lengkap di Swift!**
7. **Apakah `/usr/bin/git` selalu ada di semua jenis Mac?**
8. **Apa yang terjadi jika hasil terminal TIDAK di-pipe?**
9. **Kenapa `runGit` dilabeli `async` padahal di dalamnya tidak ada `await`?**
10. **Jika `process.run()` tidak memakai `await`, kenapa harus ada `process.waitUntilExit()`?**
11. **Apa itu `fileHandleForReading` dan tipe data dari `readDataToEndOfFile()`?**
12. **Apakah `String(data: data, encoding: .utf8)` benar mengubah buffer ke string?**
13. **Kenapa me-return `-1` di blok catch, bukannya `0` sampai seterusnya?**
14. **Apa fungsi `git worktree list` dan kenapa wajib `--porcelain`?**
15. **Apa fungsi `URL(fileURLWithPath:)`?**
16. **Untuk apa `replacingOccurrences` di sanitasi branch?**
17. **Untuk apa `git show-ref` dengan `--verify` dan `--quiet`?**
18. **Bagaimana logika resolusi `resolveDefaultBaseBranch`?**

#### 💡 Jawaban & Penjelasan Implementasi

##### 1. Bedah Kode `GitWorktreeService.swift` Baris per Baris
- **Ringkasan Inti**: `GitWorktreeService` mengontrol eksekusi CLI Git melalui subprocess `Process` dan IPC `Pipe` di dalam isolasi Swift `actor`.
- **Detail Implementasi & Logika**:
  - `public actor GitWorktreeService`: Menjamin thread-safety dan mencegah data race pada operasi Git di disk.
  - `public init() {}`: Initializer publik agar dapat diinstansiasi oleh modul lain dan target unit test.
  - `isGitRepository`: Menjalankan `git rev-parse --is-inside-work-tree` untuk validasi status repo.
  - `findExistingWorktree`: Membaca output `git worktree list --porcelain` untuk menemukan direktori worktree dari branch tertentu.
  - `resolveDefaultBaseBranch`: Resolusi bertingkat (preferred -> main -> master -> HEAD).
  - `prepareWorktree`: Menyiapkan path baru, mengecek keberadaan branch via `git show-ref`, dan menjalankan `git worktree add`.
  - `runGit`: Helper privat untuk konfigurasi dan eksekusi `Process()`, pipa I/O `Pipe()`, dan decoding buffer output.

##### 2. Akses Kontrol `public init() {}` pada Swift Modules
- **Ringkasan Inti**: Initializer bawaan Swift bertingkat akses `internal`. Tanpa `public init()`, target pengujian (*unit test*) tidak dapat menginstansiasinya.

##### 3. Fungsi `git rev-parse --is-inside-work-tree`
- **Ringkasan Inti**: Plumbing command Git untuk memastikan direktori kerja adalah root atau subfolder dari repositori Git yang valid.

##### 4. Standar Universal Exit Code POSIX / Unix
- **Ringkasan Inti**: `0` = Success/OK, `1` = General Error, `> 1` = Fatal/Specific Error.

##### 5. Makna Exit Code `128` pada Git CLI
- **Ringkasan Inti**: Kode standar Git untuk Fatal Error / Invalid Repository State (bukan repo Git, permission denied, atau worktree terkunci).

##### 6. Arsitektur `Process` dan `Pipe` pada macOS Foundation
- **Ringkasan Inti**: `Process` meluncurkan biner command-line sebagai child process OS; `Pipe` mengalirkan stream I/O biner tersebut ke memori aplikasi induk melalui kernel IPC pipe.

##### 7. Ketersediaan Biner `/usr/bin/git` pada Sistem macOS
- **Ringkasan Inti**: Selalu tersedia di semua Mac sebagai Apple System Shim di partisi sistem *read-only* terlindungi SIP.

##### 8. Konsekuensi Jika Output Subprocess Tidak Di-pipe
- **Ringkasan Inti**: Output terminal akan bocor ke console sistem Xcode dan variabel kode Swift tidak dapat membaca teks output tersebut.

##### 9. Label `async` pada Actor Methods
- **Ringkasan Inti**: Seluruh method publik/internal di dalam Swift `actor` secara otomatis berstatus `async` untuk menjamin isolasi antrian pesan (*actor mailbox isolation*) dan mencegah freeze pada main UI thread.

##### 10. Peran `process.waitUntilExit()` vs `process.run()`
- **Ringkasan Inti**: `process.run()` bersifat asynchronous non-blocking di kernel OS. `process.waitUntilExit()` menahan eksekusi thread sampai proses child selesai agar buffer terbaca utuh.

##### 11. Konversi Buffer `FileHandle` ke Tipe `Data`
- **Ringkasan Inti**: `fileHandleForReading.readDataToEndOfFile()` membaca seluruh byte stream terminal dan menyimpannya sebagai buffer byte mentah `Data`.

##### 12. Rekonstruksi Buffer UTF-8 Menjadi `String`
- **Ringkasan Inti**: `String(data: data, encoding: .utf8)` mengonversi deretan byte biner menjadi teks representasi string UTF-8 yang valid.

##### 13. Penanganan Error Sintetis Status Code `-1`
- **Ringkasan Inti**: Status `>= 0` adalah exit code resmi Git CLI; status `-1` menandakan sistem operasi macOS gagal meluncurkan biner Git sama sekali (misal izin diblokir SIP atau kehabisan memori OS).

##### 14. Determinisme Parsing via `git worktree list --porcelain`
- **Ringkasan Inti**: Format `--porcelain` menghasilkan baris kunci-nilai deterministik (`worktree <path>`, `branch <ref>`) yang kebal terhadap spasi pada nama direktori.

##### 15. URL Konstruktor `URL(fileURLWithPath:)`
- **Ringkasan Inti**: Mengonversi path sistem berkas lokal menjadi objek `URL` ber-skema `file://` dengan percent-encoding otomatis.

##### 16. Sanitasi Direktori Branch via `replacingOccurrences`
- **Ringkasan Inti**: Mengganti karakter separator subfolder `/` menjadi `-` agar direktori worktree terbuat sebagai 1 folder rapi di disk.

##### 17. Verifikasi Branch Senyap via `git show-ref --verify --quiet`
- **Ringkasan Inti**: Pengecekan eksak keberadaan ref branch lokal dengan konsumsi CPU minimal dan tanpa output stdout.

##### 18. Alur Logika Cascading Fallback `resolveDefaultBaseBranch`
- **Ringkasan Inti**: Resolusi bertingkat: `preferredBase` -> `main` -> `master` -> pointer aktif `HEAD`.

---

### [QA-20260911-02] PathHasher, Data Buffer, MD5 Mapping, dan Direktori Isolasi
- **Tanggal**: 2026-09-11 15:30
- **Scope / Target Node**: `nodes/anti-dispatch` (`Anti Dispatch/Utilities/PathHasher.swift`)
- **Kategori**: `Architecture & Pattern`
- **Tags**: `#path-hasher #md5-hashing #data-buffer #cryptokit #isolation-directory #file-path-handling #sendable #nonisolated`
- **File Referensi**:
  - `Anti Dispatch/Utilities/PathHasher.swift`
  - `global-docs/manual-guides/01-core-engine/01-path-hasher-dan-direktori-isolasi.md`

#### ❓ Pertanyaan Pengguna
1. **Jelaskan kode `PathHasher.swift` baris per baris!**
2. **Apa itu Buffer Data?**
3. **Apakah tidak ada fungsi langsung generate MD5 String di Swift, kenapa harus perulangan (`.map`)?**
4. **Apa beda `.standardizingPath`, `.standardizedFileURL`, dan `.path`?**
5. **Jika ada branch `feat/ui` dan `feat-ui`, bagaimana? Tidakkah nanti bentrok?**

#### 💡 Jawaban & Penjelasan Implementasi

##### 1. Bedah Kode `PathHasher.swift` Baris per Baris
- **Ringkasan Inti**: `PathHasher` adalah static utility untuk menstandarkan path direktori, menghitung MD5 hash 6-karakter, dan menghasilkan path isolasi `--user-data-dir`.
- **Detail Implementasi & Logika**:
  - `public enum PathHasher`: Dideklarasikan sebagai enum tanpa case (pure static namespace) dengan konformansi `Sendable` dan penanda `nonisolated`.
  - `computeShortHash(for:)`: Menstandarkan path string via `standardizingPath`, mengubah ke `Data(utf8)`, menghitung MD5 digest via `Insecure.MD5`, memetakan ke heksadesimal 32-karakter, dan mengambil 6 karakter pertama.
  - `resolveDataDirectory(for:branch:customRoot:)`: Mengambil standardized file URL, menghitung hash, menyusun folder name format `<hash>_<project>[_<branch>]`, dan menggabungkannya ke root directory `~/.antigravity-multi/`.
  - `sanitizeBranchForFolderName`: Mengganti karakter ilegal sistem berkas menjadi tanda hubung `-`.

##### 2. Konsep Memori Buffer Data (`Data` di Swift)
- **Ringkasan Inti**: `Data` adalah wadah penampung deretan angka byte biner mentah (0–255) yang tersimpan secara berurutan di RAM.
- **Detail Implementasi & Logika**:
  - `String` adalah representasi teks manusia; `Data` adalah representasi byte untuk algoritma kriptografi, network socket, dan storage SSD.

##### 3. Transformasi Byte Digest ke String Heksadesimal via `.map`
- **Ringkasan Inti**: Apple CryptoKit menghasilkan `MD5Digest` (16 byte mentah `[UInt8]`). Format `.map { String(format: "%02hhx", $0) }.joined()` adalah cara resmi tercepat di Swift untuk mengubah memori biner ke 32 string hex.

##### 4. Perbedaan Metode Normalisasi Path di Swift
| Properti / Method | Tipe Data Asal | Fungsi & Perbedaannya | Contoh Hasil |
|---|---|---|---|
| **`.path`** | `URL` | Mengambil teks representasi path sistem berkas murni (membuang skema `file://`). | Dari `file:///Users/raka/app/` diambil `"/Users/raka/app"` |
| **`.standardizedFileURL`** | `URL` | Membersihkan objek `URL` dari path relatif (`..`, `.`, atau *symlink*). | Dari `file:///Users/raka/app/../app/` jadi `file:///Users/raka/app` |
| **`.standardizingPath`** | `String` (`NSString`) | Membersihkan variabel `String` dari `//`, `~`, dan *trailing slash*. | Dari string `"/Users/raka/app//"` jadi `"/Users/raka/app"` |

##### 5. Mitigasi Tabrakan Nama Branch Folder Isolasi
- **Ringkasan Inti**: Menambahkan 4 karakter MD5 hash dari branch name asli pada ujung nama direktori (`<hash>_<project>_<sanitized-branch>-<branchHash>`) untuk menjamin 100% keunikan folder saat multi-sesi berjalan paralel.

---

### [QA-20260911-01] Overview Arsitektur, Swift Versioning, Xcode Hardened Runtime, dan Distribusi DMG
- **Tanggal**: 2026-09-11 14:02
- **Scope / Target Node**: `nodes/anti-dispatch` (`Anti Dispatch.xcodeproj`)
- **Kategori**: `Build, Tooling & DevOps`
- **Tags**: `#xcode-setup #swift-version #hardened-runtime #dmg-distribution #notarization #ls-ui-element #sparkle-updater`
- **File Referensi**:
  - `global-docs/manual-guides/00-overview/01-arsitektur-dan-alur-kerja.md`
  - `global-docs/manual-guides/00-overview/02-persiapan-project-xcode.md`

#### ❓ Pertanyaan Pengguna
1. **Anti Dispatch ini Swift versi berapa?**
2. **Kenapa tidak ada opsi "Allow Outgoing Connections" di menu Hardened Runtime Xcode?**
3. **Apa nama konfigurasi Info.plist di tampilan UI Xcode?**
4. **Apakah file DMG bisa dipakai seterusnya dan tidak seketat App Store Connect?**

#### 💡 Jawaban & Penjelasan Implementasi

##### 1. Konfigurasi Bahasa dan Kompiler Swift
- **Ringkasan Inti**: Mode bahasa project disetel ke Swift 5 (`SWIFT_VERSION = 5.0`) dan dikompilasi oleh Apple Swift 6.3.3 Compiler dengan kompatibilitas penuh untuk modern Swift Concurrency.

##### 2. Pengaturan Hardened Runtime & Akses Jaringan Non-Sandbox
- **Ringkasan Inti**: Ketika App Sandbox dinonaktifkan, macOS secara otomatis membuka izin penuh untuk seluruh koneksi socket loopback lokal (`127.0.0.1:9221-9229`) tanpa memerlukan entitlement khusus.

##### 3. Pemetaan Kunci Info.plist pada Antarmuka Xcode
- **Ringkasan Inti**:
  - `<key>LSUIElement</key>`: Di UI Xcode bernama **`Application is agent (UIElement)`** (`Boolean = YES`) untuk menyembunyikan icon di Dock.
  - `<key>NSHumanReadableCopyright</key>`: Di UI Xcode bernama **`Copyright (human-readable)`** (`String`).

##### 4. Fleksibilitas Distribusi Mandiri via Apple Disk Image (.DMG)
- **Ringkasan Inti**: Distribusi DMG non-App Store dapat digunakan selamanya tanpa masa kedaluwarsa, mengizinkan eksekusi subprocess Git & socket tingkat tinggi, serta mendukung auto-update mandiri via Sparkle 2 Framework dan notarisasi otomatis via Apple `notarytool`.
