# Hermes — Personal Operating Agent

**Status:** Draft

**Owner:** Pepe

**Lokasi:** `C:\Pepe Project\Agent\Hermes\`

## Current Implementation Status

**Priority 0 — Preservation and safety foundation: complete with owner-approved backup exception; physical-drive disaster recovery is unavailable.**

Control documents:

- [Asset manifest](docs/inventory/asset-manifest.yaml)
- [Audit notes](docs/inventory/audit-notes.md)
- [Permission policy](docs/policies/permissions.md)
- [Secret policy](docs/policies/secrets.md)
- [Legacy preservation policy](docs/policies/preservation.md)
- [Rollback procedure](docs/recovery/rollback.md)
- [Project registry](registry/projects.yaml)
- [Approved design](docs/superpowers/specs/2026-08-15-priority-0-preservation-safety-design.md)
- [Implementation plan](docs/superpowers/plans/2026-08-15-priority-0-preservation-safety.md)

**Tujuan utama:** Menjadi satu-satunya agent pribadi yang dikembangkan dan digunakan Pepe untuk berpikir, merencanakan, melakukan riset, mengelola proyek, menulis PRD, mengerjakan coding, dan menjalankan pekerjaan rutin secara otomatis.

---

## 1. Ringkasan

Hermes adalah personal operating agent milik Pepe.

Hermes dibuat untuk menggantikan pengalaman bekerja dengan banyak agent terpisah seperti Sage, Scout, dan Rex. Kemampuan terbaik dari agent-agent tersebut tidak dibuang, tetapi dikonsolidasikan menjadi skill, knowledge, dan workflow di dalam satu agent.

Pepe hanya perlu berbicara dengan Hermes. Hermes bertanggung jawab memahami maksud percakapan, memilih kemampuan yang diperlukan, menggunakan model dan tools yang sesuai, menjalankan pekerjaan, menyimpan konteks, serta melaporkan hasilnya.

Target pengalaman pengguna:

```text
Pepe
  ↓
Hermes
  ├── berpikir dan berdiskusi
  ├── melakukan riset
  ├── menyusun strategi
  ├── menulis PRD
  ├── mengelola backlog
  ├── mengerjakan coding
  ├── menjalankan test
  ├── membuat draft pull request
  ├── memonitor topik dan proyek
  └── melaporkan hasil
```

Hermes adalah satu agent dengan banyak kemampuan modular, bukan kumpulan agent yang harus dikelola secara terpisah.

---

## 2. Mengapa Hermes Dibuat

Sistem agent Pepe sebelumnya mempunyai spesialisasi yang kuat:

- Sage sebagai sparring partner, PRD writer, dan orchestrator.
- Scout sebagai research agent, trend scout, dan learning coach.
- Rex sebagai engineering executor.
- Pixel sebagai konsep design generator.
- Chimit sebagai CMO virtual untuk domain hospitality.

Sistem tersebut membuktikan bahwa pembagian peran dapat meningkatkan kualitas. Namun, semakin banyak agent juga menciptakan beban operasional:

- banyak persona dan instruksi yang harus disinkronkan;
- banyak permission dan credential yang harus dipelihara;
- beberapa gateway dan runtime;
- handoff antar-agent;
- session routing;
- status yang tersebar;
- memory yang terfragmentasi;
- perpindahan channel untuk pekerjaan yang sebenarnya saling berkaitan;
- waktu maintenance yang semakin besar.

Pepe lebih nyaman berbicara dengan satu pihak. Pepe juga tidak ingin harus menentukan apakah sebuah permintaan harus diberikan kepada Sage, Scout, atau Rex.

Hermes dibuat untuk mengurangi beban tersebut.

Prinsip utamanya:

> Satu agent untuk diajak berbicara, satu tempat untuk menyimpan konteks, dan satu sistem untuk menjalankan pekerjaan.

---

## 3. Visi

Hermes menjadi partner kerja pribadi yang memahami Pepe, proyek-proyeknya, cara berpikirnya, dan standar kualitasnya.

Hermes diharapkan mampu membantu seluruh siklus pekerjaan:

```text
Ide
→ diskusi
→ challenge
→ riset
→ strategi
→ keputusan
→ PRD
→ implementasi
→ testing
→ draft pull request
→ review
→ pembelajaran
```

Dalam jangka panjang, Hermes dapat bekerja secara proaktif ketika PC menyala:

- membaca backlog;
- memilih pekerjaan yang aman;
- melanjutkan task yang belum selesai;
- melakukan riset;
- mengerjakan kode;
- menjalankan test;
- membuat draft pull request;
- memonitor proyek;
- mengirim laporan;
- berhenti ketika membutuhkan keputusan manusia.

Hermes tidak menggantikan Pepe sebagai product owner. Hermes mengurangi pekerjaan operasional agar Pepe dapat berfokus pada keputusan, taste, prioritas, dan arah produk.

---

## 4. Sasaran

Hermes memiliki sasaran berikut:

1. Menjadi satu-satunya agent utama yang digunakan Pepe.
2. Mengurangi kebutuhan memelihara banyak agent dan gateway.
3. Menyatukan konteks strategi, riset, PRD, dan implementasi.
4. Mempertahankan kemampuan terbaik Sage, Scout, dan Rex sebagai skills.
5. Mendukung percakapan melalui teks dan suara.
6. Menggunakan GPT sebagai model utama.
7. Menggunakan Codex atau coding tool lain untuk pekerjaan engineering.
8. Menjalankan pekerjaan terjadwal ketika PC aktif.
9. Mengelola beberapa proyek tanpa mencampurkan konteksnya.
10. Belajar dari pola kerja Pepe melalui memory dan skill yang terkontrol.
11. Mengurangi kebutuhan copy-paste, dispatch manual, dan perpindahan channel.
12. Menghasilkan pekerjaan yang dapat ditinjau, dilacak, dan dibatalkan.

---

## 5. Bukan Tujuan Hermes

Hermes tidak dirancang untuk:

- mengambil keputusan bisnis besar tanpa Pepe;
- melakukan merge atau deployment production tanpa izin;
- membeli layanan atau melakukan transaksi;
- mengirim komunikasi eksternal sensitif tanpa approval;
- menyimpan password atau API key dalam memory;
- mengubah seluruh workflow-nya sendiri tanpa governance;
- menjalankan pekerjaan tanpa backlog atau tujuan yang jelas;
- mencampurkan semua informasi proyek ke dalam satu konteks tanpa struktur;
- menggantikan review manusia untuk keputusan yang sulit dibalik;
- menjadi autonomous agent tanpa batas.

Hermes diharapkan proaktif, tetapi tetap bekerja di dalam batas wewenang yang jelas.

---

## 6. Prinsip Desain

### 6.1 Satu agent, kemampuan modular

Hermes hanya memiliki satu identitas utama. Kemampuan khusus dimuat sebagai skill ketika dibutuhkan.

Contoh:

```text
Permintaan strategi
→ load strategic-sparring skill

Permintaan riset
→ load research dan hype-filter skill

Permintaan PRD
→ load prd-writer skill

Permintaan coding
→ load coding-project skill

Voice note
→ transcribe
→ proses sebagai pesan biasa
```

### 6.2 Satu pintu percakapan

Pepe tidak perlu memilih agent.

Hermes harus memahami perintah natural seperti:

- “Challenge ide ini.”
- “Coba riset dulu.”
- “Buatkan PRD.”
- “Jangan coding dulu.”
- “Sekarang kerjakan.”
- “Clone repo ini dan lanjutkan.”
- “Bikin draft PR.”
- “Pantau setiap hari.”
- “Simpan keputusan ini.”
- “Lanjutkan pekerjaan semalam.”

### 6.3 Project context tetap dipisahkan

Walaupun agent-nya satu, setiap proyek harus memiliki:

- tujuan;
- repository;
- roadmap;
- backlog;
- keputusan;
- knowledge;
- status;
- definition of done;
- permission policy.

Hermes harus mengetahui proyek mana yang sedang aktif sebelum melakukan perubahan.

### 6.4 Pekerjaan harus dapat dilacak

Setiap pekerjaan penting minimal memiliki:

```text
Project
Objective
Current status
Skill yang digunakan
Repository atau workspace
Actions performed
Verification result
Approval required
Next action
```

### 6.5 Reversible by default

Hermes boleh bergerak mandiri untuk tindakan yang mudah dibatalkan.

Tindakan yang sulit dibalik harus meminta approval.

### 6.6 Memory bukan tempat menyimpan semuanya

Memory hanya menyimpan informasi yang mempunyai nilai jangka panjang:

- preferensi Pepe;
- keputusan penting;
- tujuan proyek;
- pola kerja;
- constraint;
- pelajaran yang sudah diverifikasi.

Log build, output terminal panjang, dan detail task sementara tidak masuk ke long-term memory.

---

## 7. Kemampuan Utama

### 7.1 Personal conversation

Hermes menjadi tempat Pepe:

- berpikir;
- brainstorming;
- bertanya;
- menyampaikan ide mentah;
- mengevaluasi pilihan;
- membuat keputusan;
- merefleksikan pekerjaan.

Hermes menggunakan Bahasa Indonesia casual sebagai default dan dapat menggunakan English profesional untuk dokumen formal.

### 7.2 Strategic sparring

Kemampuan ini mengadopsi kekuatan utama Sage.

Hermes dapat:

- menguji asumsi;
- menantang ide;
- membedakan problem dan solution;
- menyusun opsi;
- membandingkan trade-off;
- mengidentifikasi risiko;
- membantu prioritas;
- memberikan rekomendasi;
- menggunakan corpus dan mental model yang relevan.

Knowledge Sage yang masih bernilai dipertahankan sebagai reference library, bukan dimasukkan seluruhnya ke system prompt.

### 7.3 Research dan intelligence

Kemampuan ini mengadopsi kekuatan utama Scout.

Hermes dapat:

- melakukan web research;
- memonitor perkembangan AI, OTT, dan product management;
- menyaring hype;
- menilai relevansi;
- menghasilkan daily brief;
- memonitor podcast;
- menyimpan insight;
- mencari kembali insight lama;
- menghubungkan riset dengan proyek aktif.

### 7.4 PRD dan project management

Hermes dapat:

- mendeteksi ketika sebuah ide sudah cukup matang;
- membuat PRD;
- menggunakan schema dan template yang konsisten;
- menyimpan PRD ke proyek yang benar;
- membuat backlog;
- memecah pekerjaan menjadi task;
- mengelola status;
- mengidentifikasi dependency dan blocker;
- mengintegrasikan ClickUp jika masih diperlukan.

### 7.5 Engineering execution

Kemampuan ini mengadopsi fungsi Rex.

Hermes dapat:

- membaca repository;
- clone repository;
- membuat branch;
- membaca PRD;
- membuat implementation plan;
- menggunakan Codex atau Claude Code;
- mengubah kode;
- menjalankan lint;
- menjalankan test;
- menjalankan build;
- melakukan visual verification;
- memperbaiki kegagalan;
- membuat commit;
- push ke branch yang diizinkan;
- membuat draft pull request;
- membuat laporan blocker.

Hermes tidak melakukan merge atau production deployment tanpa approval.

### 7.6 Voice interaction

Pepe lebih nyaman berbicara daripada mengetik. Karena itu voice adalah bagian penting dari pengalaman Hermes.

Target tahap awal:

```text
Voice note
→ local transcription
→ Hermes memproses sebagai teks
→ Hermes membalas dalam teks
```

Target lanjutan:

```text
Voice input
→ transcription
→ Hermes menjawab
→ text-to-speech
→ percakapan hands-free
```

Pilihan awal yang direkomendasikan:

- Telegram sebagai communication channel;
- local `faster-whisper` sebagai speech-to-text;
- GPT sebagai model utama;
- respons teks sebagai default;
- Edge TTS sebagai opsi balasan suara gratis.

### 7.7 Automation dan proactive work

Hermes dapat menjalankan:

- scheduled research;
- daily brief;
- podcast monitoring;
- project status check;
- backlog review;
- health check;
- overnight coding;
- morning report;
- reminder;
- recurring maintenance task.

Automation harus menghasilkan output yang dapat diaudit dan tidak boleh diam-diam melakukan tindakan berisiko.

---

## 8. Model Strategy

Hermes bukan model. Hermes adalah operating layer yang dapat memakai beberapa model.

Strategi awal:

| Pekerjaan | Model atau sistem |
|---|---|
| Percakapan biasa | GPT cepat |
| Strategi dan PRD kompleks | GPT reasoning kuat |
| Riset volume tinggi | Model cepat dengan web tools |
| Coding | Codex atau Claude Code |
| Vision dan desain | Model vision atau design tool |
| Transkripsi | Local Whisper |
| Text-to-speech | Edge TTS |
| Provider utama gagal | Fallback model |

Prioritasnya adalah menggunakan akses yang sudah termasuk dalam langganan ChatGPT jika tersedia. API berbayar hanya ditambahkan ketika ada kebutuhan yang jelas.

Hermes harus mempunyai:

- batas biaya harian atau bulanan;
- logging penggunaan model;
- fallback policy;
- pilihan model per jenis pekerjaan;
- larangan memasukkan secret ke prompt atau memory.

---

## 9. Permission Policy

### Green — otomatis

Hermes boleh melakukan:

- membaca file;
- membaca repository;
- melakukan riset;
- membuat strategi draft;
- membuat PRD draft;
- membuat branch;
- mengedit file dalam workspace proyek;
- menjalankan lint, test, dan build;
- membuat dokumentasi;
- membuat commit lokal;
- membuat laporan;
- menyimpan checkpoint.

### Yellow — izin terbatas atau approval awal

Hermes dapat melakukan setelah mendapatkan izin yang sesuai:

- push branch;
- membuat draft pull request;
- mengubah ClickUp;
- mengirim pesan ke channel internal;
- menambah dependency;
- mengubah database development;
- membuat atau mengubah skill penting.

Izin dapat diberikan per repository atau per proyek agar tidak perlu diminta berulang kali.

### Red — selalu membutuhkan approval

Hermes tidak boleh melakukan secara otomatis:

- merge ke main branch;
- production deployment;
- production database migration;
- menghapus data material;
- mengubah DNS atau domain;
- melakukan pembayaran;
- membeli layanan;
- mengirim komunikasi eksternal sensitif;
- membuka credential;
- mengubah security policy;
- meningkatkan budget;
- melakukan tindakan sulit dibalik.

---

## 10. Autonomous Work Loop

Ketika mode proaktif aktif, Hermes menggunakan loop berikut:

```text
1. Periksa project registry.
2. Periksa task aktif.
3. Lanjutkan task dari checkpoint terakhir.
4. Jika tidak ada task aktif, ambil task aman dengan prioritas tertinggi.
5. Periksa dependency dan blocker.
6. Kerjakan satu unit pekerjaan.
7. Verifikasi hasil.
8. Simpan status.
9. Lanjutkan jika aman.
10. Berhenti jika membutuhkan keputusan atau approval.
11. Kirim laporan ketika selesai, gagal, atau terblokir.
```

Status task:

```text
queued
→ researching
→ proposed
→ awaiting decision
→ planned
→ coding
→ testing
→ draft PR
→ awaiting review
→ completed
```

Hermes tidak mengambil pekerjaan tanpa tujuan, backlog, atau definition of done.

---

## 11. Project Registry

Hermes harus mempunyai registry untuk semua proyek aktif.

Struktur minimal:

```yaml
project:
priority:
status:
objective:
workspace:
repository:
roadmap:
backlog:
definition_of_done:
allowed_actions:
approval_required:
current_task:
last_checkpoint:
```

Kandidat proyek awal:

- Mabar;
- Threads;
- Mimi;
- Lily;
- proyek pribadi Pepe lainnya.

Mabar menjadi kandidat pilot pertama karena mempunyai dokumentasi, PRD, repository, dan workflow engineering yang relatif matang.

Detail Threads, Mimi, dan Lily harus diaudit sebelum automation diaktifkan.

---

## 12. Hubungan dengan Agent Lama

### Sage

Sage tidak diteruskan sebagai runtime terpisah.

Yang dipertahankan:

- strategic sparring;
- thinker corpus;
- PRD schema;
- project registry;
- ClickUp workflow;
- orchestration logic;
- decision history.

Semua dipindahkan menjadi skill dan knowledge Hermes.

### Scout

Scout tidak diteruskan sebagai agent terpisah setelah migrasi berhasil.

Yang dipertahankan:

- research workflow;
- hype filter;
- lens system;
- daily brief;
- podcast monitor;
- knowledge bank;
- insight retrieval.

Semua dipindahkan menjadi skills, cron, dan storage Hermes.

### Rex

Rex tidak perlu dipertahankan sebagai conversational agent.

Yang dipertahankan:

- coding contract;
- repository mapping;
- branch convention;
- test discipline;
- deliverable format;
- blocker format;
- GitHub workflow;
- sandbox execution.

Hermes mengambil fungsi koordinasi dan menggunakan Codex atau Claude Code sebagai coding executor.

### Pixel

Pixel belum menjadi prioritas awal.

Konsep dan dokumennya dipertahankan sebagai referensi. Kemampuan design generation dapat ditambahkan sebagai skill Hermes setelah workflow utama stabil.

### Chimit

Chimit tidak otomatis digabungkan.

Jika masih digunakan oleh Bapak atau tim MGM, Chimit tetap terpisah karena:

- pengguna berbeda;
- domain berbeda;
- memory berbeda;
- privacy boundary;
- channel berbeda.

Jika tidak lagi digunakan sebagai agent aktif, knowledge hospitality dapat dipindahkan menjadi knowledge collection khusus di Hermes.

---

## 13. Roadmap

### Priority 0 — Preservation dan safety foundation

**Tujuan:** memastikan konsolidasi tidak menghilangkan aset lama dan tidak menciptakan risiko baru.

- Inventarisasi Sage, Scout, Rex, Pixel, dan Chimit.
- Backup seluruh konfigurasi, prompts, skills, vault, dan project registry.
- Jangan menghapus runtime lama.
- Tentukan folder Hermes.
- Tentukan secret management.
- Tentukan permission policy.
- Tentukan backup dan rollback.
- Dokumentasikan sumber kebenaran setiap proyek.

**Exit criteria:**

- Semua aset lama dapat ditemukan.
- Tidak ada credential masuk repository.
- Sistem lama dapat dipulihkan.
- Permission policy disetujui.

### Priority 1 — Hermes core

**Tujuan:** Hermes dapat digunakan sebagai satu-satunya tempat percakapan dasar.

- Install Hermes.
- Konfigurasikan GPT sebagai model utama.
- Konfigurasikan fallback model.
- Buat identity dan user profile.
- Buat memory policy.
- Buat project registry awal.
- Aktifkan logging.
- Aktifkan Telegram.
- Aktifkan local Whisper.
- Uji voice note → transcript → text response.
- Buat backup otomatis untuk memory dan skills.

**Exit criteria:**

- Hermes merespons teks dan voice.
- Hermes mengenali Pepe.
- Hermes dapat membedakan proyek.
- Tidak ada biaya API tidak terkontrol.
- Gateway dapat pulih setelah restart.

### Priority 2 — Migrasi kemampuan Sage

**Tujuan:** Hermes menjadi thinking partner dan orchestrator utama.

- Migrasikan strategic-sparring.
- Migrasikan thinker corpus.
- Migrasikan PRD writer.
- Migrasikan PRD schema.
- Migrasikan project registry.
- Migrasikan decision log.
- Migrasikan ClickUp workflow jika masih digunakan.
- Buat approval gate sebelum coding.

**Exit criteria:**

- Pepe dapat berdiskusi hanya dengan Hermes.
- Hermes dapat menghasilkan PRD setara Sage.
- Hermes menyimpan keputusan ke proyek yang benar.
- Hermes tidak coding sebelum ide cukup matang atau disetujui.

### Priority 3 — Engineering pilot dengan Mabar

**Tujuan:** membuktikan alur strategi hingga draft pull request.

- Audit repository Mabar.
- Dokumentasikan command build dan test.
- Migrasikan coding contract Rex.
- Hubungkan Codex atau Claude Code.
- Buat workflow branch dan commit.
- Buat verification checklist.
- Ambil 3–5 task backlog berisiko rendah.
- Jalankan sampai draft pull request.
- Review hasil secara manual.

**Exit criteria:**

- Hermes dapat menyelesaikan task kecil.
- Test dan build berhasil.
- Draft PR dapat direview.
- Tidak ada merge atau deploy tanpa approval.
- Blocker dilaporkan secara spesifik.

### Priority 4 — Migrasi kemampuan Scout

**Tujuan:** Hermes menangani research dan intelligence secara rutin.

- Migrasikan hype filter.
- Migrasikan lens system.
- Migrasikan source list.
- Migrasikan daily brief.
- Migrasikan podcast monitor.
- Migrasikan knowledge bank.
- Aktifkan cron secara bertahap.
- Tambahkan deduplication agar insight tidak berulang.

**Exit criteria:**

- Daily brief terkirim konsisten.
- Insight relevan dan tidak berulang.
- Research dapat dikaitkan dengan proyek aktif.
- Cron failure terlihat dan dapat dipulihkan.

### Priority 5 — Overnight autonomy

**Tujuan:** Hermes dapat bekerja ketika Pepe tidak aktif.

- Buat persistent task state.
- Buat autonomous work loop.
- Buat health check dan watchdog.
- Buat morning report.
- Batasi satu task aktif pada awal pilot.
- Tetapkan timeout dan iteration limit.
- Tetapkan daily model budget.
- Hanya izinkan pekerjaan reversibel.
- Berhenti pada keputusan bisnis besar.

**Exit criteria:**

- Hermes dapat bekerja semalam tanpa mengulang task.
- Semua perubahan mempunyai checkpoint.
- Pekerjaan berhenti dengan aman ketika terblokir.
- Morning report menjelaskan hasil dan keputusan yang dibutuhkan.

### Priority 6 — Multi-project operation

**Tujuan:** memperluas Hermes dari Mabar ke proyek lain.

- Audit Threads.
- Audit Mimi.
- Audit Lily.
- Buat project registry masing-masing.
- Tentukan prioritas lintas proyek.
- Pisahkan backlog dan memory per proyek.
- Tambahkan project switching.
- Tambahkan weekly portfolio review.
- Batasi concurrent coding task.

**Exit criteria:**

- Hermes tidak mencampurkan repository.
- Setiap keputusan tersimpan pada proyek yang tepat.
- Hermes dapat menjelaskan prioritas lintas proyek.
- Tidak ada dua task yang memodifikasi resource sama secara bersamaan.

### Priority 7 — Controlled self-improvement

**Tujuan:** Hermes belajar tanpa merusak workflow.

- Aktifkan skill proposal.
- Gunakan approval untuk skill berisiko tinggi.
- Pisahkan user preference, factual memory, dan procedural skill.
- Buat review skill bulanan.
- Deteksi skill yang duplikat atau bertentangan.
- Simpan version history.
- Tambahkan rollback skill.
- Ukur apakah skill baru benar-benar meningkatkan hasil.

**Exit criteria:**

- Semua perubahan skill dapat dilacak.
- Skill buruk dapat dikembalikan.
- Tidak ada permission escalation otomatis.
- Self-improvement menghasilkan perbaikan terukur.

### Priority 8 — Retirement agent lama

**Tujuan:** mengurangi maintenance setelah Hermes terbukti stabil.

- Jalankan Hermes paralel dengan sistem lama selama masa validasi.
- Bekukan perubahan pada agent lama.
- Bandingkan kualitas dan reliability.
- Arsipkan runtime Sage, Scout, dan Rex.
- Pertahankan dokumen dan vault sebagai sumber historis.
- Hapus credential lama yang tidak lagi diperlukan.
- Dokumentasikan rollback terakhir.

**Exit criteria:**

- Hermes menangani workflow utama.
- Tidak ada dependency aktif ke gateway lama.
- Semua data penting sudah dimigrasikan.
- Sistem lama tetap tersedia sebagai arsip.
- Maintenance hanya berfokus pada Hermes.

---

## 14. Deferred dan Low Priority

Hal berikut tidak menjadi prioritas awal:

- full autonomous production deployment;
- auto-merge pull request;
- autonomous spending;
- menyatukan Chimit ke personal memory Hermes;
- membuat semua channel aktif sekaligus;
- membangun live voice experience sebelum voice note stabil;
- premium text-to-speech;
- banyak model tanpa use case;
- menjalankan banyak coding task secara paralel;
- design generation Pixel;
- fully autonomous strategy decision;
- dashboard kompleks;
- mengganti semua tools sekaligus;
- menghapus agent lama sebelum Hermes tervalidasi.

---

## 15. Success Metrics

Keberhasilan Hermes tidak dinilai dari banyaknya fitur.

Metrik utama:

### Simplicity

- Pepe hanya perlu berbicara dengan satu agent.
- Jumlah runtime dan gateway berkurang.
- Maintenance time menurun.
- Tidak ada handoff manual antar-agent.

### Quality

- PRD setara atau lebih baik dari Sage.
- Research setara atau lebih tajam dari Scout.
- Coding output setara atau lebih dapat dipercaya dari Rex.
- Memory mengambil konteks yang relevan.

### Reliability

- Scheduled job berjalan konsisten.
- Gateway dapat pulih setelah restart.
- Task tidak berulang.
- Project context tidak tertukar.
- Error dan blocker terlihat.

### Autonomy

- Hermes dapat menyelesaikan task aman tanpa pengawasan.
- Hermes berhenti pada keputusan yang benar.
- Overnight work menghasilkan draft yang dapat direview.
- Jumlah intervensi operasional Pepe menurun.

### Cost

- Penggunaan model dapat dilihat.
- Tidak ada biaya API mengejutkan.
- Model mahal hanya digunakan ketika diperlukan.
- Local transcription digunakan sebagai default.

---

## 16. Risiko Utama

### Context pollution

Mitigasi:

- pisahkan project state;
- gunakan tags;
- simpan memory secara selektif;
- jangan memasukkan log sementara ke memory.

### Permission blast radius

Mitigasi:

- read-only sebagai default;
- approval untuk tindakan sulit dibalik;
- permission per repository;
- secret terpisah dari memory dan skill.

### Skill drift

Mitigasi:

- version history;
- approval gate;
- review bulanan;
- rollback.

### Biaya model

Mitigasi:

- budget;
- model routing;
- local Whisper;
- logging;
- matikan automation yang tidak bernilai.

### Autonomous loop berjalan tanpa arah

Mitigasi:

- wajib mempunyai objective;
- backlog;
- definition of done;
- iteration limit;
- timeout;
- kill criteria.

### Ketergantungan pada satu agent

Mitigasi:

- backup;
- exportable Markdown;
- skill berbasis file;
- repository versioning;
- provider fallback;
- dokumentasi recovery.

---

## 17. Definition of Done

Hermes dianggap berhasil sebagai agent utama ketika:

1. Pepe hanya perlu berkomunikasi dengan Hermes.
2. Hermes memahami profil dan proyek Pepe.
3. Hermes menerima teks dan voice note.
4. Hermes dapat melakukan strategic sparring.
5. Hermes dapat melakukan riset.
6. Hermes dapat membuat PRD.
7. Hermes dapat mengerjakan task coding sampai draft PR.
8. Hermes dapat menjalankan pekerjaan terjadwal.
9. Hermes dapat bekerja semalam secara aman.
10. Hermes berhenti ketika membutuhkan approval.
11. Memory dan project context tidak tercampur.
12. Biaya dan permission dapat dikontrol.
13. Sage, Scout, dan Rex tidak lagi membutuhkan maintenance aktif.
14. Sistem lama tetap tersedia sebagai arsip dan rollback.

---

## 18. Guiding Statement

> Hermes adalah satu-satunya personal agent yang dikembangkan Pepe. Ia menyatukan kemampuan berpikir Sage, mata riset Scout, dan tangan engineering Rex ke dalam satu pengalaman percakapan. Hermes boleh belajar, merencanakan, dan bekerja secara proaktif, tetapi tetap menjaga batas permission, konteks proyek, biaya, dan keputusan manusia.
