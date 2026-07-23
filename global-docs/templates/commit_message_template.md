<type>(<scope>): <subject>

<body>

<footer>

---

# CONTOH PENULISAN (DILARANG DIMASUKKAN KE COMMIT MESSAGE):
# Type Options:
# - feat: Menambah fitur baru
# - fix: Memperbaiki bug
# - docs: Perubahan terkait dokumentasi (README, JSDoc, dll)
# - style: Perubahan formatting, tidak mengubah logika (spasi, titik koma)
# - refactor: Refactoring kode tanpa mengubah fungsionalitas
# - test: Menambahkan/mengubah skrip testing
# - chore: Perbaikan build process, dependencies, tooling
# 
# ATURAN ISTILAH ORCHESTRATOR (contoh: ticket, prd, design system, guideline, dll):
# - Project/Node: DILARANG menggunakan istilah orchestrator pada seluruh bagian commit.
# - Orchestrator: WAJIB menggunakan istilah orchestrator sebagai scope (contoh: docs(template): update template).

# Format Penuh:
# feat(auth): add google oauth integration
# 
# Mengimplementasikan alur masuk menggunakan kredensial Google, 
# menyimpan token ke secure storage lokal.
# 
# Resolves TICKET-02
