# MASTER PLAN — Course: Evolusi Web

> **Dokumen ini adalah satu-satunya referensi untuk seluruh pekerjaan course ini.**
> Baca ini sebelum mengerjakan apapun. Update setiap ada keputusan baru.
>
> **Lokasi project:** `/home/dev/bukudigital/evolusi-web/`
> **Repo GitHub:** `sandikodev/course-evolusi-web` (submodule dari `sandikodev/course`)
> **Penulis:** Sandikodev (@sandikodev)
> **Dibuat:** 2026-11-18

---

## 1. Promise Tunggal Course Ini

> *"Setelah membaca course ini, kamu bisa memetakan ekosistem frontend tanpa panik setiap kali framework baru muncul — karena kamu paham pola di balik perubahan, bukan hanya permukaan teknologinya."*

Ini bukan course "belajar framework X". Ini course untuk **membaca ekosistem** — memahami *mengapa* sesuatu lahir, *trade-off* apa yang dibawa, dan *sinyal* apa yang perlu diperhatikan untuk tetap relevan.

---

## 2. Target Pembaca

**Bukan untuk:** Junior, pemula, atau orang yang baru mulai frontend.

**Untuk:** Developer **mid/intermediate dan senior** yang:
- Sudah bisa satu atau lebih framework (React, Vue, Angular, Svelte, dsb.)
- Merasa "tertinggal" setiap kali framework baru muncul
- Ingin memahami *pola* di balik perubahan, bukan sekadar mengikuti hype
- Mau bisa memberi rekomendasi arsitektur yang defensible kepada tim atau klien
- Ingin naik dari "developer yang pakai framework" menjadi "developer yang mengerti framework"

**Disclaimer yang harus ada di halaman pertama:**
> Course ini mengasumsikan kamu sudah punya pengalaman kerja dengan minimal satu framework frontend modern. Konten ini tidak mengajarkan cara menulis komponen atau routing — melainkan cara membaca trade-off di balik desain setiap framework dan ekosistemnya.

---

## 3. Empat Pilar Konten

Semua bab mengalir dari empat pertanyaan besar:

```
Pilar 1 — SEJARAH & KONTEKS
"Mengapa setiap gelombang teknologi lahir?"
Setiap inovasi adalah reaksi terhadap keterbatasan sebelumnya.

Pilar 2 — SUBSTRATE & PLATFORM
"Di atas apa semua ini berjalan?"
JavaScript, TypeScript, WebAssembly, browser, edge, runtime — dan kompromi masing-masing.

Pilar 3 — PARADIGMA & RENDERING
"Bagaimana cara berpikir berubah?"
VDOM → Signals → Compiler → Resumability → (selanjutnya?)

Pilar 4 — NAVIGASI KARIER
"Bagaimana tetap relevan tanpa ikut setiap hype?"
Cara baca sinyal ekosistem, cara posisikan diri, cara bicara tentang trade-off ke tim/klien.
```

---

## 4. Outline Chapter

```
PEMBUKA
  README.md         — Siapa course ini untuk, apa yang akan didapat
  MANIFEST.md       — Keyakinan-keyakinan yang mendasari course ini

PILAR 1 — SEJARAH & KONTEKS
  bab-01/   Pra-SPA: HTML Statis, CGI, Server Templates, jQuery
  bab-02/   Gelombang SPA: AngularJS, Backbone, Ember — dan mengapa muncul
  bab-03/   React dan Revolusi VDOM — reaksi terhadap apa?
  bab-04/   Fragmentasi: Vue, Angular 2, Aurelia, Svelte — masing-masing menjawab apa?

PILAR 2 — SUBSTRATE & PLATFORM
  bab-05/   JavaScript: lahir 10 hari, mewarisi dunia
  bab-06/   TypeScript: ilusi keamanan atau lapisan yang berarti?
  bab-07/   Browser sebagai Platform: DOM, Web APIs, Web Components, Custom Elements
  bab-08/   Edge Computing & Runtime Wars: Node, Deno, Bun, Workers, Deno 2

PILAR 3 — PARADIGMA & RENDERING
  bab-09/   Empat Klaster Paradigma (dari kanan bab-01 course aureliajs — diperluas)
  bab-10/   VDOM: kekuatan, keterbatasan, dan kapan kamu tidak butuhnya
  bab-11/   Signals: konvergensi yang terjadi diam-diam
  bab-12/   Compiler sebagai Framework: Svelte, Runes, dan eliminasi runtime
  bab-13/   Resumability: Qwik dan pertanyaan tentang hidrasi
  bab-14/   SSR, Islands, Streaming: tiga cara berbeda melayani halaman pertama
  bab-15/   Meta-Frameworks: Next, Nuxt, SvelteKit, Analog — mengapa mereka ada

PILAR 4 — NAVIGASI KARIER
  bab-16/   Cara Membaca Sinyal Ekosistem: hype vs traction vs production-proven
  bab-17/   Arsitektur Defensible: cara rekomendasikan teknologi ke tim/klien
  bab-18/   Spesialisasi vs Generalisasi — dan kapan niche yang sempit justru menguntungkan
  bab-19/   Selanjutnya: WebAssembly, AI-native frontend, dan sinyal yang perlu diperhatikan
```

**Total estimasi:** ~20–25 bab, ~300–400 halaman PDF

---

## 5. Tone & Gaya Penulisan

### Perbedaan dari Course Aureliajs

Course Aureliajs adalah **tutorial teknis** — ada kode yang bisa dijalankan, ada latihan, ada langkah-langkah.

Course Evolusi Web adalah **esai teknis yang terstruktur** — tidak ada kode yang perlu dijalankan, tapi ada analisis mendalam, perbandingan, argumen dengan evidence, dan pertanyaan yang membuat pembaca berpikir.

**Model referensi gaya:** seperti buku "Designing Data-Intensive Applications" (Martin Kleppmann) — teknis tapi readable, opinionated tapi berbasis evidence, tidak takut bilang "ini trade-off yang belum ada jawaban sempurnanya".

### Aturan Penulisan

- Bahasa Indonesia, sapaan "kamu"
- Tidak ada "basa-basi motivasi" — langsung ke substansi
- Setiap klaim harus punya *mengapa* — bukan sekadar menyatakan fakta
- Boleh opinionated, tapi harus jelas mana opini dan mana fakta
- Setiap bab diakhiri dengan "Pertanyaan untuk Direnungkan" — bukan latihan coding
- Cross-reference ke `FRAMEWORK_POSITIONING.md` di course aureliajs untuk detail teknis klaster paradigma

---

## 6. Dokumen Referensi

| File | Isi |
|------|-----|
| `MASTER_PLAN.md` | Dokumen ini |
| `MANIFEST.md` | Keyakinan-keyakinan yang mendasari course (dibuat setelah MASTER_PLAN) |
| `README.md` | Halaman depan course untuk pembaca |
| `../aureliajs/FRAMEWORK_POSITIONING.md` | Analisis empat klaster paradigma (cross-reference) |

---

## 7. Status Pengerjaan

| File/Chapter | Status | Catatan |
|-------------|--------|---------|
| MASTER_PLAN.md | ✅ Done | Dokumen ini |
| MANIFEST.md | ⏳ Belum | Keyakinan-keyakinan dasar |
| README.md | ⏳ Belum | Halaman depan |
| bab-01/ | ⏳ Belum | Pra-SPA |
| bab-02/ | ⏳ Belum | Gelombang SPA pertama |
| bab-03/ | ⏳ Belum | React & VDOM Revolution |
| bab-04/ | ⏳ Belum | Fragmentasi ekosistem |
| bab-05/ | ⏳ Belum | JavaScript substrate |
| bab-06/ | ⏳ Belum | TypeScript |
| bab-07/ | ⏳ Belum | Browser sebagai platform |
| bab-08/ | ⏳ Belum | Edge & Runtime Wars |
| bab-09/ | ⏳ Belum | Empat klaster paradigma (diperluas) |
| bab-10/ | ⏳ Belum | VDOM |
| bab-11/ | ⏳ Belum | Signals |
| bab-12/ | ⏳ Belum | Compiler sebagai framework |
| bab-13/ | ⏳ Belum | Resumability |
| bab-14/ | ⏳ Belum | SSR, Islands, Streaming |
| bab-15/ | ⏳ Belum | Meta-frameworks |
| bab-16/ | ⏳ Belum | Cara baca sinyal ekosistem |
| bab-17/ | ⏳ Belum | Arsitektur defensible |
| bab-18/ | ⏳ Belum | Spesialisasi vs generalisasi |
| bab-19/ | ⏳ Belum | Selanjutnya |

---

## 8. Instruksi untuk Sesi Baru

Kalau membuka sesi baru dan diminta melanjutkan:

1. Baca file ini dulu
2. Cek tabel status di bagian 7
3. Baca chapter terakhir yang selesai sebagai referensi gaya
4. Ikuti aturan penulisan di bagian 5
5. Update tabel status setelah selesai

---

## 9. Keputusan Final (Jangan Diubah Tanpa Diskusi)

- **Bukan untuk pemula** — disclaimer wajib ada di halaman pertama
- **Esai teknis, bukan tutorial** — tidak ada latihan coding
- **Diakhiri "Pertanyaan untuk Direnungkan"** — bukan soal latihan
- **Opinionated tapi transparan** — tandai jelas mana opini mana fakta
- **Cross-reference ke aureliajs/FRAMEWORK_POSITIONING.md** untuk detail klaster
- **Bahasa:** Indonesia
- **Format:** Markdown → PDF
- **Harga estimasi:** Rp 149.000–199.000 (lebih premium dari aureliajs karena target senior)
