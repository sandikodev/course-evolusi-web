# Bab 20 — Micro-Frontends: Hype atau Solusi Nyata?

> *"Micro-frontends menerapkan prinsip microservices ke frontend. Seperti microservices, ia menyelesaikan masalah organisasi tim tertentu — dan seperti microservices, ia sering diterapkan oleh tim yang belum punya masalah itu."*

---

## Apa Itu Micro-Frontends

Micro-frontends adalah arsitektur di mana satu aplikasi frontend besar dipecah menjadi bagian-bagian yang lebih kecil — masing-masing milik tim yang berbeda, bisa di-deploy secara independen, dan dikomposis menjadi satu pengalaman yang kohesif bagi user.

```
Monolithic Frontend:         Micro-Frontend:
                             
Team A                       Team A → Header MFE
Team B          →            Team B → Product Catalog MFE
Team C   satu                Team C → Cart & Checkout MFE
Team D   repo                Team D → User Account MFE
         satu                         ↓
         deploy              Composed App Shell
```

---

## Motivasi yang Legitimate

Sebelum menilai apakah micro-frontends adalah solusi yang tepat, penting untuk memahami masalah yang dimaksud diselesaikan.

**Problem 1: Conway's Law di Frontend**

Conway's Law: "Organizations design systems that mirror their own communication structure."

Kalau kamu punya 4 tim yang terpisah mengerjakan satu frontend monolith, mereka akan saling blocking satu sama lain dalam proses deployment. Tim A tidak bisa deploy fitur baru tanpa menunggu Tim B, C, dan D siap. Ini menghambat delivery velocity.

**Problem 2: Technology Heterogeneity**

Tim yang berbeda mungkin sudah memiliki expertise di framework yang berbeda. Micro-frontends memungkinkan setiap tim memilih teknologi yang paling produktif untuk mereka.

**Problem 3: Independent Deployability**

Kalau salah satu bagian aplikasi perlu update darurat (misalnya checkout yang rusak), dengan monolith kamu harus deploy seluruh frontend. Dengan micro-frontends, hanya bagian yang relevan yang perlu di-deploy.

---

## Implementasi Micro-Frontends

Ada beberapa pendekatan implementasi:

**Build-time Integration:**
Setiap MFE di-publish sebagai npm package, lalu di-compose di satu project.
- Simple tapi deployment tidak benar-benar independen

**Server-side Composition:**
Server mengagregasi HTML dari multiple MFE services.
- Langkah pertama ke independence nyata, tapi latency bisa menjadi isu

**Iframe Composition:**
Setiap MFE dalam iframe yang terpisah.
- Isolasi sempurna tapi UX yang sangat buruk (scroll, communication, styling)

**Client-side Composition via Module Federation:**
Webpack 5 Module Federation memungkinkan aplikasi berbeda berbagi modul di runtime.

```javascript
// webpack.config.js di MFE Cart
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'cart',
      filename: 'remoteEntry.js',
      exposes: {
        './CartWidget': './src/CartWidget',
      },
    }),
  ],
};

// webpack.config.js di App Shell
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      remotes: {
        cart: 'cart@https://cart.example.com/remoteEntry.js',
      },
    }),
  ],
};
```

```javascript
// App Shell menggunakan Cart yang di-deploy terpisah
const CartWidget = React.lazy(() => import('cart/CartWidget'));
```

---

## Tantangan Micro-Frontends

**Consistency yang sulit dijaga:**
Kalau setiap tim menggunakan framework yang berbeda, design system harus sangat robust untuk menjaga konsistensi visual. Shared component library yang perlu di-maintain lintas tim.

**Duplikasi dependency:**
Kalau Tim A menggunakan React 18 dan Tim B menggunakan React 18, apakah mereka berbagi satu React instance atau dua? Dua React instances menyebabkan masalah. Module Federation membantu tapi tidak mengeliminasi masalah ini sepenuhnya.

**Performance:**
Multiple JavaScript bundles dari multiple teams yang diload di halaman yang sama. Tanpa koordinasi yang baik, Total Blocking Time bisa meningkat signifikan.

**Testing yang kompleks:**
Integration testing antar MFE sangat sulit. Siapa yang bertanggung jawab untuk test yang melibatkan lebih dari satu MFE?

**Deployment coordination:**
Meskipun deployment idealnya independen, ada kontrak antar MFE (API, shared state, events) yang bisa break ketika salah satu berubah.

---

## Kapan Micro-Frontends Justifiable

Micro-frontends menambah kompleksitas yang signifikan. Benefit-nya nyata, tapi hanya untuk organisasi yang sudah mencapai skala tertentu.

**Pertimbangkan micro-frontends ketika:**
- Multiple tim (5+ tim) mengerjakan satu frontend dan sering blocking satu sama lain
- Tim yang berbeda perlu deploy secara benar-benar independen
- Bagian aplikasi yang berbeda memiliki lifecycle release yang sangat berbeda
- Tim sudah mature dalam DevOps dan monitoring

**Hindari micro-frontends ketika:**
- Tim < 5 orang
- Tim bisa koordinasi deployment dengan mudah
- Aplikasi belum mature (premature optimization)
- Tidak ada masalah organisasi yang nyata — hanya menganggap ini "best practice"

---

## Single-SPA: Framework untuk Micro-Frontends

Single-SPA adalah framework yang memudahkan komposisi micro-frontends, terlepas dari framework yang digunakan setiap MFE:

```javascript
// App shell - bisa mengkomposisi React, Vue, Angular MFE bersamaan
import { registerApplication, start } from 'single-spa';

registerApplication({
  name: 'header',
  app: () => import('@myorg/header'),
  activeWhen: '/',
});

registerApplication({
  name: 'checkout',
  app: () => import('@myorg/checkout'),
  activeWhen: '/checkout',
});

start();
```

---

## Micro-Frontends vs Monorepo

Penting untuk tidak mencampuradukkan "micro-frontends" dengan "monorepo". Ini adalah solusi untuk masalah yang berbeda:

**Monorepo:** Semua kode di satu repository, tapi bisa punya multiple packages/apps. Deployment bisa tetap terpisah. Tools: Nx, Turborepo.

**Micro-frontends:** Deployment benar-benar independen, bisa beda repository. Komposisi terjadi di runtime, bukan build time.

Banyak tim mengira mereka butuh micro-frontends padahal yang mereka butuhkan adalah monorepo dengan build caching yang baik — jauh lebih sederhana.

---

## Pertanyaan untuk Direnungkan

1. Micro-frontends sering dijustifikasi dengan "Conway's Law" — arsitektur harus mencerminkan struktur tim. Tapi ada argumen sebaliknya: arsitektur yang baik seharusnya membantu tim berkolaborasi lebih baik, bukan hanya mencerminkan silo yang sudah ada. Mana yang lebih mendekati kebenaran?

2. Module Federation memungkinkan MFE berbeda pakai framework berbeda. Tapi ini juga berarti user mungkin harus download React, Vue, dan Angular di halaman yang sama. Apakah technology heterogeneity di micro-frontends adalah fitur atau anti-pattern?

3. Banyak perusahaan yang mengadopsi micro-frontends akhirnya kembali ke modular monolith karena kompleksitasnya tidak sepadan. Apa yang bisa kita pelajari dari cycle ini tentang bagaimana seharusnya kita mengevaluasi trend arsitektur?

---

## Selanjutnya

Micro-frontends adalah respons terhadap kompleksitas yang tumbuh. Tapi ada argumen kuat bahwa banyak kompleksitas itu tidak perlu ada — monolith yang terstruktur dengan baik bisa sangat powerful.

→ [Bab 21: Monolith yang Terhormat vs Distribusi yang Prematur](../bab-21/README.md)
