# Bab 02 — Gelombang SPA Pertama: AngularJS, Backbone, Ember

> *"Tiga framework lahir hampir bersamaan, dengan pendekatan yang sangat berbeda, untuk menjawab masalah yang sama. Ini bukan kebetulan — ini adalah tanda bahwa masalahnya sudah cukup menyakitkan untuk membutuhkan solusi, tapi belum ada yang tahu solusi yang benar."*

---

Di akhir 2000-an, rasa sakit yang sudah diuraikan di Bab 01 sudah mencapai titik kritis. Tim-tim yang membangun aplikasi web kompleks dengan jQuery murni mulai merasakan batasannya. State tersebar, tidak ada konsep komponen, tidak bisa di-test, tidak ada konvensi.

Yang menarik bukan hanya bahwa solusi muncul — tapi bahwa **tiga solusi yang sangat berbeda muncul hampir bersamaan**. Backbone.js (2010), AngularJS (2010), Ember.js (2011). Masing-masing dengan filosofi yang berbeda. Masing-masing dengan komunitas loyal yang berbeda. Masing-masing yang dianggap "benar" oleh penggunanya.

Ini adalah pelajaran penting tentang ekosistem teknologi: ketika masalah cukup besar, bukan satu solusi yang muncul — melainkan banyak, dan masing-masing membawa asumsi berbeda tentang apa yang sebenarnya masalahnya.

---

## Backbone.js: Minimal dan Fleksibel

Jeremy Ashkenas merilis Backbone.js pada Oktober 2010 dengan premis yang jelas: berikan struktur pada aplikasi JavaScript yang menggunakan jQuery, tanpa memaksakan cara melakukannya.

Backbone sangat minimal. Ia menyediakan empat konsep: `Model` (data dan logika bisnis), `Collection` (kumpulan model), `View` (menghubungkan model ke DOM), dan `Router` (URL mapping). Itu saja.

```javascript
var User = Backbone.Model.extend({
  defaults: { name: '', email: '' },
  validate: function(attrs) {
    if (!attrs.name) return "Name is required";
  }
});

var UserView = Backbone.View.extend({
  template: _.template('<p><%- name %></p>'),
  render: function() {
    this.$el.html(this.template(this.model.toJSON()));
    return this;
  }
});
```

Tidak ada two-way data binding. Tidak ada dependency injection. Tidak ada opini tentang struktur folder. Backbone memberi kerangka; developer mengisi detailnya.

**Kekuatannya:** Sangat fleksibel. Bisa diintegrasikan ke codebase yang sudah ada secara bertahap. Learning curve rendah.

**Kelemahannya:** Fleksibilitas yang terlalu besar juga berarti tidak ada konvensi. Tim yang berbeda membangun "Backbone app" yang terlihat sangat berbeda. Dan karena tidak ada two-way binding, developer harus secara manual menyinkronkan model dengan DOM — boilerplate yang banyak.

---

## AngularJS: Opinionated dan Powerful

Google merilis AngularJS pada Oktober 2010 — koinsidensi dengan Backbone, atau lebih tepatnya, keduanya menjawab kebutuhan yang sama pada waktu yang sama.

AngularJS mengambil pendekatan yang berlawanan dari Backbone: sangat opinionated. Ia membawa konsep yang belum pernah ada di dunia frontend:

**Two-way data binding:**

```html
<input ng-model="username">
<p>Halo, {{ username }}!</p>
```

Ketik di input, paragraf berubah secara real-time. Tanpa event listener manual. Tanpa DOM manipulation. Developer langsung terkesan.

**Dependency Injection** — konsep dari dunia backend (Spring, .NET) akhirnya masuk ke frontend:

```javascript
app.controller('UserCtrl', function($scope, $http, UserService) {
  UserService.getAll().then(function(users) {
    $scope.users = users;
  });
});
```

**Kelemahannya:** Semua ini tidak gratis. AngularJS bekerja dengan *dirty checking* — setiap kali ada perubahan, Angular menjalankan `$digest` loop yang memeriksa semua watcher. Ketika aplikasi besar dengan ratusan watcher, ini menjadi bottleneck performa yang serius. Dan kurva belajar sangat curam: `$scope`, `$rootScope`, directive lifecycle, `$apply`, `$watch`, transclusion.

---

## Ember.js: Convention Over Configuration

Yehuda Katz dan Tom Dale merilis Ember.js pada Desember 2011 dengan filosofi *"convention over configuration"* — seperti Ruby on Rails, tapi untuk frontend.

Ember percaya bahwa developer tidak seharusnya menghabiskan waktu memutuskan bagaimana mengorganisir kode. Framework seharusnya sudah punya jawaban untuk itu.

Ember juga menjadi salah satu framework pertama yang serius tentang **stabilitas API**: *"stability without stagnation"* — perubahan breaking bisa terjadi, tapi ada jalur migrasi yang jelas.

**Kelemahannya:** Bundle size besar untuk standar waktu itu. Dan opinionated approach yang menjadi kekuatannya juga menjadi hambatan — developer yang sudah punya cara kerja sendiri merasa dikekang.

---

## Tiga Jawaban, Satu Masalah

Yang menarik: ketiga framework ini semua sukses. Ini membuktikan bahwa tidak ada satu cara yang "benar" untuk membangun aplikasi web.

| Dimensi | Backbone | AngularJS | Ember |
|---------|----------|-----------|-------|
| Filosofi | Minimal, fleksibel | Powerful, opinionated | Convention-driven |
| Learning curve | Rendah | Tinggi | Sedang-tinggi |
| Konvensi | Hampir tidak ada | Ada tapi tidak konsisten | Sangat kuat |
| Bundle size | Kecil | Sedang | Besar |
| Cocok untuk | Tim ingin kontrol penuh | Produktivitas cepat | Framework yang "punya jawaban" |

---

## Masalah yang Belum Terselesaikan

Semua framework generasi pertama ini masih meninggalkan satu masalah besar: **bagaimana mengelola state yang kompleks secara efisien di skala besar?**

Two-way binding AngularJS menjadi mimpi buruk performa saat aplikasi tumbuh. Dan di balik semua ini ada masalah yang lebih fundamental: **rendering harus terjadi di DOM nyata, dan DOM adalah operasi yang mahal.**

Dari rasa sakit inilah React lahir.

---

## Warisan yang Relevan

**AngularJS membuktikan bahwa DI dan two-way binding bisa bekerja di frontend.** Rob Eisenberg membawa konsep ini lebih jauh ke Aurelia. Angular 2+ menginternalisasi pelajaran AngularJS.

**Backbone membuktikan bahwa minimal bisa cukup.** Filosofi "berikan struktur dasar, biarkan developer memutuskan sisanya" masih relevan — lihat Hono di backend modern, atau Preact di frontend.

**Ember membuktikan bahwa konvensi kuat adalah fitur.** Next.js, Nuxt, SvelteKit — semua meta-framework modern mengambil pelajaran ini.

---

## Pertanyaan untuk Direnungkan

1. Di proyek yang pernah kamu kerjakan, mana yang lebih kamu hargai: fleksibilitas tinggi atau konvensi kuat? Apakah jawabanmu konsisten, atau tergantung konteks?

2. AngularJS memperkenalkan DI ke frontend dan terasa revolusioner. Tapi banyak developer React tidak pernah menggunakan DI eksplisit dan tetap produktif. Apa ini katakan tentang seberapa "universal" sebuah konsep arsitektur?

3. Ketiga framework ini gagal menyelesaikan masalah performa di skala besar — tapi tetap sukses. Apa yang sebenarnya menentukan apakah teknologi diadopsi, kalau bukan menyelesaikan semua masalah?

---

## Selanjutnya

Rasa sakit dengan state management dan performa DOM yang belum terselesaikan mendorong lahirnya pendekatan yang sangat berbeda dari Facebook — yang akan mengubah cara kita semua berpikir tentang UI.

→ [Bab 03: React dan Revolusi VDOM](../bab-03/README.md)
