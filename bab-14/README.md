# Bab 14 — SSR, Islands, dan Streaming: Tiga Cara Berbeda Melayani Halaman Pertama

> *"Pertanyaan 'di mana seharusnya rendering terjadi' adalah pertanyaan arsitektur, bukan pertanyaan teknis. Jawaban yang benar bergantung pada siapa usermu, apa yang mereka butuhkan, dan apa yang bisa kamu trade-off."*

---

## Mengapa Rendering Strategy Penting

Semua framework yang kita bahas di bab-bab sebelumnya bisa digunakan dengan berbagai rendering strategy. React bisa SSR (via Next.js), CSR, atau static. Vue bisa SSR (via Nuxt), CSR, atau static. Svelte bisa semuanya via SvelteKit.

Rendering strategy bukan pilihan framework — ini pilihan arsitektur yang bergantung pada:
- Seberapa penting SEO untuk aplikasi ini?
- Seberapa sering data berubah?
- Berapa banyak JavaScript yang bisa di-trade-off untuk performa awal?
- Di mana user berada (bandwidth, perangkat)?

---

## Client-Side Rendering (CSR): The Default yang Problematic

CSR adalah model yang dipopulerkan React pada awalnya: server mengirim HTML shell kosong, browser download JavaScript, React me-render seluruh UI.

```
Browser request → HTML shell (empty) → Download JS → Execute JS → Render UI
```

**Masalah utama:**
- **Blank screen** selama JavaScript di-download dan di-execute
- **SEO yang buruk** — crawler mungkin tidak menunggu JavaScript execute
- **Performance di low-end devices** — JavaScript execution di mobile bisa lambat
- **Core Web Vitals yang buruk** — LCP, FCP, TTI semua tinggi

CSR masih valid untuk:
- **Admin dashboards** — tidak butuh SEO, user sudah mengharapkan load time
- **Internal tools** — user controlled, tidak ada SEO concern
- **Highly interactive applications** — setelah load, user tetap di halaman lama

---

## Server-Side Rendering (SSR): Kembali ke Akar

SSR adalah cara web bekerja sebelum era SPA — server menghasilkan HTML penuh untuk setiap request. Framework modern mengimplementasikan SSR dengan twist: setelah HTML tiba di browser, JavaScript "menghidupkan" halaman (hydration) untuk interaktivitas.

```
Browser request → Server execute app code → HTML penuh → Kirim ke browser
                                            → Browser tampilkan HTML (cepat!)
                                            → Download JS → Hydration → Interaktif
```

**Keuntungan:**
- FCP (First Contentful Paint) yang cepat — user melihat konten segera
- SEO yang baik — crawler mendapat HTML penuh
- Performa di perangkat lambat yang lebih baik

**Kerugian:**
- Server load lebih tinggi (harus execute app code per request)
- TTI bisa masih lambat jika hydration JavaScript besar
- State mismatch antara server dan client bisa menyebabkan hydration errors
- Caching lebih kompleks — konten personalisasi tidak bisa di-cache di CDN layer

---

## Static Site Generation (SSG): Rendering di Build Time

SSG menghasilkan HTML di build time, bukan di request time. Hasilnya adalah file HTML statis yang bisa disajikan dari CDN.

```
Build time:    Execute app code → Generate all HTML files → Deploy ke CDN
Request time:  CDN mengirim file HTML yang sudah jadi (sangat cepat)
```

**Keuntungan:**
- TTFB (Time to First Byte) yang sangat cepat — CDN edge sangat dekat dengan user
- Scaling yang sangat mudah — static files, tidak ada server yang perlu di-scale
- Reliability tinggi — tidak ada database atau server yang bisa fail per request

**Kerugian:**
- Tidak cocok untuk konten yang sering berubah (harus rebuild setiap perubahan)
- Build time bisa sangat lama untuk sites dengan banyak halaman
- Personalisasi tidak mungkin di static files

---

## Incremental Static Regeneration (ISR): Hybrid yang Pragmatis

ISR, dipopulerkan oleh Next.js, adalah hybrid antara SSR dan SSG: halaman di-generate secara statis tapi bisa di-regenerate di background ketika data berubah.

```
Request 1: Belum ada cache → Generate HTML → Simpan → Kirim ke user
Request 2: Cache ada → Kirim dari cache → (di background, regenerate kalau stale)
```

Ini memungkinkan aplikasi yang "mostly static" (blog, e-commerce catalog) untuk mendapat keuntungan CDN caching tanpa harus rebuild setiap perubahan.

---

## Islands Architecture: Minimum JavaScript

Islands architecture (dipopulerkan oleh Astro, diinspirasi essay Jason Miller "Islands Architecture" 2019) adalah model di mana halaman default adalah HTML statis, dan hanya "islands" yang interaktif yang mendapat JavaScript.

```html
<!-- Halaman Astro — kebanyakan static HTML -->
<main>
  <article>  <!-- Static, tidak ada JS -->
    <h1>{post.title}</h1>
    <p>{post.content}</p>
  </article>

  <!-- Island — mendapat JS untuk interaktivitas -->
  <CommentSection client:visible />
  <ShareButton client:load />
</main>
```

`client:load` — hydrate segera saat page load  
`client:visible` — hydrate ketika island masuk viewport  
`client:idle` — hydrate ketika browser idle  
`client:media="(max-width: 600px)"` — hydrate only on mobile

**Ini adalah partial hydration** — hanya islands yang mendapat JavaScript, sisa halaman adalah HTML murni.

---

## Streaming SSR: Render sambil Fetch

Streaming SSR adalah teknik di mana server mulai mengirim HTML ke browser sebelum seluruh halaman selesai di-render. Browser bisa mulai menampilkan konten pertama sementara server masih memproses sisa halaman.

```
Server mulai render header → Kirim ke browser
Browser tampilkan header
Server fetch data user (async)
Server render sidebar → Kirim ke browser
Browser tampilkan sidebar
Server selesai fetch data user → Render main content → Kirim ke browser
Browser tampilkan main content
```

React 18 mendukung ini via `Suspense` dan `renderToPipeableStream`. Next.js App Router dibangun di atas streaming.

```jsx
// Next.js App Router dengan Streaming
export default function Dashboard() {
  return (
    <main>
      <Header />  {/* Dikirim pertama */}
      <Suspense fallback={<Loading />}>
        <SlowDataComponent />  {/* Dikirim saat data siap */}
      </Suspense>
    </main>
  );
}
```

---

## React Server Components: Rendering tanpa JavaScript

React Server Components (RSC) adalah abstraksi yang lebih jauh — komponen yang berjalan **hanya di server**, tidak pernah di-hydrate, dan tidak ada JavaScript yang dikirim ke browser untuk mereka.

```jsx
// Server Component (default di Next.js App Router)
// Bisa mengakses database langsung, tidak ada JS yang dikirim ke browser
async function UserProfile({ userId }) {
  const user = await db.users.findById(userId); // Direct DB access
  return <div>{user.name}</div>; // Pure HTML, tidak ada hydration
}

// Client Component — eksplisit dengan 'use client'
'use client';
function LikeButton({ count }) {
  const [liked, setLiked] = useState(false);
  return <button onClick={() => setLiked(!liked)}>{count}</button>;
}
```

**Implikasi:** Komponen yang tidak butuh interaktivitas tidak perlu mengirim JavaScript ke browser sama sekali. Ini mengurangi bundle size secara drastis untuk halaman yang mostly static.

---

## Memilih Rendering Strategy

Tidak ada satu strategi yang optimal untuk semua kasus. Panduan praktis:

| Use case | Strategi yang disarankan |
|----------|--------------------------|
| Blog, dokumentasi, marketing | SSG + sedikit SSR untuk konten yang personalized |
| E-commerce catalog | SSG/ISR untuk product pages, CSR untuk cart/checkout |
| Dashboard internal | CSR — tidak butuh SEO, caching tidak prioritas |
| News site | SSR dengan caching agresif |
| Social media feed | Streaming SSR |
| Landing page dengan high traffic | SSG, deploy ke CDN |
| Highly personalized content | SSR atau CSR |

---

## Pertanyaan untuk Direnungkan

1. Banyak tim memilih CSR (React SPA) sebagai default bahkan untuk aplikasi yang butuh SEO, bukan karena ini pilihan terbaik tapi karena "ini yang paling familiar". Apakah project yang pernah kamu kerjakan bisa mendapat manfaat signifikan dari rendering strategy yang berbeda?

2. Islands architecture (Astro) sangat efisien untuk content-heavy sites. Tapi adopsinya masih jauh di bawah React/Next.js. Apa yang menurutmu paling menghambat adopsi pendekatan ini?

3. React Server Components memungkinkan akses database langsung dari komponen UI. Apakah ini "fullstack yang elegan" atau "separation of concerns yang rusak"?

---

## Selanjutnya

Rendering strategy adalah satu bagian dari ekosistem yang lebih besar. Meta-frameworks lahir untuk menyatukan rendering strategy, routing, data fetching, dan deployment menjadi satu pengalaman yang kohesif.

→ [Bab 15: Meta-Frameworks](../bab-15/README.md)
