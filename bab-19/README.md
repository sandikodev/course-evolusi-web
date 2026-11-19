# Bab 19 — BFF: Backend for Frontend

> *"BFF bukan tentang memisahkan backend. BFF tentang mengakui bahwa frontend yang berbeda memiliki kebutuhan yang berbeda — dan satu API yang mencoba melayani semua orang dengan sama baiknya seringkali tidak melayani siapapun dengan optimal."*

---

## Masalah yang BFF Selesaikan

Bayangkan sebuah API monolitik yang melayani web app, mobile app iOS, mobile app Android, dan smart TV app. Semua endpoint yang sama, response schema yang sama.

**Masalah yang muncul:**

**Over-fetching:** Web app butuh data yang sangat detail untuk dashboard. Mobile app butuh data yang minimal untuk hemat bandwidth. Satu API harus memberikan data yang cukup untuk yang paling demanding — dan semua client lain menerima data yang terlalu banyak.

**Under-fetching:** Mobile app butuh data dari 3 endpoint berbeda yang di-compose menjadi satu tampilan. Ini berarti 3 request serial atau parallel yang menambah latency dan kompleksitas client.

**Coupling frontend-backend:** Ketika mobile app butuh perubahan response format, seluruh API harus berubah — berisiko breaking web app dan TV app.

**Berbeda kebutuhan auth:** Mobile app menggunakan OAuth dengan token tertentu. Web app menggunakan session cookie. TV app menggunakan device code flow. Satu API sulit mengelola semua ini dengan clean.

---

## BFF: Satu Backend Per Frontend

Pattern BFF (Sam Newman, 2015) menyarankan: buat backend layer yang spesifik untuk setiap frontend.

```
Mobile iOS App  ←→  BFF Mobile  ←→
Mobile Android ←→  (atau share   ←→  Core APIs / Services
Web App        ←→   BFF Web)    ←→
Smart TV       ←→  BFF TV       ←→
```

Setiap BFF:
- Mengagregasi data dari multiple downstream services sesuai kebutuhan frontend-nya
- Melakukan transformasi data ke format yang optimal untuk frontend tersebut
- Mengelola auth/session yang sesuai untuk frontend tersebut
- Bisa di-deploy dan di-scale secara independen

---

## BFF di Praktik: Next.js sebagai BFF

Next.js API routes adalah implementasi BFF yang paling umum hari ini — tanpa developer harus menyebut kata "BFF":

```typescript
// pages/api/dashboard.ts — BFF untuk web dashboard
export default async function handler(req, res) {
  const [user, projects, notifications] = await Promise.all([
    fetch('/internal/users/me').then(r => r.json()),
    fetch('/internal/projects?limit=10').then(r => r.json()),
    fetch('/internal/notifications?unread=true').then(r => r.json()),
  ]);

  // Compose dan transform untuk kebutuhan dashboard
  res.json({
    user: { name: user.name, avatar: user.avatarUrl },
    projectCount: projects.total,
    projects: projects.items.slice(0, 5),
    unreadCount: notifications.total,
    recentNotifications: notifications.items.slice(0, 3),
  });
}
```

Client cukup memanggil satu endpoint yang sudah aggregated — tidak perlu tahu ada 3 downstream services.

---

## GraphQL sebagai BFF Alternative

GraphQL sering dipilih sebagai alternatif BFF karena memungkinkan client menentukan persis data yang dibutuhkan:

```graphql
# Mobile query — minimal data
query MobileDashboard {
  user { name }
  projects(limit: 5) { id name status }
}

# Web query — lebih detail
query WebDashboard {
  user { name email avatar stats { projectCount } }
  projects(limit: 10) {
    id name status budget dueDate owner { name }
  }
  notifications(unread: true, limit: 5) { id message createdAt }
}
```

Satu GraphQL schema, tapi setiap client mendapat tepat data yang dibutuhkan — menyelesaikan masalah over/under-fetching.

**Tradeoff GraphQL:** Lebih fleksibel, tapi N+1 problem, caching yang lebih sulit, dan learning curve yang lebih tinggi.

---

## tRPC: Type-Safe BFF untuk Full-Stack TypeScript

tRPC adalah pendekatan modern yang sangat elegant untuk BFF ketika frontend dan backend keduanya TypeScript:

```typescript
// server/router.ts (di Next.js API atau standalone server)
import { initTRPC } from '@trpc/server';
const t = initTRPC.create();

export const appRouter = t.router({
  dashboard: t.procedure
    .query(async ({ ctx }) => {
      const [user, projects] = await Promise.all([
        db.users.findById(ctx.userId),
        db.projects.findMany({ userId: ctx.userId, limit: 10 }),
      ]);
      return { user, projects };
    }),

  createProject: t.procedure
    .input(z.object({ name: z.string(), description: z.string() }))
    .mutation(async ({ input, ctx }) => {
      return db.projects.create({ ...input, userId: ctx.userId });
    }),
});

export type AppRouter = typeof appRouter;
```

```typescript
// client/pages/dashboard.tsx
import { trpc } from '../utils/trpc';

function Dashboard() {
  const { data } = trpc.dashboard.useQuery();
  // data adalah fully typed dari server — tanpa manual type annotation!
}
```

**Zero schema duplication:** Tipe dari server otomatis tersedia di client karena keduanya TypeScript dan berbagi type definitions. Ini mengeliminasi seluruh kategori bug "frontend tidak update tipe setelah backend berubah."

---

## Kapan BFF Overkill

BFF pattern menambah layer tambahan yang perlu dikelola. Kapan tidak diperlukan:

**Single frontend:** Kalau kamu hanya punya web app, satu API sudah cukup. BFF untuk single frontend adalah premature abstraction.

**Tim kecil:** BFF berarti lebih banyak kode untuk ditulis dan dikelola. Tim kecil mungkin lebih produktif dengan coupling yang lebih tight.

**API yang sudah flexible:** Kalau API downstream sudah menggunakan GraphQL atau sudah cukup composable, tambahan BFF layer mungkin tidak perlu.

---

## Pertanyaan untuk Direnungkan

1. Next.js API routes dan server actions pada dasarnya adalah BFF — tapi kebanyakan developer tidak memikirkannya dengan istilah tersebut. Apakah naming dan conceptual clarity ini penting, atau "BFF" hanya jargon yang mendeskripsikan sesuatu yang sudah selalu orang lakukan?

2. tRPC memberikan type safety end-to-end yang sangat kuat, tapi mengikat frontend dan backend dalam satu TypeScript monorepo. Apakah ini trade-off yang worth it, atau ini membuat deployment dan team organization lebih sulit?

3. GraphQL vs REST vs tRPC adalah debat yang sudah lama. Di konteks BFF, mana yang paling cocok untuk use case yang kamu hadapi — dan apa yang sebenarnya menentukan pilihan ini selain preferensi personal?

---

## Selanjutnya

Kalau BFF adalah cara mengorganisir backend untuk melayani frontend, micro-frontends adalah cara mengorganisir frontend itu sendiri ketika ia tumbuh terlalu besar untuk satu tim.

→ [Bab 20: Micro-Frontends](../bab-20/README.md)
