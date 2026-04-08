# 📦 Database Toko Sederhana (MySQL)

Project ini berisi contoh implementasi database sederhana menggunakan:
- Table
- Trigger
- View

---

## 🧱 1. Database
```sql
CREATE DATABASE db_toko;
USE db_toko;
```

---

## 📊 2. Tabel

### Produk
```sql
CREATE TABLE produk (
    id_produk INT AUTO_INCREMENT PRIMARY KEY,
    nama_produk VARCHAR(50),
    stok INT
);
```

### Penjualan
```sql
CREATE TABLE penjualan (
    id_jual INT AUTO_INCREMENT PRIMARY KEY,
    id_produk INT,
    jumlah INT
);
```

---

## ⚡ 3. Trigger

### 1. BEFORE INSERT
```sql
CREATE TRIGGER before_insert_penjualan
BEFORE INSERT ON penjualan
FOR EACH ROW
SET NEW.jumlah = NEW.jumlah;
```
📝 Penjelasan:  
Trigger ini berjalan sebelum data penjualan ditambahkan.  
Digunakan untuk validasi (meskipun di sini hanya contoh sederhana).

---

### 2. AFTER INSERT (Kurangi stok)
```sql
CREATE TRIGGER after_insert_penjualan
AFTER INSERT ON penjualan
FOR EACH ROW
UPDATE produk 
SET stok = stok - NEW.jumlah 
WHERE id_produk = NEW.id_produk;
```
📝 Penjelasan:  
Setelah ada penjualan, stok produk otomatis berkurang sesuai jumlah yang dibeli.

---

### 3. BEFORE UPDATE
```sql
CREATE TRIGGER before_update_penjualan
BEFORE UPDATE ON penjualan
FOR EACH ROW
SET NEW.jumlah = NEW.jumlah;
```
📝 Penjelasan:  
Trigger ini berjalan sebelum data diubah.  
Biasanya dipakai untuk validasi data sebelum update.

---

### 4. AFTER UPDATE (Update stok)
```sql
CREATE TRIGGER after_update_penjualan
AFTER UPDATE ON penjualan
FOR EACH ROW
UPDATE produk 
SET stok = stok + OLD.jumlah - NEW.jumlah 
WHERE id_produk = NEW.id_produk;
```
📝 Penjelasan:  
Jika jumlah penjualan diubah, maka stok akan disesuaikan:
- dikembalikan dulu (OLD)
- lalu dikurangi lagi (NEW)

---

### 5. BEFORE DELETE (Balikin stok)
```sql
CREATE TRIGGER before_delete_penjualan
BEFORE DELETE ON penjualan
FOR EACH ROW
UPDATE produk 
SET stok = stok + OLD.jumlah 
WHERE id_produk = OLD.id_produk;
```
📝 Penjelasan:  
Jika data penjualan dihapus, maka stok produk akan dikembalikan.

---

### 6. AFTER INSERT (Log aktivitas)
```sql
CREATE TABLE log (
    keterangan VARCHAR(100)
);

CREATE TRIGGER after_insert_log
AFTER INSERT ON penjualan
FOR EACH ROW
INSERT INTO log VALUES ('data penjualan masuk');
```
📝 Penjelasan:  
Setiap ada transaksi penjualan, sistem otomatis mencatat ke tabel log.

---

## 👁️ 4. View

### 1. View Total Penjualan
```sql
CREATE VIEW view_total_penjualan AS
SELECT id_produk, SUM(jumlah) AS total
FROM penjualan
GROUP BY id_produk;
```
📝 Penjelasan:  
Menampilkan total jumlah penjualan untuk setiap produk.

---

### 2. View Stok Produk
```sql
CREATE VIEW view_stok_produk AS
SELECT nama_produk, stok FROM produk;
```
📝 Penjelasan:  
Menampilkan daftar produk beserta stok yang tersedia.

---

## 🧪 5. Testing

```sql
INSERT INTO produk (nama_produk, stok) VALUES ('Buku', 10);

INSERT INTO penjualan (id_produk, jumlah) VALUES (1, 2);
```

---

## 🎯 Hasil
- Stok otomatis berkurang
- Data penjualan masuk
- Log tercatat
- View bisa digunakan

---

## 📌 Kesimpulan
- Trigger = otomatis jalan
- View = tampilan data
