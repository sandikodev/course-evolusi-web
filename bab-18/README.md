# Bab 18 — Edge-First Architecture: Komputasi yang Mendekati User

> *"Edge bukan hanya tentang kecepatan. Edge adalah pergeseran model komputasi — dari 'server yang menunggu request' ke 'kode yang ada di mana usermu berada'."*

---

## Apa Itu "Edge"?

"Edge" dalam konteks web computing berarti komputasi yang terjadi di titik yang paling dekat dengan user — di CDN nodes yang tersebar secara global, bukan di satu origin server.

```
Arsitektur tradisional:
User di Jakarta → Request → Origin server di US → Response (200ms+)

Edge-first:
User di Jakarta → Request → CDN node di Singapura → Response (<20ms)
```

Perbedaan latency ini bukan hanya soal kenyamanan — untuk aplikasi tertentu (trading, gaming, real-time collaboration), ini adalah perbedaan antara bisa dipakai dan tidak bisa dipakai.

---

## CDN: Dari Static File Server ke Compute Platform

CDN (Content Delivery Network) awalnya hanya untuk menyimpan dan menyajikan static files lebih cepat. Tapi modern CDN telah berevolusi menjadi compute platform.

**Cloudflare Workers:** Jalankan JavaScript/TypeScript di 300+ edge locations. Setiap request dihandle oleh V8 isolate yang baru.

**Vercel Edge Functions:** Subset dari Cloudflare Workers, diintegrasikan dengan Next.js.

**Deno Deploy:** JavaScript di edge, berbasis Deno runtime.

**AWS Lambda@Edge / CloudFront Functions:** AWS equivalent, tapi lebih terbatas.

**Fastly Compute@Edge:** Berbasis WebAssembly, bisa pakai Rust, Go, atau AssemblyScript.

---

## Kapan Edge Computing Sangat Valuable

**A/B Testing dan Personalisasi:**
Kalau kamu ingin menampilkan konten berbeda untuk segmen user yang berbeda tanpa memperlihatkan "flash" dari versi default, edge function bisa mengambil keputusan tersebut sebelum HTML sampai ke browser.

```typescript
// Edge function yang menentukan variant sebelum content sampai ke browser
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const userId = getCookieValue(request, 'user_id');
    const variant = await env.AB_KV.get(`variant:${userId}`) ?? 'control';

    // Fetch halaman yang sesuai berdasarkan variant
    const url = new URL(request.url);
    url.searchParams.set('variant', variant);

    return fetch(url.toString());
  }
};
```

**Authentication Middleware:**
Validasi JWT atau session token di edge sebelum request diteruskan ke origin, mengurangi load di origin server.

**Geolocation-based Routing:**
Redirect user ke versi site yang sesuai dengan lokasi mereka — bahasa, mata uang, regulasi data — di edge.

**Rate Limiting:**
Proteksi dari DDoS atau API abuse di edge, sebelum traffic sampai ke origin.

---

## Constraint Edge Computing

Edge computing bukan silver bullet. Ada constraint yang signifikan:

**Tidak ada filesystem:** Edge functions tidak bisa read/write ke disk.

**CPU time limit:** Cloudflare Workers: 50ms default (bisa dinaikkan). Ini cukup untuk simple logic tapi tidak untuk heavy computation.

**Memory limit:** 128MB per isolate di Cloudflare Workers.

**Cold start (minimal tapi ada):** Meski lebih cepat dari Lambda, ada overhead pertama kali isolate diinisialisasi.

**Node.js compatibility:** Tidak semua Node.js module bisa berjalan di edge. Package yang bergantung pada `fs`, `child_process`, atau native modules tidak akan bekerja.

**Stateless by default:** State tidak persisten antar request. Untuk state yang perlu persisten, butuh external store (KV, Durable Objects, database).

---

## Durable Objects: State di Edge

Cloudflare Durable Objects adalah solusi untuk masalah state di edge. Setiap Durable Object adalah "actor" dengan storage persisten yang bisa diakses secara konsisten dari seluruh dunia.

```typescript
export class Counter implements DurableObject {
  private state: DurableObjectState;
  private count: number = 0;

  constructor(state: DurableObjectState) {
    this.state = state;
  }

  async fetch(request: Request): Promise<Response> {
    this.count = (await this.state.storage.get('count')) ?? 0;

    if (request.method === 'POST') {
      this.count++;
      await this.state.storage.put('count', this.count);
    }

    return new Response(JSON.stringify({ count: this.count }));
  }
}
```

Durable Objects memungkinkan use case seperti real-time collaboration, game state, rate limiting yang lebih sophisticated — semua di edge.

---

## Edge vs Serverless: Perbedaan yang Sering Disamakan

"Edge" dan "serverless" sering digunakan secara bertukar, tapi ada perbedaan penting:

| Aspek | Serverless (Lambda) | Edge (Workers) |
|-------|---------------------|----------------|
| Lokasi | Region tertentu (us-east-1) | Dekat user (~300 lokasi) |
| Cold start | 100ms - beberapa detik | Microseconds |
| Runtime | Node.js, Python, Go, dll | V8 isolate (JS/TS/Wasm) |
| Execution time | Menit | Milliseconds |
| Memory | GB | MB |
| Pricing | Per invocation + duration | Per request + CPU ms |

Keduanya "serverless" dalam arti tidak ada server yang perlu dimanage — tapi model eksekusinya sangat berbeda.

---

## Edge-First Application Design

Membangun aplikasi edge-first membutuhkan cara berpikir yang berbeda:

**Stateless by design:** Setiap request harus self-contained. State disimpan di external store (KV, database), bukan di memory.

**Idempotent operations:** Karena edge function bisa dijalankan ulang (failure, retry), operasi harus idempotent — dijalankan berkali-kali menghasilkan hasil yang sama.

**Minimal external dependencies:** Setiap call ke external service menambah latency dan failure point. Edge function yang efisien melakukan minimal external calls.

**Cache aggressively:** Kalau data jarang berubah, cache di edge — ini yang membuat edge sangat efisien.

---

## WinterCG dan Portabilitas Edge Code

Seperti yang sudah dibahas di Bab 08, WinterCG (Web-interoperable Runtimes Community Group) sedang mendefinisikan standar untuk code yang bisa berjalan di berbagai edge runtime.

Ini penting karena tanpa standar, kode Cloudflare Workers tidak langsung portable ke Vercel Edge atau Deno Deploy. WinterCG APIs yang umum:
- `fetch()` dan Web Fetch API
- `Request`, `Response`, `Headers`
- `URL`, `URLSearchParams`
- `TextEncoder`, `TextDecoder`
- `crypto` (Web Crypto API)

---

## Pertanyaan untuk Direnungkan

1. Edge computing menjanjikan latency yang sangat rendah. Tapi latency sering bukan bottleneck utama di aplikasi web — database query, rendering, dan JavaScript execution sering lebih lambat. Untuk use case apa edge computing benar-benar membuat perbedaan yang terasa?

2. Cloudflare Workers dan sejenisnya menjalankan kode kamu di infrastruktur vendor yang kamu tidak kontrol. Bagaimana kamu mempertimbangkan trust dan security dalam konteks ini?

3. Edge-first design membutuhkan stateless architecture. Banyak aplikasi yang sudah ada dibangun dengan asumsi state yang persisten di server. Seberapa besar refactoring yang diperlukan untuk membuat aplikasi yang ada "edge-compatible" — dan apakah biayanya worth it?

---

## Selanjutnya

Frontend, backend, edge — semuanya perlu berkomunikasi. BFF (Backend for Frontend) adalah pola arsitektur yang menjawab pertanyaan: bagaimana backend seharusnya didesain untuk melayani frontend yang berbeda-beda?

→ [Bab 19: BFF — Backend for Frontend](../bab-19/README.md)
