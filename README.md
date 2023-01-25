# Restful API Laravel 9

Membuat sebuah Restful API di Laravel 9, menggunakan fitur dari Laravel yang ditujukan untuk pembuatan sebuah Rest API, yaitu API Resources.

## Install Laravel 9
Pastikan Composer sudah berhasil terinstall, selanjutnya silahkan masuk ke dalam folder dimana akan menyimpan project Rest API-nya, kemudian jalankan perintah berikut ini di dalam terminal/CMD:
```
composer create-project laravel/laravel:^9.0 laravel-restful-api
```

## Menjalankan Project
Setelah proses installasi selesai, sekarang silahkan jalankan perintah berikut ini di dalam terminal/CMD
```
cd laravel-restful-api
php artisan serve
```
Jika perintah di atas berhasil dijalankan, makan project Laravel tersebut akan berjalan di localhost dengan port `8000`, dan bisa membukanya di dalam browser dengan http://localhost:8000.

## Konfigursai Koneksi Database
Menyiapkan sebuah database untuk menyimpan data secara dinamis. Cara mengatur koneksi database melalui file `.env` lakukan seperti berikut:
Cari kode berikut pada file `.env`
```

DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=
```

Kemudian ubah menjadi seperti berikut ini:
```
DB_DATABASE=db_laravel_api
DB_USERNAME=root
DB_PASSWORD=
```

`DB_DATABASE` diisi nama database yang akan gunakan, `DB_USERNAME` dan `DB_PASSWORD` disesuaikan dengan konfigurasi dari MySQL-nya.

## Membuat Model dan Migration
Membuat Model dan Migration pada laravel, silahkan jalankan perintah berikut ini di dalam terminal/CMD dan pastikan berada di dalam project Laravel.

```
php artisan make:model Post -m
```
Perinatah di atas berhasil maka akan mendapatkan 2 file baru, yaitu:
- app/Models/Post.php
- `database/migrations/2023_01_23_043345_create_posts_table.php`

Selanjutnya buka file `database/migrations/2023_01_23_043345_create_posts_table.php`, pada `function up`, ubah kode menjadi seperti berikut ini:
```
public function up()
{
  Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('image');
    $table->string('title');
    $table->text('content');
    $table->timestamps();
  });
}
```
## Menjalankan Migration
Melakukan proses migrate, yang bertujuan agar field yang sudah dibuat digenerate ke dalam database.
silahkan jalankan perintah berikut ini di dalam terminal/CMD:
```
php artisan migrate
```

## Menambahkan Mass Assigment
Mass Assigment memungkinkan untuk mengizinkan sebuah field dari table agar dapat menyimpan sebuah data.
Silahkan buka file `app/Models/Post.php`, kemudian ubah kode menjadi seperti berikut:
```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    use HasFactory;

    protected $fillable = [
        'image',
        'title',
        'content',
    ];
}
```
Pada kode di atas terdapat properti baru yang bernama `$fillable`. Di dalam properti tersebut berisi field-field yang ada di dalam table `posts`.



