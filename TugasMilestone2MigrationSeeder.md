---
# 📘 Modul Minggu 5 – Laravel: Migration & Model

## 📖 Tugas Milestone Web-Framework
1. ✅ Buat migration untuk semua tabel (members, books, authors, publishers, loans)
2. ✅ Buat minimal 1 seeder untuk mengisi dummy data
3. ✅ Pastikan migration & seeder berjalan lancar di MySQL
4. ✅ Push kode ke repository GitHub

---


### IKUTI LANGKAH BERIKUT

Migration adalah blueprint (cetak biru) untuk membuat tabel database dengan perintah Laravel.

**Jalankan perintah diterminal:**
```bash
php artisan make:model Member -ms
```

---
### Paste kan di database/migrations/ sesuaikan dengan nama di migration nya
### - create_members_table.php

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

### Paste kan di database/seeders/ sesuaikan dengan nama di Seeder nya
### - MemberSeeder.php

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


**Terakhit Perintah diterminal:**
```bash
php artisan migrate
```
```bash
php artisan db:seed --class=MemberSeeder
```
---
###=======================================================================================

Migration adalah blueprint (cetak biru) untuk membuat tabel database dengan perintah Laravel.

**Jalankan perintah satu persatu:**
```bash
php artisan make:model Author -ms
```

---
### Paste kan di database/migrations/ sesuaikan dengan nama di migration nya
### - create_authors_table.php

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
---

### Paste kan di database/seeders/ sesuaikan dengan nama di Seeder nya
### - AuthorSeeder.php

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
**Terakhit Perintah:**
```bash
php artisan migrate
```
```bash
php artisan db:seed --class=AuthorSeeder
```
---
###=====================================================================================


Migration adalah blueprint (cetak biru) untuk membuat tabel database dengan perintah Laravel.

**Jalankan perintah diterminal:**
```bash
php artisan make:model Publisher -ms
```
### Paste kan di database/migrations/ sesuaikan dengan nama di migration nya
### - create_publishers_table.php

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
---

### Paste kan di database/seeders/ sesuaikan dengan nama di Seeder nya
### - PublisherSeeder.php

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


**Terakhit Perintah diterminal:**
```bash
php artisan migrate
```
```bash
php artisan db:seed --class=PublisherSeeder
```
---
###=====================================================================================


Migration adalah blueprint (cetak biru) untuk membuat tabel database dengan perintah Laravel.

**Jalankan perintah diterminal:**
```bash
php artisan make:model Book -ms
```
### Paste kan di database/migrations/ sesuaikan dengan nama di migration nya
### - create_books_table.php

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
---

### Paste kan di database/seeders/ sesuaikan dengan nama di Seeder nya
### - BookSeeder.php

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


**Terakhit Perintah diterminal:**
```bash
php artisan migrate
```
```bash
php artisan db:seed --class=BookSeeder
```
---
###=====================================================================================

Migration adalah blueprint (cetak biru) untuk membuat tabel database dengan perintah Laravel.

**Jalankan perintah diterminal:**
```bash
php artisan make:model Loan -ms
```
### Paste kan di database/migrations/ sesuaikan dengan nama di migration nya
### - create_loans_table.php

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

### Paste kan di database/seeders/ sesuaikan dengan nama di Seeder nya
### - DatabaseSeeder.php

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

**Terakhit Perintah diterminal:**
```bash
php artisan migrate
```
---
