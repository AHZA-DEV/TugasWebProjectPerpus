# 📚 Dokumentasi REST API - Library Management System

## Informasi Umum

**Base URL:** `http://127.0.0.1:8000/api`

**Format Response:** JSON

**Framework:** Laravel 12.33.0

---

## 📋 Daftar Endpoint

### 1. Members (Anggota)
### 2. Authors (Penulis)
### 3. Publishers (Penerbit)
### 4. Books (Buku)
### 5. Loans (Peminjaman)

---

## 1️⃣ MEMBERS API

### 1.1 Get All Members
Mendapatkan semua data anggota perpustakaan.

**Endpoint:** `GET /api/members`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
[
    {
        "id": 1,
        "name": "Siti",
        "email": "siti@mail.com",
        "phone": "081222333",
        "created_at": "2025-10-24T10:00:00.000000Z",
        "updated_at": "2025-10-24T10:00:00.000000Z"
    }
]
```

---

### 1.2 Create Member
Menambahkan anggota baru.

**Endpoint:** `POST /api/members`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "name": "Siti",
    "email": "siti@mail.com",
    "phone": "081222333"
}
```

**Validation Rules:**
- `name`: required (wajib diisi)
- `email`: required, email format, unique (harus unik)
- `phone`: required (wajib diisi)

**Response Success (201 Created):**
```json
{
    "id": 1,
    "name": "Siti",
    "email": "siti@mail.com",
    "phone": "081222333",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

**Response Error (422 Unprocessable Entity):**
```json
{
    "message": "The email has already been taken.",
    "errors": {
        "email": [
            "The email has already been taken."
        ]
    }
}
```

---

### 1.3 Get Member by ID
Mendapatkan detail anggota berdasarkan ID.

**Endpoint:** `GET /api/members/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "name": "Siti",
    "email": "siti@mail.com",
    "phone": "081222333",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

**Response Error (404 Not Found):**
```json
{
    "message": "No query results for model [App\\Models\\Member]."
}
```

---

### 1.4 Update Member
Mengupdate data anggota.

**Endpoint:** `PUT /api/members/{id}` atau `PATCH /api/members/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "name": "Siti Updated",
    "email": "siti.updated@mail.com",
    "phone": "081222444"
}
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "name": "Siti Updated",
    "email": "siti.updated@mail.com",
    "phone": "081222444",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T11:00:00.000000Z"
}
```

---

### 1.5 Delete Member
Menghapus data anggota.

**Endpoint:** `DELETE /api/members/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (204 No Content):**
```
(No content returned)
```

---

## 2️⃣ AUTHORS API

### 2.1 Get All Authors

**Endpoint:** `GET /api/authors`
 
**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
[
    {
        "id": 1,
        "name": "Andrea Hirata",
        "created_at": "2025-10-24T10:00:00.000000Z",
        "updated_at": "2025-10-24T10:00:00.000000Z"
    }
]
```

---

### 2.2 Create Author

**Endpoint:** `POST /api/authors`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "name": "Andrea Hirata"
}
```

**Validation Rules:**
- `name`: required (wajib diisi)

**Response Success (201 Created):**
```json
{
    "id": 1,
    "name": "Andrea Hirata",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

---

### 2.3 Get Author by ID

**Endpoint:** `GET /api/authors/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "name": "Andrea Hirata",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

---

### 2.4 Update Author

**Endpoint:** `PUT /api/authors/{id}` atau `PATCH /api/authors/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "name": "Andrea Hirata Updated"
}
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "name": "Andrea Hirata Updated",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T11:00:00.000000Z"
}
```

---

### 2.5 Delete Author

**Endpoint:** `DELETE /api/authors/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (204 No Content):**
```
(No content returned)
```

---

## 3️⃣ PUBLISHERS API

### 3.1 Get All Publishers

**Endpoint:** `GET /api/publishers`
 
**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
[
    {
        "id": 1,
        "name": "Gramedia",
        "created_at": "2025-10-24T10:00:00.000000Z",
        "updated_at": "2025-10-24T10:00:00.000000Z"
    }
]
```

---

### 3.2 Create Publisher

**Endpoint:** `POST /api/publishers`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "name": "Gramedia"
}
```

**Validation Rules:**
- `name`: required (wajib diisi)

**Response Success (201 Created):**
```json
{
    "id": 1,
    "name": "Gramedia",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

---

### 3.3 Get Publisher by ID

**Endpoint:** `GET /api/publishers/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "name": "Gramedia",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

---

### 3.4 Update Publisher

**Endpoint:** `PUT /api/publishers/{id}` atau `PATCH /api/publishers/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "name": "Gramedia Pustaka"
}
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "name": "Gramedia Pustaka",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T11:00:00.000000Z"
}
```

---

### 3.5 Delete Publisher

**Endpoint:** `DELETE /api/publishers/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (204 No Content):**
```
(No content returned)
```

---

## 4️⃣ BOOKS API

### 4.1 Get All Books

**Endpoint:** `GET /api/books`
 
**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
[
    {
        "id": 1,
        "title": "Laskar Pelangi",
        "author_id": 1,
        "publisher_id": 1,
        "created_at": "2025-10-24T10:00:00.000000Z",
        "updated_at": "2025-10-24T10:00:00.000000Z"
    }
]
```

---

### 4.2 Create Book

**Endpoint:** `POST /api/books`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "title": "Laskar Pelangi",
    "author_id": 1,
    "publisher_id": 1
}
```

**Validation Rules:**
- `title`: required (wajib diisi)
- `author_id`: required, exists in authors table
- `publisher_id`: required, exists in publishers table

**Response Success (201 Created):**
```json
{
    "id": 1,
    "title": "Laskar Pelangi",
    "author_id": 1,
    "publisher_id": 1,
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

**Response Error (422 Unprocessable Entity):**
```json
{
    "message": "The selected author id is invalid.",
    "errors": {
        "author_id": [
            "The selected author id is invalid."
        ]
    }
}
```

---

### 4.3 Get Book by ID

**Endpoint:** `GET /api/books/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "title": "Laskar Pelangi",
    "author_id": 1,
    "publisher_id": 1,
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

---

### 4.4 Update Book

**Endpoint:** `PUT /api/books/{id}` atau `PATCH /api/books/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "title": "Laskar Pelangi - Edisi Revisi",
    "author_id": 1,
    "publisher_id": 1
}
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "title": "Laskar Pelangi - Edisi Revisi",
    "author_id": 1,
    "publisher_id": 1,
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T11:00:00.000000Z"
}
```

---

### 4.5 Delete Book

**Endpoint:** `DELETE /api/books/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (204 No Content):**
```
(No content returned)
```

---

## 5️⃣ LOANS API

### 5.1 Get All Loans

**Endpoint:** `GET /api/loans`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
[
    {
        "id": 1,
        "member_id": 1,
        "book_id": 1,
        "loan_date": "2025-10-24",
        "return_date": null,
        "created_at": "2025-10-24T10:00:00.000000Z",
        "updated_at": "2025-10-24T10:00:00.000000Z"
    }
]
```

---

### 5.2 Create Loan

**Endpoint:** `POST /api/loans`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "member_id": 1,
    "book_id": 1,
    "loan_date": "2025-10-24",
    "return_date": null
}
```

**Validation Rules:**
- `member_id`: required, exists in members table
- `book_id`: required, exists in books table
- `loan_date`: required, date format
- `return_date`: nullable, date format

**Response Success (201 Created):**
```json
{
    "id": 1,
    "member_id": 1,
    "book_id": 1,
    "loan_date": "2025-10-24",
    "return_date": null,
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

**Response Error (422 Unprocessable Entity):**
```json
{
    "message": "The selected member id is invalid.",
    "errors": {
        "member_id": [
            "The selected member id is invalid."
        ]
    }
}
```

---

### 5.3 Get Loan by ID

**Endpoint:** `GET /api/loans/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "member_id": 1,
    "book_id": 1,
    "loan_date": "2025-10-24",
    "return_date": null,
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T10:00:00.000000Z"
}
```

---

### 5.4 Update Loan (Return Book)

**Endpoint:** `PUT /api/loans/{id}` atau `PATCH /api/loans/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body:**
```json
{
    "member_id": 1,
    "book_id": 1,
    "loan_date": "2025-10-24",
    "return_date": "2025-10-31"
}
```

**Response Success (200 OK):**
```json
{
    "id": 1,
    "member_id": 1,
    "book_id": 1,
    "loan_date": "2025-10-24",
    "return_date": "2025-10-31",
    "created_at": "2025-10-24T10:00:00.000000Z",
    "updated_at": "2025-10-24T12:00:00.000000Z"
}
```

---

### 5.5 Delete Loan

**Endpoint:** `DELETE /api/loans/{id}`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (204 No Content):**
```
(No content returned)
```

---

## 📊 HTTP Status Codes

| Status Code | Keterangan |
|-------------|-----------|
| 200 OK | Request berhasil (GET, PUT, PATCH) |
| 201 Created | Data berhasil dibuat (POST) |
| 204 No Content | Data berhasil dihapus (DELETE) |
| 404 Not Found | Data tidak ditemukan |
| 422 Unprocessable Entity | Validasi gagal |
| 500 Internal Server Error | Error pada server |

---

## 🔧 Testing dengan Postman

### Setup Postman

1. **Base URL:** `http://127.0.0.1:8000/api`
2. **Headers (untuk semua request):**
   - `Accept: application/json`
   - `Content-Type: application/json`
    - `Authorization: Bearer <token>` (gunakan token hasil login)

### Contoh Testing Flow

#### 1. Tambah Author
```
POST http://127.0.0.1:8000/api/authors
Body: {"name": "Andrea Hirata"}
```

#### 2. Tambah Publisher
```
POST http://127.0.0.1:8000/api/publishers
Body: {"name": "Gramedia"}
```

#### 3. Tambah Book
```
POST http://127.0.0.1:8000/api/books
Body: {
    "title": "Laskar Pelangi",
    "author_id": 1,
    "publisher_id": 1
}
```

#### 4. Tambah Member
```
POST http://127.0.0.1:8000/api/members
Body: {
    "name": "Siti",
    "email": "siti@mail.com",
    "phone": "081222333"
}
```

#### 5. Create Loan
```
POST http://127.0.0.1:8000/api/loans
Body: {
    "member_id": 1,
    "book_id": 1,
    "loan_date": "2025-10-24",
    "return_date": null
}
```

#### 6. Return Book (Update Loan)
```
PUT http://127.0.0.1:8000/api/loans/1
Body: {
    "member_id": 1,
    "book_id": 1,
    "loan_date": "2025-10-24",
    "return_date": "2025-10-31"
}
```

---

## 📸 Screenshot untuk Laporan

Ambil screenshot dari Postman untuk setiap operasi berikut:

### Members
- ✅ GET All Members
- ✅ POST Create Member
- ✅ GET Member by ID
- ✅ PUT Update Member
- ✅ DELETE Member

### Authors
- ✅ GET All Authors
- ✅ POST Create Author
- ✅ PUT Update Author
- ✅ DELETE Author

### Publishers
- ✅ GET All Publishers
- ✅ POST Create Publisher
- ✅ PUT Update Publisher
- ✅ DELETE Publisher

### Books
- ✅ GET All Books
- ✅ POST Create Book
- ✅ PUT Update Book
- ✅ DELETE Book

### Loans
- ✅ GET All Loans
- ✅ POST Create Loan
- ✅ PUT Update Loan (Return Book)
- ✅ DELETE Loan

---

## 🛠️ Troubleshooting

### Error 404 Not Found
- Pastikan route sudah terdaftar: `php artisan route:list`
- Pastikan server running: `php artisan serve`

### Error 422 Validation Failed
- Periksa validation rules di controller
- Pastikan format data sesuai (email, date, dll)
- Pastikan foreign key exists (author_id, publisher_id, dll)

### Error 500 Internal Server Error
- Cek log: `storage/logs/laravel.log`
- Pastikan database sudah migration: `php artisan migrate`
- Pastikan Model memiliki `$fillable` property

---

## 📝 Catatan Penting

1. **Foreign Key:** Sebelum membuat Book, pastikan Author dan Publisher sudah ada
2. **Foreign Key:** Sebelum membuat Loan, pastikan Member dan Book sudah ada
3. **Email Unique:** Email member harus unik, tidak boleh duplikat
4. **Date Format:** Format tanggal harus YYYY-MM-DD (contoh: 2025-10-24)
5. **Return Date:** Boleh null jika buku belum dikembalikan

---

## 👨‍💻 Dibuat Dengan

- **Framework:** Laravel 12.33.0
- **Database:** MySQL/PostgreSQL/SQLite
- **Testing Tool:** Postman
- **PHP Version:** 8.2+

---

**Dokumentasi ini dibuat untuk keperluan tugas Minggu 6 - REST API di Laravel**
