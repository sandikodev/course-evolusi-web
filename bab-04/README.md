# Bab 04 — Fragmentasi: Mengapa Banyak Framework Bisa Eksis Bersamaan

> *"Fragmentasi ekosistem bukan tanda kekacauan. Ini tanda bahwa ruang masalahnya cukup besar dan beragam untuk mendukung banyak solusi yang valid secara bersamaan."*

---

Pada 2015–2016, ada sesuatu yang aneh terjadi di dunia frontend: alih-alih satu pemenang yang clear, ekosistem justru semakin terfragmentasi. React, Angular 2, Vue, Ember, Aurelia — semuanya aktif dan punya komunitas yang loyal. Orang-orang mulai berbicara tentang "JavaScript fatigue" — kelelahan mengikuti tool dan framework baru yang muncul tiap bulan.

Tapi kalau kamu melihat lebih dalam, fragmentasi ini bukan kekacauan. Ini adalah hasil dari kenyataan bahwa **tidak ada satu set trade-off yang optimal untuk semua use case**.

---

## Mengapa Vue Bisa Ada di Samping React

Vue dirilis oleh Evan You pada Februari 2014 — satu tahun setelah React. Saat itu React sudah mulai mendapat traksi. Mengapa seseorang mau membangun framework lain?

Karena Evan You punya perspektif yang berbeda dari sumber yang berbeda. Ia bekerja di Google menggunakan AngularJS, dan frustrasi dengan kompleksitasnya. Tapi ia tidak ingin pergi sejauh React yang membuang konvensi HTML template sama sekali.

Vue adalah framework yang sengaja dirancang sebagai "progressive" — kamu bisa menggunakannya hanya untuk sebagian kecil halaman (seperti jQuery dulu) atau sebagai framework SPA penuh. Ini membedakannya dari React yang orientasinya lebih ke "all-in".

**Pelajaran:** Dua framework bisa koeksistensi bukan karena salah satunya salah, tapi karena mereka melayani developer yang berbeda dengan nilai yang berbeda: React untuk developer yang nyaman dengan "everything is JavaScript", Vue untuk developer yang ingin HTML tetap terasa seperti HTML.

---

## Angular 2: Rewrite Total yang Kontroversial

Ketika Google mengumumkan Angular 2 pada 2014, komunitas AngularJS syok. Ini bukan upgrade — ini rewrite total yang tidak kompatibel dengan AngularJS 1.x.

Angular 2 mengambil pendekatan yang sangat berbeda:
- TypeScript sebagai bahasa utama (bukan JavaScript biasa)
- Component-based architecture (bukan controller + scope)
- Unidirectional data flow sebagai default
- Zone.js untuk change detection
- RxJS untuk reactive programming

Ini adalah reaksi terhadap semua masalah AngularJS: performa dirty checking, kompleksitas `$scope`, kesulitan testing. Tapi biaya migrasinya sangat tinggi — kode AngularJS tidak bisa dipakai langsung di Angular 2.

**Pelajaran:** Ketika framework melakukan rewrite total, mereka kehilangan momentum komunitas. Banyak developer AngularJS yang pindah ke React atau Vue daripada migrasi ke Angular 2. Ini memberi ruang bagi Vue untuk tumbuh.

---

## Svelte: Mempertanyakan Asumsi Fundamental

Rich Harris merilis Svelte pertama kali pada 2016 dengan argumen yang mengejutkan: **framework seharusnya tidak ada di browser.** Framework seharusnya ada di build step — compiler yang mengubah kode kamu menjadi JavaScript vanilla yang langsung manipulasi DOM.

Ini mempertanyakan asumsi yang sudah diterima begitu saja oleh semua framework lain — bahwa framework harus di-download dan dijalankan di browser. React membawa ~40KB runtime. Angular membawa ~100KB+. Vue membawa ~25KB.

Svelte: hampir nol runtime.

```svelte
<script>
  let count = 0;
</script>
<button on:click={() => count++}>{count}</button>
```

Ini dikompilasi menjadi kode imperatif yang langsung manipulasi DOM — tanpa library runtime.

**Pelajaran:** Fragmentasi bisa datang dari mempertanyakan asumsi fundamental, bukan hanya dari menawarkan trade-off yang berbeda dari asumsi yang sama. Svelte membuka paradigma baru yang sebelumnya tidak terbayangkan.

---

## Peta Fragmentasi: Siapa Menjawab Apa

Kalau kita petakan mengapa masing-masing framework bisa eksis, polanya jelas:

**React** — menjawab: *"Bagaimana mengelola UI yang sangat dinamis dengan state yang kompleks?"*  
Cocok untuk: aplikasi dengan interaktivitas tinggi, tim besar, ekosistem npm yang luas

**Vue** — menjawab: *"Bagaimana membangun SPA yang progressif tanpa meninggalkan familiar-nya HTML?"*  
Cocok untuk: developer yang datang dari HTML/PHP, project yang perlu adopsi bertahap

**Angular** — menjawab: *"Bagaimana membangun aplikasi enterprise skala besar dengan banyak developer secara konsisten?"*  
Cocok untuk: tim enterprise, TypeScript-first, aplikasi yang butuh long-term maintainability

**Svelte** — menjawab: *"Bagaimana mendapat DX yang bagus tanpa membayar runtime overhead?"*  
Cocok untuk: performa kritikal, developer yang frustrasi dengan kompleksitas framework lain

**Aurelia** — menjawab: *"Bagaimana mendapat arsitektur Angular yang bersih tanpa overhead Zone.js dan NgModule?"*  
Cocok untuk: tim yang familiar dengan Angular tapi mau sesuatu yang lebih ringan dan standards-compliant

---

## JavaScript Fatigue: Masalah Nyata atau Masalah Persepsi?

"JavaScript fatigue" — istilah yang populer sekitar 2016 — merujuk pada kelelahan developer mengikuti tool, bundler, framework, dan paradigma baru yang muncul terus-menerus.

Tapi kalau dilihat lebih jernih, sebagian besar "fatigue" ini bukan dari framework yang berubah — framework inti (React, Vue, Angular) relatif stabil. Fatigue lebih banyak datang dari **toolchain**: webpack, Babel, ESLint, Prettier, dan konfigurasi yang semakin kompleks.

Yang ironis: toolchain yang kompleks ini sebagian besar hadir untuk membantu mengelola ekosistem npm yang semakin besar, yang sebagian adalah hasil dari kultur "banyak package kecil" yang dipopulerkan oleh React.

**Pelajaran:** Fragmentasi framework dan kompleksitas toolchain adalah dua masalah yang berbeda. Mencampur keduanya seringkali menghasilkan diagnosis yang salah.

---

## Prinsip di Balik Fragmentasi

Ada beberapa prinsip yang bisa disimpulkan dari periode ini:

**1. Ruang masalah yang besar mendukung banyak solusi valid**  
Kalau "membangun UI web" adalah masalah yang cukup besar dan beragam, ada ruang untuk banyak pendekatan yang masing-masing valid untuk konteks yang berbeda.

**2. Setiap framework merefleksikan nilai tim pembuatnya**  
React merefleksikan nilai Facebook: skalabilitas untuk tim besar, predictability, ekosistem yang composable. Vue merefleksikan nilai Evan You: accessibility, progressive adoption, familiar HTML. Ini bukan kebetulan.

**3. Popularitas bukan proxy untuk kualitas teknis**  
React populer bukan karena ia secara teknis superior dalam semua dimensi. Ia populer karena Facebook punya resources untuk mendukungnya, karena timing yang tepat, dan karena ia menyelesaikan masalah yang dirasakan banyak developer pada waktu itu.

**4. Fragmentasi akan terus terjadi karena masalahnya terus berkembang**  
Setiap gelombang teknologi baru (mobile, edge computing, AI) membawa kebutuhan baru yang belum sepenuhnya dijawab oleh framework yang ada. Fragmentasi bukan tanda kegagalan ekosistem — ini tanda ekosistem yang hidup.

---

## Pertanyaan untuk Direnungkan

1. Di tim kamu, apakah pilihan framework didasarkan pada analisis trade-off yang serius, atau lebih banyak dipengaruhi oleh preferensi individu, hype, atau "itu yang dipakai perusahaan besar"?

2. Kalau kamu harus merekomendasikan framework kepada tim baru yang akan membangun admin dashboard enterprise dengan 20+ developer, mana yang kamu pilih dan mengapa? Bagaimana jawabanmu berubah kalau tim-nya 2 orang untuk startup?

3. "JavaScript fatigue" sering dikritik sebagai sikap tidak profesional — developer "seharusnya" bisa mengikuti perubahan. Tapi ada argumen bahwa fatigue adalah respons rasional terhadap ekosistem yang berubah terlalu cepat tanpa alasan yang clear. Mana yang lebih mendekati kebenaran menurut pengalamanmu?

---

## Selanjutnya

Semua framework yang telah kita bahas berjalan di atas JavaScript. Sudah saatnya kita bedah substrat itu sendiri — bahasa yang tidak dirancang untuk peran yang ia mainkan hari ini.

→ [Bab 05: JavaScript — Substrate yang Tidak Direncanakan](../bab-05/README.md)


---

## Melihat Kembali: Apa yang Bisa Dipelajari dari Fragmentasi Ini

Ada beberapa pelajaran yang baru bisa dilihat dengan jelas setelah bertahun-tahun:

**1. Fragmentasi adalah bukti ekosistem yang sehat, bukan lemah.**
Ekosistem yang hanya punya satu pilihan adalah ekosistem yang dikontrol oleh satu entitas. Fragmentasi berarti ada banyak perspektif, banyak eksperimen, dan banyak kesempatan untuk menemukan apa yang benar-benar bekerja untuk berbagai konteks.

**2. "JavaScript fatigue" lebih akurat disebut "toolchain fatigue".**
Framework inti (React, Vue, Angular) relatif stabil selama bertahun-tahun. Yang berubah dengan cepat adalah toolchain: bundler (Webpack → Rollup → Vite → Rolldown), transpiler (Babel → esbuild → SWC → Oxc), test runner, linter. Ini adalah masalah yang berbeda dengan masalah framework.

**3. Pemenang di battle framework tidak selalu yang "terbaik secara teknis".**
React menang bukan karena ia secara teknis superior dalam semua dimensi — ia menang karena timing, resources dari Facebook, ekosistem yang tumbuh cepat, dan mental model yang cukup intuitif untuk disebarluaskan. Pelajaran ini relevan setiap kali ada "framework baru yang lebih baik" yang muncul.

**4. Setiap framework membawa budayanya.**
React membawa budaya "compose everything from primitives." Angular membawa budaya "follow the opinionated path." Vue membawa budaya "pragmatic and approachable." Svelte membawa budaya "eliminate the runtime." Ketika kamu bergabung dengan ekosistem suatu framework, kamu juga bergabung dengan budayanya — dan ini mempengaruhi cara kamu berpikir tentang engineering.
