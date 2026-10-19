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

- **Sumber:** ulasan Livin’ by Mandiri di Google Play Store, dikumpulkan dengan scraping (**[ISI library, misal google-play-scraper]**).
- **Jumlah:** 15.400 ulasan mentah.
- **Pelabelan otomatis dari rating:** bintang 1–2 diberi label negatif, bintang 5 positif. Ulasan bintang 3–4 dibuang karena sentimennya ambigu.
- **Data akhir:** 6.119 ulasan.
- **Kelas timpang:** ulasan negatif sekitar 78%. Untuk mengatasinya dipakai `class_weight='balanced'`.
- **Catatan data:** label dihasilkan otomatis dari rating, bukan dari anotasi manual, jadi ada ulasan yang labelnya tidak tepat (misalnya bintang rendah tetapi isinya netral).
- **[ISI: sebutkan apakah dataset disertakan di repo. Jika ya, nama pengguna sudah dihapus. Jika tidak, tulis cara menjalankan ulang scraping.]**

## Pendekatan yang dicoba dan tidak dipakai

- **Tiga kelas dari rating** (negatif, netral, positif): akurasi hanya sekitar 0,53, karena rating 3–4 tidak selalu sejalan dengan isi ulasan.
- **Pelabelan dengan lexicon:** hasilnya hanya cocok 73,6% dengan ulasan berating ekstrem, sehingga tidak cukup andal.
- **Model pretrained:** terlalu lambat dijalankan di Google Colab.

## Alur kerja

1. Scraping ulasan.
2. Pelabelan dari rating dan pembuangan rating 3–4.
3. Pra-pemrosesan teks (**[ISI langkahnya, misal: case folding, pembersihan, normalisasi kata, stopword, stemming]**).
4. Representasi teks: TF-IDF (dan TF-IDF n-gram) untuk model klasik, tokenisasi dan embedding untuk LSTM.
5. Pelatihan dan pengujian tiga skema.
6. Perbandingan hasil dan inference pada contoh ulasan baru.

## Hasil dan catatan

- Pada data yang timpang, akurasi saja bisa menipu, karena model yang selalu menebak “negatif” pun sudah mendapat akurasi tinggi. ** **
- LSTM mencapai akurasi yang sebanding dengan model klasik, tetapi lebih berat dilatih dan cepat overfit, jadi kelebihannya tidak terlihat pada ukuran data ini.

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
