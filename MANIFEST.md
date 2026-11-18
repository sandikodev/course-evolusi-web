# Manifesto: Keyakinan yang Mendasari Course Ini

> Sebelum membaca satu bab pun, kamu perlu tahu dari sudut pandang apa course ini ditulis. Keyakinan-keyakinan ini bukan fakta universal — ini adalah posisi yang diambil penulis berdasarkan pengamatan dan pengalaman. Kamu boleh tidak setuju. Tapi kalau kamu tidak tahu dari mana argumen-argumen dalam course ini berasal, kamu akan kehilangan konteksnya.

---

## Keyakinan 1: Tidak Ada Framework yang Menang — Hanya Trade-off yang Berbeda

Setiap framework yang bertahan lebih dari 3 tahun bertahan bukan karena ia "lebih baik" secara absolut, tapi karena ia memenangkan trade-off yang tepat untuk segmen pengguna yang tepat di waktu yang tepat.

React tidak menang karena virtual DOM lebih cepat dari DOM asli. React menang karena Facebook butuh cara menyatukan ribuan developer dengan mental model yang konsisten, dan virtual DOM adalah abstraktion yang cukup intuitif untuk itu.

Svelte tidak menang karena ia "lebih cepat dari React". Svelte menang karena ia membuktikan bahwa runtime bisa dieliminasi — dan itu membuka ruang pikir baru.

**Implikasi:** Kalau kamu membandingkan framework dengan menghitung benchmark angka semata, kamu membandingkan hal yang salah. Yang relevan adalah: *untuk konteks apa, untuk tim seperti apa, dengan constraint apa*.

---

## Keyakinan 2: JavaScript Tidak Memenangkan Web Karena Kualitasnya

JavaScript menjadi bahasa universal web karena monopoli distribusi, bukan karena ia adalah bahasa yang dirancang dengan baik. Lahir dalam 10 hari di bawah tekanan bisnis, JavaScript membawa cacat struktural yang tidak bisa diperbaiki tanpa melanggar kompatibilitas mundur.

TypeScript tidak memecahkan masalah ini — TypeScript hanya menyembunyikannya di build time. Di runtime, semua jaminan tipe hilang.

**Ini bukan alasan untuk meninggalkan JavaScript.** Ini adalah alasan untuk memperlakukannya dengan disiplin yang sesuai — Zod untuk validasi runtime, strict mode selalu, tidak ada `any`.

**Implikasi:** Developer yang menganggap "kode TypeScript = kode yang aman" adalah developer yang belum menghadapi bug produksi dari data yang tidak sesuai skema.

---

## Keyakinan 3: Hype adalah Sinyal, Bukan Instruksi

Setiap kali framework baru muncul dengan benchmark mengesankan dan tweet dari influencer, ada dua kemungkinan: (a) ia memang menyelesaikan masalah nyata yang belum ada solusinya, atau (b) ia adalah solusi yang mencari masalah.

Yang membedakan keduanya bukan popularitas — popularitas hanya mengukur seberapa bagus marketingnya. Yang membedakan adalah: *apakah ada orang yang menggunakannya di production untuk menyelesaikan masalah yang sebelumnya tidak bisa diselesaikan?*

**Implikasi:** Cara baca yang sehat terhadap ekosistem: dengarkan hype sebagai sinyal bahwa ada sesuatu yang menarik, tapi tunda adopsi sampai ada bukti production yang credible.

---

## Keyakinan 4: Spesialisasi Sempit di Waktu yang Tepat Lebih Menguntungkan daripada Generalisme Dangkal

Developer yang bisa "semua framework" tapi tidak benar-benar dalam di satupun akan selalu kalah dengan developer yang sangat dalam di satu niche yang langka — selama ada pasar untuk niche itu.

Paradoksnya: niche yang paling menguntungkan adalah yang peluangnya tidak terlihat jelas. Kalau semua orang sudah tahu niche itu menguntungkan, persaingannya sudah terlalu ketat.

Aurelia adalah contoh kasus nyata: 15–30 job aktif di Upwork setiap saat, hampir tidak ada kompetitor qualified. Bukan karena Aurelia adalah framework terbaik, tapi karena hampir tidak ada yang mau mempelajarinya.

**Implikasi:** Peta ekosistem yang kamu bangun setelah membaca course ini bukan untuk memilih "framework terbaik". Ini untuk menemukan *celah* — teknologi yang dibutuhkan orang tapi tidak banyak yang kuasai.

---

## Keyakinan 5: Arsitektur yang Baik Bisa Dijelaskan kepada Orang yang Tidak Teknis

Kalau kamu tidak bisa menjelaskan mengapa kamu memilih React vs Next.js vs SvelteKit dalam 3 kalimat kepada product manager — kamu belum benar-benar memahaminya. Kamu hanya mengikuti apa yang familiar.

Arsitektur defensible adalah arsitektur yang bisa dipertahankan dengan argumen yang jelas tentang trade-off — bukan dengan "ini yang terpopuler" atau "ini yang dipakai perusahaan besar".

**Implikasi:** Setiap bab dalam course ini diakhiri bukan dengan "gunakan ini" tapi dengan "pertanyaan yang perlu kamu jawab sebelum memutuskan".

---

## Keyakinan 6: Web Platform Berkembang Lebih Cepat dari yang Kebanyakan Developer Sadari

Browser modern bukan lagi "dokumen viewer". Ia adalah runtime yang bisa menjalankan WebAssembly, mengakses hardware sensor, bekerja offline, mengelola background sync, dan mengirim notifikasi push.

Banyak hal yang dulu butuh framework kini tersedia natively: Custom Elements (Web Components), CSS Grid dan Container Queries, Intersection Observer, View Transitions API. Framework yang gagal mengikuti evolusi platform akan menjadi beban yang semakin berat.

**Implikasi:** Memahami platform (browser, Web APIs, standar W3C) adalah fondasi yang lebih stabil daripada mendalami satu framework. Framework datang dan pergi. Platform bergerak lambat tapi ke arah yang benar.

---

## Keyakinan 7: Tidak Ada "Stack Terbaik" — Hanya Kecocokan antara Masalah, Tim, dan Constraint

Stack yang benar untuk startup dengan 2 developer yang bergerak cepat berbeda dari stack yang benar untuk tim 50 orang dengan kontrak enterprise 10 tahun. Stack yang benar untuk aplikasi yang SEO-nya kritikal berbeda dari stack untuk admin dashboard internal yang tidak perlu terindeks.

**Implikasi:** Jawaban atas pertanyaan "framework apa yang harus saya pakai?" selalu dimulai dengan "untuk apa, untuk siapa, dan dengan constraint apa?" — bukan dengan benchmark atau popularitas.

---

## Keyakinan 8: Frontend, Backend, dan Infrastruktur Bukan Domain Terpisah — Mereka Adalah Lapisan dari Satu Pertanyaan yang Sama

Keputusan di satu lapisan selalu mempengaruhi lapisan lain. Memilih React tanpa mempertimbangkan rendering strategy adalah keputusan yang belum selesai. Memilih SSR tanpa mempertimbangkan caching strategy adalah keputusan yang akan datang menagih. Memilih microservices tanpa mempertimbangkan operational complexity adalah keputusan yang akan menyiksa tim operations.

Developer yang hanya bisa berpikir dalam satu lapisan akan selalu membuat keputusan yang terasa benar di lapisannya tapi menimbulkan masalah di lapisan lain.

**Implikasi:** Course ini bergerak dari browser ke infrastruktur bukan karena semua developer harus ahli di semua lapisan — tapi karena memahami *hubungan antar lapisan* adalah yang membedakan developer yang bisa merekomendasikan arsitektur dari yang hanya bisa mengimplementasikannya.

---

*Kalau kamu setuju dengan semua keyakinan di atas, course ini akan mengkonfirmasi apa yang sudah kamu rasakan. Kalau kamu tidak setuju dengan beberapa, course ini akan menantangmu — dan itu justru lebih berharga.*
