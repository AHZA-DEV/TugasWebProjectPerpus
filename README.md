# Minggu 7 – API Lanjutan (Relasi & Authentication dengan Laravel Sanctum)

# Langkah Praktik

## ❶ Install Laravel Sanctum
```bash
composer require laravel/sanctum
```

## ❷ Menginstal package Sanctum ke dalam project Laravel.

## ❸ Publikasi dan Migrasi
```bash
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

## ❹ Perintah pertama menyalin file konfigurasi & migration Sanctum ke proyek.
## ❺ Perintah kedua membuat tabel personal_access_tokens di database.

## ❻ Aktifkan Middleware Sanctum

**A. Untuk Laravel di bawah 11** ada di `app/Http/Kernel.php`

Tambahkan Sanctum middleware di grup api (biasanya sudah otomatis terpasang):

```php
'api' => [
    \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
    'throttle:api',
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
],
```

**B. Untuk Laravel 11 ke atas** ada di `bootstrap/app.php`

File `app/Http/Kernel.php` tidak ada lagi di Laravel 12 (dan juga Laravel 11) karena struktur folder telah disederhanakan. Konfigurasi middleware sekarang dilakukan di file `bootstrap/app.php`.

Untuk menambahkan Sanctum middleware di grup api, Anda perlu memodifikasi file `bootstrap/app.php` dan menggunakan fungsionalitas konfigurasi middleware yang baru:

1. Buka file `bootstrap/app.php`.
2. Temukan bagian konfigurasi `withMiddleware` dan tambahkan middleware Sanctum ke dalam grup api.

Berikut adalah contoh cara melakukannya:

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

### Edit file `create_admins_table.php`:

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

### Buat Model Admin:

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

Ubah bagian ini:

```php
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
```

Dan di bagian providers:

```php
'providers' => [
    'admins' => [
        'driver' => 'eloquent',
        'model' => App\Models\Admin::class,
    ],
],
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

Tambahkan ke `DatabaseSeeder.php`:

```php
$this->call(AdminSeeder::class);
```

Jalankan:

```bash
php artisan migrate:fresh --seed
```

## Buat Controller untuk Login API

```bash
php artisan make:controller AuthController
```

### Isi file `AuthController.php`:

```php
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Admin;
use Illuminate\Support\Facades\Hash;

class AuthController extends Controller
{
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
use App\Http\Controllers\AuthController;

Route::post('/login', [AuthController::class, 'login']);

Route::middleware('auth:sanctum')->group(function () {
    Route::post('/logout', [AuthController::class, 'logout']);
    Route::apiResource('members', MemberController::class);
    Route::apiResource('authors', AuthorController::class);
    Route::apiResource('publishers', PublisherController::class);
    Route::apiResource('books', BookController::class);
    Route::apiResource('loans', LoanController::class);
});
```

**Penjelasan:**

- `/login` tidak membutuhkan token.
- Endpoint lain dilindungi oleh middleware `auth:sanctum`.

## ✅ Tes di Postman

### A. Login Admin

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

### B. Gunakan Token

Di setiap request berikutnya di Postman, tambahkan pada bagian Authorization:

- **Type:** Bearer Token
- **Token:** `1|randomlongtokenstring...` (isi dengan token dari hasil login tadi)

### C. Tes Endpoint

- `GET /api/books`
- `POST /api/members`
- `DELETE /api/authors/2`
- dll.

Semua hanya bisa diakses setelah login.

## 📌 Latihan Mahasiswa (Tugas/Milestone)

1. Implementasikan autentikasi admin menggunakan Laravel Sanctum.
2. Pastikan semua endpoint CRUD hanya dapat diakses jika login.
3. Simulasikan login dan pengujian CRUD di Postman.

## 📌 Catatan Tambahan

- Sanctum cocok untuk API sederhana (token-based).
- Jika ingin API dengan otorisasi kompleks, bisa gunakan Laravel Passport (OAuth2).
- Token akan tersimpan di tabel personal_access_tokens.
