# Laravel MVC (Model, View, Controller)
Repository ini dibuat untuk mengimplementasikan framework ``laravel`` dengan menggunakan arsitetur ``Laravel``

## Daftar Isi
- [Setup Clone](#setup-clone)
- [Setup From Zero](#setup-from-zero)

### Setup Clone
Pertama lakukan perintah berikut untuk melakukan cloning di github 
```
git clone https://github.com/Caknoooo/ams-laravel-mvc.git
```

Jangan lupa untuk menjalankan perintah berikut untuk mendownload dependencies-dependencies yang dibutuhkan
```
composer install
```

Setelah itu lakukan beberapa konfigurasi sebagai berikut
```
cp .env.example .env

SETUP SESUAI DENGAN DATABASE MYSQL pada file .env

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel-mvc
DB_USERNAME=root
DB_PASSWORD=
```

Lalu nyalakan ``XAMPP`` dan jalankan berikut 

![image](https://github.com/Caknoooo/ams-laravel-restful-api/assets/92671053/0098acc5-0603-4b62-956d-dcb3d0570291)


Setelah itu jalankan perintah berikut 
```
php artisan migrate
php artisan key:generate
php artisan config:clear
php artisan config:cache
php artisan serve
```

Hasilnya adalah sebagai berikut 

![image](https://github.com/Caknoooo/ams-laravel-restful-api/assets/92671053/c2574f9b-7d00-4476-9ced-3cdb32f8642b)

Jika ``teman-teman`` mencoba untuk membuka endpoint berikut ``http://127.0.0.1:8000/product/create`` maka hasilnya akan sebagai berikut 

![image](https://github.com/Caknoooo/ams-laravel-mvc/assets/92671053/5a1a08c7-dfe7-40a5-bb1a-96b4dcc42943)


Teman-teman bisa juga untuk mencoba beberapa endpoint yang telah disediakan pada file ``routes/web.php``

### Setup From Zero
Untuk memulai dari awal, ``anda`` perlu untuk melakukan setup laravel sebagai berikut
```
composer create-project laravel/laravel laravel-mvc
```

Setelah itu jangan lupa untuk membuka directory ``laravel-mvc`` di teks editor kalian

Setelah itu buka file ``.env`` dan ganti konfigurasi ``Database`` kalian sebagai berikut (Sesuaikan dengan ``user`` database kalian)
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel-mvc
DB_USERNAME=root
DB_PASSWORD=
```

Setelah itu jalankan perintah berikut 
```
php artisan make:migration create_products_table
php artisan make:model Product
php artisan make:controller ProductController
```

Lalu pada file **migration** yang telah dibuat sebelumnya pada folder ``database > migration > nama_file`` pada ``function up`` gantilah sebagai berikut 
```php
public function up(): void
{
    Schema::create('products', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->integer('qty');
        $table->decimal('price', 10, 2);
        $table->text('description')->nullable();
        $table->timestamps();
    });
}
```

Setelah itu pada file **model** yang telah dibuat sebelumnya pada folder ``app > Models > nama_file`` gantilah sebagai berikut 
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    use HasFactory;

    protected $fillable =
    [
        'name',
        'qty',
        'price',
        'description'
    ];
}
```

Setelah itu pada file **controller** yang telah dibuat sebelumnya pada folder ``app > Http > Controllers > nama_file`` gantilah sebgai berikut 

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Product;

class ProductController extends Controller
{
    public function index()
    {
        $products = Product::all();
        return view('products.index', ['products' => $products]);
    }

    public function create()
    {
        return view('products.create');
    }

    public function store(Request $request)
    {
        $data = $request->validate([
            'name' => 'required',
            'qty' => 'required|numeric',
            'price' => 'required|decimal:0,2',
            'description' => 'nullable'
        ]);

        $newProduct = Product::create($data);

        return redirect(route('product.index'));
    }

    public function edit(Product $product)
    {
        return view('products.edit', ['product' => $product]);
    }

    public function update(Product $product, Request $request)
    {
        $data = $request->validate([
            'name' => 'required',
            'qty' => 'required|numeric',
            'price' => 'required|decimal:0,2',
            'description' => 'nullable'
        ]);

        $product->update($data);

        return redirect(route('product.index'))->with('success', 'Product Updated Succesffully');
    }

    public function destroy(Product $product)
    {
        $product->delete();
        return redirect(route('product.index'))->with('success', 'Product deleted Succesffully');
    }
}
```

Terdapat beberapa ``function`` CRUD (Create, Read, Update, Delete) pada file tersebut yang dapat digunakan

#### View
Setelah selesai membuat ``Model`` dan ``Controller``, saatnya kita membuat ``view`` atau tampilan pada web yang akan diintegrasikan dengan ``Model`` dan ``Controller`` sebelumnya.

Kita akan menambahkan file tampilan kita pada directory ``resources > views``. Buatlah folder dengan nama ``products`` yang akan kita jadikan folder untuk menyimpan file tampilan kita.

Setelah itu, marilah kita memulai dengan membuat file ``create.blade.php``

Lalu isilah sebagai berikut 
```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Create</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        form {
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            padding: 20px;
            width: 300px;
        }

        h1 {
            text-align: center;
            color: #333;
        }

        label {
            display: block;
            margin-bottom: 8px;
            color: #555;
        }

        input,
        textarea {
            width: 100%;
            padding: 8px;
            margin-bottom: 16px;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }

        input[type="submit"] {
            background-color: #4caf50;
            color: #fff;
            cursor: pointer;
            font-size: 16px;
            padding: 10px;
            border: none;
            border-radius: 4px;
        }

        input[type="submit"]:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <form action="{{ route('product.store') }}" method="post">
        @csrf
        @method('POST')
        <h1>Create a Product</h1>
        <div>
            @if($errors->any())
            <ul>
                @foreach($errors->all() as $error)
                    <li>{{$error}}</li>
                @endforeach
            </ul>
            @endif
        </div>
        <div>
            <label for="name">Name</label>
            <input type="text" name="name" id="name" placeholder="Enter Your Name" required>
        </div>
        <div>
            <label for="qty">QTY</label>
            <input type="number" name="qty" id="qty" placeholder="Qty" required>
        </div>
        <div>
            <label for="price">Price</label>
            <input type="number" name="price" id="price" placeholder="Price" required>
        </div>
        <div>
            <label for="description">Description</label>
            <textarea name="description" id="description" cols="30" rows="10" required></textarea>
        </div>
        <div>
            <input type="submit" value="Save a Product" />
        </div>
    </form>
</body>
</html>
```

Setelah itu buatlah file ``edit.blade.php``
```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Edit Product</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        form {
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            padding: 20px;
            width: 300px;
        }

        h1 {
            text-align: center;
            color: #333;
        }

        label {
            display: block;
            margin-bottom: 8px;
            color: #555;
        }

        input {
            width: 100%;
            padding: 8px;
            margin-bottom: 16px;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }

        input[type="submit"] {
            background-color: #4caf50;
            color: #fff;
            cursor: pointer;
            font-size: 16px;
            padding: 10px;
            border: none;
            border-radius: 4px;
        }

        input[type="submit"]:hover {
            background-color: #45a049;
        }

        ul {
            list-style: none;
            padding: 0;
            margin: 0;
            color: #ff0000;
        }
    </style>
</head>
<body>
    <form method="post" action="{{route('product.update', ['product' => $product])}}">
        @csrf
        @method('put')
        <h1>Edit a Product</h1>
        <div>
            @if($errors->any())
            <ul>
                @foreach($errors->all() as $error)
                    <li>{{$error}}</li>
                @endforeach
            </ul>
            @endif
        </div>
        <div>
            <label for="name">Name</label>
            <input type="text" name="name" placeholder="Name" value="{{$product->name}}" required>
        </div>
        <div>
            <label for="qty">Qty</label>
            <input type="text" name="qty" placeholder="Qty" value="{{$product->qty}}" required>
        </div>
        <div>
            <label for="price">Price</label>
            <input type="text" name="price" placeholder="Price" value="{{$product->price}}" required>
        </div>
        <div>
            <label for="description">Description</label>
            <input type="text" name="description" placeholder="Description" value="{{$product->description}}" required>
        </div>
        <div>
            <input type="submit" value="Update" />
        </div>
    </form>
</body>
</html>
```

Setelah itu buatlah file ``index.blade.php``
```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Product List</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 20px;
        }

        .success-message {
            background-color: #4caf50;
            color: #fff;
            padding: 10px;
            text-align: center;
            margin-bottom: 20px;
        }

        .create-link {
            margin-bottom: 20px;
        }

        table {
            width: 80%;
            border-collapse: collapse;
            margin-bottom: 20px;
        }

        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }

        th {
            background-color: #4caf50;
            color: #fff;
        }

        td a {
            text-decoration: none;
            color: #333;
            margin-right: 10px;
        }

        td form {
            display: inline-block;
        }

        td form input[type="submit"] {
            background-color: #f44336;
            color: #fff;
            border: none;
            padding: 5px 10px;
            cursor: pointer;
        }

        td form input[type="submit"]:hover {
            background-color: #d32f2f;
        }
    </style>
</head>
<body>
    <h1>Product</h1>
    <div>
        @if(session()->has('success'))
            <div class="success-message">
                {{session('success')}}
            </div>
        @endif
    </div>
    <div class="create-link">
        <a href="{{route('product.create')}}">Create a Product</a>
    </div>
    <table>
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Qty</th>
            <th>Price</th>
            <th>Description</th>
            <th>Edit</th>
            <th>Delete</th>
        </tr>
        @foreach($products as $product )
            <tr>
                <td>{{$product->id}}</td>
                <td>{{$product->name}}</td>
                <td>{{$product->qty}}</td>
                <td>{{$product->price}}</td>
                <td>{{$product->description}}</td>
                <td>
                    <a href="{{route('product.edit', ['product' => $product])}}">Edit</a>
                </td>
                <td>
                    <form method="post" action="{{route('product.destroy', ['product' => $product])}}">
                        @csrf
                        @method('delete')
                        <input type="submit" value="Delete" />
                    </form>
                </td>
            </tr>
        @endforeach
    </table>
</body>
</html>
```

Terakhir, kita ganti seluruh ``router`` kita menuju ke ``controller`` yang nantinya ``controller`` akan melakukan bisnis dengan ``database`` dan controller akan mengembalikan hasil bisnis tersebut ke dalam ``views``. Sekarang buka file ``routes > web.php`` dan gantilah sebagai berikut 

```php
<?php

use App\Http\Controllers\ProductController;
use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider and all of them will
| be assigned to the "web" middleware group. Make something great!
|
*/

Route::get('/', function () {
    return view('welcome');
});

Route::get('/product', [ProductController::class, 'index'])->name('product.index');
Route::get('/product/create', [ProductController::class, 'create'])->name('product.create');
Route::post('/product', [ProductController::class, 'store'])->name('product.store');
Route::get('/product/{product}/edit', [ProductController::class, 'edit'])->name('product.edit');
Route::put('/product/{product}/update', [ProductController::class, 'update'])->name('product.update');
Route::delete('/product/{product}/destroy', [ProductController::class, 'destroy'])->name('product.destroy');
```
