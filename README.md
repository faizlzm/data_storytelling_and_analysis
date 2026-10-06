# Customer & Product Performance Analysis on Superstore Dataset

Analisis data transaksi ritel Superstore dengan Python dan Tableau untuk mengidentifikasi produk dengan pergerakan tercepat dan pelanggan dengan kontribusi pendapatan tertinggi di setiap segmen pasar, sebagai dasar akurasi stok dan program loyalitas.

## Daftar Isi

- [Latar Belakang](#latar-belakang)
- [Problem Statement](#problem-statement)
- [Struktur Repositori](#struktur-repositori)
- [Dataset](#dataset)
- [Metodologi](#metodologi)
- [Temuan Utama](#temuan-utama)
- [Kesimpulan dan Rekomendasi](#kesimpulan-dan-rekomendasi)
- [Penulis](#penulis)

---

## Latar Belakang

Superstore menghadapi dilema manajemen persediaan. Jika ketersediaan barang gagal diselaraskan dengan fluktuasi traffic pelanggan, muncul dua masalah operasional:

- **Kekurangan stok (stockout):** permintaan tinggi tidak terlayani, sehingga pendapatan hilang dan pelanggan beralih ke kompetitor.
- **Penumpukan stok (overstock):** pasar sepi, modal terperangkap, dan biaya perawatan gudang membengkak.

Diskon besar-besaran untuk "cuci gudang" sering dipakai sebagai jalan pintas membersihkan overstock, tetapi berujung pada tergerusnya profitabilitas.

## Problem Statement

> Bagaimana Superstore dapat mengidentifikasi produk dengan pergerakan tercepat (Quantity) dan pelanggan dengan kontribusi pendapatan tertinggi (Total Sales) di setiap segmen pasar, guna memprioritaskan akurasi stok logistik dan merancang program loyalitas?

## Struktur Repositori

```text
customer_and_product_performance_analysis/
├── data/
│   └── sample_superstore.csv        # Dataset mentah (9.994 baris, 21 kolom)
├── notebook/
│   └── main.ipynb                   # Data understanding dan cleaning (Python)
├── dashboard/
│   └── Customer & Product Performance Analysis.twbx   # Dashboard Tableau
├── report/
│   └── Ahmad Faiz Ali Azmi - Customer & Product Performance Analysis.pdf
└── README.md
```

## Dataset

Sumber: [Superstore Dataset (Kaggle, vivek468)](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final/data). Berisi data historis operasional ritel Superstore.

- **Jumlah baris:** 9.994 transaksi
- **Jumlah kolom:** 21
- **Periode pesanan:** 3 Januari 2014 sampai 30 Desember 2017
- **Encoding file:** `windows-1252`

| Kelompok | Kolom |
| --- | --- |
| Pesanan | `Row ID`, `Order ID`, `Order Date`, `Ship Date`, `Ship Mode` |
| Pelanggan | `Customer ID`, `Customer Name`, `Segment` |
| Lokasi | `Country`, `City`, `State`, `Postal Code`, `Region` |
| Produk | `Product ID`, `Category`, `Sub-Category`, `Product Name` |
| Metrik | `Sales`, `Quantity`, `Discount`, `Profit` |

## Metodologi

### 1. Data Preparation (`notebook/main.ipynb`)

| Kategori | Temuan | Tindakan |
| --- | --- | --- |
| Missing value | Tidak ada pada seluruh 21 kolom | Tidak perlu penanganan |
| Duplikasi | 0 baris duplikat | Tidak perlu penanganan |
| Tipe data | Sebagian besar kolom bertipe string | `Order Date` dan `Ship Date` dikonversi ke datetime; `Sales`, `Quantity`, `Discount`, `Profit` ke numerik |
| Format teks | Kode pos dan spasi berlebih | Kode pos distandarkan menjadi 5 digit (`zfill(5)`), spasi berlebih dihapus (`strip`) |
| Outlier (IQR) | `Profit`: 1.881 baris, `Sales`: 1.167 baris | **Dipertahankan** karena seluruhnya merupakan transaksi valid |

Hasil akhir disimpan oleh notebook sebagai `final_superstore_cleaned.csv`.

### 2. Analisis dan Visualisasi

Analisis dilakukan di Tableau (`dashboard/`) dengan tampilan per segmen (Consumer, Corporate, Home Office):

| Visualisasi | Pertanyaan yang dijawab |
| --- | --- |
| Proporsi segmen pelanggan | Seberapa besar kontribusi tiap segmen terhadap Sales? |
| Peringkat sub-category terlaris | Produk apa yang paling cepat bergerak di tiap segmen? |
| Tren permintaan bulanan | Kapan permintaan melonjak dan menurun? |
| Rata-rata kebutuhan sub-category bulanan | Berapa target stok per sub-category dan segmen? |
| Top 10 customer per segmen | Siapa pelanggan dengan kontribusi Sales tertinggi? |

## Temuan Utama

### 1. Consumer adalah mesin volume, B2B menahan kapital padat

| Segmen | Total Sales | Porsi Sales | Jumlah pelanggan |
| --- | ---: | ---: | ---: |
| Consumer | $1.161.401 | 50,56% | 409 |
| Corporate | $706.146 | 30,74% | 236 |
| Home Office | $429.653 | 18,70% | 148 |

Consumer menyerap lebih dari 50% total barang (19.521 dari 37.873 unit). Pasar B2B (Corporate dan Home Office) bernilai besar meski volume transaksinya lebih rendah.

### 2. Binders, Paper, dan Furnishings terlaris di semua segmen

| Sub-Category | Consumer | Corporate | Home Office |
| --- | ---: | ---: | ---: |
| Binders | 3.015 | 1.848 | 1.111 |
| Paper | 2.602 | 1.555 | 1.021 |
| Furnishings | 1.834 | 1.086 | 643 |

Keseragaman ini memberi daya tawar bagi divisi supply chain untuk kontrak pembelian skala besar. Rata-rata kebutuhan bulanan Binders mencapai 63 unit di Consumer, 39 di Corporate, dan 24 di Home Office.

### 3. Permintaan melonjak serempak pada September, November, dan Desember

Total unit terjual per bulan (gabungan 2014–2017):

| Bulan | Jan | Feb | Mar | Apr | Mei | Jun | Jul | Agu | Sep | Okt | Nov | Des |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Unit | 1.475 | 1.067 | 2.564 | 2.447 | 2.791 | 2.680 | 2.705 | 2.784 | **5.062** | 3.104 | **5.775** | **5.419** |

Pola ini sinkron di ketiga segmen. Sebaliknya, Januari dan Februari adalah periode permintaan paling rendah.

### 4. Pendapatan terkonsentrasi pada segelintir pelanggan

| Segmen | Pelanggan teratas | Total Sales | Porsi 10% pelanggan teratas |
| --- | --- | ---: | ---: |
| Consumer | Raymond Buch | $15.117 | 31,6% |
| Corporate | Tamara Chand | $19.052 | 29,2% |
| Home Office | Sean Miller | $25.043 | 31,7% |

10% pelanggan teratas menyumbang 29–32% Sales di tiap segmen. Pelanggan dengan Sales tertinggi justru berasal dari segmen terkecil (Home Office), yang membuatnya paling rentan: kehilangan satu atau dua nama besar langsung terasa.

## Kesimpulan dan Rekomendasi

1. **Optimalisasi stok berbasis fluktuasi musiman.** Naikkan batas safety stock secara agresif menjelang kuartal ketiga (September) hingga akhir tahun untuk mencegah stockout. Tahan pesanan massal (purchase order) pada Januari dan Februari agar modal tidak terperangkap menjadi stok mati. Prioritaskan Binders dan Paper karena menyerap kapasitas gudang terbesar.
2. **Program loyalitas untuk pelanggan bernilai tinggi.** Hentikan strategi diskon pukul rata. Alihkan anggaran pemasaran untuk program retensi VIP bagi pelanggan Top 10 dengan penawaran eksklusif, dan pantau pola belinya.
3. **Kontrak pembelian dan distribusi terdiferensiasi.** Amankan kontrak pembelian skala besar untuk tiga kategori terlaris, lalu bedakan skema distribusi, misalnya kemasan bulk/pallet untuk Corporate dan eceran untuk Consumer.

## Penulis

**Ahmad Faiz Ali Azmi**
Lulusan Teknik Informatika, Universitas Brawijaya. Peserta Offline Bootcamp Data Analyst Batch 4 di dibimbing.id.

GitHub: [@faizlzm](https://github.com/faizlzm)
