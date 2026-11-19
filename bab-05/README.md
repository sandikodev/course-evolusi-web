# Bab 05 — JavaScript: Substrate yang Tidak Direncanakan

> *"JavaScript adalah salah satu cerita terbesar dalam sejarah teknologi tentang bagaimana konteks, timing, dan distribusi bisa mengalahkan desain. Ia bukan bahasa terbaik untuk peran yang ia mainkan — tapi tidak ada bahasa lain yang bisa mengambil peran itu."*

---

Semua yang dibahas di Bab 01–04 berjalan di atas satu fondasi: JavaScript. Sudah saatnya kita bedah fondasi itu dengan jujur — kekuatannya, cacatnya yang tidak bisa diperbaiki, dan mengapa kita tetap harus menerimanya.

---

## Sepuluh Hari yang Mengubah Web

Mei 1995. Brendan Eich, engineer di Netscape, mendapat perintah dari manajemen: buat bahasa scripting untuk Netscape Navigator dalam waktu 10 hari. Tekanan bisnis: harus terlihat seperti Java (karena Java sedang hype), tapi harus lebih mudah untuk non-programmer.

Hasilnya adalah Mocha (kemudian LiveScript, kemudian JavaScript) — bahasa yang:
- Sintaksnya terinspirasi Java (karena marketing)
- Object model-nya dari Self (prototype-based, bukan class-based)
- First-class functions-nya dari Scheme
- Dikompilasi dalam waktu kurang dari dua minggu

Brendan Eich sendiri, dalam berbagai wawancara, mengakui bahwa banyak keputusan yang ia buat adalah kompromi kilat yang seharusnya diperbaiki kemudian. Tapi kemudian tidak pernah datang.

**Aturan yang paling menyiksa di web:** *"You cannot break the web."* Ketika jutaan situs web mengandalkan perilaku tertentu dari JavaScript — bahkan perilaku yang salah — kamu tidak bisa mengubahnya tanpa merusak jutaan situs itu.

---

## Cacat Warisan yang Tidak Bisa Diperbaiki

Ada beberapa cacat JavaScript yang sudah didokumentasikan secara luas dan tidak akan pernah bisa diperbaiki tanpa melanggar kompatibilitas mundur:

**`typeof null === 'object'`**  
Bug dari implementasi awal yang disimpan di dalam tag tipe 32-bit. Null pointer direpresentasikan sebagai nilai 0, dan tipe objek ditandai dengan bit `000`. Jadi `typeof null` membaca tanda objek dan mengembalikan `'object'`. Bug berumur 30 tahun, tidak bisa diperbaiki.

**Type coercion yang tidak predictable:**
```javascript
[] + []          // "" (string kosong)
[] + {}          // "[object Object]"
{} + []          // 0 (di beberapa konteks)
"5" - 3          // 2  (angka)
"5" + 3          // "53" (string)
0 == "0"         // true
0 == []          // true
"0" == []        // false  ← transitivitas dilanggar
```

Ini bukan hanya "quirky behavior" — ini adalah sumber bug nyata di production. JavaScript tidak gagal dengan error ketika tipe tidak cocok; ia diam-diam melakukan konversi yang seringkali bukan yang kamu maksud.

**Tidak ada integer native (sebelum BigInt):**  
Semua angka di JavaScript adalah IEEE 754 double-precision floating point. `0.1 + 0.2 === 0.3` mengembalikan `false`. `9007199254740992 === 9007199254740993` mengembalikan `true`. Menyimpan nilai uang dalam JavaScript tanpa sanitasi adalah bencana yang menunggu.

**`this` yang kontekstual:**  
Nilai `this` dalam JavaScript bergantung pada *bagaimana* fungsi dipanggil, bukan *di mana* fungsi didefinisikan. Ini adalah sumber kebingungan yang tak ada habisnya, terutama untuk developer yang datang dari bahasa OOP lain.

```javascript
const user = {
  name: 'Alice',
  greet: function() { console.log(this.name); }
};

user.greet();           // "Alice"
const fn = user.greet;
fn();                   // undefined (atau error di strict mode)
```

---

## Bagaimana JavaScript Memenangkan Web

Kalau JavaScript punya semua cacat ini, bagaimana ia bisa menjadi bahasa yang menguasai web?

**Distribusi, bukan kualitas.**

JavaScript di-bundle langsung dengan Netscape Navigator. Tidak ada install. Tidak ada konfigurasi. Setiap pengguna Netscape sudah bisa menjalankan JavaScript. Ini adalah competitive advantage yang tidak ada hubungannya dengan kualitas bahasa.

**Network effect.**

Ketika cukup banyak developer tahu JavaScript, cukup banyak library tersedia, cukup banyak tutorial ada — siklus ini memperkuat dirinya sendiri. Biaya belajar bahasa alternatif menjadi semakin tinggi secara relatif.

**Evolusi yang bertahap.**

Daripada mengganti JavaScript, komite TC39 (yang bertanggung jawab atas standar ECMAScript) memilih strategi evolusi bertahap: tambahkan fitur baru, jangan hapus fitur lama. ES6 (2015) membawa class, arrow functions, destructuring, promises, modules. Setiap tahun ada fitur baru. Bahasa yang sama, tapi semakin expressif.

---

## V8 dan Kompilasi JIT: Ketika Kecepatan Menjadi Mungkin

Salah satu faktor yang membuat JavaScript bisa dipakai untuk aplikasi kompleks adalah optimisasi mesin virtual. V8 (Google, 2008), SpiderMonkey (Mozilla), dan JavaScriptCore (Apple) mengimplementasikan **Just-In-Time compilation** — JavaScript tidak diinterpretasi baris per baris, tapi dikompilasi ke kode mesin pada runtime.

JIT compilation memungkinkan V8 mengoptimalkan kode berdasarkan pola penggunaan nyata — fungsi yang sering dipanggil mendapat optimisasi khusus. Hasilnya: JavaScript modern bisa mendekati kecepatan C++ untuk banyak use case.

Tapi JIT compilation juga membawa konsekuensi yang sering tidak dipikirkan: **performa JavaScript tidak deterministik**. Kode yang sama bisa berjalan dengan kecepatan yang sangat berbeda tergantung bagaimana V8 mendeoptimasi dan reoptimasi berdasarkan runtime behavior.

---

## CommonJS, AMD, ES Modules: Perjalanan Panjang Modularity

Satu hal yang tidak ada di JavaScript awal: sistem modul. Semua kode diletakkan di global scope, semua variabel bisa menglobber satu sama lain.

**CommonJS (2009)** — Ryan Dahl mengambil ini untuk Node.js: `require()` untuk import, `module.exports` untuk export. Synchronous, dirancang untuk server.

**AMD (Asynchronous Module Definition, 2010)** — untuk browser, karena synchronous loading tidak cocok di browser. `define()` dan `require()` dengan callbacks.

**ES Modules (ES6, 2015)** — akhirnya standar resmi: `import`/`export`. Static analysis-friendly. Tree-shakeable. Dirancang untuk berjalan native di browser.

Tapi transisi ke ES Modules tidak mulus karena ekosistem npm yang besar sudah menggunakan CommonJS. Sampai hari ini, interoperabilitas antara CommonJS dan ES Modules masih menjadi sumber kebingungan dan bug.

---

## Node.js: JavaScript Keluar dari Browser

Tahun 2009, Ryan Dahl merilis Node.js — runtime JavaScript berbasis V8 yang berjalan di server. Ini adalah momen penting: JavaScript tidak lagi terikat di browser.

Node.js memperkenalkan **event loop** dan **non-blocking I/O** sebagai model concurrency utama. Alih-alih thread terpisah untuk setiap request (model Apache), Node.js menggunakan satu thread dengan event loop yang menangani I/O secara asynchronous.

```javascript
// Non-blocking I/O — callback style (Node.js awal)
fs.readFile('data.json', (err, data) => {
  if (err) throw err;
  console.log(JSON.parse(data));
});
// Kode ini tidak memblokir — event loop lanjut ke request lain
```

Ini efisien untuk I/O-bound workload tapi bisa menjadi bottleneck untuk CPU-bound tasks karena single-threaded.

Node.js juga memunculkan sesuatu yang tidak ada sebelumnya: **developer yang bisa menulis frontend dan backend dalam bahasa yang sama.** Ini adalah faktor penting dalam lahirnya "fullstack JavaScript" sebagai konsep dan kultur.

---

## Garbage Collection: Kenyamanan dengan Harga

JavaScript menggunakan garbage collection untuk manajemen memori. Developer tidak perlu memikirkan alokasi dan dealokasi memori secara manual.

Tapi GC tidak gratis. GC pause — jeda saat garbage collector berjalan — bisa menyebabkan stuttering yang terasa di aplikasi real-time. Modern GC (V8 menggunakan incremental dan generational GC) sudah jauh lebih baik, tapi trade-off ini masih ada.

Untuk aplikasi yang membutuhkan latency yang sangat konsisten (game, trading system, real-time audio/video), JavaScript dengan GC bukan pilihan ideal. Ini salah satu alasan WebAssembly menarik — memberikan opsi untuk bagian kode yang butuh kontrol memori manual.

---

## Apa yang Bisa Dipelajari dari Ini

JavaScript mengajarkan beberapa pelajaran penting tentang teknologi secara umum:

**Pelajaran 1: Distribusi mengalahkan kualitas desain dalam menentukan adoption.**  
Language/framework yang di-bundle dengan platform yang sudah ada atau yang mendapat distribusi yang baik akan mengalahkan alternatif yang lebih baik secara teknis.

**Pelajaran 2: "Cannot break the web" adalah constraint yang sangat kuat.**  
Ini berlaku tidak hanya untuk JavaScript, tapi untuk semua teknologi yang sudah deployed di scale. Migrasi teknologi di scale adalah masalah yang sering diremehkan.

**Pelajaran 3: Evolusi bertahap lebih sustainabel dari rewrite.**  
TC39 memilih menambahkan ke JavaScript, bukan menggantinya. Hasilnya: bahasa yang sama yang ada sejak 1995 masih berjalan, dan developer modern bisa menggunakan fitur yang jauh lebih expressif.

---

## Pertanyaan untuk Direnungkan

1. JavaScript memiliki cacat desain yang sudah diketahui dan tidak bisa diperbaiki. Tapi ia tetap mendominasi web. Bagaimana ini mempengaruhi cara kamu berpikir tentang "pilih teknologi yang terbaik secara teknis"?

2. Node.js memungkinkan "fullstack JavaScript" — satu bahasa untuk frontend dan backend. Apakah ini benar-benar keuntungan, atau apakah ia mendorong developer untuk memilih teknologi berdasarkan kenyamanan bahasa daripada kesesuaian dengan masalah?

3. Bagaimana kamu menjelaskan kepada developer junior mengapa TypeScript tidak "memperbaiki" JavaScript — melainkan menambahkan lapisan di atasnya yang hilang saat runtime?

---

## Selanjutnya

Kalau JavaScript adalah bahasa yang punya cacat struktural, TypeScript hadir untuk menambalnya. Tapi seberapa jauh tambalannya efektif — dan apa yang TypeScript tidak bisa selesaikan?

→ [Bab 06: TypeScript — Keamanan Nyata atau Ilusi?](../bab-06/README.md)
