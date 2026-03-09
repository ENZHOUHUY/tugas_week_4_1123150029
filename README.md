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

---

# Step 1 — Register / Membuat Akun Baru

Di langkah ini kita akan membuat akun user baru menggunakan Firebase REST API.

### ENDPOINT
```bash
POST
https://identitytoolkit.googleapis.com/v1/accounts:signUp?key={{FIREBASE_API_KEY}}
```

### HEADERS

| Key | Value | Keterangan |
|---------|---------|---------|
| Content-Type  | application/json | Wajib untuk semua Firebase REST API  |

### Request Body (raw JSON)
```bash
{
  "email": "{{USER_EMAIL}}",
  "password": "{{USER_PASSWORD}}",
  "returnSecureToken": true
}
```

### Response
- Sukses
```bash
Response: 200 OK
{
  "kind": "identitytoolkit#SignupNewUserResponse",
  "localId": "aBcDeFgHiJkLmN",
  "email": "test@example.com",
  "displayName": "",
  "idToken": "eyJhbGciOiJSUzI1...",
  "registered": false,
  "refreshToken": "AMf-vBxK...",
  "expiresIn": "3600"
}
```

- Error
```bash
Response: 400 Bad Request
{
  "error": {
    "code": 400,
    "message": "EMAIL_EXISTS",
    "status": "INVALID_ARGUMENT"
  }
}
```

### Postman Test Script — Auto-save Token
- Copy paste ke tab "Tests" di Postman agar idToken tersimpan otomatis:
```bash
// Postman → Tests tab:
const json = pm.response.json();
if (pm.response.code === 200) {
  pm.environment.set("FIREBASE_ID_TOKEN", json.idToken);
  pm.environment.set("FIREBASE_LOCAL_ID", json.localId);
  pm.environment.set("FIREBASE_REFRESH_TOKEN", json.refreshToken);
  console.log("Register sukses. UID:", json.localId);
  console.log("PERHATIAN: Email belum diverifikasi. Lanjut ke Step 2.");
} else {
  console.log("Register gagal:", json.error.message);
}
```

---














