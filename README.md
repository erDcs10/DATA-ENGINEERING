# DATA-ENGINEERING  
# Proyek : Sistem Prediksi dan Analisa Seputar Piala Dunia 2026 Berbasis Tanya Jawab 

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
Project ini mengambil data dari berbagai API olahraga ternama lalu diolah menjadi sebuah sistem RAG menggunakan Llama 3.2 yang di run secara lokal sebagai LLM nya.  

---

## Manfaat Data / Use Case  
- **Tujuan Proyek:** Menyediakan alat analisis dan prediksi yang akurat terkait perkembangan sepak bola menjelang Piala Dunia 2026
- **Manfaat:**  
  - Menyediakan sumber data yang telah melalui proses validasi dan transformasi, sehingga siap digunakan untuk studi lanjutan.  
  - Memberikan insight bagi para pecinta bola mengenai perkembangan sepak bola menjelang Piala Dunia 2026.
  - Hasil ETL proyek ini mendukung dashboard visualisasi sebagai dasar dari pengembangan model.

---

## Serving Analisis  
Data hasil ETL disimpan kedalam Aiven dan dapat diakses untuk eksplorasi lanjutan menggunakan Google Colab atau visualisasi interaktif melalui Looker Studio. Penyimpanan ini meningkatkan keamanan dan ketersediaan data analisis tim dan data analisis pemain.

## Serving Machine Learning  
Dataset bersih diubah ke dalam format PDF/TXT agar dapat dengan dilakukan splitting dan embedding.

---

# Pipeline
## Extract ( Pengambilan Data ) 
- **Sumber Data:**  
  - API Football  – API
    (https://www.api-football.com) 
  - FIFA Ranking – Website  
    (https://www.fifa.com/en/world-rankings) 
  - Rekor Pertandingan Tim  - Website
    (https://inside.fifa.com/api/live-world-ranking/get-match-window-matches?locale=en&gender=1&rankingType=0)
  - Transfer Market - Website
    (https://www.transfermarkt.co.id/vereins-statistik/wertvollstenationalmannschaften/marktwertetop?page=)

- **Metode Pengambilan:**  
**HTTP Request (API Football):**  
    - Menggunakan API Football untuk mengambil data pemain dan data timnas yang berlaga di piala dunia.  
**Web Scraping (Fifa Website):**  
    - Mencoba mengambil tabel ranking menggunakan WebBaseLoader yang disediakan oleh langchain.  
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
