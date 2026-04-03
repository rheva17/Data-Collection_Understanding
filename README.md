# 📦 Latihan Basis Data (MySQL)

Repository ini berisi dokumentasi hasil latihan **Basis Data** menggunakan MySQL, mencakup materi dari konsep dasar hingga query lanjutan.

> **Nama:** Viona Rhema Agape
> You can check full documentation in my pages : https://rheva17.github.io/Data-Collection_Understanding/ 

---

## 📚 Daftar Materi

| Part | Topik |
|------|-------|
| [Part 1](#part-1--dasar-select) | Dasar SELECT |
| [Part 2](#part-2--distinct-alias--table-prefix) | DISTINCT, Alias & Table Prefix |
| [Part 3](#part-3--filtering-where) | Filtering dengan WHERE |
| [Part 4](#part-4--fungsi-matematika--string--agregat) | Fungsi Matematika, String & Agregat |
| [Part 5](#part-5--case-when) | CASE WHEN |
| [Part 6](#part-6--join) | JOIN |
| [Part 7](#part-7--union--subquery) | UNION & Subquery |
| [Part 8](#part-8--project-akhir--pembuatan-database) | Project Akhir – Pembuatan Database |
| [Part 9](#part-9--export-csv) | Export CSV |
| [Part 10](#part-10--group-by--having) | GROUP BY & HAVING |

---

## Part 1 — Dasar SELECT

Pengenalan struktur tabel dan perintah SELECT dasar.

**Konsep:**
- `Column` – kolom pada tabel
- `Row` – baris data
- `Data` – nilai dalam sel
- `Table` – kumpulan data terstruktur

**Query yang dipelajari:**
```sql
-- Menampilkan semua kolom
SELECT * FROM ms_produk;

-- Menampilkan kolom tertentu
SELECT nama_produk, harga FROM ms_produk;

-- Membatasi jumlah baris
SELECT nama_produk, harga FROM ms_produk LIMIT 5;
```

---

## Part 2 — DISTINCT, Alias & Table Prefix

Cara menghindari duplikasi data, memberi nama alias, dan menggunakan prefix tabel.

```sql
-- Menghilangkan duplikasi
SELECT DISTINCT nama_customer, alamat FROM ms_pelanggan;

-- Prefix nama tabel
SELECT ms_produk.kode_produk FROM ms_produk;

-- Alias kolom dengan AS
SELECT no_urut AS nomor, nama_produk AS nama FROM ms_produk;

-- Alias kolom tanpa AS
SELECT no_urut nomor, nama_produk nama FROM ms_produk;

-- Alias dengan prefix tabel
SELECT ms_produk.harga AS harga_jual FROM ms_produk;

-- Alias tabel
SELECT * FROM ms_produk t2;
SELECT nama_produk, harga FROM ms_produk t2;
```

---

## Part 3 — Filtering dengan WHERE

Menyaring data menggunakan kondisi tertentu.

```sql
-- Filter satu kondisi
SELECT * FROM ms_produk WHERE nama_produk = 'Tas Travel Organizer DQLab';

-- Filter dengan OR
SELECT * FROM ms_produk
WHERE nama_produk = 'Flashdisk DQLab 64 GB'
   OR nama_produk = 'Tas Travel Organizer DQLab'
   OR nama_produk = 'Gantungan Kunci DQLab';

-- Filter dengan operator perbandingan
SELECT * FROM ms_produk WHERE harga > 50000;

-- Filter dengan AND
SELECT * FROM ms_produk
WHERE nama_produk = 'Gantungan Kunci DQLab' AND harga < 50000;

-- Filter dengan kalkulasi kolom + ORDER BY
SELECT kode_pelanggan, nama_produk, qty, harga,
       (qty * harga) AS total
FROM ms_transaksi
WHERE (qty * harga) >= 100000
ORDER BY total DESC;
```

---

## Part 4 — Fungsi Matematika, String & Agregat

Penggunaan fungsi bawaan MySQL untuk mengolah data numerik dan teks.

```sql
-- Fungsi matematika: MOD dan EXP
SELECT StudentID, FirstName, LastName,
       MOD(Semester1, 2) AS Semester1,
       Semester2,
       EXP(MarkGrowth) AS EXP_MarkGrowth
FROM students;

-- Fungsi string: UPPER dan LOWER
SELECT StudentID,
       UPPER(FirstName) AS FirstName,
       LOWER(LastName) AS LastName
FROM students;

-- Fungsi agregat: MIN dan MAX
SELECT MIN(Semester1) AS Min1, MAX(Semester1) AS Max1,
       MIN(Semester2) AS Min2, MAX(Semester2) AS Max2
FROM students;
```

---

## Part 5 — CASE WHEN

Membuat logika kondisional dalam query SQL.

```sql
-- Klasifikasi total penjualan per order
SELECT orderNumber,
       SUM(quantityOrdered * priceEach) AS total,
       CASE
           WHEN SUM(quantityOrdered * priceEach) >= 50000 THEN 'Target Achieved'
           WHEN SUM(quantityOrdered * priceEach) <= 20000 THEN 'Less performed'
           ELSE 'Follow Up'
       END AS remark
FROM orderdetails
GROUP BY orderNumber;

-- Klasifikasi transaksi berdasarkan nilai total
SELECT kode_transaksi, kode_pelanggan, no_urut, kode_produk,
       nama_produk, qty,
       (qty * harga) AS total,
       CASE
           WHEN (qty * harga) > 300000  THEN 'High'
           WHEN (qty * harga) BETWEEN 100000 AND 300000 THEN 'Medium'
           ELSE 'Low'
       END AS kategori
FROM ms_transaksi
ORDER BY kode_transaksi, no_urut;
```

---

## Part 6 — JOIN

Menggabungkan data dari beberapa tabel.

```sql
-- INNER JOIN eksplisit
SELECT tr_penjualan.kode_transaksi,
       tr_penjualan.kode_pelanggan,
       ms_produk.nama_produk,
       tr_penjualan.qty,
       ms_produk.harga
FROM tr_penjualan
INNER JOIN ms_produk
    ON tr_penjualan.kode_produk = ms_produk.kode_produk;

-- INNER JOIN dengan sintaks implisit (WHERE)
SELECT ...
FROM tr_penjualan, ms_produk
WHERE tr_penjualan.kode_produk = ms_produk.kode_produk;

-- JOIN dengan kalkulasi kolom
SELECT tr_penjualan.kode_transaksi,
       ms_produk.nama_produk,
       ms_produk.harga,
       tr_penjualan.qty,
       (ms_produk.harga * tr_penjualan.qty) AS total
FROM tr_penjualan
INNER JOIN ms_produk
    ON tr_penjualan.kode_produk = ms_produk.kode_produk;
```

---

## Part 7 — UNION & Subquery

Menggabungkan hasil dari beberapa query dan menggunakan subquery.

```sql
-- UNION ALL dari dua tabel
SELECT *, qty * harga AS total FROM tabel_a WHERE kode_pelanggan = 'polibest03'
UNION ALL
SELECT *, qty * harga AS total FROM tabel_b WHERE kode_pelanggan = 'polibest03';

-- Subquery dengan IN dan JOIN
SELECT DISTINCT p.kode_pelanggan, p.nama_customer, p.alamat
FROM ms_pelanggan p
JOIN tr_penjualan t ON p.kode_pelanggan = t.kode_pelanggan
WHERE t.nama_produk IN (
    'Kotak Pensil DQLab',
    'Flashdisk DQLab 32 GB',
    'Sticky Notes DQLab 500 Sheets'
);

-- UNION ALL dengan filter berbeda per tabel
SELECT nama_produk, kode_produk, harga
FROM ms_produk_1
WHERE harga < 100000 AND kode_produk BETWEEN 'prod-01' AND 'prod-05'
UNION ALL
SELECT nama_produk, kode_produk, harga
FROM ms_produk_2
WHERE harga < 50000 AND kode_produk BETWEEN 'prod-06' AND 'prod-10';
```

---

## Part 8 — Project Akhir – Pembuatan Database

Membuat database lengkap dengan beberapa tabel yang saling berelasi.

**Tabel yang dibuat:**

| Tabel | Kolom Utama |
|-------|-------------|
| `customer` | id, name, address, gender, phone, email, dob |
| `invoice` | invoice_id, invoice_code, customer_id, invoice_date, product_id, total_price, pinalty |
| `payment` | payment_id, invoice_id, payment_amount, payment_time |
| `product` | id, product_name, price, speed_limit, date_active, end_active, active |
| `subscription` | id, customer_id, product_id, start_date, end_date, subscription_days, subscription_status |

---

## Part 9 — Export CSV

Hasil export data dari database ke format CSV.

📁 File hasil export tersedia di:  
[Google Drive – Export CSV](https://drive.google.com/drive/folders/1gP2gzO4tVRrY13brgI0d9qY5NAUILJTR?usp=sharing)

---

## Part 10 — GROUP BY & HAVING

Mengelompokkan data dan memfilter hasil agregasi.

```sql
-- GROUP BY dasar
SELECT ordernumber,
       SUM(quantityordered) AS itemscount,
       SUM(quantityordered * priceeach) AS total
FROM orderdetails
GROUP BY ordernumber;

-- GROUP BY + HAVING satu kondisi
SELECT ordernumber,
       SUM(quantityordered) AS itemscount,
       SUM(quantityordered * priceeach) AS total
FROM orderdetails
GROUP BY ordernumber
HAVING total > 1000;

-- GROUP BY + HAVING beberapa kondisi
SELECT ordernumber,
       SUM(quantityordered) AS itemscount,
       SUM(quantityordered * priceeach) AS total
FROM orderdetails
GROUP BY ordernumber
HAVING total > 1000 AND itemscount > 600;

-- GROUP BY + JOIN + WHERE + HAVING
SELECT o.ordernumber, o.status,
       SUM(od.quantityordered * od.priceeach) AS total
FROM orders o
JOIN orderdetails od ON o.ordernumber = od.ordernumber
WHERE o.status = 'Shipped'
GROUP BY o.ordernumber, o.status
HAVING total > 1500;
```

---

## 🛠️ Tools yang Digunakan

- **MySQL** – database engine
- **phpMyAdmin** – GUI untuk manajemen database
- **Command Prompt / Terminal** – menjalankan query MySQL

---

## 📌 Catatan

Repository ini dibuat sebagai dokumentasi latihan mandiri Basis Data
