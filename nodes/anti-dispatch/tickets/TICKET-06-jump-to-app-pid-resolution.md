---
id: TICKET-06
title: Fix Jump to App PID Resolution via TCP LISTEN Filtering
status: Done
priority: High
labels: [Bug, Process Management, Notch, Window Focus, Jump]
---

# Deskripsi
Mengatasi kegagalan aksi *Jump to App* (fokus jendela Antigravity) yang terjadi saat Antigravity dibuka setelah Anti Dispatch berjalan (di mana PID Antigravity > PID Anti Dispatch). Masalah ini berakar pada perintah `lsof -ti :<port>` yang mengambil semua socket pada port tersebut (termasuk koneksi WebSocket client dari Anti Dispatch sendiri). Akibatnya, Anti Dispatch mengembalikan PID dirinya sendiri dan memicu aktivasi ke dirinya sendiri alih-alih mengangkat jendela Antigravity.

## Acceptance Criteria (Kriteria Penerimaan)
*Daftar spesifikasi mutlak (checklists) yang harus terpenuhi agar tiket ini sah dianggap berstatus selesai (Definition of Done).*
- [x] Perintah `lsof` pada `getAllPidsForPort(_:)` dibatasi secara ketat hanya mengambil socket dengan status `LISTEN` melalui flag `["-tiTCP:\(port)", "-sTCP:LISTEN", "-n", "-P"]`.
- [x] PID aplikasi Anti Dispatch sendiri (`ProcessInfo.processInfo.processIdentifier`) difilter secara eksplisit dari array hasil `getAllPidsForPort(_:)`.
- [x] Fungsi `getPidForPort(_:)` dan `getAllPidsForPort(_:)` pada `NotchViewModel` mengembalikan PID server Antigravity secara akurat.
- [x] Unit test `testPidDiscoveryExcludesSelf` ditambahkan pada `Anti_DispatchTests.swift` dan seluruh test suite lulus (16/16 passed).

## Target Lingkup File (Affected Files)
*Daftar path file yang diinstruksikan atau berpotensi diubah sebagai referensi utama eksekusi AI.*
- `Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
- `Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH mengeksekusi tiket ini.*

- **Langkah Teknis Tereksekusi:**
  1. Mengidentifikasi pemanggilan `lsof` pada `NotchViewModel.getAllPidsForPort` yang tidak memiliki filter state TCP sehingga mencocokkan socket client WebSocket Anti Dispatch.
  2. Mengubah argumen `lsof` menjadi `["-tiTCP:\(port)", "-sTCP:LISTEN", "-n", "-P"]` untuk memastikan hanya proses server yang me-LISTEN yang diambil.
  3. Menambahkan filter pengecualian `myPid` (`ProcessInfo.processInfo.processIdentifier`) pada hasil pemindaian PID.
  4. Menambahkan unit test `testPidDiscoveryExcludesSelf` pada `Anti_DispatchTests.swift` dan memverifikasi kelulusan 16/16 test suite.
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Presentation/Notch/ViewModels/NotchViewModel.swift`
  - `Anti DispatchTests/Anti_DispatchTests.swift`
- **Catatan & Keputusan Arsitektural (Jika Ada):**
  - Isolasi socket via `-sTCP:LISTEN` menjamin ketahanan terhadap kondisi koneksi concurrent di mana banyak client membuka socket ke port CDP yang sama.
