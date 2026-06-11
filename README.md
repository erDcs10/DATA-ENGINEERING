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
    (https://inside.fifa.com/api/live-world-ranking/get-match-window-matches?locale=en&gender=1&rankingType=0)
  - Transfer Market - Website
    (https://www.transfermarkt.co.id/vereins-statistik/wertvollstenationalmannschaften/marktwertetop?page=)

### Metode Pengambilan

**HTTP Request (API Football):**
- Menggunakan API Football untuk mengambil data pemain dan data tim nasional yang berpartisipasi pada Piala Dunia.
- Data diperoleh dalam format JSON melalui endpoint API yang tersedia.

**Inspect Network Request & HTTP Request (FIFA Website):**
- Menggunakan Developer Tools (F12) untuk mengidentifikasi endpoint JSON yang digunakan oleh website FIFA.
- Mengambil data ranking FIFA, poin FIFA, konfederasi, serta data pertandingan setiap tim nasional menggunakan HTTP Request.
- Data yang diperoleh mencakup hasil pertandingan, jumlah gol masuk, dan jumlah gol kebobolan.

**Web Scraping (Transfermarkt):**
- Menggunakan library `requests` dan `pandas.read_html()` untuk mengambil tabel nilai pasar (market value) tim nasional dari halaman Transfermarkt.
- Data diekstrak dari beberapa halaman dan digabungkan menjadi satu dataset.

---

## Transform (Pembersihan & Transformasi)

### Pembersihan
- Menggabungkan seluruh tabel hasil scraping Transfermarkt menjadi satu DataFrame.
- Menghapus baris kosong (null rows) dari dataset hasil scraping.
- Memilih atribut yang relevan seperti nama negara, ranking FIFA, poin FIFA, konfederasi, market value, statistik gol, dan hasil pertandingan.
- Melakukan normalisasi nama negara dengan mengubah format teks menjadi huruf kecil dan menghilangkan perbedaan penulisan.
- Menyamakan nama negara antar dataset FIFA, Transfermarkt, dan daftar peserta Piala Dunia menggunakan proses mapping.

### Transformasi
- Mengubah data JSON FIFA menjadi dataset terstruktur menggunakan DataFrame.
- Menghitung total gol masuk (goal in) dan total gol kebobolan (goal out) berdasarkan riwayat pertandingan setiap tim nasional.
- Menentukan status pertandingan (Menang, Seri, atau Kalah) dari setiap pertandingan yang dimainkan.
- Mengekstrak dan membersihkan data market value dari hasil scraping Transfermarkt.
- Menstandarkan format data agar seluruh dataset dapat digabungkan berdasarkan identitas negara.
- Pipeline ETL dibuat secara modular sehingga proses ekstraksi, pembersihan, dan transformasi dapat digunakan kembali secara efisien.
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
  - Proses ETL dibagi menjadi beberapa fungsi terpisah sesuai tanggung jawab masing-masing proses.
  - Tahap Extract mengambil data dari beberapa sumber, yaitu API Football, endpoint JSON FIFA, dan website Transfermarkt.
  - Tahap Transform melakukan pembersihan data, normalisasi nama negara, perhitungan statistik pertandingan, serta standarisasi format data antar sumber.
  - Dataset yang telah dibersihkan kemudian dipersiapkan untuk proses penggabungan dan analisis lebih lanjut.
  - Kode diorganisir secara sekuensial dalam notebook Google Colab sehingga setiap tahapan ETL dapat dijalankan dan diuji secara mandiri.
    
- **Tools yang Digunakan:**  
  - Python 3.x
  - Library: `pandas`, `numpy`, `request`, `time`, `io.StringIO`
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
