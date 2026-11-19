# Bab 07 — Browser sebagai Platform: Lebih dari Sekadar Dokumen Viewer

> *"Banyak hal yang dulu butuh framework hari ini sudah tersedia natively di browser. Developer yang tidak mengikuti evolusi platform akan terus membayar untuk dependency yang tidak diperlukan."*

---

Browser modern adalah platform yang jauh lebih powerful dari yang sering diasumsikan. Ketika developer terlalu fokus pada framework, mereka seringkali melewatkan kemampuan platform yang bisa menggantikan dependency pihak ketiga — dengan performa yang lebih baik dan bundle size yang lebih kecil.

---

## DOM: Warisan yang Terus Berkembang

Document Object Model (DOM) lahir dari era web sebagai dokumen. API-nya mencerminkan asumsi itu: `document.getElementById`, `appendChild`, `innerHTML`. Verbose, imperatif, dan tidak konsisten antar browser di awal keberadaannya.

Itulah kenapa jQuery lahir — untuk mengabstraksikan inkonsistensi browser.

Tapi DOM terus berkembang. `querySelector` dan `querySelectorAll` membawa CSS selector syntax ke DOM API. `classList` menggantikan manipulasi `className` yang awkward. `dataset` untuk data attributes. `closest()` untuk navigasi ancestor.

```javascript
// Tanpa jQuery, modern vanilla JS
document.querySelector('.card').classList.toggle('active');
document.querySelectorAll('input[required]').forEach(el => el.reportValidity());
const card = event.target.closest('[data-card-id]');
```

Banyak yang dulu butuh jQuery kini bisa dilakukan dengan clean vanilla JavaScript. Pertanyaan relevan bukan "jQuery atau React?" tapi "apakah masalah ini butuh library sama sekali?"

---

## Web APIs: Kemampuan yang Kurang Dimanfaatkan

Browser modern menyediakan ratusan Web API. Beberapa yang sering kurang dimanfaatkan:

**Intersection Observer** — deteksi kapan element masuk/keluar viewport, tanpa scroll event listener yang mahal:
```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
    }
  });
});
document.querySelectorAll('.animate').forEach(el => observer.observe(el));
```

**ResizeObserver** — reaksi terhadap perubahan ukuran element, bukan hanya window:
```javascript
const ro = new ResizeObserver(entries => {
  for (const entry of entries) {
    console.log('New size:', entry.contentRect.width, entry.contentRect.height);
  }
});
ro.observe(document.querySelector('.sidebar'));
```

**MutationObserver** — observe perubahan di DOM tree:
```javascript
const mo = new MutationObserver(mutations => {
  for (const mutation of mutations) {
    if (mutation.type === 'childList') {
      console.log('Children changed');
    }
  }
});
mo.observe(document.body, { childList: true, subtree: true });
```

**AbortController** — batalkan fetch request:
```javascript
const controller = new AbortController();
const signal = controller.signal;

fetch('/api/data', { signal })
  .then(res => res.json())
  .catch(err => {
    if (err.name === 'AbortError') console.log('Dibatalkan');
  });

// Batalkan setelah 5 detik
setTimeout(() => controller.abort(), 5000);
```

**View Transitions API** — animasi transisi antar state/halaman yang smooth, tanpa library animasi:
```javascript
document.startViewTransition(() => {
  updateDOM(); // Perubahan DOM akan dianimasikan otomatis
});
```

---

## CSS yang Sudah Sangat Berbeda

CSS modern sudah jauh dari yang kita kenal 10 tahun lalu. Banyak hal yang dulu butuh JavaScript kini bisa dilakukan murni dengan CSS:

**CSS Custom Properties (Variables):**
```css
:root {
  --color-primary: oklch(60% 0.15 240);
  --spacing-md: 1rem;
}

.button {
  background: var(--color-primary);
  padding: var(--spacing-md);
}
```

**CSS Grid dan Flexbox** menggantikan grid system dari Bootstrap. **Container Queries** memungkinkan responsivitas berdasarkan ukuran container, bukan viewport:
```css
@container (min-width: 400px) {
  .card { flex-direction: row; }
}
```

**CSS Cascade Layers** untuk manajemen specificity yang lebih predictable. **`:has()` selector** untuk logika styling yang dulu butuh JavaScript. **`@property`** untuk CSS custom properties dengan tipe dan animasi.

**Scroll-driven animations** — animasi yang dikontrol posisi scroll, tanpa JavaScript scroll listener:
```css
@keyframes fade-in { from { opacity: 0; } to { opacity: 1; } }

.section {
  animation: fade-in linear;
  animation-timeline: view();
  animation-range: entry 25% cover 50%;
}
```

---

## Web Components: Standar yang Belum Dimenangkan

Web Components adalah serangkaian standar W3C yang memungkinkan developer membuat custom HTML elements dengan enkapsulasi dan reusability:

- **Custom Elements** — mendefinisikan tag HTML kustom
- **Shadow DOM** — DOM yang terisolasi dari DOM utama
- **HTML Templates** — template yang tidak dirender sampai digunakan

```javascript
class MyButton extends HTMLElement {
  connectedCallback() {
    this.innerHTML = `
      <button>
        <slot></slot>
      </button>
    `;
  }
}

customElements.define('my-button', MyButton);
```

```html
<my-button>Klik Saya</my-button>
```

Janji Web Components adalah komponen yang bisa dipakai di framework manapun karena mereka adalah bagian dari platform itu sendiri. Ini yang dimanfaatkan Aurelia (dan Angular Elements, Lit) untuk output yang interoperable.

**Kenyataannya lebih kompleks.** Web Components API cukup verbose. Shadow DOM membuat styling dari luar menjadi sulit. Dan ekosistem Web Components masih jauh lebih kecil dari ekosistem React.

Lib seperti **Lit** mengisi gap ini: abstraksi tipis di atas Web Components standard yang membuatnya lebih ergonomis tanpa meninggalkan standar.

---

## Service Workers dan PWA

Service Workers adalah proxy JavaScript yang berjalan di background, terpisah dari halaman web:

```javascript
// sw.js — service worker
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request)
      .then(response => response || fetch(event.request))
  );
});
```

Service Workers memungkinkan:
- **Offline support** — cache assets dan API responses
- **Background sync** — sinkronisasi data saat koneksi kembali
- **Push notifications** — notifikasi bahkan saat browser tertutup

Progressive Web Apps (PWA) menggunakan Service Workers dan Web App Manifest untuk memberikan pengalaman "app-like" dari browser: installable, offline-capable, full-screen.

**Ironi:** PWA ada sejak ~2015, tapi banyak developer memilih React Native atau Flutter untuk "bikin app mobile" — padahal banyak use case bisa diselesaikan dengan PWA yang lebih murah dan lebih mudah dideploy.

---

## WebAssembly: Bahasa Lain di Browser

WebAssembly (Wasm) adalah format binary instruction yang bisa dijalankan di browser dengan kecepatan mendekati native. Bukan pengganti JavaScript — melainkan komplemen untuk use case yang butuh performa tinggi.

```
Rust/C++/Go/Python code
        ↓
    Kompilasi ke .wasm
        ↓
    Dijalankan di browser
        ↓
    Bisa berinteraksi dengan JavaScript via bindings
```

Use case nyata:
- **Figma** — rendering engine ditulis di C++, dikompilasi ke Wasm
- **AutoCAD** — porting dari desktop ke web
- **FFmpeg** — processing video di browser
- **Photopea** — editor foto berbasis web

Wasm tidak akan menggantikan JavaScript untuk kebanyakan web development. Tapi untuk bagian kode yang butuh performa tinggi (kompresi, enkripsi, computer vision, rendering 3D), Wasm adalah alat yang tepat.

---

## Browser Sebagai OS: Ke Mana Ini Menuju

**File System Access API** — akses langsung ke file sistem user (dengan permission). **WebGPU** — akses ke GPU untuk komputasi dan rendering 3D yang sebelumnya butuh native app. **WebBluetooth** dan **WebUSB** — interaksi dengan hardware. **WebXR** — augmented dan virtual reality di browser.

Browser modern bisa:
- Membaca dan menulis file lokal
- Berkomunikasi dengan perangkat Bluetooth
- Merender 3D dengan GPU secara langsung
- Menjalankan code dari bahasa lain via Wasm
- Bekerja offline dengan data lokal
- Mengirim notifikasi push

Ini adalah platform yang jauh lebih powerful dari "dokumen viewer" yang diciptakan Tim Berners-Lee. Dan gap antara "app native" dan "web app" terus menyempit.

---

## Pertanyaan untuk Direnungkan

1. Di project kamu saat ini, ada berapa dependency yang sebenarnya bisa digantikan oleh Web API native yang sudah tersedia? Apakah timmu pernah secara aktif mengaudit ini?

2. Web Components sudah ada sejak ~2014 dan belum menggantikan framework component system manapun secara signifikan. Apa yang ini katakan tentang seberapa jauh "standar web" bisa mengalahkan "ekosistem yang sudah matang"?

3. PWA bisa menggantikan banyak use case mobile app — tapi adopsinya masih jauh di bawah React Native atau Flutter. Faktor apa yang menurutmu paling berpengaruh: kemampuan teknis, persepsi developer, atau ekspektasi user?

---

## Selanjutnya

JavaScript di browser bukanlah satu-satunya JavaScript. Node.js, Deno, Bun, Cloudflare Workers — semua adalah runtime JavaScript yang berbeda dengan filosofi dan trade-off yang berbeda. Perang runtime ini bukan hanya tentang kecepatan.

→ [Bab 08: Runtime Wars](../bab-08/README.md)
