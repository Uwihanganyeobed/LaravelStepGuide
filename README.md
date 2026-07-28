# 🚀 Laravel 9 CRUD Application Guide

A beginner-friendly step-by-step guide to building a **CRUD (Create, Read, Update, Delete)** application in **Laravel 9** using **MySQL**, **Blade Templates**, and the **MVC (Model-View-Controller)** architecture.

---

## 📋 Prerequisites

Before you begin, make sure you have installed:

- PHP 8.x
- Composer
- XAMPP (Apache & MySQL)
- Visual Studio Code
- Laravel 9

---

# Step 1: Install Composer

Download and install Composer.

👉 https://getcomposer.org/

Verify installation:

```bash
composer --version
```

---

# Step 2: Create a New Laravel Project

Open VS Code.

Open a terminal inside the folder where you want to create the project.

Run:

```bash
composer create-project laravel/laravel newapp "9.4.*"
```

Example:

```bash
composer create-project laravel/laravel inventory "9.4.*"
```

Wait until the installation completes.

---

# Step 3: Navigate to the Project

```bash
cd newapp
```

---

# Step 4: Run the Application

Start Laravel's development server.

```bash
php artisan serve
```

Open your browser and visit:

```
http://127.0.0.1:8000
```

If everything is working correctly, you'll see the Laravel welcome page.

---

# Step 5: Create a MySQL Database

Start **Apache** and **MySQL** from XAMPP.

Open **phpMyAdmin**.

Create a new database.

Example:

```
inventory_db
```

---

# Step 6: Connect Laravel to MySQL

Open the **.env** file and update your database credentials.

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=inventory_db
DB_USERNAME=root
DB_PASSWORD=
```

Save the file.

---

# Step 7: Run Default Migrations

Laravel comes with several default tables.

Create them using:

```bash
php artisan migrate
```

Default tables include:

- users
- password_reset_tokens
- failed_jobs
- personal_access_tokens

---

# Step 8: Create Your Own Table (Migration)

Generate a migration for your application's table.

Example:

```bash
php artisan make:migration create_products_table
```

The migration file will be created inside:

```
database/
└── migrations/
```

Laravel Documentation:

https://laravel.com/docs/13.x/migrations#generating-migrations

---

# Step 9: Define the Table Schema

Open the generated migration file.

Replace the `up()` method with:

```php
public function up()
{
    Schema::create('products', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->integer('qty');
        $table->decimal('price', 10, 2);
        $table->text('description');
        $table->timestamps();
    });
}
```

### Common Laravel Column Types

```php
$table->string();
$table->integer();
$table->decimal();
$table->double();
$table->float();
$table->boolean();
$table->text();
$table->longText();
$table->date();
$table->dateTime();
$table->timestamps();
```

More column types:

https://laravel.com/docs/13.x/migrations#available-column-types

---

# Step 10: Apply the Migration

Run:

```bash
php artisan migrate
```

Your new table will now be created inside MySQL.

---

# Step 11: Generate a Model

Create a model.

```bash
php artisan make:model Product
```

> **Note:** Model names should always be **Singular**.

Example:

✅ Product

❌ Products

The model is located in:

```
app/
└── Models/
    └── Product.php
```

---

# Step 12: Add Fillable Fields

Open **Product.php** and add:

```php
protected $fillable = [
    'name',
    'qty',
    'price',
    'description',
];
```

---

# Step 13: Generate a Controller

Create a controller.

```bash
php artisan make:controller ProductController
```

Or generate a Resource Controller:

```bash
php artisan make:controller ProductController --resource
```

The controller will be located in:

```
app/
└── Http/
    └── Controllers/
        └── ProductController.php
```

Laravel Documentation:

https://laravel.com/docs/13.x/controllers

---

# Step 14: Create Blade Views

Inside:

```
resources/
└── views/
```

Create a folder named:

```
products
```

Then create the following files:

```
resources/views/products/index.blade.php
resources/views/products/create.blade.php
resources/views/products/edit.blade.php
resources/views/products/show.blade.php
```

---

# Step 15: Create Routes

Open:

```
routes/web.php
```

Import the controller.

```php
use App\Http\Controllers\ProductController;
```

### Individual Routes

```php
Route::get('/products', [ProductController::class, 'index'])->name('products.index');

Route::get('/products/create', [ProductController::class, 'create'])->name('products.create');

Route::post('/products', [ProductController::class, 'store'])->name('products.store');

Route::get('/products/{product}', [ProductController::class, 'show'])->name('products.show');

Route::get('/products/{product}/edit', [ProductController::class, 'edit'])->name('products.edit');

Route::put('/products/{product}', [ProductController::class, 'update'])->name('products.update');

Route::delete('/products/{product}', [ProductController::class, 'destroy'])->name('products.destroy');
```

### OR use Resource Routes

```php
Route::resource('products', ProductController::class);
```

---

# Step 16: Receive Form Data

Import your model.

```php
use App\Models\Product;
```

Inside the `store()` method:

```php
public function store(Request $request)
{
    dd($request->all());
}
```

Use `dd()` to verify that your form is sending data correctly.

---

# Step 17: Validate User Input

Replace the `dd()` statement with validation.

```php
$request->validate([
    'name' => 'required|string|max:255',
    'qty' => 'required|integer',
    'price' => 'required|decimal:2',
    'description' => 'required'
]);
```

### Example Price Values

```
3.50
25.99
150.00
```

---

# Step 18: Save Data

```php
Product::create([
    'name' => $request->name,
    'qty' => $request->qty,
    'price' => $request->price,
    'description' => $request->description,
]);

return redirect()->route('products.index');
```

---

# Step 19: Read Data

```php
public function index()
{
    $products = Product::all();

    return view('products.index', compact('products'));
}
```

---

# Step 20: Edit Data

```php
public function edit(Product $product)
{
    return view('products.edit', compact('product'));
}
```

---

# Step 21: Update Data

```php
public function update(Request $request, Product $product)
{
    $request->validate([
        'name' => 'required',
        'qty' => 'required|integer',
        'price' => 'required|decimal:2',
        'description' => 'required'
    ]);

    $product->update($request->all());

    return redirect()->route('products.index');
}
```

---

# Step 22: Delete Data

```php
public function destroy(Product $product)
{
    $product->delete();

    return redirect()->route('products.index');
}
```

---

# 📂 Project Structure

```
newapp/
│
├── app/
│   ├── Http/
│   │   └── Controllers/
│   │       └── ProductController.php
│   │
│   └── Models/
│       └── Product.php
│
├── database/
│   └── migrations/
│       └── create_products_table.php
│
├── resources/
│   └── views/
│       └── products/
│           ├── index.blade.php
│           ├── create.blade.php
│           ├── edit.blade.php
│           └── show.blade.php
│
├── routes/
│   └── web.php
│
└── .env
```

---

# 🛠 Useful Artisan Commands

### Create Laravel Project

```bash
composer create-project laravel/laravel newapp "9.4.*"
```

### Start Server

```bash
php artisan serve
```

### Run Migrations

```bash
php artisan migrate
```

### Rollback Last Migration

```bash
php artisan migrate:rollback
```

### Refresh Migrations

```bash
php artisan migrate:refresh
```

### Fresh Migration

```bash
php artisan migrate:fresh
```

### Create Migration

```bash
php artisan make:migration create_products_table
```

### Create Model

```bash
php artisan make:model Product
```

### Create Controller

```bash
php artisan make:controller ProductController
```

### Create Resource Controller

```bash
php artisan make:controller ProductController --resource
```

---

# 🎯 Laravel MVC Workflow

```text
Create Project
      │
      ▼
Connect Database
      │
      ▼
Run Default Migrations
      │
      ▼
Create Migration
      │
      ▼
Run Migration
      │
      ▼
Create Model
      │
      ▼
Create Controller
      │
      ▼
Create Views
      │
      ▼
Create Routes
      │
      ▼
Validate Request
      │
      ▼
Create (Store)
      │
      ▼
Read (Index)
      │
      ▼
Update (Edit/Update)
      │
      ▼
Delete (Destroy)
```

---

## 🎉 Congratulations!

You have completed a basic Laravel CRUD application using:

- ✅ Laravel 9
- ✅ Composer
- ✅ MySQL
- ✅ Blade Templates
- ✅ MVC Architecture
- ✅ CRUD Operations (Create, Read, Update, Delete)
- ✅ Artisan Commands
- ✅ Database Migrations
- ✅ Models
- ✅ Controllers
- ✅ Routes
- ✅ Form Validation

Happy Coding! 🚀
