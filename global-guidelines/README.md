# Guidelines Directory (`guidelines/`)

Direktori ini adalah urat nadi arsitektur instruksional bagi AI Agent. Ia menampung seluruh batasan (*constraints*), standar koding, prosedur operasional, dan tameng anti-halusinasi yang membedakan *AI Orchestrator* yang solid dengan *AI Prompting* biasa.

## Struktur dan Jenis Pedoman
Dokumen-dokumen di sini terbagi menjadi dua kategori utama:

1. **Pedoman Universal (`coding.md`, `security.md`, dll.)**
   Berisi standar rekayasa perangkat lunak yang berlaku untuk bahasa pemrograman atau jenis aplikasi apa pun (Web, Mobile, Game, API).
2. **Pedoman Spesifik Proyek (`project-context.md`)**
   Berisi aturan eksklusif dan hasil *scan* *codebase* yang hanya valid untuk *node* proyek spesifik yang sedang dikerjakan.

## Siklus Evaluasi Diri (The Retrospective Loop)
Sistem *guidelines* ini terintegrasi erat dengan direktori `retrospectives/` yang terletak di dalam masing-masing *node* (`nodes/[nama-node]/retrospectives/`). Saat AI Agent berulang kali menemukan masalah terkait tumpukan teknologi dari suatu *node*, temuan tersebut akan diekstrak dan dibakukan secara permanen ke dalam file `project-context.md` milik *node* tersebut. Mekanisme ini memastikan AI semakin pintar seiring berjalannya proyek.
