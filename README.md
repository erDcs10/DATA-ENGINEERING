# DATA-ENGINEERING  
# Proyek : Sistem Prediksi dan Analisa Seputar Piala Dunia 2026 Berbasis Tanya Jawab 

---

## Kontributor

| Nama Lengkap                       | NIM         | Peran                |
|------------------------------------|-------------|----------------------|
| Alvina Nur Laila Anggraini         | 244311003   | Project Manager      |
| Erlangga Deanda Chandra Setya      | 244311013   | Data Engineer        |
| Muhammad Rosyid Ridlo Abdillah     | 244311020   | Data Engineer        |
| Rayyan Afif                        | 244311025   | Data Analysist       |

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
- Memfilter hanya data tim nasional.

### Transformasi
- Mengubah data JSON FIFA menjadi dataset terstruktur menggunakan DataFrame.
- Menghitung total gol masuk (goal in) dan total gol kebobolan (goal out) berdasarkan riwayat pertandingan setiap tim nasional.
- Menentukan status pertandingan (Menang, Seri, atau Kalah) dari setiap pertandingan yang dimainkan.
- Mengekstrak dan membersihkan data market value dari hasil scraping Transfermarkt.
- Menstandarkan format data agar seluruh dataset dapat digabungkan berdasarkan identitas negara.
- Mengonversi nama negara dari Bahasa Indonesia ke Bahasa Inggris menggunakan dictionary.
- Pipeline ETL dibuat secara modular sehingga proses ekstraksi, pembersihan, dan transformasi dapat digunakan kembali secara efisien.
---

## Load ( Pemindahan ke Target ) 
- Data akhir hasil transformasi dimuat ke dalam database MySQL yang di-hosting pada layanan cloud Aiven dengan koneksi SSL menggunakan sertifikat ca.pem untuk keamanan data.
- Koneksi dibangun menggunakan SQLAlchemy create_engine dengan konfigurasi tambahan pool_pre_ping=True dan pool_recycle=3600 untuk menjaga stabilitas koneksi.
- Terdapat dua tabel yang dimuat: data_pemain dan data_master, masing-masing bersumber dari file CSV.
  
---

## Arsitektur / Workflow ETL  
- **Alur Modular:**  
  - Proses ETL dibagi menjadi beberapa fungsi terpisah sesuai tanggung jawab masing-masing proses.
  - Tahap Extract mengambil data dari beberapa sumber, yaitu API Football, endpoint JSON FIFA, dan website Transfermarkt.
  - Tahap Transform melakukan pembersihan data, normalisasi nama negara, perhitungan statistik pertandingan, serta standarisasi format data antar sumber.
  - Dataset yang telah dibersihkan kemudian dipersiapkan untuk proses penggabungan dan analisis lebih lanjut.
  - Kode diorganisir secara sekuensial dalam notebook Google Colab sehingga setiap tahapan ETL dapat dijalankan dan diuji secara mandiri.
    
- **Tools yang Digunakan:**  
  - Python 3.14
  - Library: `pandas`, `numpy`, `request`, `time`, `io.StringIO`
  - Platform: Google Colab, VS Code
    
---

## Kode Program  
- **Struktur Proyek:**  
├── Pipeline Fifa WorldCup.ipynb         
├── ambil data pemain.ipynb     
    
- **Machine Learning:**  
  - Dataset bersih diekspor ke format PDF/TXT.
  - Dokumen di-split dan di-embed untuk digunakan sebagai basis pengetahuan.
  - Model LLM: Llama 3.2 yang dijalankan secara lokal.
  - Output: sistem tanya jawab yang mampu menjawab pertanyaan seputar analisis dan prediksi Piala Dunia 2026.

- **Link Projek:** 
  - ETL Pipeline: [https://colab.research.google.com/drive/1FMwSypo8F6wmvMd8d8Ckx-VGMnl93IB8?usp=sharing] & [https://colab.research.google.com/drive/1JUkWVrYuBowOaEg0Q2NBuDDgkevVObzd?usp=sharing]
  - Machine Learning:[https://github.com/erDcs10/WorldCupPredict]
  - Looker :[https://datastudio.google.com/reporting/30db07ec-14b8-42b7-bc82-5f761c1d1a9c]

---
