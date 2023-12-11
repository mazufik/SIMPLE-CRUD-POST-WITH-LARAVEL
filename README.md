# LARAVEL RESTFUL API

## Menjalankan Aplikasi

- Clone Github Repository `https://github.com/mazufik/SIMPLE-CRUD-POST-WITH-LARAVEL.git`
- Buka file .env dan setup databasenya
- Buat database sesuai dengan setup di file .env
- Jalankan `php artisan serve` di dalam terminal/cmd
- Uji coba rest api dengan software Postman, Insomnia atau lainnya.

## Tutorial

Tutorial kali ini kita akan belajar membuat restful api sederhana menggunakan framework laravel, laravel yang akan kita gunakan yaitu laravel versi 10.2.5.

## #1: Install Laravel

### Langkah 1 - Membuat Project Laravel

Sebelum membuat project laravel dipastikan teman-teman sudah menginstall composer dan php, php yang direkomendasikan php versi 8.1 ke atas.

Untuk membuat project laravel, buka terminal/cmd kemudian ketikkan perintah berikut:

```bash
 composer create-project --prefer-dist laravel/laravel:^10.0 laravel-api
```

`laravel-api` adalah nama project laravel nya, bisa teman-teman sesuaikan dengan keinginan teman-teman.

### Langkah 2 - Menjalankan Project Laravel

Setelah proses instalasi project laravel selesai, maka langkah selanjutnya adalah menjalankan project tersebut. silahkan teman-teman menjalankan perintah berikut di dalam terminal/cmd.

```bash
 cd laravel-api

 php artisan serve
```

jika berhasil, maka project akan dijalankan di url <http://localhost:8000>. buka browser dan ketikkan url tersebut.

### Langkah 3 - Menjalankan Storage Link

Karena pada tutorial ini kita akan belajar melakukan upload image, maka kita perlu menjalankan perintah storage:link di dalam project.

Perintah tersebut digunakan untuk membuat sebuah link folder, yaitu dari folder `storage/app/public` ke dalam folder `public`.

```bash
 php artisan storage:link
```

## #2: Membuat API Resources

API Resources merupakan layer yang berfungsi untuk melakukan transformasi _Model_ menjadi format **JSON** dengan mudah, cepat dan _scalable_. Dengan memanfaatkan fitur ini, kita bisa membuat format **JSON** dengan sangat terperinci dan tentu saja mempercepat kita dalam waktu pengembangan.

### Langkah 1 - Membuat API Resources

Teman-teman bisa menjalankan perintah berikut ini di dalam terminal/cmd dan pastikan sudah berada di dalam project Laravelnya.

```bash
 php artisan make:resource PostResource
```

Setelah perintah di atas dijalankan maka akan membuat file API Resource baru dengan nama `PostResource.php` di dalam folder `app/Http/Resources/PostResource.php`.

### Langkah 2 - Custom Response JSON

Kita akan membuat response JSON yang berbeda dari bawaan resource nya, maka kita akan menyesuaikan isi file `PostResource.php`. Jadi, teman-teman bisa buka file tersebut.

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class PostResource extends JsonResource
{
    // define properties
    public $status;
    public $message;
    public $resource;

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
            'data' => $this->resource
        ];
    }
}
```

## #3: Membuat Model dan Migration

**_Model_** merupakan salah satu dari arsitektur yang disebut **MVC**. **_Migration_** merupakan _version control_ dari _database_, yaitu digunakan untuk mempermudah dalam pembuatan schema database.

### Langkah 1 - Konfigurasi Koneksi Database

Untuk melakukan konfigurasi database di project Laravel, silahkan teman-teman buka file `.env` kemudian cari kode berikut ini.

```
 DB_DATABASE=laravel
 DB_USERNAME=root
 DB_PASSWORD=
```

Kemudian ubahlah sesuai dengan nama database yang teman-teman inginkan, lalu username dan password yang ada di database mysql teman-teman.

### Langkah 2 - Membuat Database di MySQL

Untuk memudahkan teman-teman bisa menggunakan **PhpMyAdmin** atau software visualisasi database yang lainnya. Pada tuturial ini saya akan menggunakan cli.

```sql
 CREATE DATABASE db_laravel_api;
```

### Langkah 3 - Membuat Model dan Migration

Untuk membuat model di project laravel, teman-teman bisa menjalankan perintah berikut ini di dalam terminal/cmd dan pastikan sudah berada di dalam project Laravelnya.

```bash
 php artisan make:model Post -m
```

Fungsi dari _flag_ `-m` adalah untuk membuat file _Migration_ nya secara otomatis ketika perintah nya di jalankan. Kalau perintah di atas berhasil maka akan menghasilkan 2 buah file yaitu `app/Models/Post.php` dan `database/migrations/2023_12_11_022205_create_posts_table.php`.

### Langkah 4 - Menambahkan Field/Kolom di Migration

Silahkan buka file `database/migrations/2023_12_11_022205_create_posts_table.php` dan pada `function up` ubah menjadi kode berikut.

```php
 public function up(): void
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

### Langkah 5 - Menambahkan Mass Assignment di Model

_Mass Assignment_ digunakan agar fiel-field yang sudah kita tambahkan di atas dapat melakukan proses manipulasi data ke dalam _database_.

Silahkan teman-teman buka file `app/Models/Post.php`, kemudian ubah kodenya menjadi berikut.

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

### Langkah 6 - Menjalankan Proses Migrate

Agar field-field yang sudah kita buat tadi menjadi sebuah tabel dan field di dalam _database_. Maka kita perlu melakukan proses migrate di dalam project Laravel.

Jalankan perintah berikut di terminal/cmd dan pastikan sudah berada di dalam project laravelnya.

```bash
 php artisan migrate
```

Untuk melihat apakah proses migrate kita berhasil atau tidak, teman-teman bisa buka _database_ melalui software visualisasi _database_ dan masuk ke dalam _database_ yang telah kita buat td. Kalau ada tabel _posts_ berarti proses migratenya berhasil.

## #4: Eloquent Accessor

**Laravel** memiliki fitur yang bernama _Mutator_, _Accessor_, dan _Casting_. Fitur-fitur ini digunakan untuk melakukan manipulasi data di dalam _attribute database_ dengan sangat mudah.

**Eloquent Accessor** memungkinkan kita mengubah nilai saat _attribut_ atau _field_ **Eloquent** diakses. Untuk mendefinisikan _Accessor_, kita bisa membuat method di dalam _Model_.

### Langkah 1 - Membuat Accessor untuk Attibute Image di Model Post

Buka file `app/Models/Post.php`, kemudian ubah kodenya menjadi seperti berikut.

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Casts\Attribute;
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

    protected function image(): Attribute
    {
        return Attribute::make(
            get: fn ($image) => asset('/storage/posts/' . $image),
        );
    }
}
```

## #5: Menampilkan Data dari Database

### Langkah 1 - Membuat Controller

Pertama-tama kita akan membuat _controller_ nya terlebih dahulu. di dalam _controller_ ini nantinya kita akan membuat method-method untuk kebutuhan **CRUD**.

Jalankan perintha berikut di dalam terminal/cmd untuk membuat _controller_ di project laravel.

```bash
 php artisan make:controller Api/PostController
```

Buka file tersebut yang berada di folder `app/Http/Controllers/Api/PostController.php`.

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

// import model "Post"
use App\Models\Post;

// import Resource "PostResource"
use App\Http\Resources\PostResource;

class PostController extends Controller
{
    public function index()
    {
        //get all posts
        $posts = Post::latest()->paginate(5);

        // return collection of posts as a resource
        return new PostResource(true, 'List Data Posts', $posts);
    }
}
```

### Langkah 2 - Membuat Route API

Buka file `routes/api.php`, lalu ubah kodenya seperti berikut.

```php
<?php

use Illuminate\Support\Facades\Route;

Route::apiResource('/posts', App\Http\Controllers\Api\PostController::class);
```

Diatas kita menambahkan _route_ baru dengan jenis `apiResource`. Dengan jenis ini, maka _route_ untuk kebutuhan CRUD akan otomatis dibuatkan oleh Laravel.

Untuk memastikan _route_ di atas berhasil ditambahkan, silahkan teman-teman jalankan perintah berikut ini di dalam terminal/cmd.

```bash
 php artisan route:list
```

### Langkah 3 - Uji Coba Rest API Menampilkan Data

Untuk menguji coba Rest API teman-teman bisa menggunakan software Postman, Insomnia atau yang lainnya. disini saya akan menggunakan extention dari vscode yang bernama `rest client`. Kemudian teman-teman bisa mengetikkan `http://localhost:8000/api/posts` dengan method `GET`. Tapi sebelumnya pastikan teman-teman telah menjalankan perintah `php artisan serve` di dalam terminal/cmd untuk menjalankan aplikasi rest apinya.

## #6: Insert Data Kedalam Database

### Langkah 1 - Menambahkan Method Store

Hal pertama yang harus dilakukan adalah menambahkan sebuah method baru di dalam _controller_, method ini yang bertugas melakukan proses _insert_ data ke dalam _database_ melalui rest api.

Buka kembali file `app/Http/Controllers/Api/PostController.php` tambahkan kode berikut di dalamnya, sehingga seperti berikut.

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

// import model "Post"
use App\Models\Post;

// import Resource "PostResource"
use App\Http\Resources\PostResource;

// import Facade "validator"
use Illuminate\Support\Facades\Validator;

class PostController extends Controller
{
    public function index()
    {
        //get all posts
        $posts = Post::latest()->paginate(5);

        // return collection of posts as a resource
        return new PostResource(true, 'List Data Posts', $posts);
    }

    public function store(Request $request)
    {
        // define validation rules
        $validator = Validator::make($request->all(), [
            'image' => 'required|image|mimes:jpeg,png,jpg,gif,svg|max:2048',
            'title' => 'required',
            'content' => 'required',
        ]);

        // check if validation fails
        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        // upload image
        $image = $request->file('image');
        $image->storeAs('public/posts', $image->hashName());

        // create post
        $post = Post::create([
            'image' => $image->hashName(),
            'title' => $request->title,
            'content' => $request->content,
        ]);

        // return response
        return new PostResource(true, 'Data Post Berhasil ditambahkan!', $post);
    }
}
```

## #7: Menampilkan Detail Data

### Langkah 1 - Menambahkan Method Show

Pada tahap ini kita akan menambahkan 1 method baru lagi di dalam _controller_  yang berfungsi untuk menampilkan detail data berdasarkan ID.

Buka kembali file `app/Http/Controllers/Api/PostController.php` tambahkan kode berikut di dalamnya, sehingga seperti berikut.

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

// import model "Post"
use App\Models\Post;

// import Resource "PostResource"
use App\Http\Resources\PostResource;

// import Facade "validator"
use Illuminate\Support\Facades\Validator;

class PostController extends Controller
{
    public function index()
    {
        //get all posts
        $posts = Post::latest()->paginate(5);

        // return collection of posts as a resource
        return new PostResource(true, 'List Data Posts', $posts);
    }

    public function store(Request $request)
    {
        // define validation rules
        $validator = Validator::make($request->all(), [
            'image' => 'required|image|mimes:jpeg,png,jpg,gif,svg|max:2048',
            'title' => 'required',
            'content' => 'required',
        ]);

        // check if validation fails
        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        // upload image
        $image = $request->file('image');
        $image->storeAs('public/posts', $image->hashName());

        // create post
        $post = Post::create([
            'image' => $image->hashName(),
            'title' => $request->title,
            'content' => $request->content,
        ]);

        // return response
        return new PostResource(true, 'Data Post Berhasil ditambahkan!', $post);
    }

    public function show($id)
    {
        // find post by ID
        $post = Post::find($id);

        // return single post as a resource
        return new PostResource(true, 'Detail Data Post!', $post);
    }
}
```

## #8: Update Data ke Dalam Database

### Langkah 1 - Menambahkan Method Update

Pada tahap ini kita akan menambahkan 1 method baru lagi di dalam _controller_, silahkan teman-teman buka kembali file `app/Http/Controllers/Api/PostController.php` tambahkan kode berikut di dalamnya, sehingga seperti berikut.

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

// import model "Post"
use App\Models\Post;

// import Resource "PostResource"
use App\Http\Resources\PostResource;

// import Fascade "Storage"
use Illuminate\Support\Facades\Storage;

// import Facade "Validator"
use Illuminate\Support\Facades\Validator;

class PostController extends Controller
{
    public function index()
    {
        //get all posts
        $posts = Post::latest()->paginate(5);

        // return collection of posts as a resource
        return new PostResource(true, 'List Data Posts', $posts);
    }

    public function store(Request $request)
    {
        // define validation rules
        $validator = Validator::make($request->all(), [
            'image' => 'required|image|mimes:jpeg,png,jpg,gif,svg|max:2048',
            'title' => 'required',
            'content' => 'required',
        ]);

        // check if validation fails
        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        // upload image
        $image = $request->file('image');
        $image->storeAs('public/posts', $image->hashName());

        // create post
        $post = Post::create([
            'image' => $image->hashName(),
            'title' => $request->title,
            'content' => $request->content,
        ]);

        // return response
        return new PostResource(true, 'Data Post Berhasil ditambahkan!', $post);
    }

    public function show($id)
    {
        // find post by ID
        $post = Post::find($id);

        // return single post as a resource
        return new PostResource(true, 'Detail Data Post!', $post);
    }

    public function update(Request $request, $id)
    {
        // define validation rules
        $validator = Validator::make($request->all(), [
            'title' => 'required',
            'content' => 'required',
        ]);

        // check if validation fails
        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        // find post by ID
        $post = Post::find($id);

        // check if image is not empty
        if ($request->hasFile('image')) {
            // upload old image
            $image = $request->file('image');
            $image->storeAs('public/posts', $image->hashName());

            // delete old image
            Storage::delete('public/posts/' . basename($post->image));

            // update post with new image
            $post->update([
                'image' => $image->hashName(),
                'title' => $request->title,
                'content' => $request->content,
            ]);
        } else {
            // update post without image
            $post->update([
                'title' => $request->title,
                'content' => $request->content,
            ]);
        }

        // return response
        return new PostResource(true, 'Data Post Berhasil diubah!', $post);
    }
}
```

## #9: Menghapus Data dari Database

### Langkah 1 - Menambahkan Method Destroy

Sekarang kita akan menambahkan method baru didalam _controller_ untuk proses menghapus data (delete) di _database_ dan file gambar.

Silahkan buka kembali file `app/Http/Controllers/Api/PostController.php`, kemudian ubah semua kodenya menjadi seperti berikut.

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

// import model "Post"
use App\Models\Post;

// import Resource "PostResource"
use App\Http\Resources\PostResource;

// import Fascade "Storage"
use Illuminate\Support\Facades\Storage;

// import Facade "Validator"
use Illuminate\Support\Facades\Validator;

class PostController extends Controller
{
    public function index()
    {
        //get all posts
        $posts = Post::latest()->paginate(5);

        // return collection of posts as a resource
        return new PostResource(true, 'List Data Posts', $posts);
    }

    public function store(Request $request)
    {
        // define validation rules
        $validator = Validator::make($request->all(), [
            'image' => 'required|image|mimes:jpeg,png,jpg,gif,svg|max:2048',
            'title' => 'required',
            'content' => 'required',
        ]);

        // check if validation fails
        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        // upload image
        $image = $request->file('image');
        $image->storeAs('public/posts', $image->hashName());

        // create post
        $post = Post::create([
            'image' => $image->hashName(),
            'title' => $request->title,
            'content' => $request->content,
        ]);

        // return response
        return new PostResource(true, 'Data Post Berhasil ditambahkan!', $post);
    }

    public function show($id)
    {
        // find post by ID
        $post = Post::find($id);

        // return single post as a resource
        return new PostResource(true, 'Detail Data Post!', $post);
    }

    public function update(Request $request, $id)
    {
        // define validation rules
        $validator = Validator::make($request->all(), [
            'title' => 'required',
            'content' => 'required',
        ]);

        // check if validation fails
        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        // find post by ID
        $post = Post::find($id);

        // check if image is not empty
        if ($request->hasFile('image')) {
            // upload old image
            $image = $request->file('image');
            $image->storeAs('public/posts', $image->hashName());

            // delete old image
            Storage::delete('public/posts/' . basename($post->image));

            // update post with new image
            $post->update([
                'image' => $image->hashName(),
                'title' => $request->title,
                'content' => $request->content,
            ]);
        } else {
            // update post without image
            $post->update([
                'title' => $request->title,
                'content' => $request->content,
            ]);
        }

        // return response
        return new PostResource(true, 'Data Post Berhasil diubah!', $post);
    }

    public function destroy($id)
    {
        // find post by ID
        $post = Post::find($id);

        // delete image
        Storage::delete('public/posts/' . basename($post->image));

        // delete post
        $post->delete();

        // return response
        return new PostResource(true, 'Data Post Berhasil dihapus!', null);
    }
}
```
