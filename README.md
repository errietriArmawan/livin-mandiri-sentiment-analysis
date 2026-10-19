# Analisis Sentimen Ulasan Livin’ by Mandiri

Proyek ini membandingkan tiga skema model untuk mengklasifikasikan sentimen ulasan aplikasi **Livin’ by Mandiri** di Google Play Store (positif vs negatif): Logistic Regression dan SVM dengan TF-IDF, serta LSTM dengan lapisan embedding.

## Ringkasan

| Skema | Fitur | Pembagian data (latih/uji) | Akurasi uji |
|---|---|---|---|
| Logistic Regression | TF-IDF | 80/20 | 0,8595 |
| SVM | TF-IDF n-gram | 70/30 | 0,8742 |
| LSTM | Embedding | 80/20 | 0,8709 |

Ketiga skema melewati target akurasi 85%. LSTM cepat mengalami overfitting dan tidak mengungguli model yang lebih sederhana, jadi pada data ini model klasik dengan TF-IDF sudah cukup baik.

## Data

- **Sumber:** ulasan Livin’ by Mandiri di Google Play Store, dikumpulkan dengan scraping  dengan menggunakan google-play-scraper.
- **Jumlah:** 15.400 ulasan mentah.
- **Pelabelan otomatis dari rating:** bintang 1–2 diberi label negatif, bintang 5 positif. Ulasan bintang 3–4 dibuang karena sentimennya ambigu.
- **Data akhir:** 6.119 ulasan.
- **Kelas timpang:** ulasan negatif sekitar 78%. Untuk mengatasinya dipakai `class_weight='balanced'`.
- **Catatan data:** label dihasilkan otomatis dari rating, bukan dari anotasi manual, jadi ada ulasan yang labelnya tidak tepat (misalnya bintang rendah tetapi isinya netral).
- dataset tersedia direpo dengan nama file ulasan aplikasi livin.csv

## Pendekatan yang dicoba dan tidak dipakai

- **Tiga kelas dari rating** (negatif, netral, positif): akurasi hanya sekitar 0,53, karena rating 3–4 tidak selalu sejalan dengan isi ulasan.
- **Pelabelan dengan lexicon:** hasilnya hanya cocok 73,6% dengan ulasan berating ekstrem, sehingga tidak cukup andal.
- **Model pretrained:** terlalu lambat dijalankan di Google Colab.

## Alur kerja

1. Scraping ulasan.
2. Pelabelan dari rating dan pembuangan rating 3–4.
3. Pra-pemrosesan teks [case folding,normalisasi kata, tokenisasi, stopword, stemming, pembersihan akhir(menghapus data duplikat) ]
4. Representasi teks: TF-IDF (dan TF-IDF n-gram) untuk model klasik, tokenisasi dan embedding untuk LSTM.
5. Pelatihan dan pengujian tiga skema.
6. Perbandingan hasil dan inference pada contoh ulasan baru.

## Hasil dan catatan

| Skema | Algoritma | Fitur | Split | Akurasi latih | Akurasi uji | Macro F1 uji | Selisih latih-uji |
|---|---|---|---|---|---|---|---|
| 1 | Logistic Regression | TF-IDF | 80/20 | 0,9083 | 0,8595 | 0,8097 | 0,0488 |
| 2 | SVM (LinearSVC) | TF-IDF n-gram (1,2) | 70/30 | 0,9680 | 0,8742 | 0,8216 | 0,0938 |
| 3 | LSTM | Embedding | 80/20 | 0,9591 | 0,8709 | 0,8171 | 0,0882 |

**Pembacaan hasil**

- **Akurasi vs macro F1.** Macro F1 (sekitar 0,81–0,82) lebih rendah dari akurasi uji (sekitar 0,86–0,87). Itu wajar karena kelas timpang (negatif sekitar 78%): akurasi cenderung terlihat lebih baik dari kemampuan model pada kelas positif yang lebih sedikit, sedangkan macro F1 memberi bobot sama pada kedua kelas.
- **Model terbaik.** SVM dengan TF-IDF n-gram unggul tipis di akurasi uji (0,8742) dan macro F1 (0,8216). Selisih antarmodel kecil (sekitar 1,5 poin akurasi), jadi ketiganya bisa dianggap setara pada data ini.
- **Overfitting.** Logistic Regression paling stabil (selisih latih-uji 0,0488). SVM dan LSTM punya selisih hampir dua kali lipat (sekitar 0,09), artinya keduanya lebih hafal data latih.
- **LSTM** tidak mengungguli model klasik, tetapi lebih berat dilatih dan cepat overfit.
- **Keterbatasan perbandingan.** Skema 2 memakai split 70/30, sedangkan skema lain 80/20, jadi perbandingannya tidak sepenuhnya setara. Label dihasilkan otomatis dari rating, sehingga sebagian label bisa tidak tepat.
## Struktur folder

```
livin-mandiri-sentiment-analysis
├── pelatihan_model_sentimen.ipynb
├── scraping_ulasan_livin.ipynb
├── requirements.txt
├── README.md
└── ulasan_aplikasi_livin.csv
```

## Cara menjalankan

Notebook dijalankan di Google Colab.

1. Buka `pelatihan_model_sentimen.ipynb` di Colab.
2. Pasang paket pada `requirements.txt`: `pip install -r requirements.txt`.
3. Jalankan seluruh sel dari atas ke bawah (**Restart session and run all**).

## Catatan

- Sebagian besar kode dibuat dengan bantuan AI. Pemilihan pendekatan, pengujian, dan evaluasi hasil dilakukan oleh penulis.
- Proyek ini dibuat sebagai bagian dari program Coding Camp (DBS Foundation).
