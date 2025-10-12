
# 📘 Modul Minggu 5 – Laravel: Migration & Model

## 🎯 Tujuan Pembelajaran

1. Mahasiswa memahami struktur MVC pada Laravel
2. Mahasiswa mampu membuat migration, model, dan seeder
3. Mahasiswa dapat menjalankan migrasi database MySQL di Laravel

---

## 📖 Materi Teori

### 1. Struktur Laravel (MVC)

Laravel menggunakan arsitektur MVC (Model–View–Controller):

- **Model** → representasi data dari tabel di database (misalnya `Book.php`, `Member.php`)
- **View** → tampilan (Blade Template atau frontend Vue.js, nanti dipisahkan)
- **Controller** → penghubung logika bisnis antara model dan view

#### Struktur folder utama:
```
app/
├── Models/              # Model database
└── Http/
    └── Controllers/
database/
├── migrations/          # File migration
└── seeders/            # File seeder
```

### 2. Migration

Migration adalah blueprint (cetak biru) untuk membuat tabel database dengan perintah Laravel.

**Perintah:**
```bash
php artisan make:migration create_books_table
php artisan migrate
```

### 3. Seeder

Seeder digunakan untuk mengisi tabel dengan dummy data awal.

**Perintah:**
```bash
php artisan db:seed
```

### 4. Model

Model merepresentasikan tabel di database.

**Contoh:** `Book.php` untuk tabel `books`

**Perintah:**
```bash
php artisan make:model Book
```

---

## 🛠️ Langkah Praktik

### 1. Konfigurasi Database di .env

Edit file `.env`:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=digital_library
DB_USERNAME=root
DB_PASSWORD=
```

### 2. Buat Migration

Buat migration untuk tabel: anggota, buku, author, penerbit, peminjaman.

```bash
php artisan make:migration create_members_table
php artisan make:migration create_authors_table
php artisan make:migration create_publishers_table
php artisan make:migration create_books_table
php artisan make:migration create_loans_table
```

### 3. Buat Model

```bash
php artisan make:model Member
php artisan make:model Author
php artisan make:model Publisher
php artisan make:model Book
php artisan make:model Loan
```

### 4. Buat Seeder

```bash
php artisan make:seeder MemberSeeder
php artisan make:seeder AuthorSeeder
php artisan make:seeder PublisherSeeder
php artisan make:seeder BookSeeder
```

---

## 📂 Struktur Project

```
database/
├── migrations/
│   ├── 2025_xx_xx_create_members_table.php
│   ├── 2025_xx_xx_create_authors_table.php
│   ├── 2025_xx_xx_create_publishers_table.php
│   ├── 2025_xx_xx_create_books_table.php
│   └── 2025_xx_xx_create_loans_table.php
└── seeders/
    ├── MemberSeeder.php
    ├── AuthorSeeder.php
    ├── PublisherSeeder.php
    ├── BookSeeder.php
    └── DatabaseSeeder.php
```

---

## 🔹 Migration Files

### 1. create_members_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void {
        Schema::create('members', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->string('phone');
            $table->timestamps();
        });
    }

    public function down(): void {
        Schema::dropIfExists('members');
    }
};
```

### 2. create_authors_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void {
        Schema::create('authors', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }

    public function down(): void {
        Schema::dropIfExists('authors');
    }
};
```

### 3. create_publishers_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void {
        Schema::create('publishers', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }

    public function down(): void {
        Schema::dropIfExists('publishers');
    }
};
```

### 4. create_books_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void {
        Schema::create('books', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->foreignId('author_id')->constrained()->onDelete('cascade');
            $table->foreignId('publisher_id')->constrained()->onDelete('cascade');
            $table->timestamps();
        });
    }

    public function down(): void {
        Schema::dropIfExists('books');
    }
};
```

### 5. create_loans_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void {
        Schema::create('loans', function (Blueprint $table) {
            $table->id();
            $table->foreignId('member_id')->constrained()->onDelete('cascade');
            $table->foreignId('book_id')->constrained()->onDelete('cascade');
            $table->date('loan_date');
            $table->date('return_date')->nullable();
            $table->timestamps();
        });
    }

    public function down(): void {
        Schema::dropIfExists('loans');
    }
};
```

---

## 🔹 Seeder Files

### 1. MemberSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class MemberSeeder extends Seeder {
    public function run(): void {
        DB::table('members')->insert([
            ['name' => 'Ali', 'email' => 'ali@mail.com', 'phone' => '081234567'],
            ['name' => 'Budi', 'email' => 'budi@mail.com', 'phone' => '081987654'],
        ]);
    }
}
```

### 2. AuthorSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class AuthorSeeder extends Seeder {
    public function run(): void {
        DB::table('authors')->insert([
            ['name' => 'Tere Liye'],
            ['name' => 'Andrea Hirata'],
        ]);
    }
}
```

### 3. PublisherSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class PublisherSeeder extends Seeder {
    public function run(): void {
        DB::table('publishers')->insert([
            ['name' => 'Gramedia'],
            ['name' => 'Mizan'],
        ]);
    }
}
```

### 4. BookSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class BookSeeder extends Seeder {
    public function run(): void {
        DB::table('books')->insert([
            ['title' => 'Bumi', 'author_id' => 1, 'publisher_id' => 1],
            ['title' => 'Laskar Pelangi', 'author_id' => 2, 'publisher_id' => 2],
        ]);
    }
}
```

### 5. DatabaseSeeder.php

Daftarkan semua seeder di `DatabaseSeeder.php`:

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder {
    public function run(): void {
        $this->call([
            MemberSeeder::class,
            AuthorSeeder::class,
            PublisherSeeder::class,
            BookSeeder::class,
        ]);
    }
}
```

---

## 🚀 Menjalankan Migration & Seeder

### Jalankan Migration
```bash
php artisan migrate
```

### Jalankan Seeder
```bash
# Jalankan semua seeder
php artisan db:seed

# Atau jalankan seeder spesifik
php artisan db:seed --class=MemberSeeder
```

### Jalankan Migration Fresh + Seeder (Reset Database)
```bash
php artisan migrate:fresh --seed
```

---

## 📌 Tugas / Milestone

1. ✅ Buat migration untuk semua tabel (members, books, authors, publishers, loans)
2. ✅ Buat minimal 1 seeder untuk mengisi dummy data
3. ✅ Pastikan migration & seeder berjalan lancar di MySQL
4. ✅ Push kode ke repository GitHub

---

## 📝 Catatan Penting

- Pastikan database `digital_library` sudah dibuat di MySQL sebelum menjalankan migration
- Urutan migration penting karena ada foreign key constraints
- Seeder harus dijalankan setelah migration berhasil
- Gunakan `php artisan migrate:fresh --seed` untuk reset database dan isi ulang data

---

## 🔗 Referensi

- [Laravel Migration Documentation](https://laravel.com/docs/migrations)
- [Laravel Seeding Documentation](https://laravel.com/docs/seeding)
- [Laravel Eloquent Models](https://laravel.com/docs/eloquent)

---

**Happy Coding! 🚀**
