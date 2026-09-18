---
id: TICKET-09
title: [BUG] Fix Waiting State Permission Prompt Detection (Domain and Proxy Prompts)
status: Done
priority: High
labels: [Bug, CDP, State Management, Decision Modal, Prompt Extraction]
---

# Laporan Bug

## Deskripsi Masalah
Sesi Antigravity yang sedang menunggu keputusan atau izin pengguna (*waiting for user decision/permission*), seperti pada prompt izin *"Allow testing proxy endpoint on fashionforth.id?"*, secara keliru dideteksi sebagai `DONE` (Completed). Hal ini disebabkan oleh evaluasi string tombol keputusan `isDecisionButton` yang gagal mengenali variasi tombol berkoma (`"Yes, allow this time"`), domain dinamis, ketiadaan selector radio semantik ARIA (`[role="radio"]`), pembatasan scope pencarian hanya pada class modal tertentu dan `targetTurn`, sehingga saat agen berhenti tanpa spinner/stop button, status langsung jatuh ke `DONE` akibat adanya riwayat teks balasan asisten sebelumnya.

## Langkah Reproduksi (Steps to Reproduce)
1. Buka instance Antigravity pada repositori `fashionforth-org/ff-prod`.
2. Berikan prompt atau instruksi yang memicu pengujian proxy/domain jaringan atau izin eksekusi tool.
3. Antigravity memunculkan prompt konfirmasi: *"Allow testing proxy endpoint on fashionforth.id?"* dengan opsi tombol/radio seperti *"Yes, allow this time"*.
4. Buka HUD Anti Dispatch atau periksa menu status bar.
5. Status sesi tercatat sebagai `Done` (*Completed*), bukan `Waiting` (*Decision Required*).

## Ekspektasi (Expected Behavior)
Anti Dispatch harus secara akurat mengenali prompt izin domain/proxy dinamis, mengekstrak pertanyaan *"Allow testing proxy endpoint on fashionforth.id?"* sebagai `decisionPrompt` dan `headline`, serta menyetel state sesi menjadi `WAITING` sehingga modal HUD `Decision Required` muncul di layar pengguna.

## Kondisi Aktual (Actual Behavior)
Status sesi langsung jatuh (*fallthrough*) ke `DONE` karena `isWaitingForInput` bernilai `false`, `isRunning` bernilai `false` (spinner/stop button berhenti), dan `latestResponse` terisi dari balasan teks sebelumnya.

---

## Acceptance Criteria (Kriteria Penyelesaian Bug)
*Daftar kriteria mutlak agar perbaikan bug ini dianggap valid.*
- [x] Fungsi `isDecisionButton` menormalisasi tanda baca, nomor urut, dan spasi sebelum mencocokkan kata kunci izin (mendukung format *"Yes, allow..."*, *"Allow on <domain>"*, *"Trust"*, *"Accept"*, *"Run"*, dll.).
- [x] Selector radio dan checkbox mendukung elemen semantik ARIA modern (`[role="radio"]`, `[role="checkbox"]`, `[data-state="checked"]`).
- [x] Deteksi kontainer izin memindai seluruh `agentPanel` untuk kartu inline yang memuat kalimat pertanyaan (berakhiran `?` atau diawali `Allow `, `Confirm `, `Permission `) dengan tombol interaktif.
- [x] `decisionPrompt` mengekstrak pertanyaan izin secara presisi tanpa tertimpa konten non-pertanyaan.
- [x] Logika pada `extract_state.js` dan fallback `CDPService.swift` disinkronkan 100%.
- [x] Unit test baru ditambahkan di `Anti_DispatchTests.swift` dan seluruh test suite lulus tanpa regresi.

## Target Lingkup File (Affected Files)
*Daftar path file yang dicurigai menjadi penyebab atau berpotensi diubah.*
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH memperbaiki bug ini.*

- **Penyebab Akar (Root Cause Analysis):**
  - Evaluasi kaku `txt.startsWith(k + ' ')` gagal pada tombol berkoma (`"Yes, allow"`).
  - Selector `activeModals` dan `permissionCards` terlalu eksklusif terhadap inline tool call card dengan class Tailwind.
  - Query radio terbatas pada `<input type="radio">` native dan mengabaikan `[role="radio"]` ARIA.
  - State fallthrough prematur ke `DONE` saat `isWaitingForInput` dan `isRunning` false tetapi `latestResponse` ada.
- **Langkah Perbaikan:**
  1. Memperbarui `isDecisionButton` dengan tokenizer pembersih tanda baca (`replace(/[()\[\]{}\/,\.;:!]/g, ' ')`), nomor urut, dan simbol untuk mendukung tombol koma (`"Yes, allow this time"`), domain dinamis (`"Allow on fashionforth.id"`), dan kata kerja otorisasi (`"trust"`, `"accept"`, `"authorize"`).
  2. Menambahkan dukungan penuh radio/checkbox semantik ARIA (`[role="radio"]`, `[role="checkbox"]`, `[data-state="checked"]`).
  3. Memperluas pemindaian kontainer kartu inline di seluruh `agentPanel` untuk kartu dengan kalimat pertanyaan berakhiran `?` atau diawali `Allow ` yang memiliki tombol aksi interaktif.
  4. Menyempurnakan pemindaian `decisionPrompt` dengan mengutamakan elemen pertanyaan berakhiran `?` atau diawali `Allow `.
  5. Menyelaraskan seluruh logika JS pada `extract_state.js` dan `CDPService.swift`.
  6. Menambahkan unit test `testProxyPermissionPromptDetectedAsWaiting` pada `Anti_DispatchTests.swift` dan memverifikasi kelulusan test suite Xcode (100% passed).
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
