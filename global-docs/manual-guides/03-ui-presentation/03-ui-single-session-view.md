# Modul 03.03: Komponen UI Single Session View

Dokumen ini memandu pembuatan `SingleSessionView.swift` — komponen status untuk skenario 1 proyek aktif.

---

## 1. Fitur Single Session View
1. **Collapsed Mode (Glanceable):** Titik status bercahaya (Cyan untuk `RUNNING`, Hijau untuk `DONE`) + nama proyek + teks ringkas status.
2. **Expanded Mode (On Hover / Click):** Menampilkan detail rincian 3-5 berkas yang sedang dianalisis, tombol lompat ke jendela IDE, dan tombol tutup sesi.

---

## 2. Kode Swift Lengkap (`SingleSessionView.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/Views/SingleSessionView.swift`:

```swift
import SwiftUI

public struct SingleSessionView: View {
    public let session: WorkspaceSession
    @Bindable var viewModel: NotchViewModel
    
    @State private var isHovered: Bool = false
    
    public var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            // Collapsed Header Row
            HStack(spacing: 8) {
                // Status Indicator Dot
                Circle()
                    .fill(statusColor)
                    .frame(width: 8, height: 8)
                    .shadow(color: session.state == .running ? Color.cyan.opacity(0.6) : .clear, radius: 4)
                
                // Workspace & Branch
                HStack(spacing: 4) {
                    Text(session.workspaceName)
                        .font(.system(size: 12, weight: .semibold))
                    
                    if let branch = session.branchName {
                        Text("(\(branch))")
                            .font(.system(size: 10.5, weight: .regular, design: .monospaced))
                            .foregroundStyle(.secondary)
                    }
                }
                
                Spacer()
                
                // Headline Status
                Text(session.headline)
                    .font(.system(size: 11, weight: .regular))
                    .foregroundStyle(session.state == .running ? Color.cyan : .secondary)
                    .lineLimit(1)
                
                // Quick Jump Button
                Button {
                    viewModel.focusSession(session)
                } label: {
                    Image(systemName: "arrow.up.forward.app")
                        .font(.system(size: 11, weight: .medium))
                        .foregroundStyle(.secondary)
                }
                .buttonStyle(.plain)
            }
            
            // Expanded Sub-steps (Tampil saat di-hover atau di-klik)
            if isHovered && !session.subSteps.isEmpty {
                VStack(alignment: .leading, spacing: 3) {
                    Divider().opacity(0.2)
                    
                    ForEach(session.subSteps.prefix(3), id: \.self) { step in
                        HStack(spacing: 4) {
                            Text("•")
                                .foregroundStyle(Color.cyan)
                            Text(step)
                                .font(.system(size: 10, design: .monospaced))
                                .foregroundStyle(.secondary)
                                .lineLimit(1)
                        }
                    }
                }
                .transition(.opacity.combined(with: .move(edge: .top)))
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
        .onHover { hovering in
            withAnimation(.spring(response: 0.3, dampingFraction: 0.8)) {
                self.isHovered = hovering
            }
        }
    }
    
    private var statusColor: Color {
        switch session.state {
        case .running: return Color.cyan
        case .done: return Color.green
        case .idle: return Color.gray
        case .standby: return Color.secondary
        case .disconnected: return Color.red
        }
    }
}
```

---

## 3. Penjelasan
* Transisi `withAnimation(.spring)` memberikan efek ekspansi halus yang nyaman dilihat saat kursor melintasi area Notch.
