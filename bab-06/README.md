# Bab 06 — TypeScript: Keamanan Nyata atau Ilusi?

> *"TypeScript tidak memperbaiki JavaScript. TypeScript menambahkan lapisan yang hilang di build time, tapi di runtime kamu tetap berjalan di JavaScript yang sama dengan semua quirk-nya."*

---

Jika JavaScript adalah fondasi yang retak, TypeScript adalah perancah yang dipasang di atasnya. Anders Hejlsberg, perancang C# di Microsoft, mengembangkan TypeScript dengan premis sederhana: tambahkan sistem tipe statis ke JavaScript yang bisa dieliminasi saat compile — sehingga output-nya tetap JavaScript valid yang berjalan di mana saja.

Ini adalah keputusan desain yang sangat pragmatis, dan ia menentukan baik kekuatan maupun keterbatasan TypeScript.

---

## Apa yang TypeScript Berikan

**Deteksi error lebih awal:**
```typescript
function greet(name: string): string {
  return "Halo, " + name.toUpperCase();
}

greet(42); // Error: Argument of type 'number' is not assignable to parameter of type 'string'
```

Tanpa TypeScript, `greet(42)` berjalan tanpa error sampai runtime — dan `42.toUpperCase()` melempar exception di production.

**Dokumentasi yang hidup:**  
Tipe adalah dokumentasi yang diverifikasi oleh compiler. Kalau kamu mengubah signature fungsi tapi lupa update semua pemanggil, TypeScript akan memberitahu kamu.

**Refactoring yang lebih aman:**  
Rename variabel atau ubah struktur object — TypeScript akan menunjukkan semua tempat yang perlu diupdate.

**Autocomplete yang bermakna:**  
IDE bisa menyarankan property dan method yang valid karena tahu tipe dari object.

---

## Type Erasure: Ilusi yang Harus Dipahami

Ini adalah bagian yang paling sering disalahpahami. TypeScript dikompilasi ke JavaScript dengan **type erasure** — semua informasi tipe dihapus.

```typescript
// TypeScript
function processUser(user: User): void {
  console.log(user.name);
}

// Dikompilasi ke JavaScript
function processUser(user) {
  console.log(user.name);
}
```

Di runtime, tidak ada `User`. Tidak ada pemeriksaan bahwa `user` benar-benar memiliki property `name`. Kalau kamu memanggil `processUser("bukan user")` dari kode JavaScript yang tidak di-typecheck, tidak ada yang akan mencegahnya.

**Implikasi kritis:** Perbatasan antara kode yang di-typecheck dan yang tidak (data dari API, localStorage, input user, library tanpa tipe) adalah titik di mana TypeScript tidak bisa memberikan jaminan apapun.

```typescript
// TypeScript percaya ini aman
const data: User = JSON.parse(localStorage.getItem('user')!);
data.name.toUpperCase(); // Bisa crash kalau localStorage berisi data yang rusak
```

---

## Gradual Typing: Kekuatan dan Kelemahan

TypeScript dirancang untuk bisa diadopsi secara bertahap — kamu bisa mulai dengan JavaScript biasa dan tambahkan tipe secara incremental. Ini yang membuat migrasi codebase JavaScript yang besar ke TypeScript mungkin dilakukan.

Tapi gradual typing juga berarti ada "escape hatch" yang bisa merusak keamanan:

**`any`:** Mematikan semua type checking untuk nilai tersebut.
```typescript
const x: any = "hello";
x.toUpperCase(); // OK
x.nonExistentMethod(); // Juga OK — TypeScript tidak tahu, tidak memeriksa
```

**`as` (type assertion):** Memaksa TypeScript percaya bahwa suatu nilai memiliki tipe tertentu, tanpa verifikasi.
```typescript
const input = document.getElementById('username') as HTMLInputElement;
// TypeScript percaya ini HTMLInputElement
// Tapi kalau element tidak ada, ini null — dan kamu akan crash
```

**`!` (non-null assertion):** Memberitahu TypeScript bahwa nilai ini tidak null/undefined.
```typescript
const element = document.getElementById('app')!;
// Kamu berjanji kepada TypeScript bahwa ini tidak null
// Kalau salah, TypeScript tidak akan mengingatkanmu
```

Semua escape hatch ini valid untuk use case tertentu. Masalahnya: dalam tekanan deadline atau di tangan developer yang tidak memahami TypeScript dengan baik, mereka dipakai untuk "menghilangkan error" daripada menyelesaikan akar masalahnya.

---

## Structural Typing: Berbeda dari yang Kamu Kira

TypeScript menggunakan **structural typing** (duck typing) — dua tipe dianggap kompatibel kalau mereka memiliki struktur yang sama, terlepas dari nama mereka.

```typescript
interface Cat { meow(): void; }
interface Dog { meow(): void; }

function makeCatMeow(cat: Cat): void {
  cat.meow();
}

const dog: Dog = { meow: () => console.log("bark?") };
makeCatMeow(dog); // Valid! Dog strukturnya sama dengan Cat
```

Ini berbeda dari **nominal typing** di Java atau C# di mana nama tipe yang menentukan kompatibilitas.

Structural typing fleksibel dan mengurangi boilerplate, tapi bisa menghasilkan false sense of security — dua tipe yang kebetulan memiliki struktur yang sama tapi semantik yang berbeda bisa dipertukarkan tanpa error.

---

## Runtime Validation: Zod, Yup, dan Perbatasan

Karena TypeScript tidak bisa menjamin keamanan di runtime, tim yang serius tentang keamanan data menggunakan **runtime validation library** di perbatasan antara dunia yang di-typecheck dan yang tidak.

**Zod** (paling populer untuk TypeScript):

```typescript
import { z } from 'zod';

const UserSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(1).max(80),
  email: z.string().email(),
});

// Di perbatasan — data dari API
const rawData = await fetchUser(userId); // unknown
const user = UserSchema.parse(rawData); // Throws jika tidak valid
// Sekarang TypeScript DAN runtime tahu user valid
```

Zod melakukan dua hal sekaligus: mendefinisikan schema untuk runtime validation, dan menghasilkan tipe TypeScript dari schema tersebut. Ini adalah pola yang menggabungkan keunggulan static typing dengan keamanan runtime yang nyata.

---

## TypeScript di Berbagai Konteks

TypeScript tidak seragam nilainya di semua konteks:

**Sangat berharga:**
- Library atau package yang akan dipakai orang lain — tipe adalah kontrak yang terdokumentasi
- Tim besar dengan banyak developer yang menulis kode yang saling bergantung
- Codebase yang akan dijaga jangka panjang dengan banyak refactoring
- Kode yang berinteraksi dengan banyak endpoint API

**Nilai marginalnya lebih rendah:**
- Script kecil yang hidup pendek
- Prototyping yang akan dibuang
- Tim kecil yang sangat familiar dengan codebase-nya

**Ini bukan argumen untuk tidak pakai TypeScript** — tapi untuk memahami *mengapa* TypeScript bernilai, bukan hanya ikut karena semua orang melakukannya.

---

## TypeScript dan Ekosistem Framework

TypeScript telah menjadi de facto standard di ekosistem framework modern:

- **Angular** — TypeScript dari hari pertama, bahasa utama
- **React** — JavaScript dulu, tapi TypeScript sekarang sangat umum dengan `.tsx`
- **Vue 3** — ditulis ulang di TypeScript, tapi masih bisa dipakai dengan JavaScript
- **Svelte** — `<script lang="ts">` untuk TypeScript
- **Aurelia 2** — TypeScript stage-3 decorators, `experimentalDecorators: false`

Yang menarik: masing-masing framework menggunakan TypeScript secara berbeda, dengan fitur yang berbeda, dan kadang dengan konfigurasi yang berbeda. Ini menyebabkan "TypeScript yang berbeda" di ekosistem yang berbeda — decorator Angular berbeda dari decorator TypeScript standar yang dipakai Aurelia.

---

## Masa Depan: Tipe di Runtime?

Ada eksperimen yang lebih radikal: bagaimana kalau tipe TypeScript bisa tersedia di runtime juga?

**TC39 Type Annotations Proposal** — proposal untuk JavaScript native yang memungkinkan type annotations ditulis tanpa dikompilasi, dan diabaikan oleh runtime (tapi diproses oleh tools). Ini bukan runtime checking, tapi akan memungkinkan tooling yang lebih baik tanpa build step.

**AssemblyScript** — subset TypeScript yang dikompilasi ke WebAssembly, bukan JavaScript. Ini "TypeScript yang lebih serius" dengan semantik yang lebih dekat ke bahasa statically-typed.

---

## Pertanyaan untuk Direnungkan

1. Di project yang kamu kerjakan, seberapa konsisten TypeScript digunakan? Apakah ada bagian codebase yang masih menggunakan `any` secara bebas, dan bagaimana ini mempengaruhi kepercayaan tim terhadap type safety secara keseluruhan?

2. Zod (atau library serupa) menggabungkan schema validation dengan TypeScript inference. Apakah ini seharusnya menjadi default untuk semua kode yang berinteraksi dengan data eksternal? Apa yang mencegah adopsi yang lebih luas?

3. TypeScript menambahkan build step. Untuk proyek kecil atau prototype, apakah overhead ini worth it? Bagaimana kamu memutuskan kapan TypeScript sesuai dan kapan JavaScript biasa lebih pragmatis?

---

## Selanjutnya

JavaScript dan TypeScript berjalan di atas sesuatu yang lebih fundamental: browser — platform yang terus berkembang dengan kemampuan yang sering kurang dimanfaatkan developer yang terlalu fokus pada framework.

→ [Bab 07: Browser sebagai Platform](../bab-07/README.md)
