---
id: TICKET-10
title: [BUG] Fix Working Goal State Precedence and False Positive Waiting Classification
status: Done
priority: High
labels: [Bug, CDP, State Management, Goal Subagent, Precedence]
---

# Laporan Bug

## Deskripsi Masalah
Saat Antigravity sedang aktif bekerja (*Working...*) mengeksekusi Goal atau Browser Subagent (seperti *"Goal: Navigate to https://fashionforth.id and inspect the console logs, network requests, and popup modal for ContentPromoRegister..."*), Anti Dispatch secara keliru mendeteksi sesi sebagai `WAITING` (*Decision Required*) dan memunculkan pop-up modal Decision HUD dengan isi teks instruksi Goal tersebut. Hal ini terjadi karena: (1) `activeModals` menangkap sembarang dialog atau subagent panel tanpa memverifikasi tombol di dalamnya adalah tombol keputusan izin, (2) keyword umum seperti `'submit'`, `'skip'`, `'no'` pada `targetTurn` memicu false positive `isDecisionButton`, (3) `isWaitingForInput` membajak status `RUNNING` secara mutlak meskipun spinner dan tombol Stop sedang aktif, dan (4) parser teks `lines.find(...)` mengekstrak paragraf deskripsi Goal menjadi `decisionPrompt`.

## Langkah Reproduksi (Steps to Reproduce)
1. Berikan tugas Goal atau pemicu subagent browser pada Antigravity yang memuat deskripsi instruksi panjang.
2. Antigravity mulai mengeksekusi tugas (menampilkan teks "Working..." dan tombol Stop aktif).
3. Anti Dispatch membuka pop-up HUD "Decision Required" dengan teks Goal tersebut dan indikator status menjadi kuning/Waiting, padahal agen sedang sibuk bekerja.

## Ekspektasi (Expected Behavior)
Selama agen sedang aktif bekerja (memiliki spinner aktif, tombol Stop aktif, atau task execution aktif) dan tidak terdapat dialog persetujuan eksplisit, status sesi harus tetap stabil di `RUNNING` (*Working...*), tidak memicu HUD Decision Required, dan tidak mengekstrak instruksi Goal sebagai prompt keputusan.

## Kondisi Aktual (Actual Behavior)
Status sesi berubah menjadi `WAITING` dan HUD Decision Required muncul dengan teks prompt Goal panjang.

---

## Acceptance Criteria (Kriteria Penyelesaian Bug)
*Daftar kriteria mutlak agar perbaikan bug ini dianggap valid.*
- [x] Pemeriksaan `activeModals` memvalidasi keberadaan tombol keputusan nyata (`modalBtns.some(isDecisionButton)`) atau opsi radio semantik sebelum menganggapnya sebagai `decisionContainer`.
- [x] Keyword `isDecisionButton` dibersihkan dari kata-kata umum non-keputusan (`submit`, `no`) yang dapat memicu false positive pada chat input atau tombol status.
- [x] Hierarki status memprioritaskan `RUNNING` ketika tombol Stop (`hasStopButton`) atau spinner (`hasSpinner`) aktif, kecuali ada modal izin eksplisit yang terverifikasi.
- [x] Logika ekstraksi prompt mengabaikan tag `<strong>Goal</strong>` dan paragraf instruksi agen non-keputusan.
- [x] Logika pada `extract_state.js` dan `CDPService.swift` disinkronkan 100%.
- [x] Unit test baru ditambahkan di `Anti_DispatchTests.swift` dan seluruh test suite lulus (17/17 passed).

## Target Lingkup File (Affected Files)
*Daftar path file yang dicurigai menjadi penyebab atau berpotensi diubah.*
- `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
- `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
- `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`

---

## AI Execution Log & Output
*⚠️ Peringatan untuk AI Agent: Bagian ini KHUSUS diisi oleh Anda SAAT dan SETELAH memperbaiki bug ini.*

- **Penyebab Akar (Root Cause Analysis):**
  - `activeModals` menerima sembarang dialog hanya dengan `modalBtns.length > 0`.
  - False positive pada keyword `'submit'` (chat input) dan `'no'` (status badge).
  - Precedence `isWaitingForInput` membajak `isRunning = hasStopButton || hasSpinner...`.
  - Parser prompt mengambil paragraf pertama di bawah judul Goal.
- **Langkah Perbaikan:**
  1. Menambahkan validasi `isDecisionButton` dan radio semantik pada evaluasi `activeModals` agar dialog non-keputusan (seperti subagent viewer, goal panel, monaco modal) tidak dianggap sebagai decision container.
  2. Mengecualikan tombol chat input, terminal input, dan monaco toolbar dari evaluasi `isDecisionButton`.
  3. Memperbaiki hierarki state precedence di mana `RUNNING` dipertahankan saat `hasStopButton` atau `hasSpinner` aktif dan tidak ada `hasExplicitDecisionModal`.
  4. Menyaring string `Goal`, `Task`, `Thinking`, `Working` dari parser judul `promptEl` dan `lines.find` agar tidak dijadikan `decisionPrompt`.
  5. Menyelaraskan seluruh logika JS pada inline fallback `CDPService.swift`.
  6. Menambahkan unit test `testActiveGoalExecutionClassifiedAsRunning` pada `Anti_DispatchTests.swift` dan memverifikasi kelulusan seluruh test suite Xcode (100% passed).
- **Ringkasan File Terpengaruh:**
  - `Anti Dispatch/Anti Dispatch/Resources/Scripts/extract_state.js`
  - `Anti Dispatch/Anti Dispatch/Core/Services/CDPService.swift`
  - `Anti Dispatch/Anti DispatchTests/Anti_DispatchTests.swift`
