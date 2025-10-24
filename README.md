# 📘 Minggu 6 – REST API di Laravel

## 🛠️ Langkah Praktik

### 1. Persiapan Environment
Pastikan project Laravel sudah berjalan dengan baik dan database terkoneksi.

### 2. Membuat Controller API
```bash
php artisan make:controller MemberController --api
php artisan make:controller AuthorController --api
php artisan make:controller PublisherController --api
php artisan make:controller BookController --api
php artisan make:controller LoanController --api
```

### 3. Konfigurasi Route API
```bash
php artisan install:api
```
Edit file `routes/api.php`

### 4. Implementasi Pembuataab model
#### 1. app/Models/Member.php
```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Member extends Model
{
    use HasFactory;
    
    protected $fillable = ['name', 'email', 'phone'];
    
    public function loans()
    {
        return $this->hasMany(Loan::class);
    }
}
```

#### 2. app/Models/Author.php
```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Author extends Model
{
    use HasFactory;
    
    protected $fillable = ['name'];
    
    public function books()
    {
        return $this->hasMany(Book::class);
    }
}
```

#### 3. app/Models/Publisher.php
```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Publisher extends Model
{
    use HasFactory;
    
    protected $fillable = ['name'];
    
    public function books()
    {
        return $this->hasMany(Book::class);
    }
}
```

#### 4. app/Models/Book.php
```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Book extends Model
{
    use HasFactory;
    
    protected $fillable = ['title', 'author_id', 'publisher_id'];
    
    public function author()
    {
        return $this->belongsTo(Author::class);
    }
    
    public function publisher()
    {
        return $this->belongsTo(Publisher::class);
    }
    
    public function loans()
    {
        return $this->hasMany(Loan::class);
    }
}
```

#### 5. app/Models/Loan.php
```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Loan extends Model
{
    use HasFactory;
    
    protected $fillable = ['member_id', 'book_id', 'loan_date', 'return_date'];
    
    public function member()
    {
        return $this->belongsTo(Member::class);
    }
    
    public function book()
    {
        return $this->belongsTo(Book::class);
    }
}
```

### 5. API Controllers

#### 1.app/Http/Controllers/MemberController.php
```php
<?php
namespace App\Http\Controllers;

use App\Models\Member;
use Illuminate\Http\Request;

class MemberController extends Controller
{
    /**
     * Display a listing of the members.
     */
    public function index()
    {
        $members = Member::all();
        return response()->json($members, 200);
    }

    /**
     * Store a newly created member.
     */
    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:members',
            'phone' => 'required|string|max:15',
        ]);

        $member = Member::create($request->all());
        return response()->json($member, 201);
    }

    /**
     * Display the specified member.
     */
    public function show($id)
    {
        $member = Member::findOrFail($id);
        return response()->json($member, 200);
    }

    /**
     * Update the specified member.
     */
    public function update(Request $request, $id)
    {
        $member = Member::findOrFail($id);
        
        $request->validate([
            'name' => 'sometimes|required|string|max:255',
            'email' => 'sometimes|required|email|unique:members,email,' . $id,
            'phone' => 'sometimes|required|string|max:15',
        ]);

        $member->update($request->all());
        return response()->json($member, 200);
    }

    /**
     * Remove the specified member.
     */
    public function destroy($id)
    {
        Member::destroy($id);
        return response()->json(null, 204);
    }
}
```

#### 2. app/Http/Controllers/AuthorController.php
```php
<?php
namespace App\Http\Controllers;

use App\Models\Author;
use Illuminate\Http\Request;

class AuthorController extends Controller
{
    /**
     * Display a listing of the authors.
     */
    public function index()
    {
        $authors = Author::all();
        return response()->json($authors, 200);
    }

    /**
     * Store a newly created author.
     */
    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|string|max:255',
        ]);

        $author = Author::create($request->all());
        return response()->json($author, 201);
    }

    /**
     * Display the specified author.
     */
    public function show($id)
    {
        $author = Author::findOrFail($id);
        return response()->json($author, 200);
    }

    /**
     * Update the specified author.
     */
    public function update(Request $request, $id)
    {
        $author = Author::findOrFail($id);
        
        $request->validate([
            'name' => 'sometimes|required|string|max:255',
        ]);

        $author->update($request->all());
        return response()->json($author, 200);
    }

    /**
     * Remove the specified author.
     */
    public function destroy($id)
    {
        Author::destroy($id);
        return response()->json(null, 204);
    }
}
```

#### 3. app/Http/Controllers/PublisherController.php
```php
<?php
namespace App\Http\Controllers;

use App\Models\Publisher;
use Illuminate\Http\Request;

class PublisherController extends Controller
{
    /**
     * Display a listing of the publishers.
     */
    public function index()
    {
        $publishers = Publisher::all();
        return response()->json($publishers, 200);
    }

    /**
     * Store a newly created publisher.
     */
    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|string|max:255',
        ]);

        $publisher = Publisher::create($request->all());
        return response()->json($publisher, 201);
    }

    /**
     * Display the specified publisher.
     */
    public function show($id)
    {
        $publisher = Publisher::findOrFail($id);
        return response()->json($publisher, 200);
    }

    /**
     * Update the specified publisher.
     */
    public function update(Request $request, $id)
    {
        $publisher = Publisher::findOrFail($id);
        
        $request->validate([
            'name' => 'sometimes|required|string|max:255',
        ]);

        $publisher->update($request->all());
        return response()->json($publisher, 200);
    }

    /**
     * Remove the specified publisher.
     */
    public function destroy($id)
    {
        Publisher::destroy($id);
        return response()->json(null, 204);
    }
}
```

#### 4. app/Http/Controllers/BookController.php
```php
<?php
namespace App\Http\Controllers;

use App\Models\Book;
use Illuminate\Http\Request;

class BookController extends Controller
{
    /**
     * Display a listing of the books.
     */
    public function index()
    {
        $books = Book::with(['author', 'publisher'])->get();
        return response()->json($books, 200);
    }

    /**
     * Store a newly created book.
     */
    public function store(Request $request)
    {
        $request->validate([
            'title' => 'required|string|max:255',
            'author_id' => 'required|exists:authors,id',
            'publisher_id' => 'required|exists:publishers,id',
        ]);

        $book = Book::create($request->all());
        return response()->json($book->load(['author', 'publisher']), 201);
    }

    /**
     * Display the specified book.
     */
    public function show($id)
    {
        $book = Book::with(['author', 'publisher'])->findOrFail($id);
        return response()->json($book, 200);
    }

    /**
     * Update the specified book.
     */
    public function update(Request $request, $id)
    {
        $book = Book::findOrFail($id);
        
        $request->validate([
            'title' => 'sometimes|required|string|max:255',
            'author_id' => 'sometimes|required|exists:authors,id',
            'publisher_id' => 'sometimes|required|exists:publishers,id',
        ]);

        $book->update($request->all());
        return response()->json($book->load(['author', 'publisher']), 200);
    }

    /**
     * Remove the specified book.
     */
    public function destroy($id)
    {
        Book::destroy($id);
        return response()->json(null, 204);
    }
}
```

#### 5. app/Http/Controllers/LoanController.php
```php
<?php
namespace App\Http\Controllers;

use App\Models\Loan;
use Illuminate\Http\Request;

class LoanController extends Controller
{
    /**
     * Display a listing of the loans.
     */
    public function index()
    {
        $loans = Loan::with(['member', 'book'])->get();
        return response()->json($loans, 200);
    }

    /**
     * Store a newly created loan.
     */
    public function store(Request $request)
    {
        $request->validate([
            'member_id' => 'required|exists:members,id',
            'book_id' => 'required|exists:books,id',
            'loan_date' => 'required|date',
            'return_date' => 'nullable|date',
        ]);

        $loan = Loan::create($request->all());
        return response()->json($loan->load(['member', 'book']), 201);
    }

    /**
     * Display the specified loan.
     */
    public function show($id)
    {
        $loan = Loan::with(['member', 'book'])->findOrFail($id);
        return response()->json($loan, 200);
    }

    /**
     * Update the specified loan.
     */
    public function update(Request $request, $id)
    {
        $loan = Loan::findOrFail($id);
        
        $request->validate([
            'member_id' => 'sometimes|required|exists:members,id',
            'book_id' => 'sometimes|required|exists:books,id',
            'loan_date' => 'sometimes|required|date',
            'return_date' => 'nullable|date',
        ]);

        $loan->update($request->all());
        return response()->json($loan->load(['member', 'book']), 200);
    }

    /**
     * Remove the specified loan.
     */
    public function destroy($id)
    {
        Loan::destroy($id);
        return response()->json(null, 204);
    }
}
```

### 6. API Routes Configuration

#### routes/api.php
```php
<?php

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\MemberController;
use App\Http\Controllers\AuthorController;
use App\Http\Controllers\PublisherController;
use App\Http\Controllers\BookController;
use App\Http\Controllers\LoanController;

/*
|--------------------------------------------------------------------------
| API Routes
|--------------------------------------------------------------------------
|
| Here is where you can register API routes for your application. These
| routes are loaded by the RouteServiceProvider and all of them will
| be assigned to the "api" middleware group. Make something great!
|
*/

// API Resource Routes
Route::apiResource('members', MemberController::class);
Route::apiResource('authors', AuthorController::class);
Route::apiResource('publishers', PublisherController::class);
Route::apiResource('books', BookController::class);
Route::apiResource('loans', LoanController::class);

// Health check endpoint
Route::get('/health', function () {
    return response()->json([
        'status' => 'OK',
        'message' => 'API is running successfully',
        'timestamp' => now()
    ]);
});
```

## 🔧 Testing API

### Menjalankan Server
```bash
php artisan serve
```
---
### 📋 Daftar Endpoint API
---
#### Members
- **GET** `http://127.0.0.1:8000/api/members` - Get all members
- **POST** `http://127.0.0.1:8000/api/members` - Create new member
   #### Create Member
    ```json
    {
        "name": "Siti Nurhaliza",
        "email": "siti@email.com",
        "phone": "081234567890"
    }
    ```
- **GET** `http://127.0.0.1:8000/api/members/{id}` - Get specific member
- **PUT** `http://127.0.0.1:8000/api/members/{id}` - Update member
- **DELETE** `http://127.0.0.1:8000/api/members/{id}` - Delete member

---

#### Authors
- **GET** `http://127.0.0.1:8000/api/authors` - Get all authors
- **POST** `http://127.0.0.1:8000/api/authors` - Create new author
      #### Create Author
    ```json
    {
        "name": "Andrea Hirata"
    }
    ```
- **GET** `http://127.0.0.1:8000/api/authors/{id}` - Get specific author
- **PUT** `http://127.0.0.1:8000/api/authors/{id}` - Update author
- **DELETE** `http://127.0.0.1:8000/api/authors/{id}` - Delete author

---

#### Publishers
- **GET** `http://127.0.0.1:8000/api/publishers` - Get all publishers
- **POST** `http://127.0.0.1:8000/api/publishers` - Create new publisher
      #### Create Publisher
    ```json
    {
        "name": "Gramedia Pustaka Utama"
    }
    ```
- **GET** `http://127.0.0.1:8000/api/publishers/{id}` - Get specific publisher
- **PUT** `http://127.0.0.1:8000/api/publishers/{id}` - Update publisher
- **DELETE** `http://127.0.0.1:8000/api/publishers/{id}` - Delete publisher

---

#### Books
- **GET** `http://127.0.0.1:8000/api/books` - Get all books
- **POST** `http://127.0.0.1:8000/api/books` - Create new book
      #### Create Book
    ```json
    {
        "title": "Laskar Pelangi",
        "author_id": 1,
        "publisher_id": 1
    }
    ```
- **GET** `http://127.0.0.1:8000/api/books/{id}` - Get specific book
- **PUT** `http://127.0.0.1:8000/api/books/{id}` - Update book
- **DELETE** `http://127.0.0.1:8000/api/books/{id}` - Delete book

---

#### Loans
- **GET** `http://127.0.0.1:8000/api/loans` - Get all loans
- **POST** `http://127.0.0.1:8000/api/loans` - Create new loan
      #### Create Loan
    ```json
    {
        "member_id": 1,
        "book_id": 1,
        "loan_date": "2024-10-24",
        "return_date": null
    }
    ```
- **GET** `http://127.0.0.1:8000/api/loans/{id}` - Get specific loan
- **PUT** `http://127.0.0.1:8000/api/loans/{id}` - Update loan
- **DELETE** `http://127.0.0.1:8000/api/loans/{id}` - Delete loan

---


## 📊 Response Codes

- **200** - Success (GET, PUT)
- **201** - Created (POST)
- **204** - No Content (DELETE)
- **400** - Bad Request
- **404** - Not Found
- **422** - Validation Error
- **500** - Server Error

## ✅ Kesimpulan

1. ✅ Berhasil membuat REST API lengkap untuk sistem manajemen perpustakaan
2. ✅ Implementasi CRUD operations untuk semua entitas
3. ✅ Validasi input data yang robust
4. ✅ Response JSON yang konsisten
5. ✅ Relasi antara model sudah terdefinisi dengan baik
6. ✅ Siap untuk testing menggunakan Postman

## 📋 Tugas / Milestone

1. ✅ Membuat API untuk semua entitas (Member, Author, Publisher, Book, Loan)
2. ✅ Semua endpoint CRUD berfungsi dengan baik
3. ✅ Response JSON valid dan konsisten
4. 📸 Screenshot hasil testing di Postman untuk laporan

---
