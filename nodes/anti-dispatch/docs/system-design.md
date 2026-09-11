# System Design

## Apa itu System Design
System Design adalah dokumen teknis komprehensif yang menjelaskan arsitektur perangkat lunak, tumpukan teknologi (*tech stack*), pola desain (*design patterns*), struktur *database*, serta cara berbagai komponen sistem saling berintegrasi. Jika PRD berfokus pada "apa" yang akan dibangun untuk pengguna, System Design berfokus secara mendalam pada "bagaimana" sistem tersebut akan dibangun dari sisi rekayasa perangkat lunak (*software engineering*) agar efisien, aman, dan dapat diskalakan (*scalable*).

Dalam pendekatan *Vibe Coding*, System Design memegang peran krusial sebagai "cetak biru arsitektur" bagi AI Agent. Dokumen ini menjadi batasan teknis (*technical constraints*) agar AI tidak membuat keputusan struktural secara sembarangan saat menulis kode. Dengan adanya acuan ini, seluruh modul, komponen, dan fungsi yang di-*generate* oleh AI akan selalu mematuhi satu standar arsitektur yang sama (misalnya, *Clean Architecture*, *MVC*, atau *MVVM*), sehingga basis kode tetap rapi dan terstruktur.

Secara rinci, sebuah dokumen System Design wajib memuat komponen-komponen berikut:
*   **Arsitektur Perangkat Lunak (*Software Architecture*):** Pola dasar yang akan diterapkan secara konsisten pada seluruh proyek (contoh: *Clean Architecture*, *Microservices*, *MVVM*).
*   **Struktur Direktori (*Folder Structure*):** Pemetaan standar untuk pengorganisasian *folder* dan aturan penempatan jenis-jenis *file* (seperti *models, views, controllers, services*).
*   **Tumpukan Teknologi (*Tech Stack* & Dependensi):** Bahasa pemrograman, *framework*, *library*, versi SDK, serta alat pendukung (*tools*) lain yang menjadi prasyarat proyek.
*   **Logika & Alur Komponen (Flowchart):** Diagram alur kerja algoritma spesifik *node* ini. Anda **WAJIB MUTLAK** memvisualisasikan alur kerja komponen utamanya menggunakan PlantUML. Simpan file sebagai `docs/diagrams/flowchart.puml` secara eksplisit lalu tautkan ke sini. Jika *node* ini adalah aplikasi bertipe *Game Engine*, Anda **WAJIB** menggantinya dengan *Micro State Diagram* (seperti alur *Player State*) menggunakan PlantUML dan simpan di `docs/diagrams/state-diagram.puml`.
*   **Skema Data (*Data Modeling*):** Rancangan dan representasi struktur data yang digunakan. Bagian ini wajib dirincikan ke dalam sub-komponen berikut:
    *   **ERD (*Entity-Relationship Diagram*):** Jika aplikasi ini memuat penyimpanan basis data (*Backend/Fullstack*), Anda **WAJIB** membuat rancangan tabel, relasi, dan tipe data menggunakan PlantUML. Simpan file sebagai `docs/diagrams/erd.puml` secara eksplisit dan tautkan ke sini.
    *   **Standar Datetime & Basis Data:** Saat mendefinisikan kolom tanggal/waktu pada skema basis data, Anda **WAJIB** memilih dan mendokumentasikan salah satu opsi baku (Opsi 1: Epoch Millis / `BIGINT` atau Opsi 2: Timestamp with Timezone UTC / `TIMESTAMPTZ`) secara konsisten dengan merujuk pada `../../global-guidelines/database.md`.
    *   **Model:** (Jika aplikasi berupa *fullstack* atau *frontend*) mendefinisikan representasi struktur *object* atau entitas secara internal di dalam kode (*state*).
    *   **DTO (*Data Transfer Object*):** (Jika aplikasi berbasis API) menjelaskan format struktur data (*payload*) yang dikirim atau diterima dalam proses komunikasi *client-server*.
*   **Desain API (*API Contracts*):** Spesifikasi alur komunikasi antar layanan atau antara *client* dan *server*, mencakup spesifikasi *endpoint*, format *request/response payload*, metode autentikasi, serta wajib menyertakan **contoh konkret dari *Header*, *Request*, dan *Response***.
*   **Arsitektur Penyebaran (Deployment Architecture):** Rincian mengenai infrastruktur *hosting*, skema penyebaran peladen (*server deployment*), penyedia awan (*cloud provider*), dan sistem *containerization* (seperti Docker) yang digunakan.
*   **Integrasi Eksternal (*Third-Party Integrations*):** Daftar dan mekanisme integrasi sistem dengan penyedia layanan pihak ketiga (seperti *payment gateway*, analitik, penyimpanan cloud).
*   **Konfigurasi Lingkungan (Environment Variables):** Daftar nama variabel lingkungan rahasia (.env) yang dibutuhkan oleh arsitektur ini beserta deskripsi utilitasnya.
*   **Keamanan & Penanganan Eror (*Security & Error Handling*):** Protokol keamanan (*encryption*), pengelolaan autentikasi/otorisasi pengguna, serta standar untuk *error logging* dan validasi data.

## Cara Melakukan Generate System Design
Pastikan dokumen PRD (`../../global-docs/prd.md`) sudah diselesaikan terlebih dahulu. Berikan draf PRD tersebut kepada AI Agent dan instruksikan untuk mengusulkan arsitektur sistem yang paling cocok. Mintalah AI Agent untuk mengajukan pertanyaan-pertanyaan spesifik terkait batasan infrastruktur, preferensi *tech stack*, target performa, skema keamanan, serta struktur data yang Anda inginkan. Berdasarkan diskusi tersebut, AI akan merumuskan dokumen teknis yang menjadi acuan penulisan kode selanjutnya.

> **Kewajiban Referensi Desain:** Sebelum AI Agent mengeksekusi penyusunan antarmuka (UI) berdasarkan arsitektur ini, ia **WAJIB** merujuk pada pedoman visual global di `../../global-docs/design-system.md` agar implementasi logika sesuai dengan pedoman estetika ekosistem.
## Anti Dispatch (macOS System Design)

### 1. Arsitektur Perangkat Lunak (Software Architecture)
Sistem dibangun menggunakan kombinasi **Clean Swift + Actor-based Concurrency** untuk memisahkan domain I/O (Background Services) dengan UI Layer secara tegas:

```
┌────────────────────────────────────────────────────────────────────────┐
│                          Presentation Layer                            │
│  [ SwiftUI Views: StandbyView, SingleSessionView, AccordionView ]       │
│  [ ViewModels: @Observable @MainActor NotchViewModel ]                 │
│  [ AppKit Controllers: NotchPanel (NSPanel), StatusBarController ]     │
└───────────────────────────────────▲────────────────────────────────────┘
                                    │ Live State Binding / Async Stream
┌───────────────────────────────────┴────────────────────────────────────┐
│                        Core & Service Layer                            │
│  • LauncherService (actor): Binary lookup, port & path hash allocator  │
│  • GitWorktreeService (actor): Git CLI subprocess wrapper              │
│  • CDPService (actor): WebSocket client & DOM script injector          │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Process Launch & Socket Probing
┌───────────────────────────────────▼────────────────────────────────────┐
│                        Target & System Domain                          │
│  • Antigravity IDE Instance (--remote-debugging-port & user-data-dir)   │
│  • macOS Window Server (NSWorkspace & PID activation)                  │
│  • Local File System (~/.antigravity-multi)                            │
└────────────────────────────────────────────────────────────────────────┘
```

---

### 2. Struktur Direktori Kode (Directory Structure)
Struktur direktori modul di dalam folder codebase `/Users/raka/Developer/repositories/projects/anti-dispatch-dir/Anti Dispatch`:

```
Anti Dispatch/
├── App/
│   ├── Anti_DispatchApp.swift          # Entry point aplikasi
│   ├── AppDelegate.swift               # Siklus hidup AppKit & registrasi panel
│   └── AppEnvironment.swift            # Dependency injection container
├── Core/
│   ├── Concurrency/
│   │   └── AsyncTimer.swift            # Utility interval polling adaptif
│   ├── Services/
│   │   ├── LauncherService.swift       # Orkestrasi peluncuran IDE & port probe
│   │   ├── GitWorktreeService.swift    # Eksekutor perintah git worktree
│   │   └── CDPService.swift            # WebSocket CDP & DOM parser
│   ├── Models/
│   │   ├── WorkspaceSession.swift      # Model data sesi aktif
│   │   ├── AgentLiveState.swift        # State snapshot agen (Running/Done/Idle)
│   │   └── AppPreferences.swift        # Konfigurasi UserDefaults
│   └── Protocols/
│       ├── LauncherServiceProtocol.swift
│       └── CDPServiceProtocol.swift
├── Presentation/
│   ├── Notch/
│   │   ├── NotchPanel.swift            # Custom NSPanel (floating, non-activating)
│   │   ├── NotchPanelController.swift  # Manajer posisi & ukuran animasi notch
│   │   ├── ViewModels/
│   │   │   └── NotchViewModel.swift    # @Observable ViewModel untuk HUD
│   │   └── Views/
│   │       ├── StandbyNotchView.swift
│   │       ├── SingleSessionView.swift
│   │       ├── MultiSessionAccordionView.swift
│   │       └── Components/
│   │           ├── PulsingDotView.swift
│   │           └── FileActivityRow.swift
│   ├── Modals/
│   │   └── BranchCreatorSheet.swift    # Form modal pembuatan worktree
│   └── MenuBar/
│       └── StatusBarController.swift   # NSStatusItem integrasi Menu Bar
├── Utilities/
│   ├── ScreenHardwareHelper.swift      # Deteksi notch & safeAreaInsets
│   └── PathHasher.swift                # MD5 6-char hash generator
└── Resources/
    └── Assets.xcassets
```

---

### 3. Tumpukan Teknologi & Dependensi (Tech Stack & Dependencies)
* **Bahasa & Compiler:** Swift 6 (Strict Concurrency Checking).
* **Framework Sistem:**
  * `SwiftUI` & `@Observable` (Macro-based state management).
  * `AppKit` (`NSPanel`, `NSWorkspace`, `NSStatusItem`, `NSOpenPanel`).
  * `CryptoKit` (`Insecure.MD5` untuk hashing direktori isolasi).
* **Testing:** `Swift Testing` (`import Testing` / `@Test`).
* **Auto-Updater:** `Sparkle 2 Framework` (via Swift Package Manager).

---

### 4. Diagram Arsitektur & Logika (PlantUML Visualizations)

* 👉 **Alur Kerja Pemindaian CDP & Polling:** [flowchart.puml](file:///Users/raka/Developer/repositories/projects/anti-dispatch-dir/anti-dispatch-ai-orchestrator/nodes/anti-dispatch/docs/diagrams/flowchart.puml)
* 👉 **Siklus Hidup Status Agen:** [state-diagram.puml](file:///Users/raka/Developer/repositories/projects/anti-dispatch-dir/anti-dispatch-ai-orchestrator/nodes/anti-dispatch/docs/diagrams/state-diagram.puml)
* 👉 **Skema Relasi Data & Model:** [erd.puml](file:///Users/raka/Developer/repositories/projects/anti-dispatch-dir/anti-dispatch-ai-orchestrator/nodes/anti-dispatch/docs/diagrams/erd.puml)

---

### 5. Skema Data & Model (Data Modeling)

```swift
// Enum Status Agen
enum AgentExecutionState: String, Codable, Sendable {
    case standby = "STANDBY"
    case idle = "IDLE"
    case running = "RUNNING"
    case done = "DONE"
    case disconnected = "DISCONNECTED"
}

// Model Sesi Aktif
struct WorkspaceSession: Identifiable, Sendable {
    let id: UUID
    let workspaceName: String
    let workspaceURL: URL
    let branchName: String?
    let pid: pid_t
    let cdpPort: Int
    var state: AgentExecutionState
    var headline: String
    var subSteps: [String]
    let createdAt: Date
    var updatedAt: Date
}
```

---

### 6. Kontrak Komunikasi CDP (API Contracts)

#### A. Port Discovery (`GET http://127.0.0.1:<port>/json/version`)
* **Request Header:**
  ```http
  GET /json/version HTTP/1.1
  Host: 127.0.0.1:9221
  ```
* **Response Payload (JSON):**
  ```json
  {
    "Browser": "Antigravity/1.0.0",
    "Protocol-Version": "1.3",
    "webSocketDebuggerUrl": "ws://127.0.0.1:9221/devtools/browser/abc-123"
  }
  ```

#### B. Script Evaluasi DOM (`Runtime.evaluate`)
* **WebSocket Message Request:**
  ```json
  {
    "id": 1,
    "method": "Runtime.evaluate",
    "params": {
      "expression": "(() => { const container = document.querySelector('[data-testid=\"agent-chat-container\"], .interactive-session') || document.body; const isSpinner = !!container.querySelector('.codicon-loading, .monaco-progress-container.active, [data-status=\"running\"]'); const statusEl = container.querySelector('.agent-status-text, .step-label, .chat-status'); const text = statusEl ? statusEl.innerText.trim() : ''; return JSON.stringify({ state: isSpinner ? 'RUNNING' : (text.toLowerCase().includes('done') ? 'DONE' : 'IDLE'), headline: text || (isSpinner ? 'Thinking...' : 'Standby'), subSteps: [] }); })()",
      "returnByValue": true
    }
  }
  ```

---

### 7. Keamanan, Isolasi & Penanganan Error
1. **Loopback-Only Binding:** Validasi ketat bahwa koneksi CDP hanya terjadi pada `127.0.0.1`.
2. **Crash Prevention & Graceful Teardown:** Ketika target IDE dihentikan, socket listener menangkap EOF secara aman, menghapus entri sesi dari `NotchViewModel`, dan mengubah tampilan ke mode standby jika tidak ada sesi lain.
3. **Adaptive Polling:** Polling aktif 800ms saat `RUNNING`, melambat ke 3000ms saat `IDLE`/`DONE` untuk menghemat CPU dan daya baterai MacBook.

