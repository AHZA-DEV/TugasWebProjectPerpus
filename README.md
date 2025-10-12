
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


## 📌 Tugas / Milestone

1. ✅ Buat migration untuk semua tabel (members, books, authors, publishers, loans)
2. ✅ Buat minimal 1 seeder untuk mengisi dummy data
3. ✅ Pastikan migration & seeder berjalan lancar di MySQL
4. ✅ Push kode ke repository GitHub

---


### 2. Migration

Migration adalah blueprint (cetak biru) untuk membuat tabel database dengan perintah Laravel.

**Jalankan perintah satu persatu:**
```bash
php artisan make:model Member -ms
```
```bash
php artisan make:model Author -ms
```
```bash
php artisan make:model Book -ms
```
```bash
php artisan make:model Loan -ms
```
```bash
php artisan make:model Publisher -ms
```

**Terakhit Perintah:**
```bash
php artisan migrate
```


## 📂 Maka Hasil nya menjadi Struktur Project seperti dibawah

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

## 🔹 Migration Files pada folder database/migrations/
isikan code tersebut sesuai pada nama file nya
Ctrl A (Blok semua code) yang ada project kamu lalu paste kan code saya berikan ini semua sesuai pada file di project

### 1. create_members_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('members', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->string('phone');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
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

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('authors', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
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

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('publishers', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
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

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('books', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->foreignId('author_id')->constrained()->onDelete('cascade');
            $table->foreignId('publisher_id')->constrained()->onDelete('cascade');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
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

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('loans', function (Blueprint $table) {
            $table->id();
            $table->foreignId('member_id')->constrained()->onDelete('cascade');
            $table->foreignId('book_id')->constrained()->onDelete('cascade');
            $table->date('loan_date');
            $table->date('return_date')->nullable();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('loans');
    }
};
```

---

## 🔹 Seeder Files pada folder database/seeders/
isikan code tersebut sesuai pada nama file nya
Ctrl A (Blok semua code) yang ada project kamu lalu paste kan code yang saya berikan ini semua sesuai pada file di project
### 1. MemberSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class MemberSeeder extends Seeder
{
    /**
     * Run the database seeds.
     */
    public function run(): void
    {
        //
        DB::table('members')->insert([
            [
                'name' => 'Ali',
                'email' => 'ali@mail.com',
                'phone' => '081234567'
            ],
            [
                'name' => 'Budi',
                'email' => 'budi@mail.com',
                'phone' => '081987654'
            ],
        ]);
    }
}
```

### 2. AuthorSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class AuthorSeeder extends Seeder
{
    /**
     * Run the database seeds.
     */
    public function run(): void
    {
        //
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

use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class PublisherSeeder extends Seeder
{
    /**
     * Run the database seeds.
     */
    public function run(): void
    {
        //
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

use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class BookSeeder extends Seeder
{
    /**
     * Run the database seeds.
     */
    public function run(): void
    {
        //
        DB::table('books')->insert([
            [
                'title' => 'Bumi',
                'author_id' => 1,
                'publisher_id' => 1
            ],
            [
                'title' => 'Laskar Pelangi',
                'author_id' => 2,
                'publisher_id' => 2
            ],
        ]);
    }
}
```

### 5. DatabaseSeeder.php

Daftarkan semua seeder di `DatabaseSeeder.php`:

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
        $faker = Faker::create('id_ID'); // Buat instance Faker dengan locale Indonesia

        $users = [];
        for ($i = 0; $i < 50; $i++) { // Ulangi untuk membuat 50 pengguna
            $users[] = [
                'name' => $faker->name,
                'email' => $faker->unique()->email, // Gunakan unique() untuk memastikan email unik
                'password' => Hash::make('password'), // Gunakan Hash::make() untuk hashing password
                'created_at' => now(),
                'updated_at' => now(),
            ];
        }

        DB::table('users')->insert($users); // Masukkan semua data sekaligus

        // Tugas
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

### Jalankan Migration pada terminal tekan ctrl + `
```bash
php artisan migrate
```

### Jalankan Seeder
```bash
# Jalankan semua seeder
php artisan db:seed
```

