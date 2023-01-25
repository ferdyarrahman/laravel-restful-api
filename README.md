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

## 


