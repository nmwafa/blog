---
title: "Panduan Lengap Rumus Excel"
layout: post
---

Tulisan ini berisi daftar rumus Microsoft Excel yang paling penting dan sering digunakan di dunia kerja, lengkap dengan kegunaan, sintaks, dan contoh kasus praktisnya.


---

## 1. Fungsi Matematika & Statistik Dasar (Paling Sering Digunakan)

Ini adalah rumus wajib yang pasti digunakan oleh hampir semua pengguna Excel untuk kalkulasi dasar.

### **SUM**
* **Kegunaan:** Menjumlahkan angka dalam rentang sel tertentu.
* **Sintaks:** `=SUM(number1, [number2], ...)`
* **Contoh Praktis:** Menghitung total pendapatan bulan ini dari baris 2 sampai 10.
  ```excel
  =SUM(B2:B10)
  ```

### **AVERAGE**
* **Kegunaan:** Menghitung nilai rata-rata dari rentang sel.
* **Sintaks:** `=AVERAGE(number1, [number2], ...)`
* **Contoh Praktis:** Menghitung rata-rata nilai ujian siswa.
  ```excel
  =AVERAGE(C2:C20)
  ```

### **COUNT & COUNTA**
* **Kegunaan:** `COUNT` menghitung jumlah sel yang berisi *angka*. `COUNTA` menghitung jumlah sel yang *tidak kosong* (berisi angka, teks, simbol, dll).
* **Sintaks:** `=COUNT(value1, ...)` / `=COUNTA(value1, ...)`
* **Contoh Praktis:** 
  Menghitung jumlah karyawan yang sudah memiliki ID Karyawan (berupa angka).
  ```excel
  =COUNT(A2:A50)
  ```
  Menghitung total karyawan yang hadir berdasarkan daftar absensi teks.
  ```excel
  =COUNTA(B2:B50)
  ```

### **MIN & MAX**
* **Kegunaan:** Mencari nilai terendah (`MIN`) atau tertinggi (`MAX`) dari sekumpulan data.
* **Sintaks:** `=MIN(number1, ...)` / `=MAX(number1, ...)`
* **Contoh Praktis:** Mencari penjualan tertinggi dan terendah bulan ini.
  ```excel
  Tertinggi: =MAX(D2:D100)
  Terendah: =MIN(D2:D100)
  ```

---

## 2. Fungsi Logika (Wajib untuk Analisis Data)

Fungsi logika digunakan untuk membuat keputusan otomatis berdasarkan kondisi atau kriteria tertentu.

### **IF**
* **Kegunaan:** Mengembalikan satu nilai jika kondisi benar (TRUE) dan nilai lain jika salah (FALSE).
* **Sintaks:** `=IF(logical_test, value_if_true, value_if_false)`
* **Contoh Praktis:** Menentukan kelulusan siswa (Lulus jika nilai >= 75).
  ```excel
  =IF(C2>=75, "Lulus", "Remedial")
  ```

### **IFERROR**
* **Kegunaan:** Mengganti pesan error Excel (seperti `#N/A`, `#DIV/0!`) dengan teks atau nilai yang kita tentukan.
* **Sintaks:** `=IFERROR(value, value_if_error)`
* **Contoh Praktis:** Mencegah error saat pembagian jika jumlah pembagi adalah 0.
  ```excel
  =IFERROR(A2/B2, 0)
  ```

### **AND & OR**
* **Kegunaan:** Digunakan bersama `IF` untuk menguji beberapa kondisi. `AND` (semua kondisi harus benar), `OR` (salah satu kondisi benar sudah cukup).
* **Sintaks:** `=AND(logical1, logical2, ...)` / `=OR(logical1, logical2, ...)`
* **Contoh Praktis:** Bonus diberikan jika Penjualan > 100 **DAN** Absensi = "Penuh".
  ```excel
  =IF(AND(B2>100, C2="Penuh"), "Dapat Bonus", "Tidak Dapat Bonus")
  ```

---

## 3. Fungsi Pencarian & Referensi (Lookup)

Digunakan untuk mencari data dari tabel lain. Ini adalah keahlian utama untuk admin, HR, dan data analyst.

### **VLOOKUP**
* **Kegunaan:** Mencari data secara vertikal dari tabel referensi berdasarkan nilai kunci.
* **Sintaks:** `=VLOOKUP(lookup_value, table_array, col_index_num, [range_lookup])`
* **Contoh Praktis:** Mencari Gaji Karyawan (kolom ke-3) di tabel referensi berdasarkan NIK (A2).
  ```excel
  =VLOOKUP(A2, DataGaji!A:C, 3, FALSE)
  ```

### **HLOOKUP**
* **Kegunaan:** Sama seperti VLOOKUP, namun mencari data secara horizontal (baris).
* **Sintaks:** `=HLOOKUP(lookup_value, table_array, row_index_num, [range_lookup])`
* **Contoh Praktis:** Mencari tarif pajak berdasarkan golongan yang tersusun mendatar.
  ```excel
  =HLOOKUP(A2, TabelPajak!A1:E3, 2, FALSE)
  ```

### **XLOOKUP (Versi Modern dari VLOOKUP)**
* **Kegunaan:** Pencarian tingkat lanjut (bisa ke kiri, bisa menentukan teks jika tidak ditemukan tanpa IFERROR). *Catatan: Hanya tersedia di Excel 2021 & Microsoft 365.*
* **Sintaks:** `=XLOOKUP(lookup_value, lookup_array, return_array, [if_not_found])`
* **Contoh Praktis:** Mencari Nama Barang berdasarkan Kode Barang.
  ```excel
  =XLOOKUP(A2, KodeBarang!A:A, NamaBarang!B:B, "Barang Tidak Ada")
  ```

### **INDEX & MATCH**
* **Kegunaan:** Alternatif VLOOKUP yang lebih dinamis (bisa mencari ke arah kiri dan tidak berat memorinya).
* **Sintaks:** `=INDEX(return_range, MATCH(lookup_value, lookup_range, 0))`
* **Contoh Praktis:** Mencari Divisi (kolom A) berdasarkan Nama (kolom C). VLOOKUP tidak bisa melakukan ini secara langsung.
  ```excel
  =INDEX(A:A, MATCH(C2, C:C, 0))
  ```

---

## 4. Fungsi Kalkulasi Bersyarat (Conditional Math)

Sangat penting untuk merekap laporan berdasarkan kategori tertentu.

### **SUMIF & SUMIFS**
* **Kegunaan:** Menjumlahkan data berdasarkan satu kriteria (`SUMIF`) atau banyak kriteria (`SUMIFS`).
* **Sintaks:** 
  `=SUMIF(range, criteria, [sum_range])`
  `=SUMIFS(sum_range, criteria_range1, criteria1, ...)`
* **Contoh Praktis:** 
  Merekap total penjualan produk "Laptop".
  ```excel
  =SUMIF(A:A, "Laptop", B:B)
  ```
  Merekap total penjualan "Laptop" di bulan "Januari".
  ```excel
  =SUMIFS(C:C, A:A, "Laptop", B:B, "Januari")
  ```

### **COUNTIF & COUNTIFS**
* **Kegunaan:** Menghitung jumlah sel berdasarkan kriteria tertentu.
* **Sintaks:** Sama seperti SUMIF/SUMIFS.
* **Contoh Praktis:** Menghitung berapa kali karyawan bernama "Budi" mengambil Cuti.
  ```excel
  =COUNTIF(B:B, "Budi")
  ```

---

## 5. Fungsi Manipulasi Teks (Data Cleaning)

Sering digunakan untuk merapikan data berantakan dari sistem (database).

### **CONCATENATE / CONCAT / Simbol &**
* **Kegunaan:** Menggabungkan teks dari beberapa sel menjadi satu.
* **Sintaks:** `=CONCAT(text1, text2)` atau `A1 & " " & B1`
* **Contoh Praktis:** Menggabungkan Nama Depan (A2) dan Nama Belakang (B2).
  ```excel
  =A2 & " " & B2
  ```

### **LEFT, MID, RIGHT**
* **Kegunaan:** Mengambil sejumlah karakter dari teks. `LEFT` (dari kiri), `RIGHT` (dari kanan), `MID` (dari tengah).
* **Contoh Praktis:** Mengambil 3 huruf pertama dari Kode Transaksi (misal: "INV-001" menjadi "INV").
  ```excel
  =LEFT(A2, 3)
  ```

### **TRIM**
* **Kegunaan:** Menghapus spasi ganda dan spasi di awal/akhir teks yang tidak perlu.
* **Contoh Praktis:** Merapikan data nama yang diketik asal-asalan "   Budi Santoso  ".
  ```excel
  =TRIM(A2)
  ```

### **UPPER, LOWER, PROPER**
* **Kegunaan:** Mengubah besar-kecilnya huruf. `UPPER` (KAPITAL SEMUA), `LOWER` (kecil semua), `PROPER` (Huruf Besar Di Awal Kata).
* **Contoh Praktis:** Membuat format nama seragam (Title Case).
  ```excel
  =PROPER(A2)
  ```

---

## 6. Fungsi Tanggal dan Waktu

Sangat berguna untuk administrasi HR, manajemen proyek, dan finance.

### **TODAY & NOW**
* **Kegunaan:** Menampilkan tanggal hari ini (`TODAY`) dan waktu saat ini (`NOW`). Fungsi ini bersifat dinamis (berubah tiap hari).
* **Sintaks:** `=TODAY()` / `=NOW()`

### **DATEDIF**
* **Kegunaan:** Menghitung selisih antara dua tanggal (dalam tahun, bulan, atau hari).
* **Sintaks:** `=DATEDIF(start_date, end_date, unit)` (Unit: "Y", "M", "D")
* **Contoh Praktis:** Menghitung umur karyawan atau masa kerja dalam tahun.
  ```excel
  =DATEDIF(B2, TODAY(), "Y")
  ```

### **EOMONTH**
* **Kegunaan:** Menghasilkan tanggal akhir bulan dari suatu tanggal tertentu.
* **Contoh Praktis:** Menentukan tanggal jatuh tempo tagihan di akhir bulan ini.
  ```excel
  =EOMONTH(TODAY(), 0)
  ```
  *(Angka 0 berarti bulan ini, 1 berarti akhir bulan depan).*
