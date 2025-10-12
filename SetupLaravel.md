# 🚀 Panduan Setup Laravel untuk Pemula

Panduan lengkap instalasi Laravel dari nol untuk pemula, mulai dari instalasi tools hingga membuat project Laravel pertama Anda.

---

## 1. Instalasi XAMPP

XAMPP adalah paket software yang berisi Apache, MySQL, PHP, dan Perl.

### Langkah-langkah:

#### Windows:

1. **Download XAMPP**
   - Kunjungi: https://www.apachefriends.org/
   - Download versi terbaru (minimal PHP 8.1)

2. **Install XAMPP**
   - Jalankan file installer (contoh: `xampp-windows-x64-8.2.12-0-VS16-installer.exe`)
   - Pilih komponen yang akan diinstall (centang: Apache, MySQL, PHP, phpMyAdmin)
   - Pilih lokasi instalasi (default: `C:\xampp`)
   - Klik "Next" hingga selesai

3. **Jalankan XAMPP Control Panel**
   - Buka XAMPP Control Panel
   - Start **Apache** dan **MySQL**
   - Pastikan status berubah menjadi hijau

---

## 2. Instalasi Composer

Composer adalah dependency manager untuk PHP yang digunakan Laravel.

### Windows:

1. **Download Composer**
   - Kunjungi: https://getcomposer.org/download/
   - Download `Composer-Setup.exe`

2. **Install Composer**
   - Jalankan `Composer-Setup.exe`
   - Pilih PHP executable (contoh: `C:\xampp\php\php.exe`)
   - Klik "Next" hingga selesai
   - **Centang** "Add PHP to PATH" jika ada opsi

3. **Restart Terminal/Command Prompt**

---

## 3. Instalasi Node.js & NPM

Node.js dan NPM diperlukan untuk mengelola assets frontend (CSS, JavaScript).

### Windows:

1. **Download Node.js**
   - Kunjungi: https://nodejs.org/
   - Download versi **LTS** (Long Term Support)

2. **Install Node.js**
   - Jalankan file installer (contoh: `node-v20.x.x-x64.msi`)
   - Klik "Next" hingga selesai
   - **Centang** "Automatically install necessary tools"

3. **Restart Terminal/Command Prompt**

---

## 4. Verifikasi Instalasi

Setelah semua tools terinstall, cek apakah sudah terinstall dengan benar.

### Cek PHP:

```bash
php -v
```

**Output yang diharapkan:**
```
PHP 8.2.12 (cli) (built: Oct 24 2023 21:15:15) (NTS Visual C++ 2019 x64)
Copyright (c) The PHP Group
Zend Engine v4.2.12, Copyright (c) Zend Technologies
```

### Cek Composer:

```bash
composer -V
```

**Output yang diharapkan:**
```
Composer version 2.6.5 2023-10-06 10:11:52
```

### Cek Node.js:

```bash
node -v
```

**Output yang diharapkan:**
```
v20.10.0
```

### Cek NPM:

```bash
npm -v
```

**Output yang diharapkan:**
```
10.2.3
```

### Cek MySQL (via XAMPP):
1. Buka Xampp jalankan Klik Start pada Apache dan Mysql
2. Buka browser
3. Akses: http://localhost/phpmyadmin
4. Jika tampil phpMyAdmin, berarti MySQL sudah berjalan

---

## 5. Instalasi Laravel

Laravel dapat diinstall dengan dua cara:

### Menggunakan Composer (Recommended)

```bash
# Buka Terminal
# Pindah ke direktori htdocs XAMPP
cd C:\xampp\htdocs    # Windows
cd /Applications/XAMPP/htdocs    # macOS
cd /opt/lampp/htdocs    # Linux

# Contoh Buat Project:
composer create-project laravel/laravel digital-library
```

**Proses ini akan memakan waktu beberapa menit.**

---

## 7. Menjalankan Laravel

### Langkah 1: Masuk ke Direktori Project

```bash
# setelah Install laravel lalu pindah/masuk ke directory project dengan cara 
cd nama-project
```
Edit file `.env`:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=nama_database
DB_USERNAME=root
DB_PASSWORD=
```

Jalankan migrate untuk membuat data base nya
```
php artisan migrate
```
lalu ketik 'yes' dan enter

### Langkah 6: Jalankan Development Server

```bash
php artisan serve
```

**Output:**
```
INFO  Server running on [http://127.0.0.1:8000].

Press Ctrl+C to stop the server
```

### Langkah 7: Buka Browser

Akses: http://127.0.0.1:8000 atau http://localhost:8000

Jika berhasil, Anda akan melihat halaman welcome Laravel! 🎉

---
