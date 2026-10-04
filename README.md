# Water Quality Classifier

Klasifikasi kelayakan air minum (*potability*) dari 9 parameter kualitas air, menggunakan **Random Forest** dan **Logistic Regression**.

## Dataset

[Water Potability](https://www.kaggle.com/datasets/adityakadiwal/water-potability) dari Kaggle (cek lisensinya di halaman dataset).

- 3.276 sampel air, 9 parameter, 1 target (`Potability`)
- Parameter: `ph`, `Hardness`, `Solids`, `Chloramines`, `Sulfate`, `Conductivity`, `Organic_carbon`, `Trihalomethanes`, `Turbidity`
- Target: `1` = layak minum, `0` = tidak layak minum
- Kelas tidak seimbang: 61% tidak layak, 39% layak
- Nilai kosong: `Sulfate` (24%), `ph` (15%), `Trihalomethanes` (5%)

## Alur Kerja

1. **Eksplorasi data:** jumlah data, proporsi kelas, nilai kosong, duplikat
2. **Pembagian data:** 80% latih, 20% uji (stratified)
3. **Visualisasi:** histogram, boxplot per kelas, heatmap korelasi
4. **Preprocessing:** nilai kosong diisi median data latih, lalu distandarisasi (`StandardScaler`)
5. **Training:** Dummy (pembanding), Logistic Regression, Random Forest
6. **Evaluasi:** akurasi, precision, recall, F1-score, ROC-AUC, confusion matrix
7. **Penanganan ketidakseimbangan kelas:** `class_weight="balanced"`
8. **Analisis fitur:** feature importance (Random Forest) dan koefisien (Logistic Regression)
9. **Perbandingan model, inference, dan kesimpulan**

## Hasil

Evaluasi pada data uji (656 sampel), kelas "layak minum" sebagai kelas positif:

| Model | Akurasi | Precision | Recall | F1-score | ROC-AUC |
|---|---|---|---|---|---|
| Dummy (pembanding) | 0,610 | 0,000 | 0,000 | 0,000 | 0,500 |
| Logistic Regression (balanced) | 0,524 | 0,415 | 0,531 | 0,466 | 0,547 |
| Random Forest (balanced) | 0,666 | 0,667 | 0,289 | 0,403 | 0,655 |

**Model terpilih: Random Forest (balanced).**

Temuan utama:

- **Akurasi saja menyesatkan.** Model Dummy yang selalu menebak "tidak layak" sudah mendapat akurasi 61%. Logistic Regression tanpa pembobotan kelas hasilnya sama persis dengan Dummy (recall 0%).
- **Pembobotan kelas diperlukan.** Dengan `class_weight="balanced"`, Logistic Regression mulai mengenali air layak minum (recall 53,1%).
- **Random Forest lebih aman.** Model ini hanya salah menyatakan 37 sampel air tidak layak sebagai layak, sedangkan Logistic Regression 192 sampel. Kesalahan jenis ini paling berbahaya pada air minum.
- **Tidak ada fitur yang dominan.** Feature importance tersebar merata dan korelasi tiap fitur dengan target sangat lemah (di bawah 0,04).

## Keterbatasan

- Performa model masih rendah: Random Forest hanya menemukan 28,9% air layak minum (recall), dan ROC-AUC Logistic Regression mendekati tebakan acak.
- Random Forest mengalami overfitting (pada pengaturan bawaan: akurasi latih 100%, akurasi uji 66,3%).
- Evaluasi memakai satu kali pembagian data, dan nilai kosong diisi dengan median.
- **Hasil prediksi tidak boleh dipakai untuk menentukan air aman diminum.** Uji laboratorium tetap diperlukan.

## Struktur Repository

```
water-quality-classifier/
├── water-quality-classifier.ipynb
├── water_potability.csv
├── requirements.txt
└── README.md
```

## Cara Menjalankan

1. Unduh atau clone repository ini.
2. Pastikan `water_potability.csv` ada di folder yang sama dengan notebook. Jika file tidak disertakan, unduh dari halaman Kaggle di atas.
3. Instal library:
   ```bash
   pip install -r requirements.txt
   ```
4. Buka `water-quality-classifier.ipynb` di Jupyter Notebook atau Google Colab, lalu jalankan semua sel secara berurutan.

## Saran Pengembangan

- Tuning hyperparameter (misalnya `min_samples_leaf` pada Random Forest) dan cross-validation
- Cara lain menangani ketidakseimbangan kelas: menggeser threshold prediksi, undersampling, atau SMOTE
- Imputasi yang lebih baik daripada median, serta algoritma lain seperti Gradient Boosting
