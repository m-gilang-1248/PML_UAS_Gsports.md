Berikut adalah **Rencana Iterasi Implementasi (Roadmap)** untuk proyek **Gsports** menggunakan Gemini CLI.

Rencana ini dibagi menjadi **6 Sprint (Tahapan)** logis. Tujuannya adalah membangun fondasi yang kuat terlebih dahulu, baru kemudian menumpuk fitur kompleks di atasnya.

**Prasyarat:** Pastikan ke-4 file (`PRD.md`, `SCHEMA.md`, `UIUX.md`, `TECH_SPEC.md`) sudah ada di root folder proyek.

---

### 🚀 Sprint 1: Setup, Fondasi & Konektivitas
**Fokus:** Struktur proyek, instalasi library, setup Firebase, dan Routing dasar.

**Langkah 1: Scaffold Proyek**
*   **Command:** `/create-app`
*   **Prompt:**
    ```text
    Buat aplikasi Flutter 'gsports'. Baca @TECH_SPEC.md.
    1. Siapkan struktur folder Clean Architecture (core, features).
    2. Update `pubspec.yaml` dengan dependencies: flutter_bloc, go_router, firebase_core, cloud_firestore, json_annotation, google_fonts, flutter_dotenv.
    3. Jalankan `flutter pub get`.
    ```

**Langkah 2: Setup Firebase**
*   **Command:** `/firebase:init`
*   **Prompt:**
    ```text
    Inisialisasi proyek Firebase baru untuk aplikasi ini.
    1. Aktifkan Authentication (Email/Password & Google).
    2. Aktifkan Firestore Database.
    3. Aktifkan Storage.
    4. Buatkan file `firebase_options.dart` untuk Flutter.
    ```

**Langkah 3: Theme & Router**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Baca @UIUX.md (bagian Design Philosophy & Palette) dan @TECH_SPEC.md (Routing).
    1. Buat `lib/core/config/theme.dart`: Implementasikan Material 3 dengan warna Primary Black (#212121) dan Accent Blue.
    2. Buat `lib/core/config/router.dart`: Setup GoRouter dengan rute awal: `/splash`, `/login`, `/home`, `/booking`.
    ```

---

### 👤 Sprint 2: Authentication & User Management
**Fokus:** Registrasi user, penyimpanan data profil ke Firestore, dan pemisahan role (User vs Mitra).

**Langkah 1: Auth Feature (Domain & Data)**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Buat fitur Auth di `lib/features/auth`. Baca @TECH_SPEC.md.
    1. Buat Repository untuk Login/Register via Firebase Auth.
    2. Setelah login sukses, simpan/update data user ke Firestore collection `users` (lihat @SCHEMA.md).
    3. Handle error state menggunakan Either/Dartz atau class Failure kustom.
    ```

**Langkah 2: Auth UI**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Buat UI Login & Register. Baca @UIUX.md bagian Authentication.
    1. Gunakan `AuthBloc` untuk state management.
    2. Style input field 'Outlined' dan tombol 'Clean/Plain'.
    3. Jika login sukses, navigasi ke `/home`.
    ```

---

### 🏟️ Sprint 3: Manajemen Data Mitra & Venue (Supply Side)
**Fokus:** Memungkinkan Mitra (atau kita sebagai Admin via backend) memasukkan data lapangan agar ada yang bisa di-booking.

**Langkah 1: Venue Entities & Repository**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Fitur Venue Management. Baca @SCHEMA.md bagian `venues`.
    1. Buat Model & Entity untuk `Venue` dan `Court`.
    2. Buat Repository untuk mengambil list venue (Get All) dan detail venue (Get By ID) dari Firestore.
    ```

**Langkah 2: Tampilan Home & Detail Venue**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Buat UI untuk User (Fitur Home & Venue). Baca @UIUX.md.
    1. `HomePage`: Tampilkan list venue menggunakan `VenueCard` widget.
    2. `VenueDetailScreen`: Tampilkan foto, fasilitas, dan list lapangan (`Court`).
    3. Gunakan `VenueBloc` untuk fetch data.
    ```

---

### 📅 Sprint 4: Core Booking System (Single User)
**Fokus:** Alur pemesanan lapangan oleh satu orang (tanpa patungan dulu) hingga status "Paid".

**Langkah 1: Booking Logic (Backend/Repo)**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Implementasi Booking Repository. Baca @SCHEMA.md bagian `bookings`.
    1. Buat fungsi `createBooking()`.
    2. Sebelum create, lakukan validasi ketersediaan: Query `bookings` di Firestore, cek apakah ada bentrok jadwal di `courtId` yang sama.
    3. Jika aman, simpan dokumen booking dengan status 'waiting_payment'.
    ```

**Langkah 2: Booking UI & Selection**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Buat UI pemilihan slot waktu di `VenueDetailScreen`. Baca @UIUX.md.
    1. Tambahkan DatePicker dan Grid Waktu.
    2. Saat slot dipilih, munculkan BottomSheet konfirmasi.
    3. Panggil `BookingBloc` untuk submit data.
    ```

**Langkah 3: Integrasi Midtrans (Mocking/Sandbox)**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Integrasi Payment Gateway. Baca @TECH_SPEC.md bagian Payment.
    1. Buat service untuk generate dummy payment URL (atau integrasi Midtrans Snap Sandbox jika API Key tersedia).
    2. Tampilkan WebView untuk pembayaran.
    3. Update status booking di Firestore menjadi 'paid' jika callback sukses.
    ```

---

### 💸 Sprint 5: Fitur Unggulan (Split Bill & Scoreboard)
**Fokus:** Menambahkan kompleksitas fitur unik aplikasi.

**Langkah 1: Logika Split Bill**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Implementasi fitur Split Bill. Baca @PRD.md dan @SCHEMA.md.
    1. Di `BookingRepository`, tambahkan logika generate 6-digit `splitCode`.
    2. Buat fungsi `joinBooking(String code)`: User lain yang input kode ini akan ditambahkan ke array `participants` di dokumen booking terkait.
    ```

**Langkah 2: Scoreboard**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Buat fitur Scoreboard. Baca @UIUX.md.
    1. Buat halaman `ScoreboardScreen`.
    2. Gunakan `ScoreboardBloc` untuk logika tambah/kurang poin (in-memory).
    3. Tambahkan tombol "Save Match" yang menyimpan hasil ke collection `matches` dan update stats user (@SCHEMA.md).
    ```

---

### 💰 Sprint 6: Monetisasi, Security & Polishing
**Fokus:** Membatasi akses (Premium check), Iklan, dan persiapan rilis.

**Langkah 1: Premium Guard & Ads**
*   **Command:** `/modify`
*   **Prompt:**
    ```text
    Implementasi Monetisasi. Baca @PRD.md.
    1. Di Booking Logic: Cek jika `sportType` adalah 'tennis'/'golf', pastikan `user.tier` == 'premium'. Jika tidak, lempar exception/error.
    2. Integrasikan `google_mobile_ads`. Tampilkan Banner Ad di HomePage HANYA jika `user.tier` == 'free'.
    ```

**Langkah 2: Security Rules**
*   **Command:** `/firebase:deploy` (atau `/firebase:consult` dulu)
*   **Prompt:**
    ```text
    Buatkan `firestore.rules`. Baca @SCHEMA.md bagian Security Rules.
    Pastikan user hanya bisa edit profil sendiri, Mitra hanya bisa edit venue sendiri, dan Booking validasi create hanya untuk user login. Deploy rules tersebut.
    ```

**Langkah 3: Pre-Launch Check**
*   **Command:** `/commit`
*   **Prompt:**
    ```text
    Jalankan analisis kode (linter), format kode, dan pastikan tidak ada error fatal sebelum rilis versi Alpha.
    ```

---

### Tips Penting Saat Eksekusi:

1.  **Jangan Lupa Test:** Setelah setiap Sprint (atau bahkan setiap langkah besar), coba jalankan aplikasi (`flutter run`). Jangan menunggu sampai Sprint 6 selesai.
2.  **Error Handling:** Jika Gemini menghasilkan kode yang error, copy error tersebut dan paste kembali ke CLI dengan perintah `/modify "Fix error ini: [paste error log]"`.
3.  **File Management:** Jangan menghapus file MD. Gemini membutuhkannya terus menerus sebagai "Memori Jangka Panjang" proyek Anda.
