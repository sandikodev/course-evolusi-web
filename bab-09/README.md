# Bab 09 — Empat Klaster Paradigma: Cara Berpikir yang Berbeda-Beda

> *"Ketika developer membandingkan framework, mereka sering membandingkan hal yang salah. Bukan karena mereka tidak cerdas — tapi karena mereka tidak menyadari bahwa framework yang mereka bandingkan hidup dalam paradigma yang fundamentally berbeda."*

---

Di course Aurelia 2 yang menjadi pasangan dari course ini, kita sudah membahas empat klaster paradigma secara singkat. Di sini kita akan memperluasnya dengan kedalaman historis dan teknis yang lebih jauh — karena memahami *mengapa* setiap klaster muncul adalah kunci untuk membaca ekosistem dengan benar.

> **Referensi:** `../aureliajs/FRAMEWORK_POSITIONING.md` untuk ringkasan cepat empat klaster.

---

## Mengapa Klasifikasi Ini Penting

Tanpa peta paradigma, perbandingan framework menjadi perbandingan yang tidak bermakna. Ketika seseorang bertanya "mana yang lebih baik, React atau Svelte?" — pertanyaan itu hampir tidak bisa dijawab karena keduanya menjawab pertanyaan yang berbeda dengan asumsi yang berbeda.

Tapi kalau kamu tahu bahwa React ada di Klaster 1 (Functional Re-Render) dan Svelte ada di Klaster 2 (Compiler-First), kamu bisa membandingkan mereka pada level yang bermakna: "untuk use case X dengan constraint Y, trade-off mana yang lebih cocok?"

---

## Klaster 1 — Functional Re-Render (React-sphere)

**Anggota:** React, Preact, SolidJS, Qwik  
**Lahir dari:** Frustrasi dengan mutable state yang sulit di-trace (AngularJS dirty checking, jQuery spaghetti)  
**Premis utama:** UI adalah fungsi deterministik dari state

```
UI = f(state)
```

**Cara berpikir:** Developer mendefinisikan transformasi data ke UI. Ketika state berubah, transformasi dijalankan ulang. Implementasi konkret (VDOM diff vs fine-grained signals) adalah detail — filosofi yang mendasari sama.

**Evolusi internal klaster:**
- React (2013): Fungsi yang re-run + VDOM diff
- Preact (2015): React-compatible tapi lebih kecil
- SolidJS (2019): Fungsi yang run-once + fine-grained signals, eliminasi VDOM
- Qwik (2021): Resumability — eliminasi hydration, event handler lazy-load

Menarik bahwa Solid dan React keduanya disebut "functional" tapi mekanismenya berlawanan: React re-run fungsi terus menerus, Solid run fungsi sekali dan signals yang update langsung.

**Kekuatan klaster ini:** Composability, predictable data flow, ekosistem yang sangat besar (React).  
**Kelemahan:** Bundle size (React runtime), hydration overhead (SSR), re-render overhead untuk aplikasi yang sangat dinamis.

---

## Klaster 2 — Compiler-First SFC (Svelte-sphere)

**Anggota:** Svelte, Astro  
**Lahir dari:** Pertanyaan: "Mengapa framework harus ada di browser?"  
**Premis utama:** Framework adalah alat build, bukan runtime

**Cara berpikir:** Kamu menulis dalam format khusus (`.svelte`, `.astro`), compiler mengubahnya menjadi JavaScript vanilla yang langsung manipulasi DOM. Tidak ada runtime framework yang perlu di-download user.

**Svelte vs Astro — perbedaan penting:**

Meskipun keduanya di klaster yang sama, Svelte adalah *component framework* sedangkan Astro adalah *meta-framework yang bisa host framework lain*. Astro tidak punya reactivity model sendiri — ia menggunakan framework lain (React, Vue, Svelte, Solid) untuk island yang interaktif.

**Svelte 5 dan Runes:**

Svelte 5 memperkenalkan Runes — `$state()`, `$derived()`, `$effect()` — yang terlihat seperti signals, tapi dikompilasi:

```svelte
<script>
  let count = $state(0);
  let doubled = $derived(count * 2);
</script>

<button onclick={() => count++}>{count} × 2 = {doubled}</button>
```

Ini adalah signals yang dikompilasi menjadi kode imperatif, bukan runtime signals. Perbedaan semantik yang penting.

**Kekuatan:** Near-zero runtime, performa sangat baik, kode yang sangat clean.  
**Kelemahan:** Sintaks proprietary yang perlu dipelajari, ekosistem lebih kecil, shadow DOM / styling bisa awkward.

---

## Klaster 3 — Pragmatic Reactivity SFC (Vue-sphere)

**Anggota:** Vue  
**Lahir dari:** Frustrasi dengan AngularJS (terlalu kompleks) tapi tidak ingin pergi sejauh React (terlalu berbeda dari HTML)  
**Premis utama:** Komponen sebagai unit stateful yang hidup, HTML tetap terasa seperti HTML

Vue adalah klaster tersendiri karena ia tidak cocok di klaster 1 atau 2:

- Bukan Klaster 1 karena komponen Vue adalah objek stateful yang hidup, bukan fungsi yang re-run
- Bukan Klaster 2 karena Vue tidak sepenuhnya compiler-first — reaktivitas tetap runtime (ES6 Proxy)
- Bukan Klaster 4 karena tidak ada class OOP atau DI yang menjadi inti arsitektur

**Tiga "rasa" Vue yang membingungkan:**

```
Options API (Vue 2 / Vue 3)  → terasa seperti Klaster 4 (OOP feel)
Composition API (Vue 3)      → terasa seperti Klaster 1 (hooks feel)
<script setup> (Vue 3)       → terasa seperti Klaster 2 (SFC feel)
```

Ini yang membuat Vue begitu adaptable — developer dari React, Angular, maupun Svelte semua merasa "familiar" saat pertama melihat Vue. Tapi mekanisme reaktivitas yang mendasari (ES6 Proxy) berbeda dari semua klaster lain.

**Rivalitas React vs Vue — populer tapi kurang tepat:**

Rivalitas ini nyata di job market, tapi secara teknis Vue lebih dekat ke Svelte daripada ke React. Keduanya SFC, keduanya state sebagai objek yang bisa dimutasi (bukan immutable), keduanya lebih "HTML-first" dari React. Yang membedakan: Svelte compiler-first (tidak ada runtime reaktivitas), Vue runtime-first (ES6 Proxy yang mendeteksi perubahan).

**Kekuatan:** Adaptable untuk berbagai background developer, learning curve gentle, Nuxt sebagai meta-framework yang matang.  
**Kelemahan:** "Tiga rasa" API bisa membingungkan, tidak ada posisi paradigma yang sangat jelas.

---

## Klaster 4 — Enterprise Class OOP (Angular-sphere)

**Anggota:** Angular, Aurelia  
**Lahir dari:** Kebutuhan enterprise untuk arsitektur yang scalable dengan banyak developer — mengambil konsep dari dunia backend (DI, class-based, strong typing)  
**Premis utama:** Komponen adalah class yang hidup, service diinjeksi, HTML murni untuk template

**Cara berpikir:** Kelas yang hidup, properti yang diobservasi, service yang diinjeksi. Ini adalah cara berpikir backend enterprise yang dipindahkan ke frontend. Developer yang nyaman dengan Spring (Java), .NET, atau NestJS akan langsung klik.

**Angular vs Aurelia — comparison yang valid:**

Karena keduanya di klaster yang sama, comparison ini bermakna:

| Dimensi | Angular 22 | Aurelia 2.0.0-rc.2 |
|---------|------------|---------------------|
| Status | Stabil, Google-backed | Release candidate |
| DI | Matang, hierarchical | `resolve()`, lebih sederhana |
| Zone.js | Legacy (bisa disable di v22) | Tidak pernah ada |
| Signals | Native sejak Angular 16 | ObserverLocator (ekuivalen) |
| Ekosistem | Sangat besar | Sangat kecil |
| Meta-framework | Angular Universal | Belum ada yang matang |

**Kekuatan:** Sangat scalable untuk tim besar, TypeScript-first, arsitektur yang sangat structured.  
**Kelemahan:** Learning curve sangat tinggi (Angular), atau kecil dan kurang terbukti (Aurelia).

---

## Konvergensi Signals: Semua Klaster Bertemu di Satu Titik

Yang menarik dari ekosistem saat ini: **semua klaster sedang konvergen ke signals** sebagai primitif reaktivitas.

```
React     → useSignal() (experimental, React 19)
Vue       → ref() / reactive() (sudah signals sejak Vue 3, 2020)
Angular   → signal(), computed(), effect() (sejak Angular 16, 2023)
Svelte 5  → $state Runes (compiler-based signals)
Solid     → createSignal() (dari awal, pelopor)
Qwik      → useSignal() (dari awal)
Preact    → @preact/signals
Aurelia   → ObserverLocator (konsep ekuivalen, nama berbeda)
```

Ini adalah momen langka dalam sejarah teknologi: semua framework yang berbeda paradigma sedang konvergen ke abstraksi yang sama. Signals adalah cara yang efisien untuk tracking dependency reaktif — dan ternyata ini lebih universal dari yang terlihat.

**Aurelia dan signals:** Aurelia sejak awal sudah menggunakan ObserverLocator yang bekerja seperti fine-grained signals. Ini adalah "ahead of its time" case — Aurelia sudah melakukan ini sebelum istilah "signals" populer, tapi tidak mendapat credit karena branding yang lemah dan komunitas yang kecil.

---

## Cara Menggunakan Peta Ini

Ketika menghadapi pertanyaan tentang framework, langkah pertama bukan "mana yang lebih baik" tapi "klaster mana yang cocok dengan konteks ini?":

**Klaster 1 cocok ketika:**
- Tim sudah familiar dengan React/hooks mental model
- Butuh ekosistem npm yang sangat luas
- UI sangat dinamis dengan banyak user interaction

**Klaster 2 cocok ketika:**
- Performa bundle size kritikal
- Content-heavy site (Astro sangat cocok)
- Tim mau belajar sesuatu yang baru dengan trade-off yang clear

**Klaster 3 cocok ketika:**
- Tim beragam background (Vue sangat adaptable)
- Need progressive adoption
- Nuxt sebagai meta-framework sudah sesuai kebutuhan

**Klaster 4 cocok ketika:**
- Tim besar, banyak developer
- Background enterprise / backend
- Long-term maintainability lebih penting dari initial velocity

---

## Pertanyaan untuk Direnungkan

1. Di tim kamu, apakah pilihan framework pernah diperdebatkan berdasarkan paradigma yang mendasari — atau lebih sering karena "yang lebih populer", "yang sudah familiar", atau "yang dipakai perusahaan besar X"?

2. Konvergensi ke signals menunjukkan bahwa semua klaster bisa belajar satu sama lain. Apakah ini akan mengarah ke satu paradigma yang dominan, atau justru akan ada bifurkasi baru yang belum terprediksi?

3. Vue punya "tiga rasa" API yang membuat banyak developer merasa familiar tapi juga membingungkan. Apakah fleksibilitas API ini adalah fitur atau technical debt?

---

## Selanjutnya

Klaster 1 membangun identitasnya di atas VDOM. Sudah saatnya kita bedah Virtual DOM secara mendalam — apa yang sebenarnya dilakukannya, di mana ia sangat tepat, dan di mana ia membayar overhead yang tidak perlu.

→ [Bab 10: VDOM — Kekuatan dan Keterbatasan](../bab-10/README.md)
