# MASTER PLAN — Course: Evolusi Web

> **Dokumen ini adalah satu-satunya referensi untuk seluruh pekerjaan course ini.**
> Baca ini sebelum mengerjakan apapun. Update setiap ada keputusan baru.
>
> **Lokasi project:** `/home/dev/bukudigital/evolusi-web/`
> **Repo GitHub:** `sandikodev/course-evolusi-web` (submodule dari `sandikodev/course`)
> **Penulis:** Sandikodev (@sandikodev)
> **Dibuat:** 2026-11-18 | **Direvisi:** 2026-11-19

---

## 1. Promise Tunggal Course Ini

> *"Memahami mengapa web berarsitektur seperti sekarang — dari browser sampai infrastruktur — sehingga kamu bisa membaca trade-off di setiap lapisan dan membuat keputusan arsitektur yang defensible, bukan sekadar mengikuti yang populer."*

Ini **bukan** course frontend. Ini **bukan** course backend. Ini adalah **teori arsitektur web secara menyeluruh** — dimulai dari browser sebagai titik terdekat dengan manusia, lalu melebar ke seluruh stack sampai ke filosofi deployment dan infrastruktur.

**Mengapa dimulai dari browser?**

Browser adalah titik yang paling dekat dengan operator/pengguna. Semua keputusan arsitektur yang ada — rendering strategy, state management, server design, CDN policy, edge computing — pada akhirnya menjawab satu pertanyaan: *"bagaimana konten sampai ke browser, dalam bentuk apa, kapan, dan dengan trade-off apa?"*

Dari pertanyaan itu mengalir seluruh spektrum yang akan dibahas di course ini:

```
Browser (render, interaktivitas, Web APIs)
    ↓
Frontend (framework, state, routing, build tools)
    ↓
Rendering strategy (CSR, SSR, SSG, ISR, streaming, partial hydration)
    ↓
Fullstack (server + client dalam satu kesatuan, meta-frameworks)
    ↓
Stack philosophy (JAMstack, MACH, composable architecture)
    ↓
Backend for Frontend (BFF, API design, GraphQL vs REST vs tRPC)
    ↓
Infrastructure (CDN, edge, serverless, containers, runtime wars)
    ↓
Deployment model (monolith vs microservices vs micro-frontends)
```

---

## 2. Target Pembaca

**Bukan untuk:** Junior, pemula, atau orang yang baru mulai web development.

**Untuk:** Developer **mid/intermediate dan senior** yang:
- Sudah bisa satu atau lebih framework/stack dan sudah deploy ke production
- Pernah merasa "tertinggal" ketika framework atau paradigma baru viral
- Ingin memahami *pola* di balik perubahan, bukan sekadar mengikuti hype
- Mau bisa merekomendasikan arsitektur kepada tim atau klien dengan argumen yang jelas
- Ingin naik dari "developer yang pakai teknologi" menjadi "developer yang mengerti teknologi"

**Disclaimer wajib di halaman pertama:**
> Course ini mengasumsikan kamu sudah punya pengalaman kerja dengan minimal satu framework atau stack web modern dan sudah pernah deploy aplikasi ke production. Konten ini tidak mengajarkan cara menulis kode — melainkan cara membaca trade-off di balik desain setiap teknologi dan arsitektur ekosistem web secara menyeluruh.

---

## 3. Judul Resmi

**Judul:** `Evolusi Web: Dari Browser ke Infrastruktur`

**Subjudul:** *Memahami Trade-off di Setiap Lapisan Stack — Frontend, Fullstack, JAMstack, dan Seterusnya*

---

## 4. Lima Pilar Konten

Semua bab mengalir dari lima pertanyaan besar:

```
Pilar 1 — SEJARAH & KONTEKS
"Mengapa setiap gelombang teknologi lahir?"
Setiap inovasi adalah reaksi terhadap keterbatasan sebelumnya.
Dimulai dari browser sebagai titik awal, berkembang ke seluruh ekosistem.

Pilar 2 — SUBSTRATE & PLATFORM
"Di atas apa semua ini berjalan?"
JavaScript, TypeScript, WebAssembly, browser APIs, edge runtime —
dan kompromi yang diwarisi dari keputusan di masa lalu.

Pilar 3 — PARADIGMA & RENDERING
"Bagaimana cara berpikir berubah?"
VDOM → Signals → Compiler → Resumability → SSR → Islands → Streaming.
Ini adalah lapisan transisi dari "frontend" ke "fullstack".

Pilar 4 — FULLSTACK & STACK PHILOSOPHY
"Bagaimana server dan client bisa jadi satu, dan apa implikasinya?"
Meta-frameworks, JAMstack, MACH, composable architecture, edge-first,
BFF, micro-frontends, monolith vs distribusi.

Pilar 5 — NAVIGASI & SINTESIS
"Bagaimana membuat keputusan yang defensible di tengah semua ini?"
Cara baca sinyal ekosistem, cara rekomendasikan arsitektur, spesialisasi vs
generalisasi, dan peta menyeluruh semua ceruk pasar.
```

---

## 5. Outline Chapter Lengkap

```
PEMBUKA
  README.md         — Siapa course ini untuk, promise, cara membaca
  MANIFEST.md       — 7 keyakinan yang mendasari course

PILAR 1 — SEJARAH & KONTEKS
  bab-01/   Pra-SPA: HTML dokumen, CGI, AJAX, jQuery ✅
  bab-02/   Gelombang SPA Pertama: AngularJS, Backbone, Ember
  bab-03/   React dan Revolusi VDOM
  bab-04/   Fragmentasi: mengapa banyak framework bisa eksis bersamaan

PILAR 2 — SUBSTRATE & PLATFORM
  bab-05/   JavaScript: lahir 10 hari, mewarisi dunia
  bab-06/   TypeScript: ilusi keamanan atau lapisan yang berarti?
  bab-07/   Browser sebagai Platform: DOM, Web APIs, Web Components, Custom Elements
  bab-08/   Runtime Wars: Node, Deno, Bun, Cloudflare Workers, dan filosofi edge

PILAR 3 — PARADIGMA & RENDERING (Transisi Frontend → Fullstack)
  bab-09/   Empat Klaster Paradigma: cara berpikir yang berbeda-beda
  bab-10/   VDOM: kekuatan, keterbatasan, dan kapan tidak dibutuhkan
  bab-11/   Signals: konvergensi yang terjadi diam-diam
  bab-12/   Compiler sebagai Framework: Svelte, Runes, eliminasi runtime
  bab-13/   Resumability: Qwik dan pertanyaan tentang hidrasi
  bab-14/   SSR, Islands, Streaming: tiga cara berbeda melayani halaman pertama

PILAR 4 — FULLSTACK & STACK PHILOSOPHY
  bab-15/   Meta-Frameworks: Next.js, Nuxt, SvelteKit, Analog — mengapa mereka semua ada
  bab-16/   JAMstack: filosofi, implementasi, batas kemampuan, dan evolusinya
  bab-17/   MACH & Composable Architecture: Microservices, API-first, Cloud-native, Headless
  bab-18/   Edge-First Architecture: Cloudflare Workers, Deno Deploy, dan apa artinya "edge"
  bab-19/   BFF (Backend for Frontend): motivasi, pola implementasi, kapan perlu
  bab-20/   Micro-Frontends: hype atau solusi nyata, dan kapan masalahnya valid
  bab-21/   Monolith yang Terhormat vs Distribusi yang Prematur

PILAR 5 — NAVIGASI KARIER & SINTESIS
  bab-22/   Cara Membaca Sinyal Ekosistem: hype vs traction vs production-proven
  bab-23/   Arsitektur yang Defensible: cara rekomendasikan teknologi ke tim/klien
  bab-24/   Spesialisasi vs Generalisasi: kapan niche sempit justru menguntungkan
  bab-25/   Peta Menyeluruh: semua stack, semua ceruk, semua trade-off dalam satu gambar
  bab-26/   Selanjutnya: WebAssembly, AI-native frontend, dan sinyal yang perlu diperhatikan
```

**Total:** 26 bab, estimasi ~400–500 halaman PDF

---

## 6. Tone & Gaya Penulisan

### Perbedaan dari Course Aureliajs

Course Aureliajs adalah **tutorial teknis** — ada kode yang dijalankan, ada latihan, ada langkah-langkah.

Course Evolusi Web adalah **esai teknis terstruktur** — tidak ada kode yang perlu dijalankan, tapi ada analisis mendalam, argumen dengan evidence, perbandingan berimbang, dan pertanyaan yang mendorong refleksi.

**Model referensi gaya:**
- *Designing Data-Intensive Applications* (Martin Kleppmann) — teknis, readable, opinionated berbasis evidence
- *A Philosophy of Software Design* (John Ousterhout) — argumen kuat, tidak takut bertentangan dengan arus utama
- *The Architecture of Open Source Applications* — konkret, berbasis kasus nyata

### Aturan Penulisan

1. **Bahasa Indonesia**, sapaan "kamu"
2. **Tidak ada basa-basi motivasi** — langsung ke substansi
3. **Setiap klaim punya *mengapa*** — bukan sekadar menyatakan fakta
4. **Tandai jelas mana opini mana fakta** — boleh opinionated tapi transparan
5. **Setiap bab diakhiri "Pertanyaan untuk Direnungkan"** — bukan latihan coding
6. **Cross-reference ke `../aureliajs/FRAMEWORK_POSITIONING.md`** untuk detail teknis klaster paradigma
7. **Narasi mengalir dari browser → stack → infrastruktur** — setiap bab membangun konteks bab berikutnya
8. **Hindari "gunakan X"** — selalu frame sebagai "X tepat ketika..., tidak tepat ketika..."

---

## 7. Dokumen Referensi

| File | Isi |
|------|-----|
| `MASTER_PLAN.md` | Dokumen ini — panduan keseluruhan course |
| `MANIFEST.md` | 7 keyakinan yang mendasari sudut pandang course |
| `README.md` | Halaman depan untuk pembaca |
| `../aureliajs/FRAMEWORK_POSITIONING.md` | Detail empat klaster paradigma (cross-reference Bab 09) |

---

## 8. Status Pengerjaan

| File/Chapter | Status | Catatan |
|-------------|--------|---------|
| MASTER_PLAN.md | ✅ Done | Dokumen ini — direvisi 2026-11-19 |
| MANIFEST.md | ✅ Done | 7 keyakinan dasar |
| README.md | ✅ Done | Halaman depan diperbarui — promise baru, struktur 26 bab |
| bab-01/ | ✅ Done | Pra-SPA: HTML dokumen, CGI, AJAX, jQuery, warisan |
| bab-02/ | ⏳ Belum | Gelombang SPA pertama |
| bab-03/ | ⏳ Belum | React & VDOM Revolution |
| bab-04/ | ⏳ Belum | Fragmentasi ekosistem |
| bab-05/ | ⏳ Belum | JavaScript substrate |
| bab-06/ | ⏳ Belum | TypeScript |
| bab-07/ | ⏳ Belum | Browser sebagai platform |
| bab-08/ | ⏳ Belum | Runtime Wars |
| bab-09/ | ⏳ Belum | Empat klaster paradigma |
| bab-10/ | ⏳ Belum | VDOM |
| bab-11/ | ⏳ Belum | Signals |
| bab-12/ | ⏳ Belum | Compiler sebagai framework |
| bab-13/ | ⏳ Belum | Resumability |
| bab-14/ | ⏳ Belum | SSR, Islands, Streaming |
| bab-15/ | ⏳ Belum | Meta-frameworks |
| bab-16/ | ⏳ Belum | JAMstack |
| bab-17/ | ⏳ Belum | MACH & Composable Architecture |
| bab-18/ | ⏳ Belum | Edge-First Architecture |
| bab-19/ | ⏳ Belum | BFF |
| bab-20/ | ⏳ Belum | Micro-frontends |
| bab-21/ | ⏳ Belum | Monolith vs distribusi |
| bab-22/ | ⏳ Belum | Cara baca sinyal ekosistem |
| bab-23/ | ⏳ Belum | Arsitektur defensible |
| bab-24/ | ⏳ Belum | Spesialisasi vs generalisasi |
| bab-25/ | ⏳ Belum | Peta menyeluruh semua stack |
| bab-26/ | ⏳ Belum | Selanjutnya |

---

## 9. Instruksi untuk Sesi Baru

1. Baca file ini dulu
2. Cek tabel status di bagian 8
3. Baca chapter terakhir yang selesai sebagai referensi gaya
4. Ikuti 8 aturan penulisan di bagian 6
5. Update tabel status setelah selesai

---

## 10. Keputusan Final (Jangan Diubah Tanpa Diskusi)

- **Judul:** `Evolusi Web: Dari Browser ke Infrastruktur`
- **Scope:** Bukan hanya frontend — meliputi seluruh stack dari browser sampai infrastruktur
- **Bukan untuk pemula** — disclaimer wajib di halaman pertama
- **Esai teknis, bukan tutorial** — tidak ada latihan coding
- **Diakhiri "Pertanyaan untuk Direnungkan"** di setiap bab
- **Opinionated tapi transparan** — tandai jelas mana opini mana fakta
- **Narasi browser-first** — mulai dari browser, melebar ke stack
- **Bahasa:** Indonesia
- **Format:** Markdown → PDF
- **Harga estimasi:** Rp 149.000–199.000 (lebih premium karena target senior)
- **26 bab** — scope lebih luas dari course aureliajs (15 bab)
