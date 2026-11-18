# Evolusi Web: Dari Browser ke Infrastruktur

*Memahami Trade-off di Setiap Lapisan Stack — Frontend, Fullstack, JAMstack, dan Seterusnya*

---

> **Course ini bukan untuk pemula.**
>
> Ia ditulis untuk developer mid/intermediate dan senior yang sudah deploy ke production, sudah kerja dengan framework modern, dan ingin naik satu level lebih dalam: memahami *mengapa* ekosistem web bergerak seperti yang ia bergerak — dari browser sampai infrastruktur.

---

## Janji Course Ini

Setelah membaca course ini, kamu akan bisa:

- **Memetakan trade-off** di setiap lapisan stack — bukan sekadar tahu cara pakainya
- **Membaca sinyal ekosistem** — bedakan hype dari traction nyata dari production-proven
- **Merekomendasikan arsitektur** kepada tim atau klien dengan argumen yang defensible
- **Memahami mengapa** Next.js, Nuxt, SvelteKit, Astro, Qwik, dan sejenisnya ada — dan apa yang masing-masing selesaikan
- **Tidak panik** setiap kali teknologi baru muncul — karena kamu paham pola di balik perubahan

Yang *tidak* akan kamu dapatkan: cara menulis komponen, cara setup routing, cara deploy step-by-step. Untuk itu, ada course lain.

---

## Mengapa Dimulai dari Browser?

Browser adalah titik terdekat dengan manusia. Semua keputusan arsitektur yang ada — rendering strategy, state management, server design, CDN policy, edge computing — pada akhirnya menjawab satu pertanyaan:

> *"Bagaimana konten sampai ke browser, dalam bentuk apa, kapan, dan dengan trade-off apa?"*

Dari pertanyaan itu mengalir seluruh spektrum yang dibahas course ini:

```
Browser
    → Frontend framework & paradigma
    → Rendering strategy (CSR, SSR, SSG, ISR, streaming, islands)
    → Fullstack (meta-frameworks)
    → Stack philosophy (JAMstack, MACH, composable)
    → Backend for Frontend
    → Edge & infrastructure
    → Deployment model
```

Ini bukan eskalasi topik yang acak. Setiap lapisan adalah jawaban atas pertanyaan yang belum terjawab di lapisan sebelumnya.

---

## Untuk Siapa

**Cocok untuk kamu kalau:**
- Sudah kerja dengan React, Vue, Angular, Next.js, atau stack lain minimal 1–2 tahun
- Pernah merasa "tertinggal" ketika framework baru viral
- Ingin bisa bicara tentang arsitektur, bukan hanya implementasi
- Mau memahami mengapa JAMstack berbeda dari SSR, bukan hanya cara kerjanya berbeda
- Ingin bisa merekomendasikan stack kepada klien dengan alasan yang lebih dari "ini yang populer"

**Tidak cocok kalau:**
- Baru mulai belajar web development
- Belum pernah deploy aplikasi ke production
- Mencari tutorial step-by-step dengan kode yang bisa dicopy-paste

---

## Struktur Course (26 Bab)

```
PILAR 1 — SEJARAH & KONTEKS
  Bab 01   Pra-SPA: sebelum framework ada
  Bab 02   Gelombang SPA pertama: AngularJS, Backbone, Ember
  Bab 03   React dan revolusi VDOM
  Bab 04   Fragmentasi: mengapa banyak framework bisa eksis bersamaan

PILAR 2 — SUBSTRATE & PLATFORM
  Bab 05   JavaScript: substrate yang tidak direncanakan
  Bab 06   TypeScript: keamanan nyata atau ilusi?
  Bab 07   Browser sebagai platform
  Bab 08   Runtime Wars: Node, Deno, Bun, Workers

PILAR 3 — PARADIGMA & RENDERING (Transisi Frontend → Fullstack)
  Bab 09   Empat klaster paradigma
  Bab 10   VDOM: kekuatan dan keterbatasan
  Bab 11   Signals: konvergensi diam-diam
  Bab 12   Compiler sebagai framework
  Bab 13   Resumability
  Bab 14   SSR, Islands, Streaming

PILAR 4 — FULLSTACK & STACK PHILOSOPHY
  Bab 15   Meta-frameworks
  Bab 16   JAMstack: filosofi dan batasannya
  Bab 17   MACH & Composable Architecture
  Bab 18   Edge-first architecture
  Bab 19   BFF (Backend for Frontend)
  Bab 20   Micro-frontends
  Bab 21   Monolith yang terhormat vs distribusi yang prematur

PILAR 5 — NAVIGASI & SINTESIS
  Bab 22   Cara membaca sinyal ekosistem
  Bab 23   Arsitektur yang defensible
  Bab 24   Spesialisasi vs generalisasi
  Bab 25   Peta menyeluruh: semua stack, semua ceruk
  Bab 26   Selanjutnya
```

---

## Cara Membaca

Course ini ditulis sebagai **esai teknis**, bukan tutorial. Tidak perlu membuka editor atau terminal.

Setiap bab diakhiri dengan **"Pertanyaan untuk Direnungkan"** — bukan soal yang ada jawaban benarnya, tapi pertanyaan yang akan memaksa kamu mengaplikasikan konsep ke konteks kamu sendiri.

**Baca linear dari Bab 01 sampai 26.** Course ini dirancang sebagai narasi yang membangun — setiap bab mengandalkan konteks yang dibangun bab sebelumnya.

Kalau sudah familiar dengan sejarah (Bab 01–04), kamu bisa mulai dari Bab 05.

---

## Baca MANIFEST.md Dulu

Sebelum membaca bab pertama, baca [`MANIFEST.md`](./MANIFEST.md) — 7 keyakinan yang mendasari sudut pandang course ini. Kalau kamu tidak tahu dari mana argumen-argumen dalam course ini berasal, kamu akan kehilangan konteksnya.

---

## Keterkaitan dengan Course Lain

Course ini adalah pasangan dari **[Course Aurelia 2](../aureliajs/)** — tapi tidak saling bergantung. Kamu bisa baca keduanya secara terpisah.

Titik temu langsung: `FRAMEWORK_POSITIONING.md` di course Aurelia 2 membahas empat klaster paradigma secara ringkas. Bab 09 course ini memperluasnya dengan kedalaman historis dan teknis yang jauh lebih dalam.

---

## Penulis

**Sandikodev** — TypeScript/web engineer  
TikTok & Facebook: @sandikodev  
Produk digital: lynk.id/sandikodev
