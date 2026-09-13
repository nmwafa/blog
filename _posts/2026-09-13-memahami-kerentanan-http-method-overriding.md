---
title: "Memahami Kerentanan HTTP Method Overriding"
layout: post
---

Setiap komunikasi dalam arsitektur web modern bertumpu pada HTTP method standar seperti `GET`, `POST`, `PUT`, dan `DELETE`. Namun, dalam praktiknya, sering kali muncul kompromi teknis demi kompatibilitas klien atau jaringan. Kompromi inilah yang melahirkan fitur **HTTP Method Overriding**—sebuah mekanisme praktis yang jika tidak dipahami dengan hati-hati dapat menjadi celah keamanan (*security hole*) yang signifikan.


### Apa Itu HTTP Method Overriding?

Secara historis, form HTML standar (`<form>`) hanya mendukung dua method utama: `GET` dan `POST`. Selain itu, banyak proxy perusahaan lawas, firewall, atau klien HTTP versi lama yang menolak request dengan verb non-standar seperti `PUT`, `PATCH`, atau `DELETE`.

Untuk memungkinkan aplikasi tetap mengusung prinsip arsitektur RESTful murni, pengembang dan framework web memperkenalkan trik: mengirim request sebagai `POST`, tetapi menyisipkan instruksi penimpa (*override*) di dalamnya.

Override umumnya dikirimkan melalui dua jalur:

* **HTTP Request Header:** seperti `X-HTTP-Method-Override`, `X-HTTP-Method`, atau `X-Method-Override`.
* **Body / Query Parameter:** seperti `_method=PUT` atau `_method=DELETE` (sering disisipkan sebagai `<input type="hidden" name="_method" value="...">`).

Ketika server menerima request tersebut, framework akan memodifikasi method internalnya dari `POST` menjadi method yang diminta sebelum request diteruskan ke handler atau controller.


### Dalam Kondisi Seperti Apa Fitur Ini Aktif di Server?

Header atau parameter override **tidak bekerja otomatis di setiap server**. Protokol HTTP dan server web statis mentah (seperti NGINX atau Apache bawaan) tidak mengubah method secara mandiri. Fitur ini hanya akan berfungsi jika server memenuhi kondisi-kondisi berikut:

1. **Framework Backend Mengaktifkan Middleware Penimpa Method**
Aplikasi backend harus secara eksplisit memuat middleware yang bertugas membaca header atau parameter override:
* **Node.js (Express.js):** Mengaktifkan pustaka seperti `method-override` (misal: `app.use(methodOverride('X-HTTP-Method-Override'))`).
* **PHP (Laravel):** Menggunakan middleware `CheckForMethodOverride` yang secara bawaan aktif di kernel HTTP.
* **Ruby on Rails:** Middleware `Rack::MethodOverride` terpasang secara default dalam request pipeline.
* **Java (Spring Boot):** Mengaktifkan bean `HiddenHttpMethodFilter`.


2. **Request Awal Berupa** `POST`
Sebagian besar framework modern membatasi logika override: **hanya request berjenis `POST` yang diizinkan untuk di-override**. Jika klien mengirim request `GET /users` dengan header `X-HTTP-Method-Override: DELETE`, server umumnya mengabaikannya demi menjaga sifat *safe* dan *idempotent* dari method `GET`.
3. **Infrastruktur Gateway Meloloskan Header Kustom**
Reverse proxy, API Gateway, atau Ingress Controller (seperti NGINX, HAProxy, atau Traefik) dikonfigurasi untuk meneruskan header non-standar ke aplikasi hulu (*upstream*) tanpa melakukan *header stripping*.
4. **Arsitektur Masih Melayani Klien Terbatas**
Server sengaja dikonfigurasi demikian untuk mendukung formulir HTML murni (tanpa fetch/AJAX) atau beroperasi di balik jaringan perusahaan yang memblokir method selain `GET`/`POST`.


### Mengapa Fitur Ini Bisa Menjadi Kerentanan?

Celah keamanan muncul akibat adanya **ketidaksinkronan interpretasi (*parsing asymmetry*)** antara lapisan penjaga (WAF/Reverse Proxy/API Gateway) dan lapisan eksekusi (Backend Framework).

```
[Attacker] 
   │ 
   │ POST /api/user/123 (Header: X-HTTP-Method-Override: DELETE)
   ▼
[WAF / API Gateway] ────► Hanya membaca "POST" (Dinilai aman / diizinkan)
   │
   ▼
[Backend Framework] ───► Membaca header override, mengeksekusi "DELETE" (Data terhapus!)

```

Skenario eksploitasi yang kerap terjadi:

* **Bypass Access Control & WAF Rules:** Gateway keamanan mungkin membatasi akses ke endpoint tertentu hanya untuk method `GET` atau memblokir method `DELETE` pada API publik. Jika WAF hanya mengevaluasi *request line* awal (`POST /endpoint HTTP/1.1`) tanpa menginspeksi header `X-HTTP-Method-Override`, penyerang dapat menyusupkan aksi terlarang.
* **Serangan CSRF Lintas Method:** Pengembang terkadang berasumsi bahwa aksi destruktif berbasis `DELETE` atau `PUT` kebal terhadap serangan Cross-Site Request Forgery (CSRF) tradisional karena form HTML biasa tidak bisa memicunya. Namun, jika backend menerima override via field form (`_method=DELETE`), skenario penyerangan CSRF via form submit kembali terbuka.
* **Cache Poisoning:** Jika reverse proxy atau CDN menyimpan cache berdasarkan kombinasi URL dan HTTP method asli tanpa memperhitungkan header override, respons hasil modifikasi data dapat tersimpan dan disajikan secara keliru ke pengguna lain.


### Contoh Skenario Serangan

Misalkan sebuah API internal membatasi akses pada resource sensitif:

```http
DELETE /api/admin/users/42 HTTP/1.1
Host: api.target.com
Authorization: Bearer <attacker_token>

--> Respons: 403 Forbidden (Ditolak oleh API Gateway)

```

Penyerang kemudian mencoba memanfaatkan celah method override:

```http
POST /api/admin/users/42 HTTP/1.1
Host: api.target.com
Authorization: Bearer <attacker_token>
X-HTTP-Method-Override: DELETE
Content-Type: application/json

{}

```

Jika gateway meloloskannya karena menganggap request tersebut sekadar `POST` biasa, backend yang memiliki middleware override aktif akan mengeksekusi fungsi hapus pengguna nomor `42` tanpa hambatan.


### Langkah Mitigasi & Pengamanan

Untuk meminimalkan risiko eksploitasi, terapkan prinsip pertahanan berlapis berikut:

* **Nonaktifkan Middleware Override Jika Tidak Relevan:** Jika aplikasi web Anda menggunakan Single Page Application (React/Vue/Angular) atau mobile app yang sudah mendukung method `PUT`/`DELETE` secara native, matikan middleware override sepenuhnya dari pipeline server.
* **Otorisasi Berbasis Aksi Nyata (Action-Based):** Jangan hanya mengandalkan filter method HTTP di gateway untuk membatasi hak akses. Validasi izin pengguna (*authorization check*) harus dilakukan langsung di level controller/handler terhadap operasi yang sebenarnya akan dijalankan.
* **Standardisasi Aturan WAF & Reverse Proxy:** Konfigurasikan WAF untuk memeriksa atau membersihkan header seperti `X-HTTP-Method-Override`, `X-Method-Override`, dan query `_method` jika arsitektur Anda tidak membutuhkannya.
* **Proteksi CSRF Universal:** Terapkan token proteksi CSRF secara ketat pada seluruh endpoint non-idempotent tanpa membedakan apakah request tiba via method asli atau via override.
