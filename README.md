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

# Step 2 — Kirim Email Verifikasi

Setelah user register, biasanya kita perlu memverifikasi email.

### ENDPOINT
```bash
POST
https://identitytoolkit.googleapis.com/v1/accounts:sendOobCode?key={{FIREBASE_API_KEY}}
```

### HEADERS

| Key | Value | Keterangan |
|---------|---------|---------|
| Content-Type  | application/json | Wajib untuk semua Firebase REST API  |

### Request Body (raw JSON)
```bash
{
  "requestType": "VERIFY_EMAIL",
  "idToken": "{{FIREBASE_ID_TOKEN}}"
}
```

### Response
- Sukses
```bash
Response: 200 OK
{
  "kind": "identitytoolkit#GetOobConfirmationCodeResponse",
  "email": "test@example.com" // ← Email tujuan pengiriman
}
```

- Error
```bash
Response: 400 Bad Request
{
  "error": {
    "code": 400,
    "message": "INVALID_ID_TOKEN", // ← idToken sudah kadaluarsa
    "status": "INVALID_ARGUMENT"
  }
}
```


### Postman Test Script
```bash
// Postman → Tests tab:
if (pm.response.code === 200) {
  const json = pm.response.json();
  console.log("Email verifikasi dikirim ke:", json.email);
  console.log("Sekarang buka inbox email dan klik link verifikasi.");
  console.log("Setelah klik, lanjut ke Step 3 untuk cek status.");
} else {
  console.log("Gagal kirim email:", pm.response.json().error.message);
}
```
---

# Step 3 — Cek Status Verifikasi Email

Untuk memastikan apakah email sudah diverifikasi atau belum, kita bisa mengecek statusnya.

### ENDPOINT A

```bash
POST
https://identitytoolkit.googleapis.com/v1/accounts:lookup?key={{FIREBASE_API_KEY}}
```

### Request Body (raw JSON)
```bash
{
  "idToken": "{{FIREBASE_ID_TOKEN}}"
}
```

### Response
- Sukses
```bash
Response: 200 OK (email belum verify)
{
  "kind": "identitytoolkit#GetAccountInfoResponse",
  "users": [
      {
        "localId": "aBcDeFgHiJkLmN",
        "email": "test@example.com",
        "displayName": "Test User",
        "passwordHash": "UkVEQUNURUQ=",
        "emailVerified": false, // ← BELUM DIVERIFIKASI
        "passwordUpdatedAt": 1700000000000,
        "providerUserInfo": [ ... ],
        "validSince": "1700000000",
        "lastLoginAt": "1700000000000",
        "createdAt": "1700000000000"
      }
  ]
}
```
```bash
Response: 200 OK (email verified)
{
  "kind": "identitytoolkit#GetAccountInfoResponse",
  "users": [
      {
        "localId": "aBcDeFgHiJkLmN",
        "email": "test@example.com",
        "emailVerified": true, // ← SUDAH DIVERIFIKASI
        "lastLoginAt": "1700000000000",
        ...
      }
  ]
}
```

---

# Step 4 — Login dengan Email dan Password

Setelah akun dibuat, user bisa login.

### ENDPOINT
```bash
POST
https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key={{FIREBASE_API_KEY}}
```

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
  "kind": "identitytoolkit#VerifyPasswordResponse",
  "localId": "aBcDeFgHiJkLmN",
  "email": "test@example.com",
  "displayName": "Test User",
  "idToken": "eyJhbGciOiJSUzI1...", // ← Firebase ID Token BARU
  "registered": true,
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
    "message": "INVALID_PASSWORD",
    "errors": [{ "message": "INVALID_PASSWORD", "domain": "global" }]
  }
}
```

### Postman Test Script — Auto-Update Token
```bash
// Postman → Tests tab:
const json = pm.response.json();
if (pm.response.code === 200) {
  // Update environment dengan idToken BARU hasil login
  pm.environment.set("FIREBASE_ID_TOKEN", json.idToken);
  pm.environment.set("FIREBASE_REFRESH_TOKEN", json.refreshToken);
  console.log("Login berhasil. Token diperbarui.");
  console.log("Lanjut ke Step 5: kirim token ke backend.");
} else {
  console.log("Login gagal:", json.error.message);
}
```
---











