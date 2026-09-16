# Modul 04.01: Menu Bar Status Bar Controller

Dokumen ini memandu pembuatan `StatusBarController.swift` menggunakan AppKit `NSStatusItem` untuk menyediakan titik akses sekunder di Menu Bar macOS di samping Floating Notch HUD.

---

## 1. Fitur Menu Bar
* Menampilkan ikon status di Menu Bar macOS.
* Menu dropdown berisi:
  - Ringkasan sesi aktif Antigravity.
  - Opsi *"Open Project..."*.
  - Opsi *"New Branch Worktree..."*.
  - Opsi *"Preferences..."*.
  - Opsi *"Quit Anti Dispatch"*.

---

## 2. Kode Swift Lengkap (`StatusBarController.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/MenuBar/StatusBarController.swift`:

```swift
import AppKit
import SwiftUI

@MainActor
public final class StatusBarController: NSObject {
    
    private var statusItem: NSStatusItem?
    private let viewModel: NotchViewModel
    
    public init(viewModel: NotchViewModel) {
        self.viewModel = viewModel
        super.init()
        setupStatusBar()
    }
    
    public func setupStatusBar() {
        statusItem = NSStatusBar.system.statusItem(withLength: NSStatusItem.variableLength)
        
        if let button = statusItem?.button {
            button.image = NSImage(systemSymbolName: "point.topleft.filled.down.to.point.bottomright.curvepath", accessibilityDescription: "Anti Dispatch")
            button.image?.isTemplate = true
        }
        
        updateMenu()
    }
    
    public func updateMenu() {
        let menu = NSMenu()
        
        // Header
        let titleItem = NSMenuItem(title: "Anti Dispatch", action: nil, keyEquivalent: "")
        titleItem.attributedTitle = NSAttributedString(
            string: "Anti Dispatch",
            attributes: [.font: NSFont.boldSystemFont(ofSize: 13)]
        )
        menu.addItem(titleItem)
        menu.addItem(NSMenuItem.separator())
        
        // Active Sessions Section
        if viewModel.activeSessions.isEmpty {
            let emptyItem = NSMenuItem(title: "No Active Agents (Standby)", action: nil, keyEquivalent: "")
            emptyItem.isEnabled = false
            menu.addItem(emptyItem)
        } else {
            for session in viewModel.activeSessions {
                let sessionTitle = "\(session.workspaceName) - \(session.state.displayLabel)"
                let item = NSMenuItem(title: sessionTitle, action: #selector(sessionItemClicked(_:)), keyEquivalent: "")
                item.target = self
                item.representedObject = session
                menu.addItem(item)
            }
        }
        
        menu.addItem(NSMenuItem.separator())
        
        // Actions
        let openProj = NSMenuItem(title: "Open Project...", action: #selector(openProjectAction), keyEquivalent: "o")
        openProj.target = self
        menu.addItem(openProj)
        
        let newBranch = NSMenuItem(title: "New Branch Worktree...", action: #selector(newBranchAction), keyEquivalent: "b")
        newBranch.target = self
        menu.addItem(newBranch)
        
        menu.addItem(NSMenuItem.separator())
        
        // Quit
        let quitItem = NSMenuItem(title: "Quit Anti Dispatch", action: #selector(quitAction), keyEquivalent: "q")
        quitItem.target = self
        menu.addItem(quitItem)
        
        statusItem?.menu = menu
    }
    
    @objc private func sessionItemClicked(_ sender: NSMenuItem) {
        if let session = sender.representedObject as? WorkspaceSession {
            viewModel.focusSession(session)
        }
    }
    
    @objc private func openProjectAction() {
        viewModel.promptOpenProject()
    }
    
    @objc private func newBranchAction() {
        viewModel.isShowingBranchModal = true
    }
    
    @objc private func quitAction() {
        NSApplication.shared.terminate(nil)
    }
}
```
