# Modul 03.04: Komponen UI Multi-Session Accordion

Dokumen ini memandu pembuatan `MultiSessionAccordionView.swift` — antarmuka accordion untuk memantau beberapa agen AI yang berjalan secara paralel di multi-worktree atau multi-project.

---

## 1. Spesifikasi Multi-Session Accordion
1. **Summary Header:** Menampilkan agregat status (misal `● 2 Running  ● 1 Done`).
2. **Accordion Rows:** Daftar sesi yang dapat diperluas untuk melihat detail status masing-masing agen.
3. **Session Actions:** Setiap baris memiliki tombol fokus (*jump to window*) dan tombol tutup sesi (*close instance*).

---

## 2. Kode Swift Lengkap (`MultiSessionAccordionView.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/Views/MultiSessionAccordionView.swift`:

```swift
import SwiftUI

public struct MultiSessionAccordionView: View {
    @Bindable var viewModel: NotchViewModel
    
    @State private var expandedSessionId: UUID? = nil
    
    public var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            // Aggregate Header
            HStack(spacing: 8) {
                let runningCount = viewModel.activeSessions.filter { $0.state == .running }.count
                let doneCount = viewModel.activeSessions.filter { $0.state == .done }.count
                
                HStack(spacing: 4) {
                    Circle()
                        .fill(Color.cyan)
                        .frame(width: 7, height: 7)
                    Text("\(runningCount) Running")
                        .font(.system(size: 11, weight: .semibold))
                }
                
                if doneCount > 0 {
                    HStack(spacing: 4) {
                        Circle()
                            .fill(Color.green)
                            .frame(width: 7, height: 7)
                        Text("\(doneCount) Done")
                            .font(.system(size: 11, weight: .semibold))
                    }
                }
                
                Spacer()
                
                // Add New Branch Button
                Button {
                    viewModel.isShowingBranchModal = true
                } label: {
                    Image(systemName: "plus")
                        .font(.system(size: 10, weight: .bold))
                        .padding(4)
                        .background(Color.white.opacity(0.1))
                        .clipShape(Circle())
                }
                .buttonStyle(.plain)
            }
            
            Divider().opacity(0.2)
            
            // Session Rows List
            VStack(spacing: 4) {
                ForEach(viewModel.activeSessions) { session in
                    SessionRowView(
                        session: session,
                        isExpanded: expandedSessionId == session.id,
                        onToggleExpand: {
                            withAnimation(.spring(response: 0.3, dampingFraction: 0.8)) {
                                if expandedSessionId == session.id {
                                    expandedSessionId = nil
                                } else {
                                    expandedSessionId = session.id
                                }
                            }
                        },
                        onFocus: { viewModel.focusSession(session) },
                        onClose: { viewModel.closeSession(session) }
                    )
                }
            }
        }
        .padding(.horizontal, 14)
        .padding(.vertical, 8)
        .background(.ultraThinMaterial)
        .clipShape(UnevenRoundedRectangle(
            topLeadingRadius: 0,
            bottomLeadingRadius: 14,
            bottomTrailingRadius: 14,
            topTrailingRadius: 0
        ))
        .overlay(
            UnevenRoundedRectangle(
                topLeadingRadius: 0,
                bottomLeadingRadius: 14,
                bottomTrailingRadius: 14,
                topTrailingRadius: 0
            )
            .stroke(Color.white.opacity(0.12), lineWidth: 0.5)
        )
        .sheet(isPresented: $viewModel.isShowingBranchModal) {
            BranchCreatorSheet(viewModel: viewModel)
        }
    }
}

/// Satu baris sesi di dalam Accordion
struct SessionRowView: View {
    let session: WorkspaceSession
    let isExpanded: Bool
    let onToggleExpand: () -> Void
    let onFocus: () -> Void
    let onClose: () -> Void
    
    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            HStack(spacing: 6) {
                // Expand/Collapse Chevron
                Button(action: onToggleExpand) {
                    Image(systemName: isExpanded ? "chevron.down" : "chevron.right")
                        .font(.system(size: 9, weight: .bold))
                        .foregroundStyle(.secondary)
                }
                .buttonStyle(.plain)
                
                // Status Dot
                Circle()
                    .fill(session.state == .running ? Color.cyan : (session.state == .done ? Color.green : Color.gray))
                    .frame(width: 6, height: 6)
                
                // Title
                Text(session.workspaceName)
                    .font(.system(size: 11.5, weight: .medium))
                
                if let branch = session.branchName {
                    Text("[\(branch)]")
                        .font(.system(size: 9.5, design: .monospaced))
                        .foregroundStyle(.secondary)
                }
                
                Spacer()
                
                // Actions
                HStack(spacing: 6) {
                    Button(action: onFocus) {
                        Image(systemName: "arrow.up.forward.app")
                            .font(.system(size: 10))
                    }
                    .buttonStyle(.plain)
                    
                    Button(action: onClose) {
                        Image(systemName: "xmark")
                            .font(.system(size: 9))
                            .foregroundStyle(.secondary)
                    }
                    .buttonStyle(.plain)
                }
            }
            
            // Expanded Sub-steps
            if isExpanded {
                VStack(alignment: .leading, spacing: 2) {
                    Text(session.headline)
                        .font(.system(size: 10, weight: .regular))
                        .foregroundStyle(session.state == .running ? Color.cyan : .secondary)
                    
                    ForEach(session.subSteps.prefix(3), id: \.self) { step in
                        Text("• \(step)")
                            .font(.system(size: 9.5, design: .monospaced))
                            .foregroundStyle(.secondary)
                            .lineLimit(1)
                    }
                }
                .padding(.leading, 18)
                .transition(.opacity)
            }
        }
        .padding(.vertical, 3)
        .padding(.horizontal, 6)
        .background(Color.white.opacity(isExpanded ? 0.05 : 0))
        .cornerRadius(6)
    }
}
```
