# Bab 08 — Runtime Wars: Node, Deno, Bun, dan Filosofi Edge

> *"Perang runtime bukan tentang siapa yang tercepat di benchmark. Ini tentang filosofi: bagaimana seharusnya server bekerja, di mana seharusnya kode berjalan, dan apa yang seharusnya menjadi abstraksi utama."*

---

Ketika Ryan Dahl merilis Node.js pada 2009, ia membuka era baru: JavaScript di server. Selama bertahun-tahun, Node.js adalah satu-satunya cara serius untuk menjalankan JavaScript di luar browser. Lalu Ryan Dahl sendiri, pada 2018, memberikan presentasi berjudul *"10 Things I Regret About Node.js"* — dan merilis Deno sebagai penerus spiritual yang lebih baik. Lalu Jarred Sumner merilis Bun dengan benchmark yang lebih cepat dari keduanya. Lalu Cloudflare membangun Workers runtime yang tidak ada di Node.js maupun browser.

Ini bukan sekadar persaingan kecepatan. Ini adalah pertanyaan filosofis tentang bagaimana server seharusnya didesain.

---

## Node.js: Warisan yang Sangat Besar

Node.js dibangun di atas beberapa keputusan desain yang sangat berpengaruh:

**Event loop dan non-blocking I/O** — model concurrency yang sangat efisien untuk I/O-bound workload. Satu thread, event loop yang menangani ribuan koneksi concurrent tanpa overhead thread-per-connection.

**npm** — package manager yang menjadi ekosistem terbesar dalam sejarah software. Lebih dari 2 juta package. Dan ini adalah kekuatan sekaligus kelemahan — `node_modules` yang bisa mencapai ratusan MB untuk project sederhana, dependency hell, supply chain security yang menjadi concern besar.

**CommonJS modules** — `require()` synchronous yang tidak sesuai dengan module system browser. Ini menciptakan dual-module problem yang masih menjadi sumber kebingungan sampai hari ini.

**Ryan Dahl's regrets:**
- Tidak menggunakan Promise dari awal (callbacks dulu, Promise belakangan)
- CommonJS bukan ES Modules
- `node_modules` yang bisa diakses dari kode user (security concern)
- `package.json` yang menjadi terlalu kompleks
- `index.js` sebagai konvensi entry point

---

## Deno: Rewrite dengan Penyesalan

Ryan Dahl merilis Deno pada 2018 (rilis 1.0 pada 2020) sebagai attempt untuk memperbaiki kesalahan Node.js — sambil tetap tidak melanggar ekosistem Node.js yang sudah ada.

**Keputusan desain Deno:**

**Security by default** — kode tidak bisa mengakses file system, network, atau environment variables tanpa explicit permission:
```bash
deno run --allow-read --allow-net main.ts
```

**ES Modules native** — `import` bukan `require`, URL-based imports:
```typescript
import { serve } from "https://deno.land/std@0.200.0/http/server.ts";
```

**TypeScript built-in** — tidak perlu setup TypeScript compiler secara manual.

**Web-compatible APIs** — Deno menggunakan fetch, Request, Response, WebStreams — API yang sama dengan browser. Kode yang berjalan di Deno sering bisa berjalan di browser juga.

**Node.js compatibility layer** — Deno kemudian menambahkan kemampuan menjalankan package npm, menjadikannya lebih pragmatis.

**Kenyataannya:** Deno gagal mengambil market share yang signifikan dari Node.js. Ekosistem npm terlalu besar. Migrasi cost terlalu tinggi. Ini adalah pelajaran yang sudah kita lihat berulang kali: kualitas teknis yang lebih baik tidak menjamin adopsi.

---

## Bun: Kecepatan sebagai Differentiator

Jarred Sumner merilis Bun pada 2022 dengan satu pitch utama: **sangat cepat**. Bun ditulis dalam Zig (bukan C++ seperti V8), menggunakan JavaScriptCore (engine WebKit, bukan V8), dan mengklaim benchmark yang 2-3x lebih cepat dari Node.js untuk banyak workload.

Bun juga menjadi "all-in-one toolkit":
- Runtime JavaScript
- Package manager (pengganti npm/yarn)
- Bundler (pengganti webpack/esbuild)
- Test runner
- TypeScript transpiler

```bash
bun install          # 10-100x lebih cepat dari npm install
bun run server.ts    # Jalankan TypeScript langsung tanpa compile
bun test             # Test runner bawaan
bun build app.ts     # Bundler
```

**Node.js compatibility** adalah prioritas Bun — sebagian besar code Node.js bisa jalan di Bun tanpa perubahan.

**Kenyataannya:** Benchmark Bun memang mengesankan, tapi performance bottleneck di sebagian besar aplikasi web bukan di JavaScript runtime — melainkan di database, network, dan I/O. Migrasi ke Bun untuk "kecepatan" seringkali tidak memberikan perbedaan yang terasa di production.

---

## Cloudflare Workers: Paradigma yang Benar-Benar Berbeda

Ini adalah yang paling menarik dan paling sering kurang dipahami. Cloudflare Workers bukan runtime JavaScript yang lebih baik — ia adalah **model komputasi yang fundamentally berbeda**.

**Traditional server model:**
```
Request → Server (Node.js, running 24/7, one location) → Response
```

**Cloudflare Workers model:**
```
Request → Nearest edge location (300+ globally) → Isolated V8 instance → Response
```

Setiap request dijalankan di V8 isolate yang baru — bukan server yang berjalan terus-menerus. Ini berarti:

- **Global by default** — kode kamu berjalan di 300+ lokasi Cloudflare secara otomatis
- **Near-zero cold start** — isolate boot dalam microseconds, bukan milliseconds
- **No server maintenance** — tidak ada server yang perlu dikelola
- **Request-based billing** — bayar per request, bukan per jam server berjalan

Tapi ada constraint yang signifikan:

**No filesystem** — Workers tidak bisa baca/tulis ke disk  
**CPU time limit** — 50ms CPU time per request (bisa dinaikkan dengan Paid plan)  
**Memory limit** — 128MB per isolate  
**No long-running processes** — tidak cocok untuk background jobs  
**Limited Node.js compatibility** — tidak semua Node.js API tersedia

Workers menggunakan **Web-compatible APIs** — fetch, Request, Response, WebCrypto, WebSockets. Kode yang ditulis untuk Workers sangat mirip dengan kode browser.

```typescript
// Cloudflare Worker
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);

    if (url.pathname === '/api/hello') {
      return new Response(JSON.stringify({ hello: 'world' }), {
        headers: { 'Content-Type': 'application/json' }
      });
    }

    return new Response('Not found', { status: 404 });
  }
};
```

---

## WinterCG: Standarisasi Runtime

Fragmentasi runtime JavaScript menciptakan masalah portabilitas: kode yang ditulis untuk Node.js tidak selalu berjalan di Deno atau Workers, dan sebaliknya.

**WinterCG (Web-interoperable Runtimes Community Group)** adalah inisiatif untuk mendefinisikan subset Web API yang harus tersedia di semua server-side runtime. Anggotanya: Cloudflare, Deno, Vercel, dan lainnya.

Kalau kamu menulis kode yang hanya menggunakan WinterCG APIs (fetch, Request, Response, WebCrypto, dll.), kodenya bisa portabel antar runtime. Ini adalah motivasi di balik "edge-compatible" label yang semakin umum di ekosistem.

---

## Kapan Pakai Runtime yang Mana

**Node.js:** Default choice untuk sebagian besar aplikasi web. Ekosistem terbesar, paling mature, paling banyak dokumentasi dan library. Pilih ini kalau tidak ada alasan kuat untuk memilih yang lain.

**Deno:** Kalau kamu memulai project baru dan menghargai security-by-default, TypeScript built-in, dan Web-compatible APIs. Juga bagus untuk tooling (Deno Deploy untuk hosting).

**Bun:** Kalau `npm install` yang lambat jadi bottleneck nyata dalam workflow development. Atau kalau kamu build application sederhana dan ingin toolkit all-in-one. Tapi berhati-hati dengan compatibility gaps untuk package yang kompleks.

**Cloudflare Workers / Edge Runtime:** Kalau aplikasimu adalah API yang butuh global low latency, tidak butuh long-running processes, dan kamu bisa hidup dengan constraint-nya. Cocok untuk: API gateway, auth middleware, A/B testing, personalization, image transformation.

---

## Implikasi untuk Architecture

Fragmentasi runtime menciptakan sesuatu yang menarik: **runtime sebagai architectural choice**, bukan hanya implementation detail.

Ketika kamu memilih Cloudflare Workers, kamu juga memilih:
- Global distribution sebagai default
- Stateless architecture (tidak ada state di server)
- Request-scoped execution (tidak ada singleton yang bertahan antar request)
- KV/Durable Objects untuk state yang perlu persisten

Ketika kamu memilih Node.js dengan long-running process, kamu memilih:
- Stateful architecture yang lebih mudah
- WebSocket yang lebih straightforward
- Background processing yang lebih fleksibel
- Satu server (atau cluster) yang perlu dimanage

Pilihan runtime adalah pilihan arsitektur. Ini perlu disadari dari awal project, bukan dipikir belakangan.

---

## Pertanyaan untuk Direnungkan

1. Node.js memiliki kelemahan desain yang diakui oleh pembuatnya sendiri. Tapi ekosistemnya begitu besar sehingga alternatif yang lebih baik sulit menggantikannya. Ini adalah pola yang berulang dalam teknologi. Bagaimana kamu mempertimbangkan "technical debt ekosistem" ketika memilih runtime atau framework?

2. Cloudflare Workers memaksa arsitektur stateless yang berbeda dari traditional server. Apakah ini constraint yang membatasi atau justru constraint yang membimbing ke arsitektur yang lebih baik?

3. Bun mendapat banyak perhatian karena benchmark yang impresif. Tapi apakah kecepatan runtime adalah bottleneck yang sering kamu temui di production? Apa yang sebenarnya menjadi bottleneck paling umum dalam aplikasi web yang kamu bangun?

---

## Selanjutnya

Kita sudah melewati substrate dan platform. Sekarang masuk ke bagian yang lebih conceptual: bagaimana cara berpikir tentang framework itu berbeda-beda, dan mengapa itu penting untuk memahami pilihan yang kamu buat.

→ [Bab 09: Empat Klaster Paradigma](../bab-09/README.md)
