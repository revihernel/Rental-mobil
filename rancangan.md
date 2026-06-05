# 🚗 DriveEase - Aplikasi Manajemen Rental Mobil & Motor

DriveEase adalah aplikasi manajemen rental mobil dan motor berbasis **Web Application** yang dideploy langsung ke **Firebase Hosting** (`websiterental.web.app`). Aplikasi ini dirancang dengan pendekatan **Mobile-First** (dioptimalkan sepenuhnya untuk kenyamanan layar handphone/tablet, namun tetap responsif dan rapi saat dibuka di browser komputer/laptop).

---

## 👥 Siapa Saja Penggunanya & Bagaimana Tampilannya?

### 1. 🌐 Tampilan Customer (Public Web/App)
Halaman ini bersifat publik sehingga calon penyewa bisa melihat armada yang tersedia tanpa perlu masuk (login) ke sistem.
* **Halaman Utama:**
  * **Halaman Mobil:** Menampilkan daftar katalog mobil lengkap dengan foto, spesifikasi singkat, dan harga sewa harian.
  * **Halaman Motor:** Menampilkan daftar katalog motor lengkap dengan foto, kapasitas mesin (cc), dan harga sewa harian.
  * **Halaman Layanan Lain (Info Statis):** Informasi tambahan untuk customer mengenai layanan sampingan seperti **Cuci Mobil** dan **Inap Mobil** (berupa info tarif/kontak, belum masuk ke sistem transaksi utama).
* **Fitur Cek Ketersediaan Real-Time:**
  * Customer dapat memasukkan **Tanggal Mulai** dan **Tanggal Selesai** sewa.
  * Sistem akan langsung menyaring dan menampilkan kendaraan mana saja yang **Ready (Tersedia)** untuk disewa pada rentang tanggal tersebut beserta total harganya.
  * **Filter & Urutan (Sort):** Customer dapat menyaring unit berdasarkan tipe transmisi (Manual/Matic), kapasitas penumpang, atau mengurutkan berdasarkan harga terendah ke tertinggi.
* **Layanan Tambahan (Add-ons) Mobil:**
  * Saat memilih mobil, customer dapat mencentang opsi layanan tambahan: **BBM** (Rp 100.000/hari) dan **Sopir** (Rp 100.000/hari).
  * Sistem di web secara dinamis akan langsung menjumlahkan biaya add-on tersebut ke total estimasi harga sewa sebelum diarahkan ke WhatsApp.
* **Tombol Hubungi WhatsApp Dinamis (Call-to-Action):**
  * **Jika unit tersedia:** Tombol berwarna hijau terang bertuliskan `🟢 Pesan Sekarang via WhatsApp` dengan pesan otomatis terformat rapi berisi detail sewa termasuk rincian add-on yang dipilih.
  * **Jika unit tidak tersedia:** Tombol berwarna kuning bertuliskan `🟡 Tanya Ketersediaan Unit Lain` agar customer tetap bisa menghubungi Admin, memberi ruang bagi Admin untuk menawarkan unit Rent-to-Rent (mitra).

### 2. 🔐 Tampilan Admin & Owner (Internal Dashboard)
Digunakan oleh tim operasional untuk mengelola bisnis sehari-hari dengan antarmuka yang efisien.
* **Fitur CRUD Kendaraan (Mobil & Motor):**
  * Admin dapat menambah, membaca, mengedit, dan menghapus (Create, Read, Update, Delete) data armada mobil maupun motor (foto, plat nomor, nama unit, tipe, harga sewa, status keaktifan).
  * **Media Upload Praktis:** Dilengkapi fitur unggah foto kendaraan menggunakan metode *drag-and-drop* atau mengambil foto langsung lewat kamera handphone.
* **Manajemen Booking & Validasi Anti-Bentrok:**
  * Admin dapat mencatat transaksi penyewaan dengan memasukkan nama pelanggan, nomor HP, unit kendaraan yang dipilih, serta rentang tanggal sewa.
  * **Pencegahan Double Booking:** Jika Admin lain atau sistem mendeteksi kendaraan tersebut sudah dibooking pada tanggal yang sama, sistem akan memblokir transaksi baru tersebut dan memunculkan peringatan bahwa unit sudah terpakai.
* **Fitur Kemudahan Operasional Admin:**
  * **Tombol Cepat Hubungi Penyewa:** Sediakan tombol pintasan WhatsApp di samping daftar booking aktif untuk langsung mengirim pesan konfirmasi atau penagihan ke nomor WhatsApp penyewa tanpa perlu menyalin nomor secara manual.
  * **Cetak Bukti Sewa (Invoice/Nota PDF):** Fitur sekali klik untuk mencetak kuitansi pembayaran atau bukti sewa digital (PDF) guna dikirimkan ke WhatsApp customer.
* **Kalender Okupansi (Papan Pantau Jadwal):**
  * Kalender interaktif untuk melihat status booking seluruh armada per hari/bulan secara visual.
  * Memudahkan Admin melihat tanggal-tanggal mana saja yang padat (peak season) atau melihat unit mana saja yang sedang menganggur.
* **📊 Halaman Kesimpulan Transaksi (Dashboard Analitik):**
  * Halaman khusus (terutama untuk Owner) yang menyajikan metrik performa bisnis dalam periode tertentu (Mingguan/Bulanan/Kustom):
    * **Metrik Operasional:** 
      * Total unit armada (Mobil & Motor).
      * Jumlah booking berjalan dan booking baru dalam minggu ini.
      * Jumlah pelanggan unik yang aktif melakukan transaksi.
    * **Metrik Keuangan:**
      * **Uang Masuk (Gross Revenue):** Total seluruh pendapatan dari sewa unit sendiri dan sewa unit R2R ke customer.
      * **Uang Keluar (Expenses):** Total biaya operasional (perbaikan unit, servis) dan modal bayar sewa ke rental mitra (R2R).
      * **Keuntungan Bersih (Net Profit):** Total keuntungan bersih setelah dikurangi uang keluar dan modal unit R2R.
      * **Total Piutang:** Nominal uang sewa yang statusnya belum lunas/baru bayar DP (Down Payment).
    * **Analisis Armada & R2R:**
      * **Rasio Okupansi (Occupancy Rate):** Persentase keaktifan armada (seberapa sering unit berjalan dibanding hanya parkir di garasi).
      * **Armada Terfavorit:** Grafik unit mobil dan motor yang paling sering disewa.
      * **Rasio R2R:** Jumlah unit mitra yang dipakai minggu ini (untuk menilai kapan waktu yang tepat menambah unit sendiri jika pasar sedang tinggi).
    * **Ekspor Laporan:** Tombol untuk mengunduh riwayat transaksi bulanan ke file Excel (CSV) untuk keperluan administrasi dan pencatatan kas internal.

---

## 🛡️ Sistem Proteksi & Pemisahan Akses

Untuk membedakan hak akses antara halaman Customer (umum) dan Admin (operasional), aplikasi menggunakan mekanisme berikut:

1. **Pemisahan URL/Rute (Routing Guard):**
   * **Rute Publik:** `/` (Home), `/mobil`, dan `/motor` dapat diakses langsung oleh siapapun.
   * **Rute Privat:** `/admin/*` (Dashboard, Kelola Armada, Kalender) diproteksi oleh kode aplikasi (*Router Guard*). Jika ada pengguna yang belum login mencoba mengakses halaman ini, sistem akan otomatis mengalihkan mereka ke halaman `/login`.
2. **Firebase Authentication:**
   * Login ke halaman Admin menggunakan email dan password yang terdaftar secara aman di database Firebase Auth.
3. **Aturan Keamanan Database (Firestore Security Rules):**
   * **Pengguna Umum (Customer):** Hanya diizinkan untuk membaca data (`read` only) katalog mobil dan motor yang aktif. Tidak diizinkan menambah atau mengubah data apa pun.
   * **Pengguna Terautentikasi (Admin):** Memiliki izin penuh untuk membuat, membaca, mengedit, dan menghapus data (`read`, `create`, `update`, `delete`) di semua tabel database (Armada, Booking, Laporan Keuangan, Blacklist).

---

## 🔄 Konsep Khusus: Tidak Ada "Full Book" (Rent-to-Rent / Sub-Rental)

Mengikuti strategi bisnis di mana *"tidak ada kata penuh bagi pelanggan"*, sistem didesain mendukung fitur **Rent-to-Rent (R2R)**:
* **Penanda Unit Luar (Sub-Rental):** 
  * Jika seluruh unit internal habis pada tanggal yang dipilih customer, Admin tetap bisa menginput booking baru dengan menandai transaksi tersebut sebagai **"Unit R2R / Rental Mitra"**.
  * Admin dapat mencatat nama rental mitra penyedia unit dan **Harga Modal Sewa** dari mitra tersebut.
* **Perhitungan Keuntungan Bersih (Net Profit):**
  * Sistem laporan keuangan otomatis menghitung keuntungan dari unit mitra: 
    `Keuntungan = Harga Jual ke Customer - Harga Modal Mitra`.
* **Visualisasi Kalender:**
  * Di kalender admin, booking untuk unit luar akan diberi label khusus (misal warna kuning/oranye dengan tag **"R2R"**) agar operasional lapangan tahu bahwa kunci/mobil harus diambil dari rental sebelah, bukan dari garasi sendiri.

---

## 🌐 Database & Penyimpanan Online (Firebase)

Aplikasi ini terhubung langsung dengan **Google Firebase** untuk penyimpanan data secara cloud/online:
* **Firebase Hosting:** Untuk menghosting aplikasi web agar dapat diakses publik melalui domain aman `websiterental.web.app` (menggunakan CDN berkecepatan tinggi dan SSL/HTTPS otomatis).
* **Firestore Database (Real-time):** Ketika Admin mencatat sewa kendaraan baru, dashboard admin lainnya dan status ketersediaan di sisi customer akan langsung diperbarui secara instan.
* **Penyimpanan Berkas (Cloud Storage):** Foto kendaraan dan dokumen KTP penyewa disimpan dalam penyimpanan awan milik Firebase.

---

## 🛠️ Tech Stack & Pengembangan

Berdasarkan keputusan desain, sistem dibangun dengan arsitektur berikut:

1. **Frontend Framework**: **React.js** dengan bundler **Vite**.
2. **Styling**: **Vanilla CSS** modern (menggunakan CSS Variables, Flexbox/Grid, dan transisi halus).
3. **Backend/Database**: **Firebase** (Firestore Database, Firebase Auth, Cloud Storage).

### 🛡️ Aturan Keamanan Database (Firestore Security Rules)
* **Katalog Kendaraan (`/vehicles`)**: Dapat dibaca oleh publik tanpa autentikasi, namun hanya bisa dimodifikasi oleh admin.
  ```javascript
  match /vehicles/{vehicleId} {
    allow read: if true;
    allow write: if request.auth != null;
  }
  ```
* **Data Transaksi & Booking (`/bookings`)**: Hanya boleh dibaca dan ditulis oleh admin yang terautentikasi.
  ```javascript
  match /bookings/{bookingId} {
    allow read, write: if request.auth != null;
  }
  ```

### 🎨 Prinsip Desain Kerapatan Layout (Compact Layout)
Untuk menghindari pemborosan ruang (*excessive whitespace*) dan membuat visualisasi data lebih efektif:
1. **Grid Katalog Kendaraan**:
   * Ukuran minimum kolom kartu diatur rapat (antara `180px` hingga `220px`).
   * Mengutamakan tampilan multi-kolom (misalnya 2 kolom di layar HP, dan 4-5 kolom di layar desktop) agar customer tidak perlu melakukan banyak *scrolling*.
2. **Ketinggian & Skala Komponen**:
   * Gambar kendaraan dibuat lebih ringkas (tinggi maks. `120px` - `145px` di HP).
   * Jarak padding dan margin dikurangi secara proporsional di layar HP (maks. `12px` - `16px`).
3. **Kontrol Variabel CSS**:
   * Menggunakan CSS Variables global untuk mengatur lebar kartu dan unit spasi, sehingga kerapatan layout dapat diatur secara seragam dari satu baris kode.

### 🎨 Estetika Desain Premium (Menghindari Kesan "AI-Generated")
Untuk memastikan aplikasi tidak terlihat seperti template standar buatan generator AI dasar, kami menerapkan panduan visual berikut:
1. **Pemusnahan Emoji sebagai Ikon Utama**:
   * Emojis (`🚗`, `🛵`, `🧼`, `🧼`) digantikan dengan **custom SVG Vector Icons** yang tajam, minimalis, dan profesional.
2. **Skema Warna Kustom & Gradasi Halus**:
   * Menghindari warna bawaan Tailwind/standar (seperti murni merah/biru/hijau).
   * Menggunakan **Tema Cerah Premium (Light Theme)**: Latar belakang bersih berbasis *Light Slate/Gray* (`#f8fafc`), kartu putih bersih (`#ffffff`) dengan bayangan halus (*box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05)*), serta warna aksen yang kontras dan dikurasi (seperti *Emerald Teal* untuk tombol sukses/pesan, dan *Vibrant Indigo* untuk tombol utama). Hal ini menjamin keterbacaan 100% di luar ruangan (*outdoor*) di bawah sinar matahari.
3. **Efek Interaktif & Micro-Animations**:
   * Kartu kendaraan harus memiliki transisi halus (`transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1)`).
   * Ketika kartu di-hover oleh kursor, ia akan sedikit terangkat (*elevated*) dan memancarkan efek bayangan lembut yang lebih tebal.
4. **Desain Komponen Form & Modal yang Elegan**:
   * Input teks menggunakan border abu-abu terang yang halus, menyala biru indigo saat diklik (*focus state*).
   * Modal menggunakan efek latar belakang semi-transparan buram (*backdrop-filter: blur(8px)*) agar menyatu secara estetis dengan halaman di belakangnya.

---

## 🚀 Pengembangan & Deployment ke Firebase

Setelah kode aplikasi siap, berikut cara menjalankan dan mendeploy aplikasi ke internet:

1. **Menjalankan di Komputer Lokal (Development):**
   * Pastikan Node.js terinstal.
   * Jalankan `npm install` untuk memasang dependensi.
   * Jalankan server lokal: `npm run dev`
   * Buka browser di alamat lokal yang disediakan (misal: `http://localhost:5173`).

2. **Mendeploy ke Firebase Hosting (Production):**
   * Lakukan build produksi: `npm run build`
   * Lakukan deployment menggunakan Firebase CLI:
     `firebase deploy --only hosting`
   * Aplikasi akan langsung ter-update di internet pada domain `websiterental.web.app`.