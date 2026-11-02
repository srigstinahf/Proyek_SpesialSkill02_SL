# Prediksi Arah Pergerakan Saham Menggunakan Machine Learning Random Forest Classifier

## Gambaran Umum Proyek

Proyek ini bertujuan untuk membangun model *machine learning* yang dapat memprediksi arah pergerakan harga saham Bank pada hari perdagangan berikutnya, yaitu **naik (1)** atau **turun (0)**, berdasarkan pola data historis dan indikator analisis teknikal.

Tujuan utama proyek ini adalah menerapkan konsep *Supervised Learning* pada kasus klasifikasi biner, mulai dari rekayasa fitur (*feature engineering*), pembagian data secara kronologis, hingga evaluasi model, serta menilai secara jujur seberapa besar sinyal prediktif yang dapat ditangkap dari data harga historis.

Dalam proyek ini, dilakukan pembangunan dan evaluasi satu algoritma *machine learning*, yaitu Random Forest Classifier.

## Tujuan Proyek

- Mengunduh dan memahami data historis harga saham BBCA.
- Merekayasa fitur analisis teknikal (Return, Moving Average, RSI, MACD, Volatilitas, dan lainnya).
- Membuat label target arah harga hari berikutnya (naik/turun).
- Membagi data secara kronologis agar tidak terjadi kebocoran data dari masa depan (*data leakage*).
- Membangun model *machine learning* untuk memprediksi arah harga saham.
- Mengevaluasi performa model menggunakan metrik evaluasi klasifikasi dan membandingkannya dengan *baseline*.
- Mengidentifikasi fitur yang paling berpengaruh terhadap prediksi model.
- Menghasilkan insight dan rekomendasi pengembangan berdasarkan hasil analisis.

## Dataset

Data yang digunakan dalam proyek ini adalah data historis harga saham yang diunduh dari Yahoo Finance.

- **Sumber:** Yahoo Finance (melalui library `yfinance`)
- **Ticker:** `BBCA.JK`
- **Periode:** 3 tahun terakhir (15 Juni 2023 s/d 15 Juni 2026)
- **Jumlah Data Awal:** 713 hari perdagangan
- **Jumlah Data Setelah Cleaning:** 694 baris
- **Variabel Target:** `Target`

Data berisi harga OHLCV (*Open, High, Low, Close, Volume*) dengan penyesuaian *corporate actions* (`auto_adjust=True`).

Interpretasi variabel target:

| Nilai | Keterangan |
|---|---|
| 0 | Harga penutupan besok tidak lebih tinggi dari hari ini (Turun) |
| 1 | Harga penutupan besok lebih tinggi dari hari ini (Naik) |

Distribusi label pada data awal:

| Label | Jumlah Hari | Persentase |
|---|---:|---:|
| Naik (1) | 300 | 42,1% |
| Turun (0) | 413 | 57,9% |

Data diunduh secara otomatis dari internet menggunakan `yfinance`, sehingga hasil dapat berbeda jika notebook dijalankan pada tanggal lain.

## Tools dan Teknologi

Tools dan library yang digunakan dalam proyek ini:

- **Python:** Bahasa pemrograman utama.
- **yfinance:** Mengunduh data harga saham dari Yahoo Finance.
- **Pandas dan NumPy:** Pengolahan dan manipulasi data.
- **Matplotlib dan Seaborn:** Visualisasi data.
- **Scikit-learn:** Normalisasi, pembangunan model, dan evaluasi machine learning.
- **Jupyter Notebook:** Lingkungan untuk analisis dan eksperimen.

## Tahapan Pengerjaan Proyek

1. **Import Library:** Mengimpor library yang dibutuhkan untuk pengolahan data, visualisasi, dan *machine learning*.
2. **Data Acquisition:** Mengunduh data historis saham BBCA selama 3 tahun menggunakan `yfinance`.
3. **Feature Engineering:** Membuat fitur analisis teknikal dari data mentah, yaitu Return harian, Moving Average (5, 10, dan 20 hari), MA Ratio, Volatilitas 5 hari, RSI, MACD dan MACD Signal, Volume Ratio, High-Low Range, serta fitur *lag* (Return 1 dan 2 hari sebelumnya).
4. **Pembuatan Target:** Membuat label biner berdasarkan perbandingan harga penutupan besok dengan hari ini.
5. **Data Cleaning:** Menghapus 19 baris yang mengandung nilai kosong (NaN) akibat perhitungan *rolling* dan *lag*.
6. **Train-Test Split:** Membagi data secara kronologis, yaitu 80% data awal sebagai data latih (555 hari) dan 20% data akhir sebagai data uji (139 hari), tanpa pengacakan.
7. **Normalisasi Fitur:** Menggunakan `StandardScaler` yang di-*fit* hanya pada data latih untuk menghindari kebocoran data.
8. **Model Training:** Melatih model Random Forest Classifier.
9. **Model Evaluation:** Mengevaluasi model pada data uji menggunakan Accuracy, Precision, Recall, F1-Score, AUC-ROC, dan perbandingan dengan *baseline*.
10. **Visualisasi:** Membuat Confusion Matrix, Kurva ROC, Feature Importance, dan perbandingan prediksi vs aktual.
11. **Simulasi Prediksi:** Memprediksi arah harga untuk hari perdagangan berikutnya menggunakan data terakhir.

## Fitur yang Digunakan

Total terdapat **13 fitur** yang digunakan sebagai input model:

| Kelompok | Fitur |
|---|---|
| Return | `Return`, `Return_lag1`, `Return_lag2` |
| Tren | `MA5`, `MA10`, `MA20`, `MA_Ratio` |
| Momentum | `RSI`, `MACD`, `MACD_Signal` |
| Volatilitas & Volume | `Volatility_5d`, `Volume_Ratio`, `HL_Range` |

## Model Machine Learning

Algoritma yang digunakan dalam proyek ini adalah **Random Forest Classifier**, yaitu algoritma *ensemble* yang membangun banyak Decision Tree dan menggabungkan prediksinya melalui voting mayoritas.

Konfigurasi model:

| Parameter | Nilai |
|---|---|
| `n_estimators` | 100 |
| `max_depth` | 5 |
| `min_samples_split` | 20 |
| `min_samples_leaf` | 10 |
| `class_weight` | `balanced` |
| `random_state` | 42 |

## Performa Model

Evaluasi dilakukan menggunakan data uji sebanyak 139 hari yang tidak digunakan selama proses pelatihan.

| Metrik Evaluasi | Nilai |
|---|---:|
| Accuracy | 52,52% |
| AUC-ROC | 0,6415 |
| Baseline Accuracy (selalu menebak kelas mayoritas) | 61,87% |

Laporan klasifikasi per kelas:

| Kelas | Precision | Recall | F1-Score | Support |
|---|---:|---:|---:|---:|
| Turun (0) | 0,76 | 0,34 | 0,47 | 86 |
| Naik (1) | 0,44 | 0,83 | 0,57 | 53 |
| Macro Avg | 0,60 | 0,58 | 0,52 | 139 |
| Weighted Avg | 0,64 | 0,53 | 0,51 | 139 |

### Interpretasi Hasil

- **Akurasi model (52,52%) berada di bawah baseline (61,87%)**, selisihnya sekitar -9,35 poin persentase. Artinya, pada data uji, model belum mengungguli strategi sederhana yang selalu menebak kelas mayoritas (Turun).
- **AUC-ROC sebesar 0,6415** menunjukkan adanya kemampuan membedakan kelas yang lebih baik daripada tebakan acak (0,5), tetapi masih terbatas.
- Model cenderung **lebih sering memprediksi Naik**: recall kelas Naik tinggi (0,83) tetapi precision-nya rendah (0,44), sedangkan recall kelas Turun rendah (0,34). Kecenderungan ini sejalan dengan penggunaan `class_weight='balanced'` pada data latih yang kelas Naik-nya lebih sedikit.

## Feature Importance

Berdasarkan analisis Feature Importance pada model Random Forest, fitur yang paling berpengaruh terhadap prediksi adalah **Return** dengan kontribusi sebesar **11,93%**.

Nilai importance dari fitur lainnya ditampilkan pada grafik di notebook.

## Simulasi Prediksi Hari Berikutnya

Model digunakan untuk memprediksi arah harga pada hari perdagangan setelah data terakhir:

| Keterangan | Hasil |
|---|---|
| Tanggal data terakhir | 15 Juni 2026 |
| Harga penutupan terakhir | Rp 6.275 |
| Prediksi | Naik |
| Probabilitas Naik | 53,5% |
| Probabilitas Turun | 46,5% |
| Tingkat keyakinan | Rendah (53,5%) |

Probabilitas yang hanya sedikit di atas 50% menunjukkan bahwa model tidak yakin terhadap prediksinya.

## Kesimpulan

Proyek ini menunjukkan penerapan *Supervised Learning* untuk memprediksi arah pergerakan harga saham BBCA, mulai dari pengambilan data, rekayasa fitur, pembagian data kronologis, pelatihan model, evaluasi, hingga simulasi prediksi.

Model Random Forest memperoleh akurasi 52,52% dan AUC-ROC 0,6415 pada data uji. Akurasi tersebut masih di bawah baseline 61,87%, sehingga model belum menunjukkan performa yang cukup untuk digunakan sebagai alat prediksi. Fitur Return menjadi fitur dengan importance tertinggi (11,93%).

Secara keseluruhan, nilai utama proyek ini terletak pada penerapan alur kerja *machine learning* yang benar untuk data deret waktu, termasuk menghindari kebocoran data, serta evaluasi hasil yang jujur terhadap keterbatasan model.
