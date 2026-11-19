# Bab 12 — Compiler sebagai Framework: Svelte dan Eliminasi Runtime

> *"Rich Harris bertanya pertanyaan yang tidak pernah dipikirkan orang lain: kalau kita tahu pada build time apa yang reaktif, mengapa kita butuh runtime untuk melacaknya?"*

---

## Pertanyaan yang Mengubah Segalanya

Tahun 2016, Rich Harris menulis sebuah artikel berjudul *"Frameworks without the framework: why didn't we think of this sooner?"* Argumennya sederhana tapi radikal: framework seharusnya bukan sesuatu yang di-download dan dijalankan di browser — framework seharusnya adalah compiler yang melakukan pekerjaan berat sebelum kode sampai ke browser.

Ini adalah paradigm shift yang nyata. Semua framework sebelumnya — React, Vue, Angular, Aurelia — mengasumsikan bahwa framework harus ada di runtime. Svelte menolak asumsi itu.

---

## Bagaimana Svelte Bekerja

Developer menulis komponen Svelte dalam format `.svelte` — SFC dengan `<script>`, `<template>`, dan `<style>`:

```svelte
<script>
  let count = 0;

  function increment() {
    count++;
  }
</script>

<button on:click={increment}>
  Diklik {count} kali
</button>

<style>
  button { background: blue; color: white; }
</style>
```

Kompiler Svelte menganalisis ini dan menghasilkan kode JavaScript imperatif yang langsung manipulasi DOM:

```javascript
// Output yang disederhanakan dari kompiler Svelte
function create_fragment(ctx) {
  let button;
  let t;
  let mounted;
  let dispose;

  return {
    c() {
      button = element("button");
      t = text(`Diklik ${ctx[0]} kali`);
      append(button, t);
    },
    m(target, anchor) {
      insert(target, button, anchor);
      if (!mounted) {
        dispose = listen(button, "click", ctx[1]);
        mounted = true;
      }
    },
    p(ctx, [dirty]) {
      if (dirty & 1) set_data(t, `Diklik ${ctx[0]} kali`);
      //                   ↑ hanya update ketika count (index 0) berubah
    }
  };
}
```

Kompiler sudah tahu pada build time bahwa teks bergantung pada `count`. Jadi ia menghasilkan kode yang hanya update teks tersebut ketika `count` berubah — tanpa VDOM, tanpa runtime diffing.

---

## Svelte 5 dan Runes: Evolusi yang Menarik

Svelte 5 memperkenalkan **Runes** — `$state()`, `$derived()`, `$effect()`. Ini terlihat seperti signals dari Klaster 1, tapi ada perbedaan fundamental:

**Svelte 5 `$state`:**
```svelte
<script>
  let count = $state(0); // $state adalah compiler hint, bukan runtime object
</script>
```

Ketika kompiler melihat `$state`, ia menghasilkan kode reaktif yang sesuai. `$state` tidak ada di runtime — ia adalah instruksi untuk kompiler.

Ini berbeda dari `signal(0)` di SolidJS atau Angular yang adalah runtime object. Svelte 5 Runes adalah *compiler directives* yang terlihat seperti signals.

**Mengapa ini penting:** Svelte tetap mempertahankan filosofi compiler-first bahkan ketika mengadopsi sintaks signals-like. Ini adalah perbedaan filosofis yang dalam, bukan hanya perbedaan sintaks.

---

## Trade-off Compiler-First

**Keuntungan:**
- Near-zero runtime — tidak ada framework library di browser
- Output JavaScript yang sangat efisien dan predictable
- Bundle size yang sangat kecil untuk komponen individual

**Kerugian:**
- Build step wajib — tidak bisa jalankan Svelte langsung di browser tanpa kompilasi
- Sintaks proprietary — `.svelte` file adalah format khusus Svelte
- Error messages bisa lebih sulit karena ada layer kompilasi antara kode kamu dan error
- Dynamic code generation lebih sulit — tidak semua pattern JavaScript bisa dikompilasi secara efisien
- Interoperabilitas dengan ekosistem React (yang sangat besar) lebih sulit

---

## Astro: Islands Architecture sebagai Compiler-First

Astro membawa filosofi compiler-first ke level yang lebih jauh untuk content-heavy sites. Premisnya: **halaman web seharusnya zero JavaScript by default**.

```astro
---
// Server-side code (runs at build time or request time)
const posts = await fetchBlogPosts();
---

<html>
  <body>
    <!-- This is static HTML, no JavaScript shipped -->
    {posts.map(post => <article><h2>{post.title}</h2></article>)}

    <!-- This "island" is interactive — only this component ships JS -->
    <SearchBar client:load />
  </body>
</html>
```

Kompiler Astro menghasilkan:
- HTML statis untuk semua content yang tidak butuh interaktivitas
- JavaScript yang sangat minimal hanya untuk "islands" yang interaktif

Dan di tiap island, kamu bisa pakai framework apapun — React, Vue, Svelte, Solid. Astro tidak memiliki opini tentang library komponen; ia hanya memastikan hanya JavaScript yang diperlukan yang dikirim ke browser.

---

## SvelteKit: Meta-Framework untuk Svelte

Seperti Next.js untuk React, SvelteKit adalah meta-framework untuk Svelte yang menambahkan:
- File-based routing
- Server-side rendering
- Static site generation
- API routes

```
src/routes/
  +page.svelte          # Halaman
  +page.server.ts       # Server-side data loading
  +layout.svelte        # Layout wrapper
  api/users/+server.ts  # API endpoint
```

SvelteKit adalah salah satu meta-framework yang paling elegant — karena Svelte sendiri sudah sangat efisien, SvelteKit memiliki overhead yang sangat kecil.

---

## Compiler-First di Dunia Backend

Pola compiler-first tidak eksklusif untuk frontend. Di backend, ada trend yang mirip:

**Vite** — build tool yang menggunakan ES modules native di development, menghasilkan output yang sangat optimal untuk production.

**esbuild** — bundler yang ditulis di Go, 10-100x lebih cepat dari webpack karena mengambil keputusan di build time yang webpack tunda ke runtime.

**Prisma** — ORM yang menggunakan schema definition untuk menghasilkan client yang type-safe. Schema dikompilasi ke type yang sangat spesifik.

Pattern yang sama: lakukan pekerjaan di build time, bukan runtime — sehingga apa yang berjalan di production lebih kecil, lebih cepat, dan lebih predictable.

---

## Pertanyaan untuk Direnungkan

1. Svelte membutuhkan build step — tapi hari ini hampir semua project JavaScript modern membutuhkan build step juga (TypeScript, Vite, bundling). Apakah "butuh compiler" masih menjadi kelemahan yang signifikan dalam konteks modern?

2. Astro's zero-JavaScript-by-default adalah sangat radikal untuk dunia yang sudah terbiasa dengan React-everywhere. Kapan pendekatan ini benar-benar worth it, dan kapan overhead mental modelnya lebih besar dari manfaatnya?

3. Runes di Svelte 5 mengambil sintaks signals-like tapi tetap compiler-based. Apakah ini membuat Svelte 5 "bergabung" dengan klaster signals, atau justru ini membuktikan bahwa klasifikasi klaster bisa berevolusi?

---

## Selanjutnya

Kalau compiler menghilangkan runtime, Qwik mengambil pendekatan yang berbeda: bagaimana kalau kita tidak perlu menjalankan JavaScript sama sekali sampai user benar-benar berinteraksi?

→ [Bab 13: Resumability](../bab-13/README.md)
