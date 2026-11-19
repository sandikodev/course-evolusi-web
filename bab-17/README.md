# Bab 17 — MACH & Composable Architecture

> *"MACH bukan framework atau teknologi — ini adalah filosofi arsitektur yang menolak one-size-fits-all solution dan memeluk spesialisasi sebagai kekuatan."*

---

## MACH: Akronim yang Merangkum Filosofi

MACH adalah akronim yang diperkenalkan MACH Alliance (2020):

- **M** — Microservices: setiap fungsi bisnis adalah service independen yang bisa di-deploy terpisah
- **A** — API-first: semua fungsionalitas exposed via API, tidak ada tight coupling
- **C** — Cloud-native: memanfaatkan cloud services daripada mengelola infrastructure sendiri
- **H** — Headless: frontend terpisah dari backend, berkomunikasi via API

Ini bukan teknologi baru — ini adalah artikulasi formal dari arsitektur yang sudah dipraktikkan oleh perusahaan teknologi besar selama bertahun-tahun.

---

## Mengapa MACH Muncul di Commerce

MACH lahir terutama dari dunia e-commerce enterprise, sebagai reaksi terhadap platform monolitik seperti Magento, SAP, atau Hybris yang memiliki masalah klasik:

**Masalah monolith commerce:**
- Upgrade versi = risiko breaking changes di seluruh sistem
- Customisasi satu fitur memerlukan deploy ulang seluruh platform
- Scaling harus dilakukan untuk seluruh sistem, bukan komponen yang bottleneck
- Teknologi terkunci ke platform vendor

**Solusi MACH:**
```
Monolith:
[Frontend + Commerce + CMS + Search + Auth + Payment]
  = Satu sistem, deploy bersama, scale bersama

MACH:
[Headless Frontend]
    ↓ API calls ke:
[Commerce API] [CMS API] [Search API] [Auth API] [Payment API]
  = Service terpisah, deploy terpisah, scale terpisah
```

---

## Headless: Pemisahan yang Fundamental

"Headless" berarti backend tidak memiliki "kepala" — tidak ada UI yang terikat padanya. Backend adalah pure API yang bisa dikonsumsi oleh siapapun.

**Headless CMS:**
- Contentful, Sanity, Strapi, Dato CMS
- Menyimpan dan mengelola konten
- Expose via API (REST atau GraphQL)
- Frontend bebas memilih cara menampilkan konten

**Headless Commerce:**
- Shopify Storefront API, Medusa, Vendure
- Mengelola produk, inventory, cart, checkout
- Frontend bebas memilih UI
- Bisa punya multiple frontends (web, mobile, kiosk) dari satu backend

**Headless Auth:**
- Auth0, Supabase Auth, Clerk
- Mengelola user authentication
- Semua frontend dan backend service bisa menggunakan service yang sama

---

## API-First: Kontrak sebagai Fondasi

API-first berarti desain API adalah langkah pertama, bukan implementasi. API adalah kontrak antara producer dan consumer.

Ini memiliki implikasi penting:

**Parallel development:** Frontend dan backend bisa dikembangkan secara paralel kalau API contract sudah disepakati. Frontend bisa mock API response sementara backend masih dibangun.

**Multiple consumers:** Satu API bisa dikonsumsi oleh web frontend, mobile app, third-party integrations, dan internal tools — tanpa perubahan ke API.

**Versioning yang explicit:** Breaking changes membutuhkan versi API baru, memberikan waktu bagi consumers untuk migrasi.

---

## Microservices: Kekuatan dan Realita

Microservices adalah arsitektur di mana aplikasi adalah kumpulan service kecil yang masing-masing menjalankan proses independen dan berkomunikasi via API atau message queue.

**Kekuatan teoritis:**
- Independent deployment
- Independent scaling
- Technology heterogeneity (service berbeda bisa pakai tech stack berbeda)
- Fault isolation (kegagalan satu service tidak cascade ke semua)

**Realita yang sering diabaikan:**

**Distributed systems complexity:** Semua masalah klasik distributed systems muncul — network failures, partial failures, eventual consistency. Debugging menjadi jauh lebih sulit.

**Operational overhead:** Monitoring 20 services jauh lebih kompleks dari monitoring 1 monolith. Butuh observability stack yang mature.

**Network latency:** Request yang dulu in-process sekarang melalui network. Latency bertambah, failure modes bertambah.

**"Death by a thousand microservices":** Teams yang over-decompose service sering menemukan diri mereka dengan service yang terlalu kecil — "nanoservices" — yang overhead komunikasinya lebih mahal dari manfaat pemisahannya.

---

## Composable Commerce: Praktik di Dunia Nyata

Implementasi MACH di e-commerce enterprise bisa terlihat seperti ini:

```
Customer-facing frontend (Next.js di Vercel)
           ↓
    ┌──────┴──────┐
    ↓             ↓
Commercetools   Contentful
(Product,       (Marketing
 Inventory,      content,
 Cart,           Blog,
 Order)          CMS)
    ↓             ↓
    └──────┬──────┘
           ↓
    Algolia Search
    (Product search,
     filtering,
     faceted navigation)
           ↓
    Stripe (Payments)
           ↓
    Auth0 (Authentication)
```

Setiap kotak adalah best-of-breed service yang bisa diganti secara independen kalau ada alternatif yang lebih baik atau lebih murah.

---

## Ketika MACH Tidak Cocok

MACH adalah solusi untuk masalah skala tertentu. Untuk banyak tim, biayanya melebihi manfaatnya.

**Masalah utama:**

**Cost:** Setiap service SaaS yang dipakai (Contentful, Commercetools, Algolia) memiliki biaya. Untuk perusahaan kecil, total cost of ownership bisa lebih tinggi dari platform monolitik.

**Complexity:** Mengintegrasikan 5-10 service pihak ketiga membutuhkan engineering effort yang signifikan. API changes di satu service bisa cascade ke effort di banyak tempat.

**Latency:** Request yang harus memanggil multiple API secara serial bisa lebih lambat dari monolith yang melakukan in-process calls.

**Team size dependency:** MACH benefits maksimal ketika ada tim yang bisa fokus pada setiap service. Tim kecil yang mengelola 10 service berbeda mungkin lebih produktif dengan monolith yang well-structured.

---

## Panduan: Kapan MACH, Kapan Monolith

**Pertimbangkan MACH ketika:**
- Tim enterprise dengan 50+ developer
- Multiple frontend channels (web, mobile, kiosk)
- Kebutuhan untuk mengganti komponen platform secara independen
- Budget untuk best-of-breed SaaS services
- Expertise dalam distributed systems

**Pertahankan monolith ketika:**
- Tim kecil (< 20 developer)
- Single channel
- Budget terbatas
- Butuh kecepatan iterasi tinggi
- Belum ada bottleneck nyata yang membutuhkan decomposition

---

## Pertanyaan untuk Direnungkan

1. MACH sering dipromosikan sebagai solusi untuk "legacy monolith yang lambat". Tapi banyak monolith yang lambat bukan karena arsitekturnya, tapi karena tidak ada investasi dalam modernisasi. Apakah MACH adalah solusi untuk masalah arsitektur, atau escapism dari masalah engineering culture?

2. Kalau setiap komponen MACH adalah best-of-breed, kamu bergantung pada banyak vendor. Bagaimana kamu mempertimbangkan risiko: salah satu vendor menaikkan harga drastis, berubah terms of service, atau shut down?

3. Microservices memerlukan mature DevOps dan observability. Di project yang pernah kamu kerjakan, apakah organisasi sudah memiliki maturity yang cukup untuk mendapat manfaat dari microservices, atau justru menambah chaos?

---

## Selanjutnya

MACH membutuhkan infrastruktur yang bisa mendukung banyak service yang independent. Edge computing mengubah cara kita berpikir tentang di mana service tersebut berjalan.

→ [Bab 18: Edge-First Architecture](../bab-18/README.md)
