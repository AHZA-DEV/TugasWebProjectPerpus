# Modul 7 – API Lanjutan (Relasi & Authentication dengan Laravel Sanctum) 
# Langkah Praktik

## ❶ Install Laravel Sanctum ( SKIP )
```bash
composer require laravel/sanctum
```

## ❷ Menginstal package Sanctum ke dalam project Laravel.

## ❸ Publikasi dan Migrasi ( SKIP )
```bash
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

## ❹ Perintah pertama menyalin file konfigurasi & migration Sanctum ke proyek.
## ❺ Perintah kedua membuat tabel personal_access_tokens di database.

## ❻ Aktifkan Middleware Sanctum

**Untuk Laravel 12 ke atas** ada di `bootstrap/app.php`

File `app/Http/Kernel.php` tidak ada lagi di Laravel 12 (dan juga Laravel 11) karena struktur folder telah disederhanakan. Konfigurasi middleware sekarang dilakukan di file `bootstrap/app.php`.

Untuk menambahkan Sanctum middleware di grup api, Anda perlu memodifikasi file `bootstrap/app.php` dan menggunakan fungsionalitas konfigurasi middleware yang baru:

1. Buka file `bootstrap/app.php`.
```php
use Illuminate\Foundation\Application;
use Illuminate\Foundation\Configuration\Exceptions;
use Illuminate\Foundation\Configuration\Middleware;
use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;

return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        api: __DIR__.'/../routes/api.php',
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
    )
    ->withMiddleware(function (Middleware $middleware) {
        $middleware->prependToGroup(
            group: 'api',
            middleware: EnsureFrontendRequestsAreStateful::class,
        );
    })
    ->withExceptions(function (Exceptions $exceptions) {
        // ...
    })
    ->create();
```

Secara default, Laravel 12 dengan Sanctum yang terinstall sudah menyertakan `EnsureFrontendRequestsAreStateful::class` di grup api secara otomatis. Jadi, dalam banyak kasus, Anda mungkin tidak perlu melakukan perubahan manual apa pun jika Anda menginstal Sanctum sesuai petunjuk resmi.

Namun, jika Anda perlu memodifikasi atau memastikan middleware tersebut ada, `bootstrap/app.php` adalah lokasi yang tepat di Laravel 12.

## Buat Model & Seeder untuk Admin

### Buat migration:
```bash
php artisan make:model Admin -m
```

### Edit file `backend/database/migrations/create_admins_table.php`:

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('admins', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->string('password');
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('admins');
    }
};
```

### Buat Model Admin Edit file `app/Models/Admin.php`:
```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;

class Admin extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;

    protected $fillable = [
        'name',
        'email',
        'password',
    ];

    protected $hidden = [
        'password',
    ];
}
```

**Penjelasan Baris per Baris:**

- `use HasApiTokens` → mengaktifkan fitur `createToken()` yang disediakan oleh Sanctum yang berfungsi agar model tersebut:
  - Bisa membuat token (`createToken()`)
  - Bisa mengelola token personal access
  - Bisa mengautentikasi via API token bearer

Tanpa trait ini, Laravel tidak mengenali fungsi `createToken()`.

- `extends Authenticatable` → supaya model bisa digunakan untuk autentikasi (login/logout).
- `use HasFactory, Notifiable` → trait tambahan Laravel untuk factory dan notifikasi.
- `$fillable` → menentukan field mana yang bisa diisi via mass assignment.
- `$hidden` → field yang tidak akan muncul di JSON response.

### 3. Pastikan config/auth.php menggunakan model admin untuk guard api

Periksa di: `config/auth.php`

```php
<?php

return [

    /*
    |--------------------------------------------------------------------------
    | Authentication Defaults
    |--------------------------------------------------------------------------
    |
    | This option defines the default authentication "guard" and password
    | reset "broker" for your application. You may change these values
    | as required, but they're a perfect start for most applications.
    |
    */

    'defaults' => [
        'guard' => env('AUTH_GUARD', 'web'),
        'passwords' => env('AUTH_PASSWORD_BROKER', 'users'),
    ],

    /*
    |--------------------------------------------------------------------------
    | Authentication Guards
    |--------------------------------------------------------------------------
    |
    | Next, you may define every authentication guard for your application.
    | Of course, a great default configuration has been defined for you
    | which utilizes session storage plus the Eloquent user provider.
    |
    | All authentication guards have a user provider, which defines how the
    | users are actually retrieved out of your database or other storage
    | system used by the application. Typically, Eloquent is utilized.
    |
    | Supported: "session"
    |
    */

    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'admins',
        ],
        'api' => [
            'driver' => 'sanctum',
            'provider' => 'admins',
        ],
    ],

    /*
    |--------------------------------------------------------------------------
    | User Providers
    |--------------------------------------------------------------------------
    |
    | All authentication guards have a user provider, which defines how the
    | users are actually retrieved out of your database or other storage
    | system used by the application. Typically, Eloquent is utilized.
    |
    | If you have multiple user tables or models you may configure multiple
    | providers to represent the model / table. These providers may then
    | be assigned to any extra authentication guards you have defined.
    |
    | Supported: "database", "eloquent"
    |
    */

    'providers' => [
        'admins' => [
            'driver' => 'eloquent',
            'model' => App\Models\Admin::class,
        ],

        // 'users' => [
        //     'driver' => 'database',
        //     'table' => 'users',
        // ],
    ],

    /*
    |--------------------------------------------------------------------------
    | Resetting Passwords
    |--------------------------------------------------------------------------
    |
    | These configuration options specify the behavior of Laravel's password
    | reset functionality, including the table utilized for token storage
    | and the user provider that is invoked to actually retrieve users.
    |
    | The expiry time is the number of minutes that each reset token will be
    | considered valid. This security feature keeps tokens short-lived so
    | they have less time to be guessed. You may change this as needed.
    |
    | The throttle setting is the number of seconds a user must wait before
    | generating more password reset tokens. This prevents the user from
    | quickly generating a very large amount of password reset tokens.
    |
    */

    'passwords' => [
        'users' => [
            'provider' => 'users',
            'table' => env('AUTH_PASSWORD_RESET_TOKEN_TABLE', 'password_reset_tokens'),
            'expire' => 60,
            'throttle' => 60,
        ],
    ],

    /*
    |--------------------------------------------------------------------------
    | Password Confirmation Timeout
    |--------------------------------------------------------------------------
    |
    | Here you may define the number of seconds before a password confirmation
    | window expires and users are asked to re-enter their password via the
    | confirmation screen. By default, the timeout lasts for three hours.
    |
    */

    'password_timeout' => env('AUTH_PASSWORD_TIMEOUT', 10800),

];
```

### Buat seeder:

```bash
php artisan make:seeder AdminSeeder
```

Edit `database/seeders/AdminSeeder.php`:

```php
<?php
namespace Database\Seeders;

use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;

class AdminSeeder extends Seeder
{
    public function run(): void
    {
        DB::table('admins')->insert([
            'name' => 'Super Admin',
            'email' => 'admin@mail.com',
            'password' => Hash::make('password'),
        ]);
    }
}
```

Tambahkan ke `database/seeders/DatabaseSeeder.php`:

```php
<?php

namespace Database\Seeders;

use App\Models\User;
// use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Faker\Factory as Faker; // Impor class Faker
use Illuminate\Support\Facades\Hash; // Impor Hash untuk password

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        // Panggil semua seeder
        $this->call([
            MemberSeeder::class,
            AuthorSeeder::class,
            PublisherSeeder::class,
            BookSeeder::class,
            LoanSeeder::class,
            AdminSeeder::class
        ]);
        // $this->call(AdminSeeder::class);
    }
}
```

Jalankan:

```bash
php artisan migrate:fresh --seed
```

## Buat Controller untuk Login API

```bash
php artisan make:controller AuthController
```

### Isi file `app/Http/Controllers/AuthController.php`:

```php
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Admin;
use Illuminate\Support\Facades\Hash;

class AuthController extends Controller
{
    // ✅ REGISTER
    public function register(Request $request)
    {
        // Validasi input
        $request->validate([
            'name' => 'required',
            'email' => 'required|email|unique:admins',
            'password' => 'required'
        ]);

        // Buat admin baru
        $admin = Admin::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password)
        ]);

        return response()->json([
            'message' => 'Register success',
            'token' => $admin->createToken('admin-token')->plainTextToken,
            'admin' => $admin
        ]);
    }

    // ✅ LOGIN
    public function login(Request $request)
    {
        // Validasi input
        $request->validate([
            'email' => 'required|email',
            'password' => 'required'
        ]);

        // Cek admin berdasarkan email
        $admin = Admin::where('email', $request->email)->first();

        if (!$admin || !Hash::check($request->password, $admin->password)) {
            return response()->json(['message' => 'Invalid credentials'], 401);
        }

        // Buat token baru
        $token = $admin->createToken('admin-token')->plainTextToken;

        return response()->json([
            'message' => 'Login success',
            'token' => $token,
            'admin' => $admin
        ]);
    }

    // ✅ LOGOUT
    public function logout(Request $request)
    {
        $request->user()->tokens()->delete();
        return response()->json(['message' => 'Logged out']);
    }
}
```

### Penjelasan Baris:

- `Hash::check()` → memeriksa kecocokan password input dengan hash di DB.
- `$admin->createToken()` → membuat token untuk autentikasi.
- Token dikirim kembali ke client untuk disimpan di header Authorization.

### Update Route API (`routes/api.php`)

Tambahkan:

```php
<?php

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;
use Illuminate\Container\Attributes\Auth;
use App\Http\Controllers\AuthorController;
use App\Http\Controllers\MemberController;
use App\Http\Controllers\PublisherController;
use App\Http\Controllers\BookController;
use App\Http\Controllers\LoanController;
use App\Http\Controllers\AuthController;

Route::get('/user', function (Request $request) {
    return $request->user();
})->middleware('auth:sanctum');

Route::post('/login', [AuthController::class, 'login']);

Route::post('/register', [AuthController::class, 'register']);

Route::middleware('auth:sanctum')->group(function () {
    Route::post('/logout', [AuthController::class, 'logout']);
    Route::apiResource('members', MemberController::class);
    Route::apiResource('authors', AuthorController::class);
    Route::apiResource('publishers', PublisherController::class);
    Route::apiResource('books', BookController::class);
    Route::apiResource('loans', LoanController::class);
});
```
---

## ✅ Tes di Postman

# 📚 Dokumentasi REST API - Library Management System

## Informasi Umum

**Base URL:** `http://127.0.0.1:8000/api`

### A. Register Admin

**POST** → `http://localhost:8000/api/login`

Body (JSON):
```json
{
    "name": "adminRegister",
    "email": "adminRegister@mail.com",
    "password": "password123"
}
```

**✅ Hasil:**
```json
{
    "message": "Login success",
    "token": "1|randomlongtokenstring...",
    "admin": {
        "id": 1,
        "name": "adminRegister",
        "email": "adminRegister@mail.com"
    }
}
```
### B. Logout Admin
**Endpoint:** `POST /api/logout`

**Request Headers:**
```
Accept: application/json
Content-Type: application/json
Authorization: Bearer <token>
```

**Response Success (200 OK):**
```json
{
    "message": "Logged Out",
}
```
### c. Login Admin

**POST** → `http://localhost:8000/api/login`

Body (JSON):
```json
{
    "email": "admin@mail.com",
    "password": "password"
}
```

**✅ Hasil:**
```json
{
    "message": "Login success",
    "token": "1|randomlongtokenstring...",
    "admin": {
        "id": 1,
        "name": "Super Admin",
        "email": "admin@mail.com"
    }
}
```
---

## 📋 Daftar Endpoint

### [1. Members (Anggota)](#1️⃣-members-api)
### [2. Authors (Penulis)](2️⃣-AUTHORS-API)
### [3. Publishers (Penerbit)](#3️⃣-publishers-api)
### [4. Books (Buku)](#4️⃣-books-api)
### [5. Loans (Peminjaman)](#5️⃣-loans-api)


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
## 💡 Tips Testing

1. **Simpan token** setelah login untuk digunakan di request berikutnya
2. **Gunakan environment variables** di Postman untuk manage token dan base URL
3. **Test error scenarios**: invalid token, missing fields, invalid IDs
4. **Check response codes**: 200 (success), 201 (created), 401 (unauthorized), 404 (not found)
5. **Validate data relationships**: pastikan book memiliki author dan publisher yang valid
