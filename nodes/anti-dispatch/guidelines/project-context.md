# Panduan Spesifik Proyek: Anti Dispatch

Dokumen ini merupakan pedoman *custom* yang mengikat aturan, peringatan eksklusif, dan penyesuaian khusus yang **hanya relevan** pada proyek **Anti Dispatch** (`nodes/anti-dispatch/`).

> [!IMPORTANT]
> **Tugas Administratif Anda:** File ini adalah pusat penyimpanan aturan khusus untuk node ini. AI Agent wajib mencatat spesifikasi *tech stack*, batasan, dan hasil pindai (*codebase scan*) dari aplikasi node ini di bawah.

---

## 🔍 Hasil Pemindaian Codebase (Codebase Scan)

* **Lokasi Codebase:** `/Users/raka/Developer/repositories/projects/anti-dispatch-dir/Anti Dispatch`
* **Tipe Proyek:** macOS App (SwiftUI + AppKit Utility Panel)
* **Kondisi Awal:** Starter template Xcode (`Anti_DispatchApp.swift`, `ContentView.swift`, target tests `Anti DispatchTests` & `Anti DispatchUITests`). Belum ada dependensi eksternal atau modul kustom.
* **Target OS:** macOS 14.0 (Sonoma) ke atas.

---

## 🛠️ Aturan Fundamental Proyek

1. **Tech Stack & Framework Utama:**
   - **Bahasa & Concurrency:** Swift 6 modern concurrency (`actor`, `async/await`, `@Observable`).
   - **UI Layer:** SwiftUI dikombinasikan dengan AppKit (`NSPanel`, `NSVisualEffectView`, `NSStatusItem`, `NSWorkspace`).
   - **Testing Framework:** `Swift Testing` (`import Testing` / `@Test`) untuk unit testing logika isolasi, git parser, port probing, dan CDP parser.
   - **Linter & Formatting:** `swift-format` / `SwiftLint` standar resmi.

2. **Isolasi Memori & Direktori Kerja:**
   - Direktori isolasi agen AI: `~/.antigravity-multi/<6-char-md5-hash>_<project_name>[_<sanitized_branch>]`.
   - Rumus hash: 6 karakter awal heksadesimal dari MD5 hash absolute path workspace (`CryptoKit.Insecure.MD5`).
   - Flag peluncuran Antigravity wajib menyertakan:
     `--remote-debugging-port=<allocated_port> --user-data-dir="<data_dir>"`

3. **Batasan Komunikasi CDP (Chrome DevTools Protocol):**
   - **Host:** Eksklusif `127.0.0.1` / `localhost`. Dilarang membuka binding ke `0.0.0.0` atau IP eksternal.
   - **Rentang Port:** `9221 – 9229`.
   - **Metode Evaluasi:** Read-only melalui `Runtime.evaluate` payload JavaScript mandiri. Dilarang memicu command destruktif via CDP.
   - **Adaptive Polling:** **800ms** saat status `RUNNING`, **3000ms** saat `IDLE`/`DONE`, zero-polling saat tidak ada sesi aktif.

4. **Windowing & Notch Placement:**
   - Gunakan subclass `NSPanel` dengan atribut: `styleMask = [.borderless, .nonactivatingPanel]`, `level = .floating` (atau `.statusBar`), `collectionBehavior = [.canJoinAllSpaces, .fullScreenAuxiliary]`.
   - Deteksi fisik notch: `NSScreen.safeAreaInsets.top > 0`. Jika layar tidak memiliki notch (layar eksternal atau Mac non-notch), alihkan ke *Floating Capsule Pill* di `x = midX - width/2`, `y = frame.maxY - height - 4`.

---

## 📐 Aturan Khusus & Konvensi Proyek

1. **Pemisahan Actor Service (Clean Concurrency):**
   - Seluruh operasi IO dan background subprocess wajib diisolasi di dalam Swift `actor`:
     - `LauncherService`: Bertanggung jawab mencari binary IDE, port kosong, dan peluncuran instance.
     - `GitWorktreeService`: Bertanggung jawab menjalankan subprocess `/usr/bin/git` (`rev-parse`, `worktree list`, `worktree add`).
     - `CDPService`: Bertanggung jawab atas socket HTTP probing, WebSocket CDP lifecycle, dan parser JSON state.

2. **Zero-Comment Policy & Clean Code:**
   - Patuhi aturan `global-guidelines/coding.md`: Jangan menambahkan komentar basa-basi pada kode. Nama fungsi, method, dan variabel harus deklaratif dan jelas (*self-documenting*).

3. **Penanganan Error & Resiliensi:**
   - Kegagalan koneksi CDP tidak boleh membuat aplikasi crash. Gunakan pendekatan *soft-fail* dengan status `DISCONNECTED` / `STANDBY` jika socket ditutup atau target IDE di-terminate.

4. **Kepatuhan Mode 2 (Prompt-Driven):**
   - Setiap kali menyelesaikan sebuah prompt/fitur dari developer, buat tiket retrospektif di folder `tickets/` dengan format standar dan centang seluruh acceptance criteria sebelum menyusun entri di `CHANGELOG.md`.
