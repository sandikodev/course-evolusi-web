# Bab 03 — React dan Revolusi VDOM

> *"React tidak muncul karena Jordan Walke adalah jenius yang melihat jauh ke depan. React muncul karena Facebook punya masalah spesifik yang tidak bisa diselesaikan dengan cara yang ada — dan solusi mereka kebetulan memiliki implikasi yang jauh lebih luas."*

---

Tahun 2011, Facebook menghadapi masalah engineering yang konkret: cara mengelola antarmuka yang sangat dinamis di News Feed dan Ads Manager. Data berubah dengan cepat, dari banyak sumber, dan UI harus mencerminkan perubahan itu secara real-time.

Dengan pendekatan yang ada — AngularJS atau Backbone — tim Facebook kesulitan. Two-way binding AngularJS sulit di-debug ketika data mengalir dari banyak arah sekaligus. Manual DOM manipulation Backbone terlalu verbose dan error-prone untuk UI yang kompleks.

Jordan Walke, engineer di Facebook, bereksperimen dengan ide yang terinspirasi dari XHP (PHP component system yang sudah dipakai Facebook): bagaimana kalau kita anggap UI sebagai fungsi dari state? Bagaimana kalau setiap kali state berubah, kita cukup hitung ulang seluruh UI dan terapkan hasilnya?

---

## Ide yang Terdengar Gila

Pada awalnya, ide "re-render seluruh halaman setiap kali state berubah" terdengar tidak masuk akal dari sisi performa. Developer yang terbiasa dengan optimisasi DOM manual pasti mengernyitkan dahi.

Tapi Jordan Walke dan timnya menyadari sesuatu: **kamu tidak harus benar-benar me-render ulang ke DOM asli setiap kali.** Kamu bisa me-render ke representasi JavaScript yang jauh lebih murah — apa yang kemudian disebut Virtual DOM — lalu bandingkan representasi baru dengan yang lama, dan hanya terapkan perbedaannya ke DOM nyata.

```
State berubah
    ↓
Jalankan fungsi render komponen
    ↓
Hasilkan Virtual DOM tree baru (object JavaScript di memori)
    ↓
Bandingkan dengan Virtual DOM tree sebelumnya (diffing)
    ↓
Hitung minimal set perubahan yang perlu diterapkan
    ↓
Terapkan perubahan ke DOM nyata (reconciliation)
```

Proses ini — terutama bagian diffing dan reconciliation — disebut **reconciliation** di React. Dan ini adalah trade-off yang sadar: kamu menggunakan lebih banyak memori (menyimpan dua salinan VDOM) dan CPU (untuk proses diff), tapi kamu mengurangi operasi DOM nyata yang mahal.

---

## JSX: Kontroversi yang Menjadi Konvensi

Ketika React diperkenalkan ke publik di JSConf 2013, tidak ada yang lebih kontroversial dari JSX — sintaks yang mencampur markup HTML-like dengan JavaScript.

```jsx
function UserCard({ name, email }) {
  return (
    <div className="card">
      <h3>{name}</h3>
      <p>{email}</p>
    </div>
  );
}
```

Reaksi pertama kebanyakan developer: *"Ini melanggar separation of concerns! HTML harus terpisah dari JavaScript!"*

Tapi tim React berargumen bahwa separation of concerns yang relevan bukan antara HTML dan JavaScript — melainkan antara **komponen** satu dengan yang lain. Komponen seharusnya mengandung semua yang dibutuhkan untuk merender dirinya: markup, logika, dan (kemudian) style.

JSX bukan HTML. Ini adalah syntax sugar untuk pemanggilan fungsi `React.createElement()`:

```javascript
// JSX:
<div className="card"><h3>{name}</h3></div>

// Dikompilasi menjadi:
React.createElement("div", { className: "card" },
  React.createElement("h3", null, name)
)
```

Yang artinya JSX adalah kode JavaScript biasa yang mengembalikan object (Virtual DOM node). Ini yang membuat React sangat powerful — kamu bisa menggunakan semua kekuatan JavaScript (map, filter, kondisional, dll.) langsung di dalam template.

---

## Unidirectional Data Flow

Salah satu keputusan desain React yang paling berpengaruh adalah **unidirectional data flow** — data hanya mengalir satu arah: dari parent ke child via props.

```
State (di komponen atas)
    ↓ (props)
Komponen anak
    ↓ (props)
Komponen cucu
```

Ini adalah kebalikan dari two-way binding AngularJS. Kalau state perlu berubah berdasarkan aksi user di komponen anak, komponen anak memanggil callback yang diberikan parent — data mengalir ke bawah, event mengalir ke atas.

Awalnya ini terasa lebih verbose. Tapi keuntungannya besar: **data flow yang predictable**. Ketika ada bug, kamu bisa trace ke mana data berubah dengan lebih mudah. Tidak ada "data berubah entah dari mana" seperti yang sering terjadi dengan two-way binding.

---

## Flux dan Redux: State Management sebagai Masalah Tersendiri

Ketika Facebook mendeklarasikan unidirectional data flow, mereka juga memperkenalkan **Flux** — arsitektur state management yang mengikuti prinsip yang sama.

Lalu pada 2015, Dan Abramov dan Andrew Clark merilis **Redux** — implementasi Flux yang lebih sederhana dan predictable, dengan tiga prinsip:

1. **Single source of truth**: seluruh state aplikasi ada di satu object store
2. **State is read-only**: satu-satunya cara mengubah state adalah dengan dispatch action
3. **Changes are made with pure functions**: reducer adalah fungsi murni yang menerima state lama + action, mengembalikan state baru

Redux menjadi sangat populer karena satu fitur killer: **time-travel debugging**. Karena setiap perubahan state terdokumentasi sebagai action, kamu bisa mundur dan maju melalui history state aplikasi untuk debug.

Tapi Redux juga mengajarkan sesuatu yang mahal: **seberapa jauh kamu harus merestrukturisasi aplikasi untuk mendapat aliran data yang predictable.** Boilerplate Redux — action types, action creators, reducers, selectors — sangat banyak untuk use case sederhana.

---

## React Hooks: Revolusi Kedua

Pada React Conf 2018, Dan Abramov memperkenalkan **Hooks** — cara baru menggunakan state dan lifecycle features tanpa menulis class.

Sebelum Hooks:

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  render() {
    return (
      <button onClick={() => this.setState({ count: this.state.count + 1 })}>
        {this.state.count}
      </button>
    );
  }
}
```

Setelah Hooks:

```javascript
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

Hooks bukan hanya syntactic sugar. Mereka memungkinkan **reuse logika stateful** antar komponen tanpa render props atau higher-order components. Custom hooks menjadi cara yang elegan untuk mengekstrak dan berbagi logika.

Ini adalah pergeseran yang lebih dalam dari yang terlihat: React bergerak dari objek (class) ke fungsi sebagai unit komposisi utama.

---

## Dampak Jangka Panjang

React mengubah cara industri berpikir tentang beberapa hal fundamental:

**Komponen sebagai unit** — React mempopulerkan komponen sebagai cara mengorganisir UI. Semua framework yang datang sesudahnya (Vue, Svelte, Angular 2+) mengadopsi model komponen.

**Deklaratif vs imperatif** — Daripada mendeskripsikan *bagaimana* mengubah UI (imperatif, seperti jQuery), React memperkenalkan cara mendeskripsikan *apa* yang seharusnya UI tampilkan berdasarkan state (deklaratif).

**Ekosistem npm** — React mendorong kultur "gunakan banyak package kecil yang melakukan satu hal" daripada "gunakan satu framework yang melakukan segalanya". Ini berkontribusi pada eksplosif pertumbuhan ekosistem npm.

**Mindset "JavaScript dulu"** — React membalik paradigma: alih-alih HTML yang diperkaya dengan JavaScript (AngularJS), React adalah JavaScript yang mengeluarkan HTML. Ini adalah pergeseran filosofis yang masih menjadi perdebatan hari ini.

---

## Yang React Tidak Selesaikan

React berhasil memecahkan masalah state management yang kompleks dan rendering yang predictable. Tapi ia membawa masalah baru atau meninggalkan masalah lama:

**Bundle size**: React core tidak besar, tapi ekosistem yang dibutuhkan untuk aplikasi production (React, ReactDOM, state management, routing, dll.) bisa mencapai ratusan KB.

**Hydration**: Ketika React dirender di server, browser harus mengunduh seluruh bundle JavaScript lagi untuk "menghidupkan" HTML yang sudah ada. Proses ini — hydration — mahal dan menciptakan window waktu di mana halaman terlihat sudah jadi tapi belum interaktif.

**Server-side rendering**: React awalnya murni client-side. Menambahkan SSR tidak trivial dan membutuhkan setup yang rumit (atau menggunakan Next.js yang menjadi abstraksi di atasnya).

**Re-render overhead**: Meskipun VDOM mengurangi DOM operation yang mahal, proses diff itu sendiri memiliki cost. Untuk komponen dengan data yang sangat sering berubah, ini bisa menjadi bottleneck.

Masalah-masalah inilah yang mendorong lahirnya pendekatan berikutnya.

---

## Pertanyaan untuk Direnungkan

1. React memilih VDOM sebagai trade-off antara developer experience dan performa. Apakah trade-off ini masih valid pada tahun 2026, atau sudah ada alternatif yang memberikan DX yang sama dengan overhead lebih kecil?

2. Flux dan Redux memperkenalkan boilerplate yang signifikan dengan argumen bahwa state management yang predictable lebih penting dari kesederhanaan kode. Kapan trade-off ini worth it, dan kapan overhead-nya tidak sebanding?

3. React mempopulerkan "JavaScript dulu" sebagai paradigma — markup sebagai output JavaScript, bukan JavaScript sebagai enhancement HTML. Apakah ada sesuatu yang hilang dari pendekatan ini dibanding "HTML dulu" seperti yang diadvokasi Aurelia, Angular, atau Vue dengan SFC-nya?

---

## Selanjutnya

React tidak sendirian di dunia ini. Sementara React mendominasi, ekosistem terus menghasilkan alternatif — beberapa yang berbagi filosofi React, beberapa yang secara eksplisit bereaksi terhadapnya. Mengapa banyak framework bisa eksis bersamaan?

→ [Bab 04: Fragmentasi](../bab-04/README.md)
