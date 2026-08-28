---
title: "Mengenal Confusion Matrix"
layout: post
---

Pernahkah Anda bertanya-tanya, bagaimana cara kita tahu bahwa sebuah kecerdasan buatan (*Artificial Intelligence* / AI) benar-benar pintar dan tidak sekadar "hoki" saat menebak sesuatu?

Bayangkan Anda membuat sistem penyaring (*filter*) email spam. Dari 100 email yang masuk, bagaimana cara memastikan sistem Anda bekerja dengan benar tanpa membuang email penting dari bos atau keluarga ke folder spam?


Di dunia *Machine Learning*, kita menggunakan alat ukur yang bernama **Confusion Matrix** (Matriks Kebingungan). Jangan tertipu namanya—tujuannya justru membuat kita **tidak bingung** dalam menilai performa model AI kita!

---

## Apa Itu Confusion Matrix?

Secara sederhana, **Confusion Matrix adalah tabel rapor evaluasi** untuk model klasifikasi. Tabel ini membandingkan dua hal:
1. **Prediksi Model:** Apa yang ditebak oleh AI.
2. **Kenyataan Sebenarnya (*Actual*):** Apa fakta aslinya di dunia nyata.

---

## 4 Komponen Utama Confusion Matrix

Dalam klasifikasi biner (dua pilihan, misal: *Spam* vs *Bukan Spam*, atau *Sakit* vs *Sehat*), terdapat 4 kotak hasil:

| | Sebenarnya Positif (Spam) | Sebenarnya Negatif (Email Biasa) |
|---|---|---|
| **Ditebak Positif (Spam)** | **True Positive (TP)**<br>*Tepat Sasaran* | **False Positive (FP)**<br>*Salah Tuduh (Alarm Palsu)* |
| **Ditebak Negatif (Email Biasa)** | **False Negative (FN)**<br>*Kecolongan / Terlewat* | **True Negative (TN)**<br>*Tepat Mengabaikan* |

### Penjelasan Istilah dengan Bahasa Santai:

1. **True Positive (TP) - *Tebakan Tepat*:**
   - **Kenyataan:** Email Spam.
   - **Prediksi:** Spam.
   - **Hasil:** Sukses! Email sampah berhasil dibuang ke folder spam.

2. **True Negative (TN) - *Aman & Sesuai*:**
   - **Kenyataan:** Email Biasa / Penting.
   - **Prediksi:** Email Biasa.
   - **Hasil:** Bagus! Email tetap masuk ke kotak masuk utama Anda.

3. **False Positive (FP) - *Salah Tuduh / Alarm Palsu (Error Tipe I)*:**
   - **Kenyataan:** Email Biasa (Surat tawaran kerja / email penting).
   - **Prediksi:** Spam.
   - **Hasil:** Bahaya! Anda bisa kehilangan kesempatan penting karena email terbuang.

4. **False Negative (FN) - *Kecolongan (Error Tipe II)*:**
   - **Kenyataan:** Email Spam / Penipuan Phishing.
   - **Prediksi:** Email Biasa.
   - **Hasil:** Bahaya! Email penipuan lolos dan berisiko Anda klik secara tidak sengaja.

---

## Mengapa Nilai Akurasi Saja Tidak Cukup?

Banyak orang mengira nilai **Akurasi 95%** sudah pasti hebat. Faktanya, akurasi bisa sangat menipu pada data yang tidak seimbang (*imbalanced data*).

> **Contoh Kasus Medis:**
> Dari 1.000 pasien, hanya ada 5 orang yang benar-benar mengidap penyakit langka. 
> Jika model AI dibuat "pemalas" dan selalu menebak *"Semua Pasien Sehat"*, model tersebut tetap mendapatkan **Akurasi 99,5%**! 
> 
> Namun, model ini **gagal total** karena membiarkan 5 orang yang sakit pulang tanpa pengobatan (terjadi 5 *False Negative*).

Oleh karena itu, kita membutuhkan metrik turunan dari Confusion Matrix.

---

## 4 Metrik Penting dari Confusion Matrix

### 1. Akurasi (Accuracy)
Mengukur seberapa sering model menebak dengan benar secara keseluruhan.
$$\text{Accuracy} = \frac{\text{TP} + \text{TN}}{\text{TP} + \text{TN} + \text{FP} + \text{FN}}$$
*Kapan dipakai?* Ketika jumlah kelas data seimbang.

### 2. Presisi (Precision)
Mengukur dari semua yang dituduh positif oleh model, berapa banyak yang memang benar-benar positif.
$$\text{Precision} = \frac{\text{TP}}{\text{TP} + \text{FP}}$$
*Kapan penting?* Saat kita ingin meminimalkan **False Positive** (misal: sistem deteksi fraud perbankan yang tidak boleh sembarangan memblokir kartu nasabah baik-baik).

### 3. Recall / Sensitivitas
Mengukur dari semua data yang sebenarnya positif, berapa persen yang berhasil ditemukan oleh model.
$$\text{Recall} = \frac{\text{TP}}{\text{TP} + \text{FN}}$$
*Kapan penting?* Saat kita ingin meminimalkan **False Negative** (misal: deteksi kanker/penyakit, deteksi kebakaran, atau deteksi bom).

### 4. F1-Score
Rata-rata harmonik penyeimbang antara *Precision* dan *Recall*.
$$\text{F1-Score} = 2 \times \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$$
*Kapan dipakai?* Ketika Anda membutuhkan keseimbangan antara Presisi dan Recall pada data yang tidak seimbang.

---

## Ringkasan Singkat

- **Confusion Matrix** adalah tabel pemetaan performa model klasifikasi AI.
- Membantu kita melihat di mana letak kesalahan model: apakah sering **salah tuduh (FP)** atau justru sering **kecolongan (FN)**.
- Menjadi fondasi untuk menghitung metrik penting seperti **Precision, Recall, dan F1-Score**.
