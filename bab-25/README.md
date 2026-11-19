# Bab 25 — Peta Menyeluruh: Semua Stack, Semua Ceruk, Semua Trade-off

> *"Peta bukan wilayah. Tapi tanpa peta, kamu tidak tahu di mana kamu berdiri."*

---

## Cara Membaca Bab Ini

Bab ini berbeda dari yang lain. Ini bukan esai dengan argumen yang mengalir — ini adalah referensi. Gunakan sebagai cheat sheet ketika kamu perlu membuat keputusan arsitektur atau positioning karier.

Setiap bagian adalah rangkuman dari hal yang sudah dibahas secara mendalam di bab-bab sebelumnya. Kalau sesuatu belum jelas, referensikan ke bab yang relevan.

---

## Peta 1: Empat Klaster Paradigma Frontend

```
KLASTER 1: FUNCTIONAL RE-RENDER
  Anggota: React, Preact, SolidJS, Qwik
  Mental model: UI = f(state)
  Sintaks: JSX/TSX
  Rendering: VDOM (React/Preact) atau Fine-grained signals (Solid/Qwik)
  Cocok untuk: Tim React-familiar, ekosistem npm luas, UI sangat dinamis
  Meta-framework: Next.js (React), SolidStart (Solid), Qwik City (Qwik)

KLASTER 2: COMPILER-FIRST SFC
  Anggota: Svelte, Astro
  Mental model: Framework sebagai compiler, bukan runtime
  Sintaks: SFC .svelte atau .astro
  Rendering: Compiled ke DOM imperatif, near-zero runtime
  Cocok untuk: Performa kritikal, content-heavy sites (Astro), eliminasi bundle
  Meta-framework: SvelteKit (Svelte), Astro sudah meta-framework

KLASTER 3: PRAGMATIC REACTIVITY SFC
  Anggota: Vue
  Mental model: Objek stateful, HTML-first, fleksibel
  Sintaks: SFC .vue (3 rasa: Options/Composition/<script setup>)
  Rendering: ES6 Proxy-based reactivity + optimized VDOM
  Cocok untuk: Tim beragam background, progressive adoption
  Meta-framework: Nuxt

KLASTER 4: ENTERPRISE CLASS OOP
  Anggota: Angular, Aurelia
  Mental model: Class yang hidup, DI, HTML murni terpisah
  Sintaks: .ts + .html terpisah, binding via atribut
  Rendering: Signals/ObserverLocator (direct DOM, no VDOM)
  Cocok untuk: Tim besar enterprise, TypeScript-first, long-term maintainability
  Meta-framework: Analog.js (Angular), tidak ada yang matang untuk Aurelia
```

---

## Peta 2: Rendering Strategies

```
CSR (Client-Side Rendering)
  Kapan: Dashboard internal, authenticated apps, tidak butuh SEO
  Trade-off: Bundle size + blank screen awal vs simplicity
  Tools: React (Vite), Vue (Vite), Angular

SSG (Static Site Generation)
  Kapan: Blog, dokumentasi, marketing, konten yang jarang berubah
  Trade-off: Build time vs TTFB ultra-cepat
  Tools: Astro, 11ty, Hugo, Next.js getStaticProps

SSR (Server-Side Rendering)
  Kapan: SEO penting, konten personalisasi, data sering berubah
  Trade-off: Server cost vs FCP cepat
  Tools: Next.js, Nuxt, SvelteKit, Analog

ISR (Incremental Static Regeneration)
  Kapan: Mostly static tapi perlu update tanpa full rebuild
  Trade-off: Kompleksitas caching vs freshness
  Tools: Next.js, Nuxt

Islands Architecture
  Kapan: Content-heavy dengan interaktivitas minimal
  Trade-off: Mental model kompleks vs minimum JavaScript
  Tools: Astro, 11ty

Streaming SSR
  Kapan: Page dengan campuran fast dan slow data
  Trade-off: Kompleksitas vs percepatan Time to First Byte
  Tools: React 18 + Next.js App Router

React Server Components
  Kapan: Komponen yang tidak butuh interaktivitas, akses DB langsung
  Trade-off: Mental model baru vs bundle size yang lebih kecil
  Tools: Next.js App Router

Resumability
  Kapan: Landing pages, e-commerce, TTI kritikal
  Trade-off: Kompleksitas developer vs zero hydration
  Tools: Qwik, Qwik City
```

---

## Peta 3: Stack Philosophy

```
CSR SPA + Headless API
  Kapan: Dashboard, authenticated apps, tim terpisah FE/BE
  Contoh: React + Hono/Express, Vue + Fastify
  Trade-off: Simplicity BE vs extra network round-trip

JAMstack
  Kapan: Marketing sites, blogs, e-commerce catalog
  Contoh: Astro + Contentful + Netlify
  Trade-off: Build time constraint vs CDN performance + security

MACH (Composable)
  Kapan: Enterprise e-commerce, multiple channel, tim besar
  Contoh: Next.js + Commercetools + Contentful + Algolia
  Trade-off: Vendor cost + integration complexity vs best-of-breed flexibility

Fullstack Meta-Framework
  Kapan: Product teams, unified deployment, faster iteration
  Contoh: Next.js, Nuxt, SvelteKit, Remix
  Trade-off: Vendor coupling (Next+Vercel) vs productivity

Edge-First
  Kapan: Global low latency, A/B testing, auth middleware
  Contoh: Cloudflare Workers + KV + Durable Objects
  Trade-off: Stateless constraint vs ultra-low latency globally
```

---

## Peta 4: Backend Patterns untuk Frontend

```
Monolith
  Kapan: Tim < 20 developer, early stage, single domain
  Trade-off: Deployment coupling vs simplicity
  
Modular Monolith
  Kapan: Tim 20-50 developer, domain boundaries masih evolving
  Trade-off: Organisasi kode lebih rumit vs deployment simplicity

BFF (Backend for Frontend)
  Kapan: Multiple clients dengan kebutuhan berbeda (web + mobile)
  Contoh: Next.js API routes, tRPC, GraphQL gateway
  Trade-off: Extra layer vs optimal data shape per client

Microservices
  Kapan: Tim 50+ developer, independent scalability nyata diperlukan
  Trade-off: Operational complexity vs independent deployability

Serverless Functions
  Kapan: Spiky traffic, event-driven, tidak ada long-running process
  Contoh: AWS Lambda, Vercel Functions, Netlify Functions
  Trade-off: Cold start + stateless constraint vs zero ops
```

---

## Peta 5: Ceruk Pasar Berdasarkan Demand vs Supply

```
SANGAT KOMPETITIF (hindari kecuali sudah senior):
  React generalist, Next.js generalist

KOMPETITIF (perlu differentiator tambahan):
  Vue/Nuxt, Angular enterprise, TypeScript fullstack

MODERAT (sweet spot untuk mid-level):
  SvelteKit, Remix, Astro, Edge computing

RENDAH KOMPETISI - HIGH VALUE (target untuk niche specialist):
  Performance optimization specialist
  Web accessibility specialist  
  Aurelia rescue/migration
  Angular 1 → modern migration
  Cloudflare Workers specialist
  Legacy stack modernization
  
EMERGING (high risk, high reward):
  AI-native frontend integration
  WebAssembly developer
  Web3 frontend (niche tapi ada)
```

---

## Peta 6: Decision Framework untuk Memilih Stack

```
Langkah 1: Definisikan use case
  → Content-heavy, mostly static? → JAMstack/Astro
  → Highly interactive SPA? → CSR dengan framework pilihan
  → SEO + interaktivitas? → SSR meta-framework
  → Real-time? → WebSockets + serverless atau edge

Langkah 2: Evaluasi tim
  → React familiar? → Next.js/Remix sebagai default
  → Vue familiar? → Nuxt
  → Angular familiar? → Analog atau SvelteKit (kalau mau belajar)
  → Fresh start, performa penting? → SvelteKit atau Astro

Langkah 3: Evaluasi constraint
  → SEO kritikal? → SSR atau SSG, jangan CSR
  → Budget server terbatas? → SSG atau edge
  → Tim besar (50+)? → Angular atau React dengan konvensi kuat
  → Tim kecil (< 10)? → Apapun yang tim sudah familiar

Langkah 4: Evaluasi deployment
  → Cloudflare? → Workers + Pages
  → Vercel? → Next.js (native)
  → Self-hosted? → SvelteKit, Astro, atau any meta-framework
  → AWS? → Lambda + CloudFront, atau Amplify
```

---

## Sintesis: Apa yang Tidak Berubah

Di tengah semua perubahan yang sudah kita bahas, ada beberapa hal yang tetap konstan:

**1. User experience adalah satu-satunya hal yang benar-benar penting.**
Framework, rendering strategy, arsitektur — semua adalah alat. Yang dievaluasi user adalah seberapa cepat halaman load, seberapa smooth interaksi, seberapa reliable aplikasi. Pilih stack yang membantu kamu deliver user experience yang baik untuk konteksmu — bukan yang terbaik secara abstract.

**2. Kompleksitas yang tidak diperlukan selalu mahal.**
Microservices yang tidak perlu, frameworks yang overkill, abstraksi yang prematur — semua memiliki cost yang nyata. Occam's Razor berlaku dalam arsitektur: pilih solusi paling sederhana yang menyelesaikan masalah dengan baik.

**3. Platform web terus berevolusi.**
Hal yang hari ini butuh library besok mungkin sudah native di browser. Skill yang paling tahan lama adalah pemahaman tentang web platform itu sendiri — HTTP, HTML, CSS, JavaScript, Web APIs — bukan fluency di framework tertentu.

**4. Trade-off tidak bisa dihindari, hanya dipilih.**
Setiap pilihan arsitektur mengorbankan sesuatu. Pekerjaan developer yang baik bukan menemukan pilihan yang tidak punya trade-off — tapi memilih trade-off yang paling sesuai dengan konteks, dan mengkomunikasikan pilihan itu dengan jelas.

---

## Pertanyaan untuk Direnungkan

1. Melihat semua peta di bab ini, di mana posisi kamu sekarang — dan ke mana kamu ingin bergerak dalam 1-2 tahun ke depan?

2. Peta mana yang paling sering kamu butuhkan dalam pekerjaan sehari-hari? Apakah ada yang terasa seperti blind spot?

3. Kalau kamu harus meringkas seluruh course ini menjadi 3 kalimat untuk seseorang yang tidak memiliki waktu membaca semuanya, apa yang kamu katakan?

---

## Selanjutnya

Ini bukan akhir dari ekosistem web — tapi akhir dari scope course ini. Di bab terakhir, kita lihat ke depan: sinyal apa yang sudah mulai terlihat, dan bagaimana cara mempersiapkan diri untuk gelombang berikutnya.

→ [Bab 26: Selanjutnya](../bab-26/README.md)
