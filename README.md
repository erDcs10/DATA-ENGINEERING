# DATA-ENGINEERING  
# Proyek : Sistem Prediksi dan Analisa Seputar Piala Dunia 2026 Berbasis RAG

---

## Kontributor

| Nama Lengkap                       | NIM         | Peran                |
|------------------------------------|-------------|----------------------|
| Alvina Nur Laila Anggraini         | 244311003   | Project Manager      |
| Erlangga Deanda Chandra Setya      | 244311013   | Data Scientist       |
| Muhammad Rosyid Ridlo Abdillah     | 244311020   | Data Analyst         |
| Rayyan Afif                        | 244311025   | Data Engineer        |

---

## Deskripsi Proyek  
Project ini di kembangkan untuk mengidentifikasi pola hubungan antara luas kebakaran hutan dan lahan dengan peningkatan emisi karbon (CO₂e) di Indonesia. Dengan Tujuan memahami kontribusi kebakaran hutan terhadap emisi karbon dan menentukan tingkat emisi berdasarkan luas lahan, luas kebakaran, dan jumlah emisi karbon. Selain itu, Project ini juga menganalisis perubahan luas kebakaran hutan dan emisi karbon per provinsi serta mengidentifikasi wilayah dengan tingkat emisi tinggi akibat kebakaran hutan beserta pola perubahannya setiap tahun

---

## Manfaat Data / Use Case  
- **Tujuan Proyek:** Menyediakan data terintegrasi yang menggambarkan pola hubungan antara luas kebakaran hutan dan lahan dengan peningkatan emisi karbon (CO₂e) di Indonesia.
- **Manfaat:**  
  - Menyediakan sumber data yang telah melalui proses validasi dan transformasi, sehingga siap digunakan untuk studi lanjutan.  
  - Membuka peluang bagi pengembang teknologi prediktif, seperti model machine learning untuk mitigasi bencana 
  - Hasil ETL proyek ini mendukung dashboard visualisasi sebagai upaya meningkatkan efisiensi analisis data lingkungan

---

## Serving Analisis  
Data hasil ETL disimpan dalam format PostgreSQL yang terstruktur dan dapat diakses untuk eksplorasi lanjutan menggunakan Google Colab atau visualisasi interaktif melalui Looker Studio. Penyimpanan ini memungkinkan analisis korelasi, distribusi wilayah emisi tinggi, dan pembuatan dashboard lingkungan.

## Serving Machine Learning  
Dataset bersih digunakan untuk membangun model klasifikasi tingkat emisi karbon menggunakan beberapa algoritma machine learning. Proyek ini mengimplementasikan K-Means Clustering untuk pengelompokan wilayah, serta menggunakan PCA untuk reduksi dimensi dan visualisasi. Model juga divalidasi dengan metode DBSCAN dan Gaussian Mixture Model.

---

# Pipeline
## Extract ( Pengambilan Data ) 
- **Sumber Data:**  
  - Luas Kebakaran Hutan dan Lahan  – SISKLHK
    (https://statistik.menlhk.go.id/sisklhkX/data_statistik/ppi/table7_6) 
  - Emisi Karbon (Ton CO2e) Akibat Karhutla – Kaggle  
    (https://statistik.menlhk.go.id/sisklhkX/data_statistik/ppi/table7_8) 
  - Data Luas Tutupan Lahan - Google Earth Engine
    - Dataset: `ESA/WorldCover/v100`  
    - Administrasi Provinsi: `FAO/GAUL/2015/level1`

- **Metode Pengambilan:**  
**GeoSpatial (Google Earth Engine):**  
    - Menggunakan Earth Engine API untuk menghitung luas tutupan lahan berdasarkan kelas seperti hutan, pertanian, semak belukar, dll.  
    - Proses melibatkan reduksi histogram klasifikasi citra dan konversi hasil ke hektar untuk masing-masing provinsi.  
**Web Scraping (Statistik KLHK):**  
    - Data emisi dan kebakaran diambil dari tabel HTML publik menggunakan `pandas.read_html()` dengan kata kunci “Provinsi”.  
---

## Transform ( Pembersihan & Transformasi )   
- **Pembersihan:**  
  - Menghapus kolom dan baris kosong (`dropna()`), serta mengisi missing value seperti `STATUS` → "Member State".
  - Normalisasi nama kolom agar seragam (contoh: `2018_x` → `Jumlah_Emisi_2018`)

- **Transformasi:**  
  - Menggabungkan ketiga dataset (`df_emisi`, `df_kebakaran`, `df_geo`) berdasarkan kolom `Provinsi`.
  - Menghitung `Total_Emisi` dan menambahkan label `Kategori_Emisi` berdasarkan clustering.
  - Pipeline modular memungkinkan efisiensi dan reusabilitas dalam pengolahan data.

---

## Load ( Pemindahan ke Target ) 
- **Target:**  
  - Sebuah tabel baru di dalam database pada server Aiven. Tabel ini merupakan output utama yang dapat diakses oleh layanan lain untuk melakukan analisis langsung di database.

- **Metode:**  
  - Fungsi to_sql() dari pandas digunakan untuk menulis data dari DataFrame langsung ke tabel di database PostgreSQL
  - konfigurasi fungsi to_sql() diatur dengan parameter-parameter kunci:
    - name diisi dengan yang mendefinisikan nama tabel tujuan
    - con diisi dengan variabel engine, yaitu objek koneksi dari SQLAlchemy
      yang telah dikonfigurasi sebelumnya untuk terhubung ke database Aiven
  - Data diverifikasi dengan membaca 5 baris pertama dari tabel baru tersebut menggunakan pd.read_sql() dan df.head()

---

## Arsitektur / Workflow ETL  
- **Alur Modular:**  
  - Proses ETL diringkas dalam sebuah fungsi transformasi() yang mencakup langkah-langkah membaca, membersihkan, mengisi, menggabungkan, dan mengubah data.
  -  Kode diorganisir secara sekuensial di dalam notebook Google Colab.

- **Tools yang Digunakan:**  
  - Python 3.x
  - Library: `pandas`, `numpy`, `sqlalchemy`, `matplotlib`, `seaborn`, `sklearn`, `scipy`
  - Platform: Google Colab

---

## Kode Program  
- **Struktur Kode:**  
  - Terdapat 2 notebook: satu untuk ETL, satu untuk Machine Learning.
  - Nama variabel dan fungsi deskriptif: `df_merge`, `transform_data()`, `Total_Emisi`, dll.
    
- **Machine Learning:**  
  - Model utama: K-Means Clustering (3 kategori: rendah, sedang, tinggi)
  - Visualisasi PCA untuk melihat distribusi cluster
  - Model pembanding: DBSCAN, **Gaussian Mixture Model
  - Evaluasi dengan heatmap korelasi dan scatter plot hasil PCA

- **Link Projek:** 
  - ETL Pipeline: [https://colab.research.google.com/drive/1QXWeLagmfu2rTsXHOVhHGJySGZJXSZC9?usp=sharing](https://colab.research.google.com/drive/1YwSpaGzl-FJRWDLpzKLOP7Lya2XAyNgs?usp=sharing)
  - Machine Learning: [https://colab.research.google.com/drive/1ZOKVncLMsOHem02JAikUDV3UPLFzNGO7?usp=sharing](https://colab.research.google.com/drive/1ZOKVncLMsOHem02JAikUDV3UPLFzNGO7?usp=sharing)
  - Looker :[(https://lookerstudio.google.com/reporting/e72b27de-4529-491c-9849-a7408707a18a)](https://lookerstudio.google.com/reporting/e72b27de-4529-491c-9849-a7408707a18a)

---
