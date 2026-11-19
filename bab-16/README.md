# Bab 16 — JAMstack: Filosofi, Implementasi, dan Batasannya

> *"JAMstack bukan hanya tentang teknis. Ini adalah pergeseran cara berpikir tentang bagaimana web seharusnya dibangun — dari 'server yang mengelola segalanya' ke 'API yang spesialis, frontend yang mandiri'."*

---

## JAMstack: Lebih dari Akronim

JAMstack — JavaScript, APIs, Markup — pertama kali dipopulerkan Mathias Biilmann (CEO Netlify) sekitar 2015-2016. Tapi ini bukan hanya deskripsi teknologi — ini adalah filosofi arsitektur.

**Prinsip inti JAMstack:**

1. **Pre-rendering**: Sebanyak mungkin markup di-generate sebelum request (build time atau edge)
2. **Decoupling**: Frontend terpisah dari backend/CMS
3. **API-first**: Data dan fungsi diakses via APIs, bukan server-side tightly coupled
4. **CDN-first**: Konten disajikan dari CDN, bukan origin server

---

## Mengapa JAMstack Muncul

Arsitektur web tradisional (WordPress, Drupal, Monolith) memiliki beberapa masalah:

**Scaling yang mahal:** Server harus bisa handle traffic spike. Scaling berarti provisioning lebih banyak server.

**Security surface yang besar:** Database, server runtime, CMS admin — semua adalah attack vector.

**Performance yang tidak konsisten:** Server render memiliki latency yang bergantung pada server location dan load.

**Developer experience yang buruk:** Deploy sering berarti FTP ke shared hosting, atau setup server infrastructure yang kompleks.

JAMstack menjawab semua ini:
- **Scaling:** Static files di CDN — CDN meng-handle traffic spike secara otomatis
- **Security:** Tidak ada runtime server yang bisa di-compromise, tidak ada database yang exposed
- **Performance:** CDN edge sangat dekat dengan user — TTFB yang sangat rendah
- **DX:** Git push → build → deploy dalam minutes

---

## Ekosistem JAMstack

JAMstack melahirkan ekosistem yang kaya:

**Static Site Generators (SSG):**
- Hugo (Go) — sangat cepat untuk build
- Eleventy (11ty) — JavaScript, sangat fleksibel
- Gatsby — React-based, sangat powerful tapi berat
- Jekyll — Ruby, sudah ada sebelum "JAMstack" populer
- Astro — modern, component-based

**Headless CMS:**
- Contentful
- Sanity
- Strapi (self-hosted)
- Dato CMS
- Prismic

**Deployment Platforms:**
- Netlify (yang mempopulerkan JAMstack)
- Vercel
- Cloudflare Pages
- GitHub Pages

---

## JAMstack di Praktik: E-Commerce

E-commerce adalah use case yang baik untuk melihat JAMstack dalam aksi:

```
Contentful (CMS)          Shopify (Commerce)
       ↓                        ↓
  Build time:
  Fetch products dari API → Generate HTML halaman product
       ↓
  Deploy ke Netlify CDN
       ↓
  User request /products/t-shirt
       ↓
  CDN kirim pre-built HTML (sangat cepat)
       ↓
  Client-side: add to cart (Shopify API), checkout
```

Halaman product: pre-built, cepat, SEO friendly.
Cart dan checkout: client-side, real-time, butuh JavaScript.

Ini adalah islands architecture dalam konteks JAMstack — sebagian besar konten static, interaktivitas hanya di tempat yang perlu.

---

## Batas-Batas JAMstack

JAMstack tidak cocok untuk semua kasus. Memahami batasannya sama pentingnya dengan memahami kekuatannya:

**Konten yang sangat sering berubah:**
Kalau product catalog berubah setiap menit, build ulang setiap menit tidak praktis. ISR (Incremental Static Regeneration) membantu tapi menambah kompleksitas.

**Personalisasi yang dalam:**
Konten yang berbeda untuk setiap user sulit di-cache di CDN. JAMstack + heavy personalization seringkali berarti CSR di client — yang menghilangkan beberapa keuntungan JAMstack.

**Real-time features:**
Live dashboard, chat, collaborative editing — semua membutuhkan koneksi yang persistent atau polling yang tidak cocok dengan pure static.

**Kompleksitas build:**
Sites dengan ratusan ribu halaman bisa punya build time yang sangat lama. Gatsby pernah terkenal dengan build yang bisa berjam-jam untuk sites besar.

---

## Evolusi JAMstack: Composable Architecture

JAMstack telah berkembang. Istilah "Composable Architecture" atau "MACH Architecture" mencerminkan evolusi ini — bukan hanya tentang pre-rendering, tapi tentang bagaimana semua bagian stack dikomposisikan.

**Dari JAMstack yang naif:**
```
Build all pages statically → Deploy ke CDN
```

**Ke JAMstack yang mature:**
```
SSG untuk konten yang stabil
ISR untuk konten yang jarang berubah
SSR untuk konten yang sangat personalized
Edge functions untuk transformasi dan A/B testing
Client-side untuk interaktivitas
```

Tidak lagi "gunakan satu pendekatan", tapi "gunakan pendekatan yang tepat untuk setiap bagian."

---

## Pertanyaan untuk Direnungkan

1. JAMstack menjanjikan security yang lebih baik karena "tidak ada server yang bisa di-compromise." Tapi kamu tetap bergantung pada API services pihak ketiga — CMS, auth, payment. Apakah ini benar-benar mengurangi security surface, atau hanya memindahkannya?

2. JAMstack memanfaatkan CDN edge untuk performa global. Tapi banyak aplikasi yang kamu kerjakan mungkin tidak butuh global distribution — usernya lokal. Apakah JAMstack masih worth it untuk aplikasi dengan user base yang terpusat?

3. Vercel dan Netlify sangat memudahkan deployment JAMstack. Tapi ini berarti banyak tim bergantung pada vendor yang spesifik. Bagaimana kamu mempertimbangkan vendor lock-in dalam architecture decision untuk proyek jangka panjang?

---

## Selanjutnya

JAMstack adalah salah satu manifestasi dari filosofi yang lebih besar: composable architecture. MACH adalah artikulasi yang lebih formal dari filosofi ini di skala enterprise.

→ [Bab 17: MACH & Composable Architecture](../bab-17/README.md)
