# Modul 02.01: Data Models & Entities

Dokumen ini memandu pembuatan entitas data utama yang merepresentasikan sesi Antigravity IDE, status live agen AI, dan konfigurasi preferensi pengguna.

---

## 1. Kode Swift Lengkap (`WorkspaceSession.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Core/Models/WorkspaceSession.swift`:

```swift
import Foundation

/// Status eksekusi agen AI
public enum AgentExecutionState: String, Codable, Sendable, CaseIterable {
    case standby = "STANDBY"
    case idle = "IDLE"
    case running = "RUNNING"
    case done = "DONE"
    case disconnected = "DISCONNECTED"
    
    public var displayLabel: String {
        switch self {
        case .standby: return "Standby"
        case .idle: return "Idle"
        case .running: return "Thinking & Working..."
        case .done: return "Completed"
        case .disconnected: return "Disconnected"
        }
    }
}

/// Model yang mewakili satu sesi instans Antigravity IDE aktif
public struct WorkspaceSession: Identifiable, Sendable, Equatable {
    public let id: UUID
    public let workspaceName: String
    public let workspaceURL: URL
    public let branchName: String?
    public let pid: pid_t
    public let cdpPort: Int
    public var state: AgentExecutionState
    public var headline: String
    public var subSteps: [String]
    public let createdAt: Date
    public var updatedAt: Date
    
    public init(
        id: UUID = UUID(),
        workspaceName: String,
        workspaceURL: URL,
        branchName: String? = nil,
        pid: pid_t,
        cdpPort: Int,
        state: AgentExecutionState = .standby,
        headline: String = "Standby",
        subSteps: [String] = [],
        createdAt: Date = Date(),
        updatedAt: Date = Date()
    ) {
        self.id = id
        self.workspaceName = workspaceName
        self.workspaceURL = workspaceURL
        self.branchName = branchName
        self.pid = pid
        self.cdpPort = cdpPort
        self.state = state
        self.headline = headline
        self.subSteps = subSteps
        self.createdAt = createdAt
        self.updatedAt = updatedAt
    }
}
```

---

## 2. Model Preferensi Pengguna (`AppPreferences.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Core/Models/AppPreferences.swift`:

```swift
import Foundation
import SwiftUI

public struct AppPreferences {
    @AppStorage("cdpPortRangeStart") public static var cdpPortRangeStart: Int = 9221
    @AppStorage("cdpPortRangeEnd") public static var cdpPortRangeEnd: Int = 9229
    @AppStorage("pollingIntervalRunningMs") public static var pollingIntervalRunningMs: Int = 800
    @AppStorage("pollingIntervalIdleMs") public static var pollingIntervalIdleMs: Int = 3000
    @AppStorage("launchAtLogin") public static var launchAtLogin: Bool = true
    @AppStorage("enableHaptics") public static var enableHaptics: Bool = true
}
```

---

## 3. Penjelasan
* `WorkspaceSession` memenuhi protokol `Identifiable` dan `Sendable` agar kompatibel penuh dengan SwiftUI list and Swift Concurrency.
* `@AppStorage` pada `AppPreferences` menghubungkan nilai konfigurasi ke `UserDefaults` secara otomatis.
