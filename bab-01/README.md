# Bab 01 — Pra-SPA: Sebelum Framework Ada

> *"Untuk memahami mengapa sesuatu berubah, kamu harus tahu betapa menyiksanya keadaan sebelum perubahan itu terjadi."*

---

Sebagian besar developer yang belajar frontend hari ini langsung masuk ke era React atau Vue. Mereka tidak pernah merasakan — atau bahkan mendengar — tentang bagaimana web dibangun sebelum framework modern ada. Ini bukan masalah kalau kamu hanya ingin *menggunakan* framework. Ini jadi masalah kalau kamu ingin *memahaminya*.

Karena setiap framework lahir sebagai reaksi terhadap rasa sakit yang spesifik. Dan kalau kamu tidak tahu rasa sakitnya, kamu tidak bisa mengevaluasi apakah solusinya benar-benar menyelesaikan masalah — atau hanya memindahkannya ke tempat lain.

---

## Web Sebagai Dokumen, Bukan Aplikasi

Web lahir pada 1991 dari pikiran Tim Berners-Lee di CERN. Tujuannya sederhana dan spesifik: cara bagi fisikawan untuk berbagi dokumen penelitian. HTML adalah bahasa untuk mendeskripsikan struktur dokumen. HTTP adalah protokol untuk mengambil dokumen itu dari server.

Tidak ada yang namanya "state". Tidak ada yang namanya "interaktivitas". Tidak ada yang namanya "komponen". Setiap request menghasilkan dokumen baru. Setiap klik link adalah perjalanan ke dokumen lain. Web adalah *perpustakaan*, bukan *aplikasi*.

Ini penting: **arsitektur web awal dirancang dengan asumsi yang sangat berbeda dari apa yang kita butuhkan sekarang.** Semua kerumitan yang kita hadapi hari ini — hidrasi, state management, routing client-side — berakar dari fakta bahwa kita sedang membangun aplikasi di atas platform yang dirancang untuk dokumen.

---

## CGI dan Server-Side Rendering: Generasi Pertama Dinamisme

Ketika bisnis mulai melihat web sebagai medium komersial di pertengahan 1990-an, kebutuhan baru muncul: halaman yang isinya bisa berubah. Produk yang berbeda untuk URL yang berbeda. Harga yang diambil dari database, bukan ditulis langsung di HTML.

Jawabannya adalah **CGI (Common Gateway Interface)**. Server bisa menjalankan program eksternal (Perl, kemudian PHP, Python, Ruby) dan output-nya — string HTML — dikirimkan ke browser. Setiap request, server menjalankan program, program membaca dari database, program menghasilkan HTML, HTML dikirim ke browser.

```
Browser → Request → Server → Program (PHP/Perl) → Database → HTML → Browser
```

Ini adalah **Server-Side Rendering** dalam bentuk paling primitifnya. Dan ia bekerja dengan sangat baik untuk use case saat itu: dokumen dengan isi dinamis.

Yang tidak bekerja: interaktivitas. Kalau user mengklik tombol dan kamu ingin memperbarui sebagian halaman tanpa reload — tidak bisa. Setiap interaksi yang butuh data baru dari server harus me-reload seluruh halaman.

Bayangkan mengisi form multi-langkah. Kamu mengisi step 1, klik "Lanjut", server memvalidasi, dan kalau ada error — kamu mendapat halaman baru dengan semua field kosong kembali. User experience-nya menyiksa.

---

## Template Engines dan MVC Server-Side

Ketika web mulai lebih kompleks, mixing logika bisnis dengan output HTML di dalam satu skrip PHP menjadi mimpi buruk pemeliharaan. Kode seperti ini nyata dan umum:

```php
<?php
$conn = mysql_connect("localhost", "user", "pass");
$result = mysql_query("SELECT * FROM products WHERE id = " . $_GET['id']);
$row = mysql_fetch_array($result);
echo "<html><head><title>" . $row['name'] . "</title></head>";
echo "<body><h1>" . $row['name'] . "</h1>";
echo "<p>Harga: " . $row['price'] . "</p>";
?>
```

Ada SQL injection di sini. Ada XSS di sini. Ada spaghetti code di sini. Dan ini *normal* untuk eranya.

Solusinya adalah pemisahan concerns: **template engines** (Smarty, ERB, Jinja, Blade) memisahkan logika dari presentasi. MVC server-side (Rails, Django, Laravel, CodeIgniter) memisahkan model, view, dan controller.

```
Controller → mengambil data dari Model → melempar ke View (template) → HTML
```

Ini adalah perbaikan arsitektur yang nyata. Kode lebih terorganisir, lebih mudah ditest, lebih mudah dikelola tim.

Tapi masalah fundamental tetap sama: **setiap interaksi yang butuh data baru = reload halaman penuh**. Dan semakin web bergerak ke arah "aplikasi" daripada "dokumen", semakin menyakitkan batasan ini.

---

## AJAX: Perubahan Fundamental yang Datang Diam-Diam

Tahun 1999, Microsoft menambahkan `XMLHttpRequest` ke Internet Explorer 5 untuk kebutuhan internal Outlook Web Access. Tidak ada yang tahu waktu itu bahwa ini akan mengubah arsitektur web secara fundamental.

Ide dasarnya: browser bisa mengirim request HTTP *di latar belakang*, tanpa me-reload halaman, dan memperbarui sebagian DOM berdasarkan respons yang diterima.

```javascript
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4 && xhr.status === 200) {
    document.getElementById('result').innerHTML = xhr.responseText;
  }
};
xhr.open('GET', '/api/data', true);
xhr.send();
```

Google Maps (2005) membuktikan potensinya: peta yang bisa di-drag, zoom, dan di-navigate tanpa reload halaman. Google Suggest (embrio Google Autocomplete) menampilkan saran pencarian secara real-time. Gmail memuat email baru tanpa refresh.

Jesse James Garrett menyebut teknik ini **AJAX** (Asynchronous JavaScript and XML) dalam esai 2005-nya, dan nama itu menempel.

Sekarang web *bisa* jadi aplikasi. Tapi cara melakukannya masih manual, berantakan, dan sangat bergantung pada jQuery.

---

## jQuery: Solusi Pragmatis untuk Era yang Berantakan

Browser wars adalah mimpi buruk developer. Internet Explorer, Firefox, Safari, Opera — masing-masing mengimplementasikan DOM API secara berbeda. Kode yang berjalan di Firefox mungkin crash di IE. Mendeteksi browser, menulis kode kondisional untuk tiap browser, lalu men-debug di semua browser adalah pekerjaan nyata yang memakan waktu nyata.

John Resig merilis jQuery pada 2006 dengan premis sederhana: *"Write less, do more."* Lebih spesifik: tulis sekali, jalan di semua browser.

```javascript
// Tanpa jQuery — verbose, berbeda tiap browser
var el = document.getElementById('menu');
el.addEventListener('click', function() { /* ... */ });
// atau di IE: el.attachEvent('onclick', function() { /* ... */ });

// Dengan jQuery — satu baris, semua browser
$('#menu').click(function() { /* ... */ });
```

jQuery tidak mengubah paradigma arsitektur web. Ia hanya membuat cara kerja yang sudah ada (DOM manipulation, AJAX, event handling) jauh lebih ergonomis dan portable.

Tapi ada implikasi arsitektural dari dominasi jQuery yang sering luput diperhatikan: **jQuery mendidik jutaan developer untuk berpikir dalam terms manipulasi DOM imperatif.** Kamu mengambil elemen, kamu mengubahnya, kamu menyembunyikannya. State ada di DOM, bukan di JavaScript.

Ini bekerja untuk halaman dengan interaktivitas sedang. Ini mulai rusak ketika interaktivitas menjadi kompleks.

---

## Rasa Sakit yang Mendorong Perubahan

Bayangkan kamu membangun aplikasi web yang cukup kompleks — katakanlah, interface manajemen project dengan real-time update, filter, sort, dan drag-and-drop — menggunakan jQuery murni.

Kamu akan menghadapi masalah-masalah ini:

**Masalah 1: State tersebar**
State aplikasimu ada di mana-mana — di nilai DOM, di variabel global, di localStorage, di response AJAX yang belum selesai. Tidak ada single source of truth. Ketika state berubah di satu tempat, kamu harus ingat untuk memperbarui semua tempat lain yang bergantung padanya. Kalau lupa satu, bug muncul.

**Masalah 2: Tidak ada konsep "komponen"**
Kalau kamu punya dropdown yang dipakai di 10 tempat berbeda, kamu punya 10 kopi HTML dengan 10 kopi event listener. Ketika kamu perlu mengubah satu detail perilaku dropdown itu, kamu mengubahnya di 10 tempat. Kalau lupa satu, inkonsistensi muncul.

**Masalah 3: Sulit di-test**
Kode jQuery yang baik masih terikat ke DOM. Test unitnya butuh browser atau jsdom yang mensimulasikan browser. Test yang bergantung pada DOM lebih lambat, lebih rapuh, dan lebih sulit ditulis daripada test yang hanya menguji fungsi murni.

**Masalah 4: Skala team**
Konvensi jQuery adalah "tidak ada konvensi". Setiap developer punya cara sendiri mengorganisir kode. Kode yang ditulis developer A tidak mudah dibaca developer B karena tidak ada struktur yang disepakati.

Ini adalah rasa sakit nyata yang dialami tim-tim yang membangun aplikasi web kompleks di akhir 2000-an dan awal 2010-an. Dan dari rasa sakit inilah gelombang pertama JavaScript MVC framework lahir.

---

## Warisan yang Dibawa Sampai Sekarang

Sebelum lanjut ke bab berikutnya, ada warisan dari era pra-SPA yang masih relevan hari ini dan sering diabaikan:

**Server-Side Rendering bukan "cara lama" — ia adalah cara yang benar untuk banyak use case.** Ketika Next.js, Nuxt, dan SvelteKit "menemukan kembali" SSR, mereka tidak menemukan sesuatu yang baru. Mereka kembali ke arsitektur yang sudah terbukti bekerja selama dekade — dengan tambahan hidrasi untuk interaktivitas client-side.

**Form submission tanpa JavaScript adalah fitur, bukan keterbatasan.** Web bekerja tanpa JavaScript. Kalau kamu membangun aplikasi yang 100% bergantung pada JavaScript untuk fungsi dasarnya, kamu memperkenalkan fragility. Ini bukan argumen untuk tidak pakai JavaScript — ini argumen untuk progressive enhancement.

**Pemisahan HTML, CSS, dan JavaScript adalah konvensi yang punya alasan.** Ketika JSX mencampur markup dan logic, ia mengorbankan sesuatu. Ada developer yang menganggap itu trade-off yang worth it. Ada yang tidak. Yang penting: ini bukan soal "mana yang lebih modern" tapi soal nilai apa yang lebih kamu prioritaskan.

---

## Pertanyaan untuk Direnungkan

1. Banyak aplikasi yang kamu kerjakan hari ini — apakah mereka benar-benar membutuhkan client-side routing dan state management yang kompleks, atau mereka bisa diselesaikan dengan SSR sederhana yang lebih maintainable?

2. jQuery memiliki sintaks yang dianggap "legacy" hari ini, tapi ia masih dipakai di lebih dari 70% website dunia. Apa yang bisa kamu pelajari dari longevity-nya tentang bagaimana teknologi benar-benar diadopsi di industri?

3. Kalau kamu harus menjelaskan kepada developer junior mengapa kita tidak lagi menulis aplikasi dengan jQuery murni, apa argumen kamu? Dan apakah argumen itu berlaku universal, atau hanya untuk use case tertentu?

---

## Selanjutnya

Kita sudah lihat dari mana web bermula dan rasa sakit apa yang mendorong perubahan. Di bab berikutnya, kita masuk ke gelombang pertama framework JavaScript — AngularJS, Backbone, Ember — dan pertanyaan penting: mengapa tiga framework dengan pendekatan yang sangat berbeda bisa muncul hampir bersamaan?

→ [Bab 02: Gelombang SPA Pertama](../bab-02/README.md)
