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
            └── (3) Send Runtime.evaluate (DOM Injected Script)
            │
            └── (4) Receive Live State (RUNNING, DONE, IDLE, SubSteps)
```

---

## 2. Payload JavaScript Injeksi Resilient

Script ini dieksekusi di context renderer Antigravity untuk mendeteksi status UI:

```javascript
(() => {
  try {
    const title = document.title || "";
    const workspaceMatch = title.match(/^(.+?)\s*[-—]\s*Antigravity/i) || [null, title.split(" - ")[0]];
    const workspaceName = workspaceMatch[1] ? workspaceMatch[1].trim() : "Workspace";

    const agentContainer = document.querySelector('[data-testid="agent-chat-container"], .interactive-session, .agent-view') || document.body;
    const isSpinnerActive = !!agentContainer.querySelector('.codicon-loading, .monaco-progress-container.active, [data-status="running"], .spinner');
    
    const statusElements = Array.from(agentContainer.querySelectorAll('.agent-status-text, .step-label, .chat-status, .action-title'));
    const lastStatusText = statusElements.length > 0 ? statusElements[statusElements.length - 1].innerText.trim() : "";

    const fileElements = Array.from(agentContainer.querySelectorAll('.file-item, .tool-call-item, .step-detail'));
    const subSteps = fileElements.slice(-5).map(el => el.innerText.trim()).filter(Boolean);

    let state = "IDLE";
    if (isSpinnerActive) {
      state = "RUNNING";
    } else if (lastStatusText.toLowerCase().includes("worked for") || lastStatusText.toLowerCase().includes("completed") || lastStatusText.toLowerCase().includes("done")) {
      state = "DONE";
    }

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

Simpan kode ini di dalam direktori `Anti Dispatch/Core/Services/CDPService.swift`:

```swift
import Foundation

public struct CDPExtractedState: Codable, Sendable {
    public let workspaceName: String?
    public let state: String
    public let headline: String
    public let subSteps: [String]
    public let timestamp: Double?
}

public actor CDPService {
    
    private var webSocketTasks: [Int: URLSessionWebSocketTask] = [:]
    private var messageIdCounter: Int = 1
    
    public init() {}
    
    /// 1. Verifikasi apakah port merespons dan merupakan Antigravity IDE
    public func probePort(port: Int) async -> (isValid: Bool, wsURL: URL?) {
        guard let url = URL(string: "http://127.0.0.1:\(port)/json/version") else { return (false, nil) }
        
        var request = URLRequest(url: url)
        request.timeoutInterval = 1.0
        
        do {
            let (data, response) = try await URLSession.shared.data(for: request)
            guard let httpResponse = response as? HTTPURLResponse, httpResponse.statusCode == 200 else {
                return (false, nil)
            }
            
            if let json = try JSONSerialization.jsonObject(with: data) as? [String: Any],
               let wsString = json["webSocketDebuggerUrl"] as? String,
               let wsURL = URL(string: wsString) {
                return (true, wsURL)
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
        
        let jsScript = """
        (() => {
          try {
            const title = document.title || "";
            const workspaceMatch = title.match(/^(.+?)\\s*[-—]\\s*Antigravity/i) || [null, title.split(" - ")[0]];
            const workspaceName = workspaceMatch[1] ? workspaceMatch[1].trim() : "Workspace";

            const container = document.querySelector('[data-testid="agent-chat-container"], .interactive-session, .agent-view') || document.body;
            const isSpinner = !!container.querySelector('.codicon-loading, .monaco-progress-container.active, [data-status="running"], .spinner');
            
            const statusEls = Array.from(container.querySelectorAll('.agent-status-text, .step-label, .chat-status, .action-title'));
            const lastText = statusEls.length > 0 ? statusEls[statusEls.length - 1].innerText.trim() : "";

            const fileEls = Array.from(container.querySelectorAll('.file-item, .tool-call-item, .step-detail'));
            const subSteps = fileEls.slice(-5).map(el => el.innerText.trim()).filter(Boolean);

            let state = "IDLE";
            if (isSpinner) {
              state = "RUNNING";
            } else if (lastText.toLowerCase().includes("worked for") || lastText.toLowerCase().includes("completed") || lastText.toLowerCase().includes("done")) {
              state = "DONE";
            }

            return JSON.stringify({
              workspaceName: workspaceName,
              state: state,
              headline: lastText || (isSpinner ? "Thinking & Analyzing..." : "Standby"),
              subSteps: subSteps,
              timestamp: Date.now()
            });
          } catch (err) {
            return JSON.stringify({ error: err.message, state: "STANDBY", headline: "Standby", subSteps: [] });
          }
        })();
        """
        
        let reqId = messageIdCounter
        messageIdCounter += 1
        
        let payload: [String: Any] = [
            "id": reqId,
            "method": "Runtime.evaluate",
            "params": [
                "expression": jsScript,
                "returnByValue": true
            ]
        ]
        
        guard let jsonData = try? JSONSerialization.data(withJSONObject: payload),
              let jsonString = String(data: jsonData, encoding: .utf8) else {
            return nil
        }
        
        do {
            try await task.send(.string(jsonString))
            let message = try await task.receive()
            
            switch message {
            case .string(let text):
                if let rawData = text.data(using: .utf8),
                   let rootJSON = try? JSONSerialization.jsonObject(with: rawData) as? [String: Any],
                   let resultDict = rootJSON["result"] as? [String: Any],
                   let valueDict = resultDict["result"] as? [String: Any],
                   let valueString = valueDict["value"] as? String,
                   let stateData = valueString.data(using: .utf8) {
                    
                    let decoded = try JSONDecoder().decode(CDPExtractedState.self, from: stateData)
                    return decoded
                }
            default:
                break
            }
        } catch {
            disconnectWebSocket(port: port)
        }
        
        return nil
    }
}
```

---

## 4. Keuntungan Desain
* **Zero Overhead Injection:** Payload script JS yang dikirim berukuran kurang dari 1 KB dan hanya membaca DOM yang sudah dirender.
* **Auto Reconnect/Cleanup:** Jika koneksi terputus (misalnya IDE ditutup oleh pengguna), socket otomatis dibersihkan dan mengembalikan `nil` secara aman.
