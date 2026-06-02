# 🚗 DriveEase - Serverless Cross-Platform Car Rental Application

DriveEase adalah aplikasi penyewaan mobil lintas platform (Android & iOS) yang dibangun dengan pendekatan **Serverless Architecture**. Proyek ini menggunakan **React Native dengan TypeScript (Expo)** di sisi klien dan memanfaatkan seluruh ekosistem **Firebase** sebagai infrastruktur backend, database, autentikasi, serta penyimpanan file.

Proyek ini dirancang dengan fokus pada ketahanan tipe data (*type-safety*), skalabilitas NoSQL, dan otomatisasi logika bisnis tingkat lanjut (*advanced business logic*).

---

## 🏗️ 1. Arsitektur & Tech Stack

Aplikasi ini sepenuhnya berjalan di atas ekosistem *cloud/serverless*, mengeliminasi kebutuhan manajemen server tradisional:

* **Frontend Mobile:** React Native (TypeScript) menggunakan **Expo SDK** untuk kompilasi native iOS dan Android yang optimal dari satu basis kode.
* **State Management:** Zustand / React Context API (Type-safe state container).
* **Database Utama:** Cloud Firestore (NoSQL Document-Based Database dengan dukungan Real-time Listener).
* **Cloud Storage:** Firebase Storage (Penyimpanan biner untuk berkas foto mobil dan dokumen identitas).
* **User Authentication:** Firebase Auth (Manajemen sesi user aman berbasis token JWT bawaan).
* **Automated Services:** Firebase Extensions (*Trigger Email from Firestore*) & Firebase Cloud Functions untuk operasi komputasi sensitif.

---

## 📊 2. Desain Arsitektur Data NoSQL (Cloud Firestore)

Data disimpan dalam bentuk *Collections* (Koleksi) dan *Documents* (Dokumen JSON). Skema di bawah ini telah dioptimalkan untuk meminimalkan biaya *Read/Write* pada database NoSQL.

### A. Koleksi: `users`
*Path: `/users/{userId}`*
Menyimpan profil pengguna, kredensial dasar, dan status verifikasi KYC (*Know Your Customer*).
```json
{
  "uid": "AUTH_GENERATED_UNIQUE_ID",
  "name": "Revi Putra Hernel",
  "email": "revi@example.com",
  "phone": "081234567890",
  "role": "customer", 
  "ktp_selfie_url": "https://firebasestorage.googleapis.com/v0/b/driveease.appspot.com/o/kyc%2Fselfie_123.jpg",
  "is_verified": false, 
  "created_at": "2026-06-02T10:00:00Z"
}
```

### B. Koleksi: `cars`
*Path: `/cars/{carId}`*
Menyimpan inventaris armada mobil dan melacak tanggal pemesanan aktif untuk mencegah *overlapping*.
```json
{
  "brand": "Toyota",
  "model": "Avanza Veloz",
  "plate_number": "BA 1234 OP",
  "transmission": "automatic", 
  "price_per_day": 350000,
  "image_url": "https://firebasestorage.googleapis.com/v0/b/...",
  "status": "available", 
  "booked_dates": [
    "2026-06-10",
    "2026-06-11",
    "2026-06-12"
  ], 
  "average_rating": 4.8
}
```

### C. Koleksi: `bookings`
*Path: `/bookings/{bookingId}`*
Mencatat seluruh siklus sewa, pembayaran, hingga kalkulasi denda pengembalian.
```json
{
  "booking_code": "RNT-20260602-XYZ",
  "user_id": "AUTH_GENERATED_UNIQUE_ID",
  "user_name": "Revi Putra Hernel",
  "car_id": "CAR_DOCUMENT_ID",
  "car_name": "Toyota Avanza Veloz",
  "start_date": "2026-06-15T08:00:00Z",
  "end_date": "2026-06-17T08:00:00Z",
  "actual_return_date": null, 
  "with_driver": false,
  "total_price": 700000, 
  "penalty_fee": 0, 
  "status": "pending", 
  "payment_proof_url": null,
  "payment_status": "unpaid", 
  "created_at": "2026-06-02T17:00:00Z"
}
```

### D. Koleksi: `reviews`
*Path: `/reviews/{reviewId}`*
```json
{
  "booking_id": "BOOKING_DOCUMENT_ID",
  "user_id": "AUTH_GENERATED_UNIQUE_ID",
  "user_name": "Revi Putra Hernel",
  "car_id": "CAR_DOCUMENT_ID",
  "rating": 5, 
  "comment": "Mobilnya bersih banget, AC dingin, tarikan mantap!",
  "created_at": "2026-06-18T09:00:00Z"
}
```

### E. Koleksi: `mail`
*Path: `/mail/{mailId}`*
Koleksi khusus yang dipantau oleh Firebase Extension untuk mengirim notifikasi email otomatis.
```json
{
  "to": "revi@example.com",
  "message": {
    "subject": "DriveEase - Pembayaran Dikonfirmasi!",
    "html": "<h3>Halo Revi,</h3><p>Pembayaran Anda untuk booking RNT-20260602-XYZ telah kami terima. Silakan ambil kendaraan sesuai jadwal.</p>"
  }
}
```

---

## 🔄 3. Alur Kerja Fitur Advanced

### 1. Sistem Registrasi & KYC (Selfie KTP)
* **Langkah 1:** Pelanggan mengisi formulir pendaftaran di aplikasi React Native.
* **Langkah 2:** Aplikasi membuka kamera HP menggunakan `expo-image-picker` untuk mengambil foto selfie pelanggan memegang KTP.
* **Langkah 3:** Berkas gambar diunggah ke Firebase Storage pada direktori `/kyc/{uid}.jpg`. URL publik yang dihasilkan disimpan ke dalam dokumen pengguna di Firestore (`ktp_selfie_url`) dengan parameter awal `is_verified: false`.
* **Langkah 4:** Admin meninjau dokumen lewat panel admin/console. Jika foto valid, admin mengubah `is_verified` menjadi `true`. Di aplikasi mobile, *state* pelanggan otomatis diperbarui secara *real-time* dan membuka akses ke fitur pemesanan.

### 2. Deteksi Overlap Jadwal Otomatis (Anti-Double Booking)
* **Langkah 1:** Pelanggan memilih tanggal mulai dan selesai sewa pada komponen Kalender di aplikasi mobile (misal: 11 Juni hingga 13 Juni 2026).
* **Langkah 2:** Aplikasi menghasilkan *array* string berisi rentang tanggal target: `['2026-06-11', '2026-06-12', '2026-06-13']`.
* **Langkah 3:** Aplikasi melakukan kueri ke koleksi `cars` dengan validasi:
    ```typescript
    import { query, collection, where } from 'firebase/firestore';
    
    const carQuery = query(
      collection(db, 'cars'),
      where('status', '==', 'available'),
      where('booked_dates', 'not-in', targetDates)
    );
```
* **Langkah 4:** Firestore mengembalikan daftar armada yang berstatus kosong pada rentang waktu tersebut, mengeliminasi risiko tumpang tindih pesanan.

### 3. Kalkulator Denda Keterlambatan Otomatis
* **Langkah 1:** Ketika mobil dikembalikan oleh pelanggan, Admin membuka detail pesanan di aplikasi dan menekan tombol **"Selesai Sewa"**.
* **Langkah 2:** Sistem mencatat waktu riil pengembalian ke dalam field `actual_return_date`.
* **Langkah 3:** Aplikasi menjalankan logika komparasi waktu menggunakan library `date-fns`:
    ```typescript
    const parseEnd = new Date(booking.end_date);
    const parseActual = new Date(booking.actual_return_date);
    
    if (parseActual > parseEnd) {
      const diffInHours = Math.ceil((parseActual.getTime() - parseEnd.getTime()) / (1000 * 60 * 60));
      const penaltyRatePerHour = 50000; 
      const totalPenalty = diffInHours * penaltyRatePerHour;
      
      // Update field penalty_fee dan status booking di Firestore
    }
```
* **Langkah 4:** Status booking diubah menjadi `completed`, kolom `penalty_fee` diperbarui, dan tanggal sewa mobil tersebut di dalam array `booked_dates` dihapus agar mobil kembali tersedia untuk pelanggan lain.

### 4. Integrasi Gateway Notifikasi Email Otomatis
* **Langkah 1:** Ekstensi Firebase *Trigger Email from Firestore* dipasang di konsol Firebase.
* **Langkah 2:** Setiap kali status pembayaran berubah (`payment_status === 'paid'`), aplikasi mobile atau fungsi cloud secara otomatis membuat satu dokumen baru di dalam koleksi `mail`.
* **Langkah 3:** Ekstensi mendeteksi dokumen baru tersebut, mengambil field `to` dan `message`, lalu mengirimkannya langsung ke kotak masuk email pelanggan menggunakan server SMTP yang dikonfigurasi (e.g., Mailgun, Mailtrap, atau Brevo).

### 5. Sistem Ulasan dan Agregasi Rating
* **Langkah 1:** Begitu status transaksi berubah menjadi `completed`, aplikasi React Native mendeteksi perubahan tersebut lewat *listener* aktif dan menampilkan modal pemberian rating (bintang 1-5) dan ulasan teks.
* **Langkah 2:** Data disimpan ke koleksi `reviews`.
* **Langkah 3:** Aplikasi memicu fungsi hitung rata-rata rating untuk mobil terkait, lalu memperbarui field `average_rating` pada dokumen mobil di koleksi `cars` menggunakan metode transaksi atomik Firestore (`runTransaction`) guna memastikan konsistensi data.

---

## 🔐 4. Firebase Security Rules (Keamanan Data)

Keamanan data pada arsitektur *serverless* dikelola langsung di tingkat database menggunakan Firebase Rules.

### Firestore Rules (`firestore.rules`)
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    match /users/{userId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && request.auth.uid == userId;
    }
    
    match /cars/{carId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    
    match /bookings/{bookingId} {
      allow read: if request.auth != null && (resource.data.user_id == request.auth.uid || get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin');
      allow create: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.is_verified == true;
      allow update: if request.auth != null;
    }
    
    match /reviews/{reviewId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null && request.resource.data.user_id == request.auth.uid;
    }
  }
}
```

---

## 🚀 5. Panduan Instalasi & Setup

Ikuti langkah-langkah berikut untuk menjalankan proyek di lingkungan lokal komputer Anda:

### Prasyarat Sistem
* Node.js (Versi 18 atau versi terbaru)
* Akun Google Firebase (Gratis/Spark Plan)
* Aplikasi **Expo Go** terinstal di perangkat Android atau iOS Anda.

### Langkah 1: Kloning Repositori & Instalasi Dependensi
```bash
# Kloning proyek ini
git clone https://github.com/username/driveease.git
cd driveease

# Instalasi paket library pihak ketiga
npm install
```

### Langkah 2: Konfigurasi SDK Firebase
Buat file baru bernama `firebaseConfig.ts` di dalam folder root atau `/config` proyek Anda, lalu masukkan kunci API dari Firebase Console Anda:
```typescript
import { initializeApp } from "firebase/app";
import { getAuth } from "firebase/auth";
import { getFirestore } from "firebase/firestore";
import { getStorage } from "firebase/storage";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
export const db = getFirestore(app);
export const storage = getStorage(app);
```

### Langkah 3: Menjalankan Aplikasi Jalur Expo
```bash
# Memulai server lokal metro bundler Expo
npx expo start
```
Setelah server berjalan, gunakan kamera smartphone Anda untuk memindai **QR Code** yang muncul di terminal (atau via aplikasi **Expo Go** di Android) untuk membuka dan menjalankan aplikasi secara *live* dan interaktif.


git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/revihernel/Rental-mobil.git
git push -u origin main