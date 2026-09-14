# Modul 03.02: Komponen UI Standby View

Dokumen ini memandu pembuatan `StandbyNotchView.swift` — antarmuka yang muncul saat belum ada instance Antigravity IDE yang aktif.

---

## 1. Spesifikasi Tampilan
* Menampilkan badge logo status standby di kiri.
* Tombol cepat *"Open Project..."* (membuka folder picker).
* Tombol cepat *"New Branch..."* (membuka modal pembuat worktree).
* Background menggunakan material gelap `ultraThinMaterial` dengan aksen border tipis Apple HIG.

---

## 2. Kode Swift Lengkap (`StandbyNotchView.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Presentation/Notch/Views/StandbyNotchView.swift`:

```swift
import SwiftUI

public struct StandbyNotchView: View {
    @Bindable var viewModel: NotchViewModel
    
    public var body: some View {
        HStack(spacing: 12) {
            // Status Icon & Title
            HStack(spacing: 6) {
                Circle()
                    .fill(Color.secondary.opacity(0.5))
                    .frame(width: 8, height: 8)
                
                Text("Standby")
                    .font(.system(size: 11.5, weight: .medium, design: .default))
                    .foregroundStyle(.secondary)
            }
            
            Divider()
                .frame(height: 14)
                .opacity(0.3)
            
            // Action Buttons
            HStack(spacing: 8) {
                Button {
                    viewModel.promptOpenProject()
                } label: {
                    HStack(spacing: 4) {
                        Image(systemName: "folder.badge.plus")
                        Text("Open Project...")
                    }
                    .font(.system(size: 11, weight: .semibold))
                    .padding(.horizontal, 8)
                    .padding(.vertical, 4)
                    .background(Color.white.opacity(0.08))
                    .cornerRadius(6)
                }
                .buttonStyle(.plain)
                
                Button {
                    viewModel.isShowingBranchModal = true
                } label: {
                    HStack(spacing: 4) {
                        Image(systemName: "arrow.triangle.branch")
                        Text("New Branch...")
                    }
                    .font(.system(size: 11, weight: .semibold))
                    .padding(.horizontal, 8)
                    .padding(.vertical, 4)
                    .background(Color.cyan.opacity(0.15))
                    .foregroundStyle(Color.cyan)
                    .cornerRadius(6)
                }
                .buttonStyle(.plain)
            }
        }
        .padding(.horizontal, 14)
        .padding(.vertical, 6)
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
```

---

## 3. Penjelasan Desain
* `UnevenRoundedRectangle`: Menjaga sudut atas tetap rata (`0pt`) agar menempel presisi di tepi bezel layar MacBook, sementara sudut bawah melengkung (`14pt`).
