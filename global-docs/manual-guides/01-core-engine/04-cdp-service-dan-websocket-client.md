# Modul 01.04: CDP Service & WebSocket Client

Dokumen ini memandu pembuatan `CDPService` — modul pemantau latar belakang yang berkomunikasi via **Chrome DevTools Protocol (CDP)** untuk mengekstraksi status live agen AI tanpa mengganggu proses IDE.

---

## 1. Arsitektur Komunikasi CDP

```
Anti Dispatch (CDPService) 
    │
    ├── (1) HTTP GET http://127.0.0.1:9221/json/version (Fingerprinting)
    │
    └── (2) WebSocket ws://127.0.0.1:9221/devtools/page/...
            │
            └── (3) Send Runtime.evaluate (Memuat Script extract_state.js)
            │
            └── (4) Receive Live State (RUNNING, DONE, IDLE, SubSteps)
```

---

## 2. File JavaScript Mandiri (`extract_state.js`)

Untuk menjaga kode Swift tetap bersih, mudah diuji, dan mempertahankan *syntax highlighting* JavaScript, script DOM extractor dipisahkan ke dalam file JavaScript mandiri.

Simpan file ini di dalam direktori **`Anti Dispatch/Resources/Scripts/extract_state.js`** (pastikan ditambahkan ke *Target Membership* `Anti Dispatch` di Xcode sebagai *Bundle Resource*):

```javascript
(() => {
  try {
    // 1. Ekstraksi nama workspace dari document title
    const title = document.title || "";
    const workspaceMatch = title.match(/^(.+?)\s*[-—]\s*Antigravity/i) || [null, title.split(" - ")[0]];
    const workspaceName = workspaceMatch[1] ? workspaceMatch[1].trim() : "Workspace";

    // 2. Deteksi container chat / agen
    const agentContainer = document.querySelector('[data-testid="agent-chat-container"], .interactive-session, .agent-view') || document.body;
    
    // 3. Deteksi apakah spinner / proses berpikir agen sedang aktif
    const isSpinnerActive = !!agentContainer.querySelector('.codicon-loading, .monaco-progress-container.active, [data-status="running"], .spinner');
    
    // 4. Ekstraksi teks status terakhir
    const statusElements = Array.from(agentContainer.querySelectorAll('.agent-status-text, .step-label, .chat-status, .action-title'));
    const lastStatusText = statusElements.length > 0 ? statusElements[statusElements.length - 1].innerText.trim() : "";

    // 5. Ekstraksi daftar sub-steps / tool call terbaru (maksimal 5 item terakhir)
    const fileElements = Array.from(agentContainer.querySelectorAll('.file-item, .tool-call-item, .step-detail'));
    const subSteps = fileElements.slice(-5).map(el => el.innerText.trim()).filter(Boolean);

    // 6. Evaluasi State Mesin
    let state = "IDLE";
    if (isSpinnerActive) {
      state = "RUNNING";
    } else if (lastStatusText.toLowerCase().includes("worked for") || lastStatusText.toLowerCase().includes("completed") || lastStatusText.toLowerCase().includes("done")) {
      state = "DONE";
    }

    // 7. Kembalikan representasi JSON string
    return JSON.stringify({
      workspaceName: workspaceName,
      state: state,
      headline: lastStatusText || (isSpinnerActive ? "Thinking & Analyzing..." : "Standby"),
      subSteps: subSteps,
      timestamp: Date.now()
    });
  } catch (err) {
    return JSON.stringify({ error: err.message, state: "STANDBY", headline: "Standby", subSteps: [] });
  }
})();
```

---

## 3. Kode Swift Lengkap (`CDPService.swift`)

Simpan kode ini di dalam direktori **`Anti Dispatch/Core/Services/CDPService.swift`**:

```swift
import Foundation

public struct CDPExtractedState: Sendable {
    public let workspaceName: String?
    public let state: String
    public let headline: String
    public let subSteps: [String]
    public let timestamp: Double?
    
    public init(
        workspaceName: String? = nil,
        state: String,
        headline: String,
        subSteps: [String] = [],
        timestamp: Double? = nil
    ) {
        self.workspaceName = workspaceName
        self.state = state
        self.headline = headline
        self.subSteps = subSteps
        self.timestamp = timestamp
    }
}

// MARK: - Explicit Nonisolated Codable Conformance (Swift 6 Strict Concurrency)
extension CDPExtractedState: Codable {
    private enum CodingKeys: String, CodingKey {
        case workspaceName, state, headline, subSteps, timestamp
    }
    
    public nonisolated init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        self.workspaceName = try container.decodeIfPresent(String.self, forKey: .workspaceName)
        self.state = try container.decode(String.self, forKey: .state)
        self.headline = try container.decode(String.self, forKey: .headline)
        self.subSteps = try container.decode([String].self, forKey: .subSteps)
        self.timestamp = try container.decodeIfPresent(Double.self, forKey: .timestamp)
    }
    
    public nonisolated func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        try container.encodeIfPresent(workspaceName, forKey: .workspaceName)
        try container.encode(state, forKey: .state)
        try container.encode(headline, forKey: .headline)
        try container.encode(subSteps, forKey: .subSteps)
        try container.encodeIfPresent(timestamp, forKey: .timestamp)
    }
}

public actor CDPService {
    
    private var webSocketTasks: [Int: URLSessionWebSocketTask] = [:]
    private var messageIdCounter: Int = 1
    
    /// Payload JavaScript yang dimuat dari bundle atau fallback default
    private let extractStateScript: String
    
    public init(customScript: String? = nil) {
        if let customScript = customScript {
            self.extractStateScript = customScript
        } else if let scriptURL = Bundle.main.url(forResource: "extract_state", withExtension: "js", subdirectory: "Resources/Scripts"),
                  let loadedScript = try? String(contentsOf: scriptURL, encoding: .utf8) {
            self.extractStateScript = loadedScript
        } else if let scriptURL = Bundle.main.url(forResource: "extract_state", withExtension: "js", subdirectory: "Scripts"),
                  let loadedScript = try? String(contentsOf: scriptURL, encoding: .utf8) {
            self.extractStateScript = loadedScript
        } else if let fallbackURL = Bundle.main.url(forResource: "extract_state", withExtension: "js"),
                  let loadedScript = try? String(contentsOf: fallbackURL, encoding: .utf8) {
            self.extractStateScript = loadedScript
        } else {
            // Fallback default script jika dijalankan pada Unit Test tanpa bundle assets
            self.extractStateScript = """
            (() => {
                const title = document.title || "Workspace";
                const isRunning = !!document.querySelector('.codicon-loading, .monaco-progress-container.active, [data-status="running"]');
                return JSON.stringify({
                    workspaceName: title.split(" - ")[0],
                    state: isRunning ? "RUNNING" : "IDLE",
                    headline: isRunning ? "Thinking & Analyzing..." : "Standby",
                    subSteps: [],
                    timestamp: Date.now()
                });
            })();
            """
        }
    }
    
    /// 1. Verifikasi apakah port merespons dan ambil target page Antigravity IDE
    public func probePort(port: Int) async -> (isValid: Bool, wsURL: URL?) {
        guard let url = URL(string: "http://127.0.0.1:\(port)/json/list") else { return (false, nil) }
        
        var request = URLRequest(url: url)
        request.timeoutInterval = 1.0
        
        do {
            let (data, response) = try await URLSession.shared.data(for: request)
            guard let httpResponse = response as? HTTPURLResponse, httpResponse.statusCode == 200 else {
                return (false, nil)
            }
            
            if let jsonArray = try JSONSerialization.jsonObject(with: data) as? [[String: Any]] {
                let pageTarget = jsonArray.first(where: {
                    let type = $0["type"] as? String
                    let urlStr = $0["url"] as? String ?? ""
                    return type == "page" && urlStr.contains("workbench.html")
                }) ?? jsonArray.first(where: { ($0["type"] as? String) == "page" })
                
                if let pageTarget = pageTarget,
                   let wsString = pageTarget["webSocketDebuggerUrl"] as? String,
                   let wsURL = URL(string: wsString) {
                    return (true, wsURL)
                }
            }
        } catch {
            return (false, nil)
        }
        
        return (false, nil)
    }
    
    /// 2. Buka koneksi WebSocket ke target port
    public func connectWebSocket(port: Int, wsURL: URL) {
        if webSocketTasks[port] != nil { return } // Sudah terhubung
        
        let task = URLSession.shared.webSocketTask(with: wsURL)
        task.resume()
        webSocketTasks[port] = task
    }
    
    /// 3. Tutup koneksi WebSocket
    public func disconnectWebSocket(port: Int) {
        webSocketTasks[port]?.cancel(with: .goingAway, reason: nil)
        webSocketTasks.removeValue(forKey: port)
    }
    
    /// 4. Evaluasi status DOM renderer Antigravity melalui WebSocket
    public func evaluateState(port: Int) async -> CDPExtractedState? {
        guard let task = webSocketTasks[port] else { return nil }
        
        let reqId = messageIdCounter
        messageIdCounter += 1
        
        let payload: [String: Any] = [
            "id": reqId,
            "method": "Runtime.evaluate",
            "params": [
                "expression": extractStateScript,
                "returnByValue": true
            ]
        ]
        
        guard let jsonData = try? JSONSerialization.data(withJSONObject: payload),
              let jsonString = String(data: jsonData, encoding: .utf8) else {
            return nil
        }
        
        do {
            try await task.send(.string(jsonString))
            
            for _ in 0..<5 {
                let message = try await task.receive()
                
                switch message {
                case .string(let text):
                    guard let rawData = text.data(using: .utf8),
                          let rootJSON = try? JSONSerialization.jsonObject(with: rawData) as? [String: Any] else {
                        continue
                    }
                    
                    if let respId = rootJSON["id"] as? Int, respId == reqId {
                        if let resultDict = rootJSON["result"] as? [String: Any],
                           let valueDict = resultDict["result"] as? [String: Any] {
                            if let valueString = valueDict["value"] as? String,
                               let stateData = valueString.data(using: .utf8) {
                                return try JSONDecoder().decode(CDPExtractedState.self, from: stateData)
                            } else if let valueObject = valueDict["value"] as? [String: Any],
                                      let objData = try? JSONSerialization.data(withJSONObject: valueObject) {
                                return try JSONDecoder().decode(CDPExtractedState.self, from: objData)
                            }
                        }
                        return nil
                    }
                default:
                    break
                }
            }
        } catch {
            disconnectWebSocket(port: port)
        }
        
        return nil
    }
}
```

---

## 4. Keuntungan Pemisahan File Script
1. **Clean Code & Separation of Concerns:** Kode Swift `CDPService.swift` murni fokus menangani protokol jaringan WebSocket, actor concurrency, dan decoding data tanpa terdistraksi ratusan baris string JavaScript.
2. **Maintenance & Developer Experience:** File JavaScript di `Resources/Scripts/extract_state.js` mendapatkan *syntax highlighting*, linting, dan *formatting* otomatis di editor.
3. **Unit Test Friendly:** Initializer `init(customScript:)` memungkinkan injeksi script tiruan (*mock payload*) secara mudah saat menjalankan *unit test*.
4. **Auto Reconnect/Cleanup:** Jika koneksi terputus (misalnya IDE ditutup oleh pengguna), socket otomatis dibersihkan dan mengembalikan `nil` secara aman.
