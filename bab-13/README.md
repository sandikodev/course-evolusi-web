# Bab 13 — Resumability: Mempertanyakan Hidrasi

> *"Miško Hevery menciptakan AngularJS, lalu menyaksikan semua masalahnya selama bertahun-tahun. Ketika ia keluar dari Google untuk membuat Qwik, ia membawa satu insight radikal: hidrasi adalah dosa asal web modern."*

---

## Hidrasi: Masalah yang Semua Orang Terima Begitu Saja

Ketika aplikasi React (atau Vue, atau Angular) di-render di server, prosesnya terlihat efisien:

1. Server mengeksekusi kode, menghasilkan HTML
2. Browser menerima HTML, langsung tampilkan konten
3. Browser download JavaScript bundle
4. JavaScript "menghidupkan" HTML yang sudah ada (hydration)

Langkah 4 adalah yang jarang dipertanyakan. Hidrasi adalah proses di mana browser "re-render" seluruh application tree di JavaScript, mencocokkan dengan HTML yang sudah ada, dan memasang event listener.

**Masalah dengan hidrasi:**

Proses ini menduplikasi pekerjaan. Server sudah mengeksekusi kode untuk menghasilkan HTML. Browser harus mengeksekusi kode yang sama lagi hanya untuk "menghidupkan" HTML tersebut. Untuk aplikasi besar, ini bisa memakan waktu 1-5 detik di mana halaman terlihat sudah jadi tapi belum bisa diklik — **"uncanny valley" of web apps**.

---

## Qwik dan Resumability

Miško Hevery (pencipta AngularJS) merilis Qwik dengan satu pertanyaan: **"Bagaimana kalau kita tidak perlu hidrasi sama sekali?"**

Jawabannya adalah **resumability** — alih-alih menduplikasi eksekusi di client, server menyerialisasi seluruh state aplikasi ke HTML, dan browser "melanjutkan" dari sana tanpa harus re-execute.

```html
<!-- HTML yang dihasilkan Qwik -->
<button
  on:click="./chunk-abc123.js#handleClick"
  q:obj="{'count':0}"
>
  Klik (0)
</button>
```

Ketika user mengklik tombol:
1. Browser membaca `on:click` attribute
2. Download hanya chunk yang diperlukan (`chunk-abc123.js`) — bukan seluruh bundle
3. Eksekusi handler dari state yang sudah terserialisasi di HTML

Tidak ada "mount" seluruh aplikasi. Tidak ada hidrasi. Hanya download dan eksekusi kode yang benar-benar diperlukan pada saat itu.

---

## Lazy Loading sebagai Default, Bukan Optimization

Di React, code splitting adalah optimization yang dilakukan secara eksplisit dengan `React.lazy()` dan `Suspense`. Developer memutuskan bagian mana yang perlu di-lazy-load.

Di Qwik, **semua kode adalah lazy-loaded secara default**. Kompiler Qwik secara otomatis memecah seluruh kode menjadi micro-chunks yang di-load on-demand.

```typescript
// Qwik — semua handler otomatis jadi lazy chunk
export const Counter = component$(() => {
  const count = useSignal(0);

  return (
    <button onClick$={() => count.value++}>   // ← $ suffix = lazy chunk
      {count.value}
    </button>
  );
});
```

Simbol `$` menandai "seam" — titik di mana kompiler memotong kode menjadi chunk terpisah yang bisa di-lazy-load.

---

## Trade-off Resumability

**Keuntungan:**
- Time to Interactive (TTI) yang sangat cepat — tidak perlu download/eksekusi bundle besar sebelum user bisa berinteraksi
- JavaScript yang di-download hanya yang dibutuhkan untuk interaksi yang terjadi
- Sangat cocok untuk landing pages, marketing sites, e-commerce yang butuh performa awal

**Kerugian:**
- Mental model yang sangat berbeda — developer perlu memahami apa yang "seam" dan apa yang tidak
- Serialisasi state ke HTML memiliki limitasi — tidak semua state bisa di-serialisasi
- Debugging lebih sulit — eksekusi tersebar di banyak micro-chunks
- Qwik masih relatif muda dan ekosistemnya kecil
- Tidak semua aplikasi mendapat manfaat signifikan — SPAs dengan banyak interaktivitas kompleks mungkin tidak cocok

---

## Partial Hydration vs Resumability

Sebelum Qwik, ada **partial hydration** — ide yang populer di ekosistem Astro dan 11ty. Alih-alih hidrasi seluruh halaman, hanya "islands" yang interaktif yang di-hidrasikan.

```
Full hydration:    Hidrasikan seluruh halaman (expensive)
Partial hydration: Hidrasikan hanya islands yang perlu (cheaper)
Resumability:      Tidak perlu hidrasi sama sekali (cheapest)
```

Partial hydration adalah improvement dari full hydration tapi tetap membutuhkan hidrasi untuk islands. Resumability menghilangkan konsep hidrasi sama sekali.

---

## Qwik City: Meta-Framework Qwik

Qwik City adalah meta-framework yang dibangun di atas Qwik:
- File-based routing (mirip Next.js/SvelteKit)
- Loaders — data fetching yang berjalan di server dan hasilnya langsung tersedia di komponen
- Actions — form handling yang ergonomis
- Middleware

---

## Siapa yang Butuh Resumability?

Jujur: tidak semua aplikasi. Resumability paling berharga untuk:
- **E-commerce** — TTI yang cepat sangat berpengaruh ke conversion rate
- **Landing pages dan marketing sites** — setiap detik load time berpengaruh
- **Sites dengan banyak traffic** — efisiensi payload menjadi penting di skala

Untuk **admin dashboards, SaaS applications, internal tools** — user sudah mengharapkan waktu load, dan kompleksitas teknis Qwik mungkin tidak sepadan dengan manfaatnya.

---

## Pertanyaan untuk Direnungkan

1. Resumability membutuhkan developer memikirkan "seam" — titik di mana kode dipotong menjadi lazy chunks. Apakah ini cognitive overhead yang terlalu tinggi untuk produktivitas tim, atau ini adalah trade-off yang layak untuk aplikasi yang tepat?

2. Kalau TTI yang cepat adalah tujuan utama, apakah resumability lebih baik dari pendekatan alternatif seperti streaming SSR (React Server Components) atau islands architecture (Astro)?

3. Hidrasi diperlukan karena browser butuh "menjahit" HTML statis dengan reactivity. Apakah ada solusi selain resumability untuk masalah ini — atau apakah Qwik's approach adalah satu-satunya cara fundamental yang berbeda?

---

## Selanjutnya

Rendering bukan hanya tentang framework — ini tentang di mana dan kapan HTML dibuat. SSR, islands, dan streaming adalah tiga pendekatan yang berbeda untuk pertanyaan yang sama.

→ [Bab 14: SSR, Islands, dan Streaming](../bab-14/README.md)
