# Modul 03.05: Modal UI Branch Creator Sheet

Dokumen ini memandu pembuatan form modal `BranchCreatorSheet.swift` untuk membuat branch dan Git worktree baru secara instan dari GUI.

---

## 1. Fitur Form
* Memilih repositori induk yang sedang aktif atau memilih folder via browser.
* Input nama cabang baru (misal `feat/payment-gateway`).
* Memilih cabang dasar (*Base Branch* default: `main`).
* Tombol aksi *"Launch Branch Instance"* yang memicu `LauncherService` dan `GitWorktreeService`.

---

## 2. Kode Swift Lengkap (`BranchCreatorSheet.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Modals/BranchCreatorSheet.swift`:

```swift
import SwiftUI

public struct BranchCreatorSheet: View {
    @Bindable var viewModel: NotchViewModel
    @Environment(\.dismiss) private var dismiss
    
    @State private var selectedRepoURL: URL? = nil
    @State private var branchName: String = ""
    @State private var baseBranch: String = "main"
    @State private var isLaunching: Bool = false
    @State private var localError: String? = nil
    
    public init(viewModel: NotchViewModel) {
        self.viewModel = viewModel
        // Default gunakan repo dari sesi pertama jika ada
        _selectedRepoURL = State(initialValue: viewModel.activeSessions.first?.workspaceURL)
    }
    
    public var body: some View {
        VStack(alignment: .leading, spacing: 14) {
            // Header
            HStack {
                Text("Launch New Branch Instance")
                    .font(.system(size: 14, weight: .bold))
                Spacer()
                Button {
                    dismiss()
                } label: {
                    Image(systemName: "xmark.circle.fill")
                        .foregroundStyle(.secondary)
                }
                .buttonStyle(.plain)
            }
            
            Divider()
            
            // Repository Selector
            VStack(alignment: .leading, spacing: 4) {
                Text("Target Repository")
                    .font(.system(size: 11, weight: .semibold))
                    .foregroundStyle(.secondary)
                
                HStack {
                    Text(selectedRepoURL?.lastPathComponent ?? "Pilih folder git...")
                        .font(.system(size: 12))
                        .lineLimit(1)
                    
                    Spacer()
                    
                    Button("Browse...") {
                        promptSelectRepo()
                    }
                    .font(.system(size: 11))
                }
                .padding(8)
                .background(Color.white.opacity(0.06))
                .cornerRadius(6)
            }
            
            // Branch Name Input
            VStack(alignment: .leading, spacing: 4) {
                Text("New Branch Name")
                    .font(.system(size: 11, weight: .semibold))
                    .foregroundStyle(.secondary)
                
                TextField("contoh: feat/user-auth", text: $branchName)
                    .textFieldStyle(.roundedBorder)
                    .font(.system(size: 12, design: .monospaced))
            }
            
            // Base Branch Selector
            VStack(alignment: .leading, spacing: 4) {
                Text("Base Branch")
                    .font(.system(size: 11, weight: .semibold))
                    .foregroundStyle(.secondary)
                
                Picker("", selection: $baseBranch) {
                    Text("main").tag("main")
                    Text("master").tag("master")
                }
                .pickerStyle(.segmented)
            }
            
            if let err = localError {
                Text(err)
                    .font(.system(size: 10.5))
                    .foregroundStyle(Color.red)
            }
            
            // Action Buttons
            HStack {
                Spacer()
                
                Button("Cancel") {
                    dismiss()
                }
                .keyboardShortcut(.cancelAction)
                
                Button {
                    launchBranch()
                } label: {
                    if isLaunching {
                        ProgressView()
                            .controlSize(.small)
                    } else {
                        Text("Launch Instance")
                            .fontWeight(.semibold)
                    }
                }
                .buttonStyle(.borderedProminent)
                .tint(Color.cyan)
                .disabled(branchName.trimmingCharacters(in: .whitespaces).isEmpty || selectedRepoURL == nil || isLaunching)
                .keyboardShortcut(.defaultAction)
            }
        }
        .padding(18)
        .frame(width: 380)
    }
    
    private func promptSelectRepo() {
        let panel = NSOpenPanel()
        panel.canChooseFiles = false
        panel.canChooseDirectories = true
        panel.allowsMultipleSelection = false
        
        if panel.runModal() == .OK, let url = panel.url {
            self.selectedRepoURL = url
        }
    }
    
    private func launchBranch() {
        guard let repoURL = selectedRepoURL else { return }
        let cleanBranch = branchName.trimmingCharacters(in: .whitespaces)
        
        isLaunching = true
        localError = nil
        
        Task {
            await viewModel.launchProject(at: repoURL, branch: cleanBranch)
            isLaunching = false
            if viewModel.errorMessage == nil {
                dismiss()
            } else {
                self.localError = viewModel.errorMessage
            }
        }
    }
}
```
