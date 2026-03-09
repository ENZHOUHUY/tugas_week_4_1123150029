# Cara Bikin Project Baru di Firebase

1. Buka situs: https://firebase.google.com
2. Klik Go to Console (pastikan sudah login dengan akun Google).
3. Klik Create a new Firebase project.
4. Masukkan nama project yang kamu inginkan.
5. Klik Continue → lalu Create Project.
6. Tunggu proses pembuatan project sampai selesai, lalu klik Continue.

### Tutor
<p align="center">

  <img width="300" src="https://github.com/user-attachments/assets/12253306-bf24-4806-8d00-f4f0465564ed" />
  <img width="300" src="https://github.com/user-attachments/assets/2ade237e-b5ca-4668-bd95-74238a4c8407" />
  <img width="300" src="https://github.com/user-attachments/assets/6373d203-fc5d-4744-8eb3-0ca4753cdaa3" />
  <img width="300" src="https://github.com/user-attachments/assets/0ac5ab3a-b47e-423e-acad-6a115f9d7b38" />
  <img width="300" src="https://github.com/user-attachments/assets/8e9c830c-f320-4ed0-becb-444e182b1d02" />
</p>


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

### Tutor
<p align="center">
  <img width="300" src="https://github.com/user-attachments/assets/07c0c3b1-3675-4709-b0f7-23773f936acb" />
  <img width="300" src="https://github.com/user-attachments/assets/57396d6f-da6c-4af6-a977-9d25e746b476" />
  <img width="300" src="https://github.com/user-attachments/assets/39b01352-8640-4eda-93aa-a4ef0483bf38" />
  <img width="300" src="https://github.com/user-attachments/assets/abe47d35-6cde-4ec1-959b-7b753dddbbea" />


  
</p>

---

# Mengaktifkan Authentication

Supaya user bisa register dan login, kita perlu mengaktifkan fitur authentication.

Langkahnya:

1. Buka tab Authentication di Firebase.
2. Klik Get Started.
3. Pilih metode Email/Password.
4. Centang Enable Email/Password.
5. Klik Save.

### Tutor
<p align="center">
  <img width="300" src="https://github.com/user-attachments/assets/68fbf07f-78b6-48f2-984c-e2fb93d96ea3" />
  <img width="300" src="https://github.com/user-attachments/assets/b39bf1b8-8fb6-4ff1-afc2-228738110049" />
  <img width="300" src="https://github.com/user-attachments/assets/b7e5303d-7d2e-40ff-bb4d-74623302cdcd" />
</p>

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

### Tutor
<p align="center">
  <img width="" src="https://github.com/user-attachments/assets/499155e9-3f63-4480-adf2-600d3686d9de" />
</p>

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

### Tutor
<p align="center">
  
  <img width="" src="https://github.com/user-attachments/assets/9368bbb1-1a88-46b8-bfed-ea596a100f91" />
  <br>
  <img width="" src="https://github.com/user-attachments/assets/c91f510d-b933-4ee9-a0ad-7bb43bd68b38" />
  <br>
  <img width="" src="https://github.com/user-attachments/assets/73705e95-f9df-42ed-b311-f198e579e2b9" />
</p>

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
### Tutor
<p align="center">

  
  <img width="300" src="https://github.com/user-attachments/assets/c661c205-589b-402d-969b-f602bdca0611" />
  <img width="300" src="https://github.com/user-attachments/assets/2efab5b5-f361-4a7b-833a-d2256ca994a1" />
  <img width="300" src="https://github.com/user-attachments/assets/cc7a720f-9c5f-41e7-b378-4df9653407e5" />
  <img width="300" src="https://github.com/user-attachments/assets/4ab095e3-33a4-4f70-864b-2096e020beae" />
  <img width="300" src="https://github.com/user-attachments/assets/b57d1d92-190a-42a7-bf20-4ee6dbb6fd43" />
</p>


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

### Tutor
<p align="center">
  <img width="300" src="https://github.com/user-attachments/assets/b8563304-f7f2-4ba5-aac3-ee2fdb55da58" />
  <img width="300" src="https://github.com/user-attachments/assets/68c7677f-16c6-4373-8240-d3fecca101de" />
</p>

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
### Tutor
<p align="center">
  
  <img width="300" src="https://github.com/user-attachments/assets/be48e106-03a2-464c-aba6-a47da3d56dfe" />
  <img width="300" src="https://github.com/user-attachments/assets/3ee411a7-05cd-4f53-891d-579d9f3878dd" />
  <img width="300" src="https://github.com/user-attachments/assets/70291b3c-72e0-4c70-8aa9-56490f37d081" />
</p>


---

# Step 5 — Backend Membuat JWT Sendiri

Setelah login, biasanya token Firebase akan dikirim ke backend untuk diverifikasi.
<br>
Jika valid, backend akan membuat JWT miliknya sendiri.

Contoh response dari backend:
```bash
{
  "success": true,
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiJ9...",
    "token_type": "Bearer",
    "expires_in": 86400,
    "user": {
      "id": 1,
      "uid": "aBcDeFgHiJkLmN",
      "email": "test@example.com",
      "email_verified": true,
      "role": "user"
    }
  }
}
```
Token ini biasanya berlaku 24 jam

# Step 6 — Client Menggunakan Token Backend

Setelah mendapat JWT dari backend, semua request ke backend harus memakai token tersebut.

Contoh request:
```
GET
/products
```
Header:
```
Authorization: Bearer BACKEND_TOKEN
```
Jika token valid → request berhasil.
<br>
Jika token tidak ada atau kadaluarsa → backend akan mengembalikan 401 Unauthorized.








