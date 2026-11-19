# Bab 23 — Arsitektur yang Defensible

> *"Arsitektur yang baik bukan arsitektur yang 'benar' secara absolut. Arsitektur yang baik adalah arsitektur yang bisa kamu pertahankan dengan argumen yang jelas, dan yang bisa diubah ketika asumsinya terbukti salah."*

---

## Apa Itu "Defensible"

"Defensible" dalam konteks ini berarti: kalau kamu ditanya "mengapa kamu memilih arsitektur ini?", kamu bisa menjawab dengan argumen yang berbasis pada trade-off yang nyata — bukan pada popularitas, bukan pada apa yang dipakai perusahaan besar, bukan pada preferensi personal semata.

Arsitektur yang defensible memiliki:
1. **Premise yang jelas** — apa asumsi yang dipakai?
2. **Trade-off yang explicit** — apa yang dikorbankan untuk apa yang didapat?
3. **Kondisi kemenangan** — kapan arsitektur ini terbukti tepat?
4. **Exit strategy** — apa yang terjadi kalau asumsinya salah?

---

## Cara Membangun Argumen Arsitektural

**Langkah 1: Definisikan konteks dengan spesifik**

Arsitektur yang baik untuk startup 3 orang berbeda dari arsitektur yang baik untuk perusahaan 300 orang. Jangan mulai dengan "mana yang terbaik" — mulai dengan "terbaik untuk siapa, dalam kondisi apa, dengan constraint apa."

Pertanyaan yang perlu dijawab:
- Berapa banyak developer yang akan bekerja dengan codebase ini?
- Seberapa sering release cycle?
- Apa yang terjadi kalau sistem down?
- Apakah SEO penting?
- Berapa traffic yang diantisipasi?
- Apakah ada regulatory requirement?

**Langkah 2: Identifikasi trade-off, bukan "yang terbaik"**

Setiap pilihan arsitektur mengorbankan sesuatu. Explicit trade-off adalah tanda pemikiran yang mature.

Contoh yang lemah: "Kita pakai React karena ini yang terbaik."
Contoh yang defensible: "Kita pakai React karena tim sudah familiar dan ekosistem npm yang luas penting untuk use case kita. Trade-off-nya adalah bundle size yang lebih besar dan hydration overhead — tapi untuk authenticated dashboard internal ini acceptable."

**Langkah 3: Antisipasi counter-argument**

Kalau argumenmu tidak tahan terhadap counter-argument yang wajar, argumenmu belum selesai.

"Kenapa tidak Vue?" — "Vue juga valid, tapi tim saat ini lebih familiar dengan React dan kami tidak punya waktu untuk learning curve. Kalau tim berubah, Vue bisa menjadi pertimbangan."

"Kenapa tidak SSR dari awal?" — "Dashboard internal tidak butuh SEO dan user sudah expect loading time. CSR dengan React cukup dan lebih simple untuk tim saat ini."

---

## Architecture Decision Records (ADR)

ADR adalah cara mendokumentasikan keputusan arsitektur sehingga bisa di-audit di masa depan.

Format yang sederhana tapi effective:

```markdown
# ADR 001: Gunakan React untuk Frontend Dashboard

## Status
Accepted — 2026-01-15

## Context
Tim 5 developer perlu membangun admin dashboard untuk customer support team. 
SEO tidak penting (authenticated). Traffic rendah (~100 concurrent users).
3 dari 5 developer familiar dengan React.

## Decision
Gunakan React dengan Vite untuk CSR. Tidak pakai SSR.

## Consequences

### Positif
- Tim bisa produktif segera (familiar)
- Ekosistem component library yang luas
- Simple deployment (static files)

### Negatif
- Bundle size lebih besar dari Svelte
- Hydration overhead tidak relevan tapi ada
- Tidak ada SSR kalau kebutuhan berubah (bisa ditambah via Next.js)

## Alternatives Considered
- Svelte/SvelteKit: performa lebih baik tapi learning curve untuk tim
- Vue/Nuxt: valid, tapi React ecosystem lebih dikenal tim
- Angular: overkill untuk use case ini dan team size ini
```

ADR yang ditulis membuat keputusan bisa di-review: "Kalau kondisi berubah (tim berubah, SEO perlu, traffic naik), apakah argumen ini masih valid?"

---

## Komunikasi ke Non-Technical Stakeholders

Arsitektur yang defensible harus bisa dijelaskan kepada orang yang tidak teknis — product manager, CTO yang non-coding, klien.

**Hindari jargon teknis tanpa konteks:**
"Kita perlu refactor ke microservices dengan event-driven architecture menggunakan Kafka."

**Gunakan analogi dan business impact:**
"Saat ini tim kita tidak bisa deploy fitur baru di bagian checkout tanpa testing ulang seluruh aplikasi — seperti harus renovasi seluruh gedung hanya untuk mengganti satu ruangan. Kita perlu memisahkan checkout menjadi bagian yang bisa di-update independen. Ini membutuhkan 3 bulan engineering effort, tapi setelahnya kita bisa release fitur checkout 5x lebih cepat."

---

## Arsitektur sebagai Risiko Management

Cara lain untuk berpikir tentang keputusan arsitektur: **setiap pilihan adalah taruhan pada asumsi tertentu.** Arsitektur yang baik adalah arsitektur yang:

1. Membuat taruhan yang explicit
2. Meminimalkan biaya kalau taruhannya salah
3. Memudahkan validasi apakah taruhannya benar

Contoh:
- "Kita taruhan bahwa traffic kita tidak akan melebihi 1000 concurrent users dalam 1 tahun ke depan." → Kalau salah, biaya scale-up? Berapa lama untuk refactor?
- "Kita taruhan bahwa tim kita tidak akan butuh SSR dalam 2 tahun ke depan." → Kalau salah, berapa biaya migrasi ke Next.js?

Kalau biaya "kalah taruhan" terlalu tinggi, kamu perlu arsitektur yang lebih defensive — walaupun ada extra cost saat ini.

---

## Kapan Mengubah Arsitektur

Arsitektur yang baik juga tahu kapan sudah waktunya berubah. Tanda-tanda:

**Friction yang semakin meningkat:**
Kalau tugas yang dulu mudah sekarang makin sulit — deploy lebih lama, bug lebih sering, velocity menurun — arsitektur mungkin perlu ditinjau.

**Asumsi yang terbukti salah:**
"Kita pikir tidak butuh SSR, ternyata SEO jadi critical." Ini adalah saat yang tepat untuk re-evaluate.

**Team yang berubah:**
Arsitektur yang optimal untuk tim 5 orang bisa tidak optimal untuk tim 50 orang — dan sebaliknya.

**Technology yang berubah signifikan:**
Ketika React Hooks muncul, banyak class component patterns perlu direvisi. Ketika Next.js App Router muncul, cara berpikir tentang data fetching berubah.

---

## Pertanyaan untuk Direnungkan

1. Coba ambil satu keputusan arsitektur dari project kamu saat ini — apakah bisa kamu tulis ADR yang mendeskripsikan context, decision, dan trade-off? Kalau tidak bisa, apa yang membuat kamu tidak yakin tentang argumen di balik keputusan tersebut?

2. Bagaimana cara kamu menjelaskan "kenapa kita butuh refactoring" kepada stakeholder yang berpikir "kalau masih jalan, kenapa diubah"? Argumen apa yang menurutmu paling efektif?

3. Apakah ada keputusan arsitektur yang kamu sesali — yang terlihat bagus pada saat dibuat tapi kemudian terbukti salah? Apa yang bisa dipelajari tentang cara asumsinya salah?

---

## Selanjutnya

Arsitektur yang defensible adalah skill yang berguna untuk siapapun. Tapi ada pertanyaan strategis yang lebih besar: apakah lebih menguntungkan menjadi generalis yang bisa banyak hal, atau spesialis yang sangat dalam di satu hal?

→ [Bab 24: Spesialisasi vs Generalisasi](../bab-24/README.md)
