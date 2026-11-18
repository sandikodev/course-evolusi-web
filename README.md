# Evolusi Web: Memahami Ekosistem Frontend, Bukan Sekadar Mengikutinya

> Course ini **bukan untuk pemula.** Ia ditulis untuk developer mid/intermediate dan senior yang sudah bisa menggunakan framework frontend dan ingin naik satu level lebih dalam: memahami *mengapa* ekosistem bergerak seperti yang ia bergerak.

---

## Janji Course Ini

Setelah membaca course ini, kamu akan bisa:

- **Memetakan trade-off** di balik setiap framework — bukan sekadar tahu cara pakainya
- **Membaca sinyal ekosistem** — bedakan hype dari traction nyata dari production-proven
- **Merekomendasikan arsitektur** kepada tim atau klien dengan argumen yang defensible
- **Tidak panik** setiap kali framework baru muncul — karena kamu paham pola di balik perubahan

Yang *tidak* akan kamu dapatkan dari course ini: cara menulis komponen, cara setup routing, atau cara deploy aplikasi. Untuk itu, ada course lain.

---

## Untuk Siapa

**Cocok untuk kamu kalau:**
- Sudah kerja dengan React, Vue, Angular, atau framework lain minimal 1–2 tahun
- Pernah merasa "tertinggal" ketika framework baru viral di Twitter/X
- Ingin bisa bicara tentang arsitektur, bukan hanya implementasi
- Mau memahami mengapa Angular berbeda dari Vue, bukan hanya cara sintaksnya berbeda

**Tidak cocok kalau:**
- Baru mulai belajar frontend
- Belum pernah deploy aplikasi web ke production
- Mencari tutorial step-by-step dengan kode yang bisa dicopy-paste

---

## Struktur Course

```
PEMBUKA
  MANIFEST.md    — Keyakinan yang mendasari course ini (baca ini dulu)

PILAR 1 — SEJARAH & KONTEKS
  Bab 01   Pra-SPA: sebelum framework ada
  Bab 02   Gelombang SPA pertama: AngularJS, Backbone, Ember
  Bab 03   React dan revolusi VDOM
  Bab 04   Fragmentasi: mengapa banyak framework bisa eksis bersamaan

PILAR 2 — SUBSTRATE & PLATFORM
  Bab 05   JavaScript: substrate yang tidak direncanakan
  Bab 06   TypeScript: keamanan nyata atau ilusi?
  Bab 07   Browser sebagai platform: DOM, Web APIs, Web Components
  Bab 08   Edge, runtime, dan war yang terjadi diam-diam

PILAR 3 — PARADIGMA & RENDERING
  Bab 09   Empat klaster paradigma: cara berpikir yang berbeda-beda
  Bab 10   VDOM: kekuatan, keterbatasan, dan kapan tidak dibutuhkan
  Bab 11   Signals: konvergensi yang tidak banyak dibicarakan
  Bab 12   Compiler sebagai framework: Svelte dan eliminasi runtime
  Bab 13   Resumability: Qwik dan pertanyaan tentang hidrasi
  Bab 14   SSR, Islands, Streaming: tiga cara berbeda melayani halaman pertama
  Bab 15   Meta-frameworks: mengapa mereka semua muncul bersamaan

PILAR 4 — NAVIGASI KARIER
  Bab 16   Cara membaca sinyal ekosistem
  Bab 17   Arsitektur yang defensible
  Bab 18   Spesialisasi vs generalisasi
  Bab 19   Selanjutnya: sinyal yang perlu diperhatikan
```

---

## Cara Membaca

Course ini ditulis sebagai **esai teknis**, bukan tutorial. Kamu tidak perlu membuka editor atau terminal. Yang kamu butuhkan adalah waktu untuk berpikir.

Setiap bab diakhiri dengan **"Pertanyaan untuk Direnungkan"** — bukan soal yang ada jawaban benarnya, tapi pertanyaan yang akan memaksa kamu mengaplikasikan konsep ke konteks kamu sendiri.

Urutan pembacaan yang disarankan: **linear dari Bab 01 sampai 19**. Course ini dirancang sebagai narasi yang mengalir — setiap bab membangun di atas bab sebelumnya.

Kalau sudah familiar dengan sejarahnya (Bab 01–04), kamu bisa mulai dari Bab 05.

---

## Keterkaitan dengan Course Lain

Course ini adalah pasangan dari **[Course Aurelia 2](../aureliajs/)** — tapi tidak saling bergantung. Kamu bisa baca keduanya secara terpisah.

Yang terhubung langsung:
- `FRAMEWORK_POSITIONING.md` di course Aurelia 2 membahas empat klaster paradigma secara ringkas. Bab 09 course ini memperluasnya dengan kedalaman historis dan teknis yang lebih jauh.

---

## Penulis

**Sandikodev** — TypeScript/Aurelia engineer  
TikTok & Facebook: @sandikodev  
Produk digital: lynk.id/sandikodev
