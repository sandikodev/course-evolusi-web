# Bab 26 — Selanjutnya: Sinyal yang Perlu Diperhatikan

> *"Masa depan sudah ada — hanya distribusinya yang belum merata."*  
> — William Gibson

---

## Ini Bukan Prediksi

Bab ini tidak berisi prediksi tentang "framework mana yang akan menang" atau "teknologi apa yang akan menggantikan React." Prediksi seperti itu hampir selalu salah, dan sering menyesatkan.

Yang lebih berguna adalah mengidentifikasi **tren yang sudah terjadi** dan bertanya: kalau tren ini berlanjut, apa implikasinya? Ini yang bisa kamu monitor dan evaluasi sendiri seiring waktu.

---

## Tren 1: Konvergensi Signals dan Primitif Reaktif

Sudah dibahas di Bab 11, tapi layak disebut lagi sebagai tren jangka panjang: semua framework bergerak ke signals sebagai primitif reaktif. TC39 Signals Proposal sedang dalam progress.

**Kalau tren ini berlanjut:**
- Perbedaan antar framework di level reaktivitas akan semakin kecil
- Framework akan bersaing lebih di level DX, ekosistem, dan tooling — bukan mekanisme reaktivitas
- Interoperabilitas komponen antar framework menjadi lebih mungkin

**Yang perlu diperhatikan:**
- Status TC39 Signals Proposal
- Apakah React mengadopsi signals native (React 19+ experimentals)

---

## Tren 2: AI-Assisted Development Mengubah Cara Kerja

AI coding assistants (GitHub Copilot, Cursor, v0, Bolt) sedang mengubah cara developer bekerja. Ini bukan tentang "AI menggantikan developer" — ini tentang bagaimana cara skill yang valuable bergeser.

**Apa yang semakin mudah dengan AI:**
- Boilerplate code (CRUD, form validation, component scaffolding)
- Migrasi syntax antar framework
- Debugging error yang sudah pernah dilihat AI
- Menulis test untuk code yang sudah ada

**Apa yang tidak bisa digantikan AI:**
- Mengevaluasi trade-off arsitektur dalam konteks spesifik
- Memutuskan kapan technical debt layak ditanggung
- Berkomunikasi keputusan teknis ke non-technical stakeholders
- Debugging masalah novel yang belum pernah ada sebelumnya
- Memahami business domain yang sangat spesifik

**Implikasinya untuk karier:**
Skill yang paling defensible adalah skill judgment dan communication — bukan skill syntax atau implementation.

---

## Tren 3: AI-Native Frontend Patterns

Selain AI sebagai alat pengembangan, AI mulai menjadi *fitur* di dalam aplikasi frontend. Ini menciptakan kategori baru masalah teknis:

**Streaming UI:** LLM responses datang sebagai stream. Frontend perlu menampilkan konten yang terus bertambah secara smooth. `ReadableStream`, `TextDecoder`, dan React's streaming primitives menjadi penting.

**Optimistic UI dengan AI uncertainty:** Ketika AI menghasilkan konten, hasilnya tidak deterministik. UI perlu menangani ini dengan grace — bukan hanya loading spinner, tapi fallback, retry, dan partial display.

**Tool calling dan structured output:** LLM bisa memanggil tools (fetch data, run code, search). Frontend perlu menampilkan proses ini dengan transparansi yang baik.

**AI SDK patterns:** Vercel AI SDK, LangChain.js, dan sejenisnya menjadi lapisan baru yang perlu dipahami.

---

## Tren 4: Web Platform Terus Bertambah Kemampuan

Setiap tahun browser mendapat kemampuan baru yang memungkinkan hal yang sebelumnya butuh library:

**View Transitions API** — transisi antar halaman yang smooth tanpa library animasi  
**Navigation API** — pengganti History API yang lebih modern  
**Popover API** — tooltip dan popover native tanpa JavaScript  
**Declarative Shadow DOM** — Web Components yang bisa di-SSR  
**CSS Anchor Positioning** — positioning relatif terhadap elemen lain, tanpa JS  
**Container Queries** — responsivitas berdasarkan ukuran container, sudah didukung luas

**Implikasinya:**
- Library yang menyelesaikan masalah yang sudah diselesaikan oleh platform akan kehilangan relevansi
- Developer yang memahami web platform akan bisa deliver lebih banyak dengan dependency lebih sedikit
- Performance budget di banyak aplikasi bisa dikurangi dengan adopsi Web APIs modern

---

## Tren 5: Edge-First Architecture Semakin Mature

Cloudflare Workers, Vercel Edge, Deno Deploy — semua berkembang dan ekosistemnya makin matang. WinterCG standards semakin banyak diadopsi.

**Kalau tren ini berlanjut:**
- "Deploy globally by default" akan menjadi standard, bukan premium feature
- Aplikasi yang dibangun edge-first akan memiliki keunggulan latency yang semakin sulit dikompetisi oleh origin-only architecture
- Stateful edge computing (Cloudflare Durable Objects, Workers KV) akan membuka use case yang sebelumnya butuh origin server

**Yang perlu diperhatikan:**
- Bagaimana serverless pricing berevolusi (saat ini bisa mahal di scale tertentu)
- Apakah WinterCG berhasil menjadi standar yang diadopsi luas

---

## Tren 6: WebAssembly Mulai Mendapat Use Case Nyata

Wasm sudah ada sejak 2017 tapi adopsinya masih terbatas pada use case performa ekstrem (Figma, game, video editing). Beberapa perkembangan yang menarik:

**WASI (WebAssembly System Interface)** — interface standar untuk Wasm berinteraksi dengan OS, membuat Wasm portabel lintas platform

**Component Model** — cara untuk membuat Wasm modules yang composable seperti library

**Wasmtime, wasmer** — runtime Wasm di server yang semakin mature

**Kalau Wasm Component Model matang:**
- Bisa jadi fondasi untuk polyglot programming di web — komponen Rust, Go, Python yang bisa dipakai bersama di satu aplikasi
- Edge functions bisa ditulis di bahasa yang lebih ekspressif dari JavaScript

---

## Tren 7: Rendering Pipeline yang Lebih Granular

React Server Components, Partial Prerendering (Next.js), dan konsep "streaming" menunjukkan arah: bukan lagi memilih antara "semua di server" atau "semua di client" — tapi granular control atas di mana dan kapan setiap bagian dirender.

**Partial Prerendering (PPR):** Halaman dirender dengan shell statis yang langsung dikirim, sementara dynamic parts streaming in asynchronously.

**React Server Components + Suspense:** Server components untuk data, client components untuk interaktivitas, Suspense untuk loading states — semua bisa dikombinasikan di granularity komponen.

Ini adalah kompleksitas yang meningkat, tapi juga kontrol yang lebih besar atas performance trade-off.

---

## Yang Tidak Akan Berubah

Di tengah semua tren ini, beberapa hal fundamental tidak akan berubah:

**HTTP masih akan menjadi protocol utama.** Tidak ada yang akan menggantikan request-response model untuk sebagian besar use case web.

**HTML masih akan menjadi format dokumen.** Upaya untuk menggantikan HTML sudah ada — semua gagal. Web Components menggunakannya. AI menghasilkannya. Browser merendernnya.

**JavaScript masih akan berjalan di browser.** WebAssembly akan menjadi complement, bukan replacement. Terlalu banyak investasi ekosistem di JavaScript.

**User masih akan peduli dengan speed dan reliability.** Performance budget, Core Web Vitals, SEO — semua adalah proxy untuk "seberapa baik pengalaman user." Ini tidak akan berubah selama ada user.

---

## Cara Tetap Relevan

Berdasarkan semua yang sudah dibahas di kursus ini, berikut prinsip untuk tetap relevan:

**1. Kuasai platform, bukan hanya framework.**
Framework datang dan pergi. HTML, HTTP, CSS, JavaScript, Web APIs — ini bertahan. Invest di sini.

**2. Pahami trade-off, bukan hanya cara pakai.**
Semua framework bisa dipakai. Hanya sebagian developer yang bisa menjelaskan mengapa memilih satu daripada yang lain — dan ini yang membuat mereka valuable.

**3. Baca sinyal, jangan reaktif terhadap hype.**
Pakai framework Hype Cycle mental model. Tunggu production case studies sebelum adopsi. Evaluasi ulang setelah 1 tahun.

**4. Pilih satu area untuk menjadi sangat dalam.**
T-shaped atau Pi-shaped. Generalis dangkal memiliki competitive moat yang sangat lemah.

**5. Komunikasi teknikal yang clear adalah differentiator.**
Kemampuan menjelaskan trade-off kepada non-technical stakeholders adalah skill langka dan sangat valuable.

---

## Penutup: Mengapa Ini Semua Penting

Kursus ini bukan tentang memilih framework yang "benar." Tidak ada framework yang benar secara absolut — ada framework yang tepat untuk konteks tertentu.

Kursus ini tentang membangun kemampuan untuk **berpikir tentang trade-off** dengan lebih jernih. Ketika kamu bisa:
- Memetakan sebuah teknologi ke dalam konteks sejarah dan paradigma yang lebih besar
- Mengidentifikasi asumsi yang mendasari sebuah pilihan arsitektur
- Mengkomunikasikan trade-off dengan jelas kepada orang yang berbeda background
- Membaca sinyal ekosistem dengan lebih akurat

...kamu tidak akan pernah tertinggal secara fundamental, bahkan ketika framework yang spesifik sudah usang.

Karena masalah yang diselesaikan web — menyampaikan informasi dan pengalaman dari server ke user dengan cepat, reliably, dan dengan cost yang wajar — tidak akan berubah. Cara kita menyelesaikannya akan terus berevolusi. Dan developer yang memahami *mengapa* cara tertentu dipilih akan selalu bisa beradaptasi dengan cara yang baru.

---

*Terima kasih sudah membaca sampai di sini.*

*Kalau ada bagian yang ingin kamu diskusikan lebih dalam, pertanyaan tentang use case spesifik, atau feedback tentang course ini — hubungi di TikTok/Facebook @sandikodev.*
