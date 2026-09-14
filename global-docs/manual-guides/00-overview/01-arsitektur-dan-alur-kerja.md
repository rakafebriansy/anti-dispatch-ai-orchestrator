# Modul 00.01: Arsitektur & Alur Kerja Sistem

Dokumen ini menjelaskan rancangan arsitektur tingkat tinggi (*high-level architecture*) dan bagaimana seluruh komponen di dalam **Anti Dispatch** berinteraksi satu sama lain.

---

## 1. Arsitektur Komponen

Anti Dispatch dibangun di atas pola **Clean Swift + Actor-based Concurrency** yang memisahkan tanggung jawab sistem menjadi 4 lapisan utama:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        1. Presentation Layer                           │
│  • NotchPanel (Custom NSPanel Level .floating / .statusBar)            │
│  • SwiftUI Views: StandbyNotchView, SingleSessionView, AccordionView   │
│  • NotchViewModel (@Observable @MainActor)                             │
└───────────────────────────────────▲────────────────────────────────────┘
                                    │ Live State Binding / Async Stream
┌───────────────────────────────────┴────────────────────────────────────┐
│                    2. Background Engine (Swift Actors)                 │
│  • LauncherService: Alokasi port, direktori isolasi, spawn subproses   │
│  • GitWorktreeService: Eksekutor /usr/bin/git subprocess               │
│  • CDPService: Loopback port probe, WebSocket client, DOM evaluator    │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Process Arguments & Socket Communication
┌───────────────────────────────────▼────────────────────────────────────┐
│                  3. External & Target Applications                     │
│  • Antigravity IDE (Electron Process with CDP on 127.0.0.1:9221-9229)  │
│  • macOS Window Server (NSWorkspace, NSRunningApplication)             │
│  • Local File System (~/.antigravity-multi)                            │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Alur Kerja Inti (Core Workflows)

### A. Alur Peluncuran Proyek & Isolasi Memori
1. Pengguna memilih folder proyek melalui GUI Notch HUD (`NSOpenPanel`).
2. `LauncherService` menghitung MD5 hash 6-karakter dari absolute path direktori proyek.
3. Menghasilkan jalur direktori isolasi memori:
   ```bash
   data_dir = ~/.antigravity-multi/<6-char-hash>_<project-name>[_<branch>]
   ```
4. `LauncherService` memindai rentang port `9221-9229` via socket loopback dan mengalokasikan port kosong pertama (misal `9221`).
5. Meluncurkan Antigravity IDE via `NSWorkspace` dengan argumen:
   ```bash
   /Applications/Antigravity\ IDE.app --args \
     "<project_path>" \
     --remote-debugging-port=9221 \
     --user-data-dir="<data_dir>"
   ```
6. Mengembalikan `PID` proses ke `NotchViewModel`.

---

### B. Alur Ekstraksi Status Live Agen (CDP Monitoring Loop)
1. `CDPService` melakukan probing ke `http://127.0.0.1:<port>/json/version` untuk memverifikasi identitas Electron Antigravity IDE.
2. Membuka koneksi WebSocket ke `webSocketDebuggerUrl`.
3. Mengirimkan perintah `Runtime.evaluate` yang mengeksekusi script JavaScript di renderer Antigravity untuk mengecek:
   - Apakah spinner / progress aktif? (`RUNNING`)
   - Apakah teks status memuat kata `"done"`, `"worked for"`, `"completed"`? (`DONE`)
   - Daftar 3-5 file atau tool calls terbaru yang sedang dianalisis.
4. Payload JSON yang diterima di-*parse* ke dalam model `AgentLiveState`.
5. `NotchViewModel` menerima perubahan dan secara reaktif merender status di Notch HUD.
6. **Adaptive Polling Timer:**
   - Interval **800ms** saat status `RUNNING`.
   - Interval **3000ms** saat status `IDLE` atau `DONE`.
   - Zero-polling saat tidak ada sesi aktif.

---

### C. Alur Pembuatan Cabang Git Worktree Baru
1. Pengguna membuka modal form `BranchCreatorSheet`.
2. `GitWorktreeService` memverifikasi validitas repo via `/usr/bin/git rev-parse`.
3. Memeriksa apakah branch sudah terhubung ke worktree yang ada via `git worktree list --porcelain`.
4. Jika belum, mengeksekusi:
   ```bash
   git worktree add -b <new-branch> <parent_folder>/<project-name>-<sanitized-branch> <base-branch>
   ```
5. Mengirimkan URL worktree baru ke `LauncherService` untuk meluncurkan instance Antigravity terisolasi di port baru (misal `9222`).

---

## 3. Batasan Desain Penting
* **Strict Localhost Only:** Semua komunikasi CDP wajib terikat ke `127.0.0.1` / `::1`. Dilarang membuka soket ke jaringan luar.
* **Non-Activating NSPanel:** Panel notch tidak boleh mencuri fokus keyboard pengguna saat muncul atau diperbarui (`NSPanel.isFloatingPanel = true`, `styleMask = [.nonactivatingPanel]`).
* **Hardware Notch Adaptive:** Panel otomatis menempel di notch fisik jika `NSScreen.safeAreaInsets.top > 0`, atau menjadi kapsul melayang di tengah atas layar jika pada layar standar/eksternal.
