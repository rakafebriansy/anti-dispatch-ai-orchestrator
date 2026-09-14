# Modul 04.02: Window Focus & App Lifecycle

Dokumen ini memandu penyusunan `AppDelegate.swift` dan `Anti_DispatchApp.swift` untuk mengelola inisialisasi seluruh controller (`NotchPanelController`, `StatusBarController`, `NotchViewModel`) dan aktivasi jendela IDE instan via PID (`NSRunningApplication.activate`).

---

## 1. Kode Swift Lengkap (`AppDelegate.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/App/AppDelegate.swift`:

```swift
import AppKit
import SwiftUI

public final class AppDelegate: NSObject, NSApplicationDelegate {
    
    public var viewModel: NotchViewModel!
    public var notchController: NotchPanelController!
    public var statusBarController: StatusBarController!
    
    public func applicationDidFinishLaunching(_ notification: Notification) {
        // 1. Inisialisasi ViewModel utama
        self.viewModel = NotchViewModel()
        
        // 2. Inisialisasi Floating Notch Panel
        self.notchController = NotchPanelController(viewModel: viewModel)
        
        // 3. Inisialisasi Menu Bar Controller
        self.statusBarController = StatusBarController(viewModel: viewModel)
    }
    
    public func applicationShouldHandleReopen(_ sender: NSApplication, hasVisibleWindows flag: Bool) -> Bool {
        notchController.updatePosition()
        return true
    }
}
```

---

## 2. Kode Swift Lengkap (`Anti_DispatchApp.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/App/Anti_DispatchApp.swift`:

```swift
import SwiftUI

@main
struct Anti_DispatchApp: App {
    @NSApplicationDelegateAdaptor(AppDelegate.self) var appDelegate
    
    var body: some Scene {
        // Menggunakan Settings scene agar tidak memunculkan window default macOS
        Settings {
            PreferencesView()
        }
    }
}

struct PreferencesView: View {
    @AppStorage("cdpPortRangeStart") private var portStart: Int = 9221
    @AppStorage("cdpPortRangeEnd") private var portEnd: Int = 9229
    @AppStorage("pollingIntervalRunningMs") private var pollRunning: Int = 800
    @AppStorage("pollingIntervalIdleMs") private var pollIdle: Int = 3000
    
    var body: some View {
        Form {
            Section("CDP Port Range") {
                TextField("Start Port", value: $portStart, format: .number)
                TextField("End Port", value: $portEnd, format: .number)
            }
            
            Section("Adaptive Polling Intervals (ms)") {
                TextField("Running Interval (ms)", value: $pollRunning, format: .number)
                TextField("Idle Interval (ms)", value: $pollIdle, format: .number)
            }
        }
        .padding(20)
        .frame(width: 350)
    }
}
```

---

## 3. Penjelasan
* `@NSApplicationDelegateAdaptor` menghubungkan siklus hidup AppKit `NSApplicationDelegate` secara mulus ke dalam siklus hidup SwiftUI `App`.
* Mengganti `WindowGroup` dengan `Settings` mencegah macOS memunculkan jendela kosong putih saat aplikasi baru pertama kali dibuka.
