# Bab 11 — Signals: Konvergensi yang Terjadi Diam-Diam

> *"Signals bukan tren baru. Signals adalah re-discovery dari sesuatu yang sudah lama diketahui di dunia reactive programming — dan sekarang semua framework sedang menemukan kembali hal yang sama secara bersamaan."*

---

Kalau kamu memperhatikan ekosistem frontend 3 tahun terakhir, ada sesuatu yang menarik: hampir semua framework secara independen bergerak ke signals. React bereksperimen dengan useSignal(). Vue 3 sudah pakai ref()/reactive() yang pada dasarnya adalah signals. Angular 16 merilis signal() native. Svelte 5 merilis Runes yang dikompilasi ke signals. Solid dari awal berbasis signals. Qwik juga.

Ini bukan kebetulan. Ini adalah konvergensi menuju solusi yang lebih efisien untuk reactive UI — dan memahami *mengapa* membantu kamu membuat keputusan yang lebih baik.

---

## Apa Itu Signals

Signals adalah primitif reaktif: **sebuah nilai yang, ketika berubah, secara otomatis memberitahu semua "subscriber" yang bergantung padanya.**

```javascript
// Implementasi konseptual sederhana
function createSignal(initialValue) {
  let value = initialValue;
  const subscribers = new Set();

  return {
    get() {
      // Track siapa yang membaca nilai ini
      if (currentEffect) subscribers.add(currentEffect);
      return value;
    },
    set(newValue) {
      value = newValue;
      // Notifikasi semua subscriber
      subscribers.forEach(sub => sub());
    }
  };
}
```

Prinsip dasarnya sederhana. Tapi implementasinya di berbagai framework berbeda dalam cara mereka menangani dependency tracking, batching, dan reactivity scope.

---

## Perbandingan Implementasi

**React `useState` (bukan signals):**
```jsx
const [count, setCount] = useState(0);
// Update memicu re-render seluruh komponen
setCount(count + 1);
```

**React `useSignal` (eksperimental/third-party):**
```jsx
const count = useSignal(0);
// Hanya binding yang bergantung pada count yang update
count.value++;
```

**Vue `ref()`:**
```javascript
const count = ref(0);
count.value++; // Reactive, hanya update yang bergantung
```

**SolidJS `createSignal()`:**
```javascript
const [count, setCount] = createSignal(0);
setCount(c => c + 1); // Fine-grained, komponen tidak re-run
```

**Angular `signal()`:**
```typescript
const count = signal(0);
count.update(c => c + 1); // Atau count.set(newValue)
```

**Svelte 5 `$state`:**
```svelte
let count = $state(0);
count++; // Kompiler mengubah ini ke signals yang dikompilasi
```

Semua terlihat berbeda tapi konsep yang mendasari sama: nilai yang tracked, perubahan yang otomatis propagate ke subscriber.

---

## Kenapa Signals Lebih Efisien dari VDOM

Perbedaan fundamental: VDOM melakukan *reconciliation top-down* — mulai dari root, bandingkan seluruh tree. Signals melakukan *update bottom-up* — hanya bagian yang bergantung pada nilai yang berubah yang diupdate.

```
VDOM:
State berubah → Jalankan fungsi render komponen → Buat VDOM baru →
Diff dengan yang lama → Hitung patch minimal → Update DOM

Signals (fine-grained):
Nilai berubah → Langsung update hanya binding yang bergantung → Done
```

Untuk komponen yang sering dirender dengan banyak state, fine-grained signals bisa jauh lebih efisien. Tapi ini bukan gratis — signals membutuhkan dependency tracking yang lebih sophisticated, dan ini punya overhead sendiri untuk aplikasi yang sangat jarang berubah.

---

## Computed Values dan Effects

Signals biasanya hadir bersama dua primitif lain:

**Computed/Derived values** — nilai yang diturunkan dari signals lain, lazily evaluated dan cached:

```javascript
// SolidJS
const count = createSignal(0);
const doubled = createMemo(() => count() * 2); // Hanya recompute kalau count berubah

// Vue
const count = ref(0);
const doubled = computed(() => count.value * 2);

// Angular
const count = signal(0);
const doubled = computed(() => count() * 2);
```

**Effects** — side effects yang berjalan ketika signals yang mereka bergantung pada berubah:

```javascript
// SolidJS
createEffect(() => {
  console.log('Count changed:', count());
  // Otomatis re-run ketika count berubah
});

// Vue
watchEffect(() => {
  console.log('Count changed:', count.value);
});

// Angular
effect(() => {
  console.log('Count changed:', count());
});
```

---

## Tantangan: Reaktivitas yang Terlalu Jauh

Signals powerful tapi juga bisa menciptakan masalah baru:

**Reactive loops:** Effect A mengubah Signal B, yang men-trigger Effect C, yang mengubah Signal A.

**Over-reactive code:** Kalau kamu tidak hati-hati dengan dependency tracking, efek bisa berjalan lebih sering dari yang dimaksud.

**Debugging yang lebih sulit:** Ketika ada update yang unexpected, trace ke signal yang bertanggung jawab bisa lebih sulit dari trace ke re-render komponen yang explicit.

Ini adalah masalah yang sudah lama dikenal dalam dunia reactive programming — dan setiap framework punya cara berbeda untuk menanganinya. Angular menggunakan zone-less model dengan explicit effects. Solid menggunakan ownership dan disposal. Vue punya watchEffect dengan cleanup mechanism.

---

## Signals dan Interoperabilitas

Karena setiap framework punya implementasi signals sendiri, ada masalah interoperabilitas: React signals tidak kompatibel dengan Vue signals, dsb.

**Alien Signals** dan **TC39 Signals Proposal** adalah upaya untuk standardisasi. Kalau proposal TC39 diterima, semua framework bisa menggunakan signals primitif yang sama dari platform — seperti halnya Promise menjadi standar dan menggantikan berbagai library callback-based.

---

## Signals di Aurelia

Aurelia sejak awal menggunakan ObserverLocator — mekanisme yang secara konseptual identik dengan fine-grained signals. Ketika properti class berubah, hanya binding yang bergantung padanya yang diupdate.

Perbedaannya dengan signals modern: Aurelia menggunakan getter/setter interception (tidak perlu wrap nilai dalam container khusus), sementara React/Vue/Angular menggunakan Signal object yang explicit.

```typescript
// Aurelia — properti biasa, diobservasi otomatis
export class Counter {
  count = 0; // Tidak perlu signal() atau ref()

  increment() {
    this.count++; // Hanya binding yang bergantung pada count yang update
  }
}
```

```typescript
// Angular signals
export class Counter {
  count = signal(0); // Harus explicit

  increment() {
    this.count.update(c => c + 1);
  }
}
```

Aurelia lebih "magical" tapi lebih ergonomis. Angular lebih explicit tapi lebih verbose. Trade-off yang berbeda dari filosofi yang berbeda.

---

## Pertanyaan untuk Direnungkan

1. Jika semua framework sedang konvergen ke signals, apakah ini berarti perbedaan antar framework akan semakin kecil? Atau ada dimensi lain yang akan tetap membedakan mereka?

2. TC39 Signals Proposal berpotensi menjadi standar platform. Bagaimana ini akan mempengaruhi ekosistem framework — apakah framework akan jadi lebih tipis di atas platform, atau akan tetap menambahkan layer abstraksi mereka sendiri?

3. Aurelia sudah menggunakan konsep setara signals dari awal (2015) tapi tidak mendapat credit untuk ini. Apa yang bisa kita pelajari tentang pentingnya penamaan dan branding dalam adopsi teknologi?

---

## Selanjutnya

Signals adalah primitif reaktif di runtime. Svelte mengambil pendekatan yang berbeda: reaktivitas dikompilasi, bukan runtime. Ini adalah trade-off yang fundamental.

→ [Bab 12: Compiler sebagai Framework](../bab-12/README.md)
