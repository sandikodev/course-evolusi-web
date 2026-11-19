# Bab 21 — Monolith yang Terhormat vs Distribusi yang Prematur

> *"Microservices tidak membuat sistem yang buruk menjadi baik. Mereka membuat sistem yang baik menjadi lebih scalable. Kalau kamu mulai dengan sistem yang buruk, microservices akan membuat kamu punya sistem yang buruk dan distributed."*

---

## Monolith Mendapat Reputasi Buruk yang Tidak Layak

Di diskusi arsitektur modern, "monolith" sering diucapkan seperti kata umpatan. Monolith = legacy, monolith = teknikal debt, monolith = tidak scalable.

Tapi ini adalah simplifikasi yang berbahaya. Beberapa sistem paling scalable dan maintainable di dunia dibangun sebagai monolith yang well-structured. Shopify, Basecamp, Stack Overflow — semua berjalan sebagai monolith (atau "modular monolith") pada sebagian besar sejarahnya.

Masalahnya bukan monolith. Masalahnya adalah **monolith yang tidak terstruktur** — kode yang tumbuh tanpa disiplin sampai menjadi "big ball of mud."

---

## Tiga Jenis "Monolith"

Penting untuk membedakan:

**1. Modular Monolith (baik):**
Satu deployment unit, tapi kode diorganisir dalam modul yang well-defined dengan batas yang jelas. Modulnya bisa berkomunikasi via interface, bukan langsung mengakses internal satu sama lain.

```
Single Deployment
  ├── user/ (modul user, internal hidden)
  ├── billing/ (modul billing, internal hidden)
  ├── projects/ (modul projects)
  └── shared/ (shared utilities)
```

**2. Distributed Monolith (buruk):**
Kode yang di-deploy sebagai multiple service, tapi sangat tightly coupled — service tidak bisa di-deploy tanpa koordinasi dengan service lain. Kamu dapat semua kerumitan distributed systems tanpa manfaatnya.

**3. Big Ball of Mud (sangat buruk):**
Kode yang tidak ada struktur sama sekali — semua bisa akses semua, tidak ada boundaries yang jelas. Ini yang orang maksud ketika bicara tentang "legacy monolith yang perlu diganti."

---

## Modular Monolith: Arsitektur yang Underrated

Martin Fowler dan beberapa arsitek terkemuka sudah lama mengadvokasi modular monolith sebagai starting point yang lebih baik dari microservices:

**Alasan 1: Easier to refactor**
Boundaries di dalam monolith lebih mudah diubah dari boundaries antar services. Kalau kamu salah tentukan domain boundaries (yang sering terjadi di awal), refactoring di monolith jauh lebih murah.

**Alasan 2: Simpler operations**
Satu service yang perlu di-monitor, di-debug, dan di-deploy. Tidak ada network calls antar services yang perlu dimonitor. Tidak ada distributed transaction problem.

**Alasan 3: Lower latency**
Calls antar modul di dalam monolith adalah in-process — microseconds. Calls antar microservices adalah network calls — milliseconds. Untuk use case yang highly interdependent, ini sangat signifikan.

**Alasan 4: Easier development**
Developer bisa run seluruh aplikasi di satu perintah. Tidak perlu setup banyak services dan dependencies.

---

## Kapan Distribusi Menjadi Justified

Distribusi memiliki cost yang nyata — network latency, partial failure modes, operational complexity, debugging difficulty. Ini justified hanya ketika ada benefit yang lebih besar dari cost-nya.

**Justified ketika:**

**Independent scalability yang nyata:** Bagian sistem tertentu perlu scale sangat berbeda dari bagian lain. Contoh: image processing service yang sangat CPU-intensive perlu scale sangat berbeda dari user auth service.

**Independent deployability yang perlu:** Tim yang berbeda perlu deploy bagian sistem yang berbeda dengan sangat sering, dan coupling deployment menjadi bottleneck nyata — bukan hanya teori.

**Technology isolation yang genuine:** Bagian sistem yang spesifik benar-benar butuh teknologi yang berbeda (misalnya ML inference di Python, real-time features di Go, main app di Node.js).

**Fault isolation yang critical:** Kegagalan satu bagian sistem tidak boleh mempengaruhi bagian lain yang unrelated. Misalnya: kegagalan recommendation engine tidak boleh membuat checkout gagal.

---

## Strangler Fig Pattern: Migrasi yang Pragmatis

Kalau kamu sudah punya monolith dan perlu migrate ke distribusi, Strangler Fig Pattern adalah pendekatan yang paling pragmatis:

```
Step 1: Buat façade/proxy di depan monolith
        [New façade] → [Monolith]

Step 2: Pindahkan satu domain ke service baru
        [New façade] → [Monolith - domain A]
                    → [New Service A]

Step 3: Terus pindahkan domain satu per satu
        [New façade] → [Monolith - remaining domains]
                    → [Service A]
                    → [Service B]
                    → [Service C]

Step 4: Kalau monolith kosong, retire
        [New façade] → [Service A]
                    → [Service B]
                    → [Service C]
```

Kunci: **tidak ada "big bang rewrite"**. Setiap step kecil, deliverable, dan bisa di-rollback.

---

## Pelajaran dari yang Sudah Melewatinya

Banyak perusahaan yang sudah melalui cycle microservices sudah berbagi pelajaran mereka:

**Amazon:** Pelopor microservices. Mereka melakukannya karena genuinely perlu scale independent. Tapi Jeff Bezos sendiri (via "API Mandate" memo) menekankan: "Anyone who doesn't do this will be fired" — karena tanpa mandate yang keras, tidak ada yang akan melakukannya.

**Netflix:** Juga pelopor microservices. Mereka invest sangat besar dalam tooling (Hystrix, Eureka, Zuul) karena distributed systems membutuhkan tooling yang sangat matang.

**DHH (Basecamp/Ruby on Rails creator):** Vokal dalam mengadvokasi "majestic monolith" — Basecamp tetap monolith dan berjalan sangat efisien.

**Stack Overflow:** Berjalan dengan sangat sedikit server karena kode sangat well-optimized. Monolith yang efisien.

---

## Panduan Keputusan

```
Mulai dengan: Modular monolith yang well-structured
              ↓
Ketika merasakan: Deployment coupling yang memperlambat delivery secara nyata?
              ↓ Ya
Extract: Domain yang paling independent dan paling sering berubah
              ↓
Validasi: Apakah operasi lebih mudah? Delivery lebih cepat? Worth the complexity?
              ↓ Ya
Continue: Extract domain berikutnya
              ↓ Tidak
Reconsider: Mungkin modular monolith yang lebih baik, bukan distribusi
```

---

## Pertanyaan untuk Direnungkan

1. Di project yang pernah kamu kerjakan, apakah pilihan microservices/distribusi didasarkan pada masalah nyata yang dirasakan, atau lebih kepada "ini yang dilakukan perusahaan teknologi besar" atau "ini yang lebih modern"?

2. "Distributed monolith" — microservices yang tightly coupled — adalah outcome yang sering terjadi ketika tim migrate ke microservices tanpa disiplin domain boundaries. Bagaimana kamu bisa membedakan "genuine microservices" dari "distributed monolith yang terdisguise"?

3. Kalau kamu diminta merekomendasikan arsitektur untuk startup dengan 10 developer yang baru mulai, apa rekomendasi kamu — dan bagaimana rekomendasi itu berubah kalau startup tersebut sudah berkembang ke 100 developer?

---

## Selanjutnya

Semua yang sudah dibahas di kursus ini — dari framework sampai arsitektur — adalah trade-off yang perlu dibaca dengan benar. Di Pilar 5, kita masuk ke aspek yang paling praktis: bagaimana menjadi developer yang bisa membaca ekosistem, merekomendasikan arsitektur, dan memposisikan diri dengan cerdas.

→ [Bab 22: Cara Membaca Sinyal Ekosistem](../bab-22/README.md)
