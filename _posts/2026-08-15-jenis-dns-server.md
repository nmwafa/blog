---
title: "Mengenal 5 Jenis DNS Server dan Fungsinya dalam Jaringan Internet"
layout: post
---

Setiap kali Anda mengetikkan nama domain seperti `google.com` atau `coursera.org` di peramban (browser), sistem tidak langsung menghubungkan Anda ke server tujuan melalui nama tersebut. Internet bekerja menggunakan alamat angka yang disebut **IP Address**. Di sinilah **DNS (Domain Name System)** berperan sebagai buku telepon internet yang menerjemahkan nama domain yang mudah diingat manusia menjadi IP address yang dipahami oleh mesin.

Proses penerjemahan ini melibatkan hierarki beberapa server khusus yang saling bekerja sama. Berikut adalah penjelasan mengenai **5 jenis DNS server utama** dan perannya masing-masing.

---

## 1. Caching Name Servers

**Caching Name Server** berfungsi menyimpan salinan sementara (*cache*) dari permintaan DNS yang pernah dilakukan sebelumnya.

* **Cara kerja:** Saat Anda mengunjungi sebuah situs, server ini memeriksa apakah IP address situs tersebut sudah ada di memorinya. Jika ada dan masa berlakunya (*Time to Live* / TTL) masih valid, server langsung mengembalikan jawaban tersebut tanpa perlu menanyakannya lagi ke server lain.
* **Tujuan utama:** Mengurangi beban lalu lintas jaringan (*bandwidth*) dan memangkas waktu tunggu (*latency*) bagi pengguna akhir.

---

## 2. Recursive Name Servers (DNS Recursor)

**Recursive Name Server** bertindak sebagai perantara atau "detektif" yang mencari jawaban atas nama pengguna. Server ini biasanya disediakan oleh Internet Service Provider (ISP) atau penyedia DNS publik (seperti Google 8.8.8.8 atau Cloudflare 1.1.1.1).

* **Cara kerja:** Jika data domain yang diminta tidak ditemukan di dalam cache lokal, Recursive Server akan melakukan seluruh tugas penelusuran dengan menghubungi server-server DNS lainnya secara berurutan (mulai dari Root, TLD, hingga Authoritative) sampai menemukan IP address yang dicari.
* **Peran:** Mengambil alih proses pencarian yang kompleks sehingga komputer pengguna hanya perlu menunggu hasil akhirnya.

---

## 3. Root Name Servers

**Root Name Server** merupakan tingkat pertama dalam hierarki resolusi DNS global. Server ini menjadi titik awal pencarian ketika Recursive Server tidak memiliki data domain yang diminta.

* **Cara kerja:** Root server tidak menyimpan IP address dari situs spesifik (seperti `wikipedia.org`), melainkan menyimpan informasi lokasi dari **TLD Name Servers** berdasarkan ekstensi domainnya.
* **Fakta:** Secara global, terdapat 13 alamat IP root server utama (diberi label A hingga M) yang dikelola oleh berbagai organisasi internasional, dengan ratusan salinan server fisik di seluruh dunia melalui teknologi *Anycast*.

---

## 4. TLD (Top-Level Domain) Name Servers

**TLD Name Server** mengelola informasi untuk kelompok domain dengan akhiran atau ekstensi yang sama.

* **Kategori TLD:**
* **gTLD (Generic Top-Level Domain):** seperti `.com`, `.org`, `.net`, `.edu`.
* **ccTLD (Country Code Top-Level Domain):** seperti `.id` (Indonesia), `.uk` (Inggris), `.jp` (Jepang).


* **Cara kerja:** Ketika menerima kueri dari Recursive Server, TLD Server akan mengarahkan pencarian ke server yang memegang kendali penuh atas domain tersebut, yaitu Authoritative Name Server.

---

## 5. Authoritative Name Servers

**Authoritative Name Server** adalah perhentian terakhir dalam rantai pencarian DNS. Server ini memegang catatan DNS (*DNS Records* seperti A, AAAA, MX, CNAME) resmi dan definitif untuk domain tertentu.

* **Cara kerja:** Server ini memberikan jawaban final berupa IP address aktual dari domain yang diminta kembali ke Recursive Server.
* **Kepemilikan:** Biasanya dikonfigurasi langsung oleh pemilik domain melalui penyedia hosting, registrar domain, atau layanan DNS terkelola (seperti AWS Route 53, Cloudflare, atau Namecheap).

---

## Bagaimana Kelimanya Bekerja Bersama?

Alur sederhana ketika Anda membuka suatu website:

1. **Komputer Pengguna** mengirim kueri ke **Recursive Name Server**.
2. **Recursive Server** memeriksa **Caching Name Server** lokal. Jika ada, IP langsung dikembalikan ke pengguna.
3. Jika tidak ada di cache, **Recursive Server** bertanya ke **Root Name Server**.
4. **Root Server** mengarahkan ke **TLD Name Server** (misalnya server khusus `.org`).
5. **TLD Server** mengarahkan ke **Authoritative Name Server** milik domain target.
6. **Authoritative Server** memberikan IP address asli ke **Recursive Server**.
7. **Recursive Server** menyimpan data tersebut ke dalam **Cache** untuk penggunaan mendatang, lalu mengantarkan IP address ke komputer Anda sehingga halaman web terbuka.

Hierarki lima DNS server ini memastikan proses pencarian alamat di internet berjalan cepat, terstruktur, dan terdesentralisasi ke seluruh penjuru dunia.
