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
```php
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
```php
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

## Menjalankan Storage link
Secara _default_ Laravel hanya bisa membaca file yang berada di dalam _folder_ `public`, sedangkan untuk menyimpan file-file yang di _upload_ ke dalam _folder_ yang bernama `storage`. Dengan menjalankan perintah _artisan_ `storage:link`, akan secara otomatis membuatkan sebuah link atau _shortcut_ dari _folder_ `storage` ke dalam _folder_ `public.

Silahkan jalankan perintah berikut ini di dalam terminal/CMD:
```
php artisan storage:link
```

## Membuat API Resources
API Resources merupakan sebuah layer yang mentransformasikan data yang di dapatkan dari Model dan mengubahnya menjadi format JSON dengan lebih cepat.

Melakukan generate resources silahkan jalankan perintah berikut ini di dalam terminal/CMD:
```
php artisan make:resource PostResource
```
maka akan mendapatkan 1 file resource baru `app/Http/Resources/PostResource.php`. Selanjutnya dapat melakukan kustomisasi resource seperti berikut ini:
```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class PostResource extends JsonResource
{
    public $status;
    public $message;
    /**
     * Transform the resource into an array.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return array|\Illuminate\Contracts\Support\Arrayable|\JsonSerializable
     */
    public function __construct($status, $message, $resource)
    {
        parent::__construct($resource);
        $this->status = $status;
        $this->message = $message;
    }


    public function toArray($request)
    {
        return [
            'success' => $this->status,
            'message' => $this->message,
            'data' => $this->resource,
        ];
    }
}
```
Menambahkan 2 properti baru, yaitu `$status` dan `$message`.

Pada _method_ `__construct`, dimana fungsi ini akan pertama kali di jalankan ketika _class_ tersebut di panggil, yang di dalamnya parsing 3 _variable_:
- $status : merupakan properti yang mana isinya nanti akan berupa nilai boolean, yaitu __true__ atau __false__.
- $message : merupakan properti yang mana isinya nanti akan berupa pesan tentang hasil yang dibuat.
- $resource : merupakan data yang akan di transformasi, akan berupa data Model yang di kirim dari _Controller_.

Dalam _method_ `toArray` merupakan _return_ ke 3 _variable_.

## Membuat Controller
Jalankan perintah berikut ini dalam terminal/CMD:
```
php artisan make:controller Api/PostController
```
Maka akan terbentuk 1 file controller baru `app/Http/Controllers/Api/PostController.php`.

kemudian ubah kode-nya menjadi seperti berikut:
```php
<?php

namespace App\Http\Controllers\Api;

use App\Models\Post;
use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use App\Http\Resources\PostResource;
use Illuminate\Support\Facades\Validator;
use Illuminate\Support\Facades\Storage;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::latest()->paginate(5);
        return new PostResource(true, 'List data posts', $posts);
    }

    public function store(Request $request)
    {
        //define validation rules
        $validator = Validator::make($request->all(), [
            'image'     => 'required|image|mimes:jpeg,png,jpg,gif,svg|max:2048',
            'title'     => 'required',
            'content'   => 'required',
        ]);

        //check if validation fails
        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        //upload image
        $image = $request->file('image');
        $image->storeAs('public/posts', $image->hashName());

        //create post
        $post = Post::create([
            'image'     => $image->hashName(),
            'title'     => $request->title,
            'content'   => $request->content,
        ]);

        //return response
        return new PostResource(true, 'Data Post Berhasil Ditambahkan!', $post);
    }

    public function show(Post $post)
    {
        //return single post as a resource
        return new PostResource(true, 'Data Post Ditemukan!', $post);
    }

    public function update(Request $request, Post $post)
    {
        //define validation rules
        $validator = Validator::make($request->all(), [
            'title'     => 'required',
            'content'   => 'required',
        ]);

        //check if validation fails
        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        //check if image is not empty
        if ($request->hasFile('image')) {

            //upload image
            $image = $request->file('image');
            $image->storeAs('public/posts', $image->hashName());

            //delete old image
            Storage::delete('public/posts/' . $post->image);

            //update post with new image
            $post->update([
                'image'     => $image->hashName(),
                'title'     => $request->title,
                'content'   => $request->content,
            ]);
        } else {

            //update post without image
            $post->update([
                'title'     => $request->title,
                'content'   => $request->content,
            ]);
        }

        //return response
        return new PostResource(true, 'Data Post Berhasil Diubah!', $post);
    }

    public function destroy(Post $post)
    {
        //delete image
        Storage::delete('public/posts/' . $post->image);

        //delete post
        $post->delete();

        //return response
        return new PostResource(true, 'Data Post Berhasil Dihapus!', null);
    }
}
```

## Membuat Route API Resource
Setelah membuat controller, selanjutnya menambahkan route dengan membuka file `routes/api.php`, kemudian ubah kode-nya menjadi seperti berikut:
```php
<?php

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| API Routes
|--------------------------------------------------------------------------
|
| Here is where you can register API routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| is assigned the "api" middleware group. Enjoy building your API!
|
*/

Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});

Route::apiResource('/posts', App\Http\Controllers\Api\PostController::class);

```

Untuk memastikan route telah berhasil di tambahkan, silahkan jalankan perintah berikut ini di dalam terminal/CMD:

```
php artisan route:list
```

## Uji Coba Rest API
Lakukan uji coba Rest API menggunakan aplikasi Postman, dengan URL berikut http://localhost:8000/api/posts dan untuk method-nya `GET`.
Jika berhasil maka akan menampilkan response data dan code http `200`

