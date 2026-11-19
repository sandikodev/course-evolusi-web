# Bab 15 — Meta-Frameworks: Mengapa Mereka Semua Ada

> *"Framework menyelesaikan masalah rendering dan state. Meta-framework menyelesaikan masalah bagaimana kamu menyatukan rendering, routing, data fetching, deployment, dan developer experience menjadi satu kesatuan yang kohesif."*

---

## Masalah yang Meta-Frameworks Selesaikan

Ketika React mencapai popularitas besar, developer menghadapi masalah baru: React hanya menyelesaikan bagian rendering. Untuk membangun aplikasi production yang lengkap, kamu masih perlu:

- **Routing** — React Router, atau buat sendiri
- **Data fetching** — `useEffect` + fetch, atau SWR, atau React Query
- **SSR** — setup Webpack + babel + server yang sangat kompleks
- **Code splitting** — konfigurasi manual
- **CSS** — pilih dan configure sendiri
- **Testing** — setup Jest + Testing Library sendiri
- **Build & deployment** — Webpack config yang bisa mencapai ratusan baris

Ini adalah "configuration fatigue" yang menjadi kritik besar terhadap ekosistem JavaScript. Meta-frameworks lahir untuk menyediakan opinionated solution yang menyatukan semua ini.

---

## Next.js: React Meta-Framework yang Mendominasi

Vercel merilis Next.js pada Oktober 2016 dengan premis sederhana: React dengan SSR yang mudah disetup. Dari versi ke versi, Next.js berkembang menjadi meta-framework yang sangat komprehensif.

**Evolusi Next.js yang penting:**

**Pages Router (original):** File-based routing dengan `pages/` directory. `getStaticProps`, `getServerSideProps`, `getStaticPaths` untuk data fetching.

**App Router (Next.js 13+):** Rewrite besar yang memanfaatkan React Server Components. `app/` directory. Layout nesting. Streaming built-in. Server Actions untuk mutations.

```
app/
  layout.tsx          # Root layout
  page.tsx            # Home page
  about/
    page.tsx          # /about
  dashboard/
    layout.tsx        # Dashboard layout
    page.tsx          # /dashboard
    settings/
      page.tsx        # /dashboard/settings
```

**Next.js dan Vercel:** Next.js dikembangkan oleh Vercel, dan ini adalah elephant in the room. Banyak fitur Next.js bekerja paling baik ketika di-deploy ke Vercel. `next/image` optimal di Vercel. Edge functions seamless di Vercel. Ini bukan conspiracy — tapi ini adalah vendor consideration yang perlu disadari.

---

## Nuxt: Vue Meta-Framework

Nuxt (saat ini versi 4) adalah equivalent Next.js untuk Vue. File-based routing, SSR/SSG, TypeScript, universal rendering.

Yang menarik tentang Nuxt adalah filosofi "auto-imports" — komponen, composables, utils bisa diimport secara otomatis tanpa import statement eksplisit:

```typescript
// Tanpa auto-import:
import { ref, computed } from 'vue';
import MyComponent from '~/components/MyComponent.vue';

// Dengan Nuxt auto-imports — ini langsung tersedia:
const count = ref(0);
const doubled = computed(() => count.value * 2);
// MyComponent juga tersedia di template tanpa import
```

Ini sangat convenient tapi bisa membuat debugging lebih sulit karena tidak jelas dari mana sesuatu berasal.

---

## SvelteKit: Svelte Meta-Framework

SvelteKit adalah meta-framework Svelte yang sangat elegant karena Svelte sendiri sudah sangat efisien. Routing dan data loading sangat terintegrasi:

```typescript
// src/routes/users/[id]/+page.server.ts
import type { PageServerLoad } from './$types';

export const load: PageServerLoad = async ({ params, fetch }) => {
  const user = await fetch(`/api/users/${params.id}`).then(r => r.json());
  return { user };
};
```

```svelte
<!-- src/routes/users/[id]/+page.svelte -->
<script>
  export let data; // Automatically typed from load function
</script>

<h1>{data.user.name}</h1>
```

Type safety end-to-end antara server load function dan client component, tanpa konfigurasi tambahan.

---

## Analog.js: Angular Meta-Framework

Angular memiliki Angular Universal untuk SSR, tapi Analog.js (community project) mengambil pendekatan meta-framework yang lebih modern:

- Vite-based (lebih cepat dari Angular CLI default)
- File-based routing
- API routes
- SSR/SSG support

Ini adalah contoh menarik: komunitas Angular membangun meta-framework mereka sendiri karena Angular resmi tidak bergerak cukup cepat ke pola modern.

---

## Pola yang Semua Meta-Framework Berbagi

Meskipun berbeda implementasi, semua meta-framework modern berbagi pola yang sama:

**File-based routing** — struktur direktori menentukan routes:
```
pages/ atau app/ atau src/routes/
  index.tsx            → /
  about.tsx            → /about
  users/[id].tsx       → /users/:id
```

**Co-located data loading** — data fetching bersamaan dengan komponen:
```
page.tsx               → UI komponen
page.server.ts         → Data yang diload di server
```

**Layouts** — wrapper yang bisa nested:
```
layout.tsx             → Dibungkus ke semua children routes
users/layout.tsx       → Hanya dibungkus ke routes di bawah /users
```

**API routes** — backend endpoints dalam satu project:
```
api/users/route.ts     → GET/POST /api/users
```

---

## Trade-off Meta-Framework

**Keuntungan:**
- Produktivitas tinggi dari convention yang sudah ada
- Deployment yang mudah (terutama ke platform yang didesain untuk framework tersebut)
- Best practices sudah built-in
- Community dan dokumentasi yang besar

**Kerugian:**
- Vendor lock-in yang potensial (terutama Next.js ke Vercel)
- Learning curve untuk "cara meta-framework" di atas "cara framework"
- Upgrade breaking changes yang sering (Next.js App Router vs Pages Router)
- Kurang flexibel untuk use case yang keluar dari opinionated path

---

## Pertanyaan untuk Direnungkan

1. Next.js dan Vercel memiliki hubungan yang sangat dekat. Seberapa jauh ini mempengaruhi keputusan arsitektur yang "terasa natural" di Next.js tapi sebenarnya optimized untuk deployment ke Vercel?

2. Auto-imports di Nuxt sangat convenient tapi mengurangi explicitness kode. Di mana kamu menarik garis antara "magic yang produktif" dan "magic yang membingungkan"?

3. SvelteKit, Nuxt, dan Next.js semua mengkonverge ke pola yang hampir sama (file-based routing, co-located data loading, layouts). Apakah ini tanda konvergensi yang sehat, atau semua framework hanya mengikuti Next.js karena popularitasnya?

---

## Selanjutnya

Meta-frameworks menyelesaikan masalah bagaimana membangun aplikasi. Tapi ada pertanyaan yang lebih besar tentang bagaimana konten dan data seharusnya diorganisir. JAMstack adalah filosofi yang menjawab pertanyaan itu.

→ [Bab 16: JAMstack](../bab-16/README.md)
