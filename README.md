# Cara Bikin Project Baru di Firebase

1. Buka situs: https://firebase.google.com
2. Klik Go to Console (pastikan sudah login dengan akun Google).
3. Klik Create a new Firebase project.
4. Masukkan nama project yang kamu inginkan.
5. Klik Continue → lalu Create Project.
6. Tunggu proses pembuatan project sampai selesai, lalu klik Continue.

---

# Cara Membuat App Web di Firebase

Setelah project berhasil dibuat:

1. Klik + Add App.
2. Pilih Web App (ikon </>).
3. Masukkan App Nickname (nama bebas untuk aplikasimu).
4. Klik Register App.
5. Setelah itu Firebase akan menampilkan konfigurasi aplikasi.

Di sini kamu akan melihat apiKey.
<br>
Nilai apiKey ini nanti akan digunakan di Environment Postman.

---

# Mengaktifkan Authentication

Supaya user bisa register dan login, kita perlu mengaktifkan fitur authentication.

Langkahnya:

1. Buka tab Authentication di Firebase.
2. Klik Get Started.
3. Pilih metode Email/Password.
4. Centang Enable Email/Password.
5. Klik Save.

---

# Setup Environment di Postman

Supaya tidak perlu menulis ulang data terus-menerus, kita gunakan Environment di Postman.

Langkahnya:

1. Buka Postman.
2. Klik Environments di sidebar kiri.
3. Klik tanda + (Create new environment).
4. Tambahkan variable seperti pada tabel di bawah.

| Variable | Initial Value | Keterangan |
|---------|---------|---------|
| FIREBASE_API_KEY  | AIzaSyB_xxx...  | Web API Key dari Firebase Console  |
| FIREBASE_ID_TOKEN  |   | Diisi otomatis setelah login (via Test script)  |
| BACKEND_BASE_URL  | http://localhost:8080/v1 | Base URL backend kamu  |
| BACKEND_TOKEN  |   | Token JWT dari backend (diisi setelah verify)  |
| USER_EMAIL  | test@example.com | Email untuk testing  |
| USER_PASSWORD  | Test@12345 | Password untuk testing  |




















