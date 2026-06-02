# 🚗 DriveEase - Aplikasi Manajemen Rental Mobil (Internal)

DriveEase adalah aplikasi manajemen rental mobil lintas platform (Android & iOS) untuk skala bisnis kecil. Aplikasi ini digunakan secara internal oleh **Admin** dan **Owner** untuk mencatat transaksi dan mengelola armada secara praktis dari handphone, tanpa perlu sistem pendaftaran mandiri oleh pelanggan.

---

## 👥 Siapa Saja Penggunanya?

### 1. Admin (Pengelola Operasional)
* **Pencatatan Sewa:** Menginput data penyewa (nama, nomor HP, foto KTP) secara manual saat ada yang datang menyewa.
* **Kelola Pengembalian:** Mencatat waktu pengembalian mobil dan menghitung denda keterlambatan jika ada.
* **Kelola Mobil:** Menambah unit mobil baru, memperbarui tarif sewa harian, atau mengubah status ketersediaan mobil.

### 2. Owner (Pemilik Bisnis)
* **Pantau Bisnis:** Melihat total pemasukan dan transaksi rental yang sedang berjalan atau sudah selesai.
* **Cek Armada:** Memantau mobil mana saja yang sedang disewa atau siap jalan secara real-time.

---

## 🌐 Database & Penyimpanan Online (Firebase)

Aplikasi ini terhubung langsung dengan **Google Firebase** untuk penyimpanan data secara cloud/online:
* **Sinkronisasi Instan (Real-time):** Ketika Admin mencatat sewa mobil baru di HP-nya, Owner bisa langsung melihat pembaruan data tersebut di HP pribadinya secara instan tanpa perlu memuat ulang (*refresh*) aplikasi.
* **Dukungan Mode Offline (Bekerja Tanpa Internet):** Jika Admin mencatat transaksi di daerah yang susah sinyal, data sewa akan tersimpan sementara di memori HP terlebih dahulu. Begitu HP terhubung kembali dengan internet, aplikasi otomatis mengirimkan (*push*) data tersebut ke Firebase tanpa perlu diinput ulang.
* **Keamanan Data:** Informasi penyewa dan riwayat pembayaran disimpan dengan aman di database cloud Firestore.
* **Penyimpanan Berkas:** Foto KTP penyewa disimpan dalam penyimpanan awan (Cloud Storage) milik Firebase yang aman dan dapat diakses kapan saja oleh Admin/Owner.

---

## ✨ Fitur Utama

* **📅 Kalender Okupansi & Ketersediaan Mobil (Sistem Seperti Hotel):**
  * Halaman **"Papan Pantau Jadwal"** berformat kalender/grid.
  * Menampilkan baris daftar mobil dan kolom tanggal sepanjang bulan berjalan.
  * **Status Warna:**
    * 🟢 **Hijau (Ready):** Mobil siap disewa pada tanggal tersebut.
    * 🔴 **Merah (Booked):** Mobil sudah terpakai (ketika diklik, akan memunculkan detail penyewa).
  * Admin dapat dengan mudah melihat tanggal tertentu (misalnya tanggal 15) untuk mencari unit mana yang masih kosong atau siapa yang sedang memakainya.

* **Pencatatan Sewa Instan & Layanan Tambahan (Add-ons):**
  * Admin bisa langsung memasukkan pesanan pelanggan secara manual.
  * Admin dapat menambahkan opsi layanan tambahan dengan mudah (misalnya centang pilihan **"Dengan Sopir"**, **"Antar-Jemput Bandara"**, atau **"BBM/Bensin"**) dan sistem akan otomatis mengakumulasikan biayanya ke dalam total tagihan.

* **Anti Bentrok Jadwal:** Sistem mendeteksi otomatis ketersediaan mobil sehingga Admin tidak bisa menginput sewa ganda untuk mobil yang sama di tanggal yang sama.

* **⛔ Peringatan Daftar Hitam (Blacklist System):**
  * Admin atau Owner dapat menandai nomor KTP/HP penyewa yang bermasalah (misalnya membawa lari unit, merusak mobil, atau pembayaran macet) agar masuk ke daftar hitam.
  * Saat Admin mencoba menginput penyewaan baru dengan nomor KTP/HP yang terdaftar di daftar hitam, aplikasi akan memunculkan peringatan merah penolakan secara instan.

* **Kalkulator Denda Otomatis:** Sistem otomatis menghitung denda keterlambatan saat Admin memproses pengembalian mobil.

* **Ringkasan Pendapatan Sederhana:** Grafik atau catatan pemasukan bulanan untuk membantu Owner memantau perkembangan bisnis.

---

## 🛠️ Cara Menjalankan Aplikasi di HP

Untuk mencoba aplikasi ini langsung di handphone Anda:

1. **Persiapan:**
   * Pastikan Anda sudah menginstal aplikasi **Expo Go** (bisa diunduh gratis di Google Play Store atau Apple App Store).
   * Pastikan komputer dan handphone Anda terhubung ke jaringan internet yang sama.

2. **Jalankan Aplikasi:**
   * Buka folder proyek di komputer Anda.
   * Jalankan perintah untuk memulai program.
   * Scan kode QR yang muncul di layar komputer menggunakan kamera handphone (atau melalui aplikasi Expo Go).
   * Aplikasi akan langsung terbuka di handphone Anda.

   saddas