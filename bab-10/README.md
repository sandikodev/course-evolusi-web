# Bab 10 — VDOM: Kekuatan, Keterbatasan, dan Kapan Tidak Dibutuhkan

> *"Virtual DOM adalah abstraksi yang sangat bagus untuk masalah yang sangat spesifik. Masalahnya: ia sering dipakai untuk masalah yang tidak membutuhkannya."*

---

Virtual DOM adalah salah satu abstraksi paling berpengaruh dalam sejarah web development. Dipopulerkan oleh React, kemudian diadopsi Vue, dan terus jadi referensi diskusi performa. Tapi apa sebenarnya yang dilakukan VDOM, kapan ia benar-benar menguntungkan, dan kapan ia hanya overhead?

---

## Apa yang Sebenarnya Dilakukan VDOM

Masalah yang ingin diselesaikan VDOM adalah ini: **DOM operation mahal**. Mengubah DOM bisa memicu reflow (recalculate layout), repaint (redraw pixels), dan compositing — semua operasi yang melibatkan browser rendering pipeline yang mahal.

Tanpa abstraksi, developer harus secara manual memutuskan operasi DOM minimal yang dibutuhkan untuk transisi dari state A ke state B. Ini sulit, error-prone, dan tidak maintainable di skala besar.

VDOM menawarkan trade-off: biarkan framework menghitung operasi minimal yang dibutuhkan, dengan cost membuat representasi JavaScript dari DOM di memori dan melakukan diff.

```
State A (lama)     State B (baru)
      ↓                  ↓
  VDOM tree A       VDOM tree B
           ↓         ↓
              Diffing
                ↓
         Minimal patch
                ↓
          DOM operations
```

**Yang sering disalahpahami:** VDOM bukan lebih cepat dari DOM. VDOM *lebih predictable* dalam menentukan operasi DOM minimal yang dibutuhkan, dibanding developer yang melakukan DOM manipulation manual.

---

## Algoritma Diff: Tradeoff di Balik Layar

React's reconciliation algorithm membuat beberapa asumsi untuk membuat diffing efisien (O(n) bukan O(n³)):

1. **Komponen yang berbeda tipe menghasilkan tree yang berbeda** — kalau `<div>` diganti `<span>`, React tidak mencoba recycling, tapi rebuild seluruh subtree
2. **`key` prop memungkinkan React mencocokkan elemen di list yang berurutan berubah**

```jsx
// Tanpa key — React mungkin re-render semua item saat list berubah
{items.map(item => <Item data={item} />)}

// Dengan key — React bisa cocokkan item yang ada dan hanya update yang berubah
{items.map(item => <Item key={item.id} data={item} />)}
```

Kalau kamu pernah mendapat warning "Each child in a list should have a unique key" — ini adalah kebutuhan algoritma diff untuk bekerja efisien.

---

## Di Mana VDOM Sangat Tepat

**UI yang sangat dinamis dengan banyak concurrent updates:**
Ketika banyak hal berubah sekaligus — notifications masuk, counter terupdate, list berubah, tooltip muncul — VDOM memungkinkan batching semua update tersebut dan menghitung satu minimal patch ke DOM.

**Rendering yang unpredictable:**
Kalau kamu tidak tahu seberapa banyak DOM yang akan berubah dari satu render ke render berikutnya, VDOM memberikan "auto-optimization" yang reasonable.

**Tim yang besar dengan developer dari berbagai level:**
VDOM memungkinkan developer menulis kode yang declarative tanpa memikirkan DOM optimization. Framework yang handle optimisasi — developer cukup fokus ke logika bisnis.

---

## Di Mana VDOM Menjadi Overhead

**Komponen yang jarang berubah:**
Kalau sebuah komponen merender satu kali dan jarang update, kamu membayar overhead VDOM untuk keuntungan yang minimal.

**Aplikasi yang lebih mirip "dokumen" dari "aplikasi":**
Blog, landing page, dokumentasi — tidak butuh reactive UI yang complex. VDOM adalah overkill.

**Update yang sangat fine-grained:**
Kalau kamu tahu persis satu properti yang berubah (counter yang naik 1), VDOM melakukan pekerjaan lebih dari yang dibutuhkan. Fine-grained reactivity (signals) lebih efisien di sini.

---

## React Fiber: Ketika VDOM Bertemu Concurrency

React 16 memperkenalkan **Fiber** — reimplementasi reconciler yang memungkinkan rendering interruptible. Sebelum Fiber, reconciliation adalah proses synchronous yang tidak bisa dihentikan — kalau tree besar, browser bisa freeze sementara React melakukan diff.

Fiber memecah reconciliation menjadi unit kerja kecil yang bisa dijeda dan dilanjutkan:

```
Render phase (interruptible):
  ← Hitung apa yang perlu berubah, bisa dipause
  ← Prioritize high-priority updates (user interaction > data loading)

Commit phase (synchronous):
  → Terapkan perubahan ke DOM (tidak bisa dipause — harus atomic)
```

Ini adalah fondasi untuk **Concurrent Mode** dan features seperti `useTransition` (tandai update sebagai non-urgent) dan `useDeferredValue` (defer update yang tidak kritikal).

---

## Kritik yang Adil terhadap VDOM

**"VDOM adalah pure overhead"** — terlalu simplistic. VDOM memiliki overhead nyata (memory, CPU untuk diff), tapi juga menggantikan overhead lain (developer harus manual manage DOM). Trade-off ini worth it untuk banyak use case.

**"Svelte lebih cepat dari React"** — benchmark yang sering dikutip. Benar untuk microbenchmark. Tapi di aplikasi production yang kompleks, performa sering ditentukan oleh faktor lain (network, database, rendering pipeline). Perbedaan framework biasanya bukan bottleneck utama.

**"Semua framework seharusnya tidak pakai VDOM"** — pendapat yang lebih nuanced. Signals dan compiler-first approach memang lebih efisien untuk banyak use case. Tapi VDOM masih valid untuk use case di mana unpredictability update yang tinggi adalah kenyataan.

---

## Alternatif VDOM di Dunia Nyata

**Fine-grained Signals (Solid, Vue, Angular):**
Tracking dependency di level properti individual. Ketika `count` berubah, hanya binding yang bergantung pada `count` yang diupdate — tanpa diff.

**Compiler-based (Svelte):**
Kompiler menganalisis template dan menghasilkan kode imperatif yang langsung update DOM. Tidak ada runtime diffing sama sekali.

**Incremental DOM (Angular sebelum Signals):**
Alih-alih membuat dua tree dan diff, Angular merender ke DOM langsung tapi melacak apa yang berubah.

Semua alternatif ini lebih efisien dari VDOM untuk use case yang sesuai. Tapi mereka juga punya constraint — tidak ada yang universally superior.

---

## Pertanyaan untuk Direnungkan

1. Kalau VDOM adalah trade-off yang baik untuk "UI yang sangat dinamis", berapa persen dari UI yang kamu build sehari-hari benar-benar "sangat dinamis"? Apakah kamu sering over-engineering dengan framework yang lebih powerful dari yang dibutuhkan?

2. React Concurrent Mode memungkinkan rendering yang bisa diprioritaskan. Apakah kamu pernah menggunakan `useTransition` atau `useDeferredValue` di production? Kalau tidak, apakah ini karena kamu tidak perlu atau karena kamu tidak tahu ada?

3. Benchmark "Svelte lebih cepat dari React" sering dikutip tapi jarang relevan di production. Bagaimana kamu seharusnya melakukan performance evaluation yang lebih valid untuk keputusan framework di project nyata?

---

## Selanjutnya

Semua framework sedang konvergen ke signals. Tapi tidak semua "signals" itu sama — dan memahami perbedaannya penting untuk membuat keputusan yang tepat.

→ [Bab 11: Signals — Konvergensi yang Terjadi Diam-Diam](../bab-11/README.md)


---

## VDOM di Era React Server Components

React Server Components (RSC) mengubah cara kita memandang VDOM secara fundamental. Server Components tidak di-render ke Virtual DOM — mereka di-render ke format wire khusus (React Server Component Payload) yang dikirim ke browser.

```
Server Component → RSC Payload (tidak ada JS ke browser)
Client Component → JavaScript + Virtual DOM (hydration seperti biasa)
```

Ini artinya VDOM sekarang bukan lagi arsitektur yang monolitik untuk seluruh aplikasi — melainkan tool yang dipakai selektif hanya untuk bagian yang interaktif. Komponen yang tidak butuh interaktivitas tidak butuh VDOM sama sekali.

Ini adalah langkah React menuju paradigma yang lebih mirip Islands Architecture — tapi dari dalam framework React sendiri, bukan sebagai framework baru.

**Ironi yang menarik:** React, yang mempopulerkan VDOM, kini sedang membangun jalan keluar dari VDOM untuk bagian aplikasi yang tidak memerlukannya.
