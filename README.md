Laravel CRUD Application Guide (Step-by-Step)

This guide shows how to build a simple CRUD (Create, Read, Update, Delete) application in Laravel using the MVC (Model-View-Controller) architecture and MySQL.

1. Install Composer

Download and install Composer from:

https://getcomposer.org/

Verify installation:

composer --version
2. Create a New Laravel Project

Open VS Code.

Open a terminal inside the folder where you want your project.

Run:

composer create-project laravel/laravel newapp "9.4.*"

Example:

composer create-project laravel/laravel inventory "9.4.*"

Wait until the installation finishes.

3. Open the Project
cd newapp
4. Run the Laravel Application

Start the development server:

php artisan serve

Open the browser:

http://127.0.0.1:8000

You should see the Laravel welcome page.

5. Create a MySQL Database

Start XAMPP:

Apache
MySQL

Open phpMyAdmin and create a database.

Example:

inventory_db
6. Connect Laravel to MySQL

Open the .env file and update the database configuration.

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=inventory_db
DB_USERNAME=root
DB_PASSWORD=

Save the file.

7. Run Default Migrations

Laravel comes with some default tables such as:

users
password_reset_tokens
failed_jobs
personal_access_tokens

Create them by running:

php artisan migrate
8. Create Your Own Database Table (Migration)

After the default tables are created, generate a migration for your application table.

Example:

php artisan make:migration create_products_table

The migration file will be created inside:

database/
    migrations/

Laravel Documentation:

https://laravel.com/docs/13.x/migrations#generating-migrations

9. Define the Table Schema

Open the generated migration file and edit the up() method.

Example:

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

Common column types include:

$table->string();
$table->integer();
$table->boolean();
$table->decimal();
$table->double();
$table->float();
$table->date();
$table->dateTime();
$table->text();
$table->longText();
$table->timestamps();

See all available column types:

https://laravel.com/docs/13.x/migrations#available-column-types

10. Apply the New Migration

Run:

php artisan migrate

Your products table will now be created in the database.

11. Create the Model

Generate a model for the table.

php artisan make:model Product

Note: Model names should always be singular.

The model will be created in:

app/
    Models/
        Product.php
12. Add Fillable Fields

Inside Product.php, add the $fillable property.

protected $fillable = [
    'name',
    'qty',
    'price',
    'description',
];

This allows Laravel to perform mass assignment safely.

13. Create a Controller

Generate a controller.

php artisan make:controller ProductController

The controller will be created in:

app/
    Http/
        Controllers/
            ProductController.php

Laravel Documentation:

https://laravel.com/docs/13.x/controllers

14. Create the Views

Inside:

resources/
    views/

Create a folder named:

products

Example files:

resources/views/products/index.blade.php
resources/views/products/create.blade.php
resources/views/products/edit.blade.php
resources/views/products/show.blade.php
15. Create Routes

Open:

routes/web.php

Import the controller.

use App\Http\Controllers\ProductController;

Example routes:

Route::get('/products', [ProductController::class, 'index'])->name('products.index');

Route::get('/products/create', [ProductController::class, 'create'])->name('products.create');

Route::post('/products', [ProductController::class, 'store'])->name('products.store');

Route::get('/products/{product}', [ProductController::class, 'show'])->name('products.show');

Route::get('/products/{product}/edit', [ProductController::class, 'edit'])->name('products.edit');

Route::put('/products/{product}', [ProductController::class, 'update'])->name('products.update');

Route::delete('/products/{product}', [ProductController::class, 'destroy'])->name('products.destroy');

Or generate all CRUD routes automatically:

Route::resource('products', ProductController::class);
16. Handle Form Submission

Import the model into the controller.

use App\Models\Product;

Example:

public function store(Request $request)
{
    dd($request->all());
}

Use dd() to inspect the submitted data before saving it to the database.

17. Validate User Input

After confirming the submitted data, replace dd() with validation.

$request->validate([
    'name' => 'required|string|max:255',
    'qty' => 'required|integer',
    'price' => 'required|decimal:2',
    'description' => 'required'
]);

Note: For the decimal:2 validation rule, enter values with two decimal places, for example:

3.33
25.00
199.99
18. Save Data to the Database
Product::create([
    'name' => $request->name,
    'qty' => $request->qty,
    'price' => $request->price,
    'description' => $request->description,
]);

return redirect()->route('products.index');
19. Retrieve All Products
public function index()
{
    $products = Product::all();

    return view('products.index', compact('products'));
}
20. Edit a Product
public function edit(Product $product)
{
    return view('products.edit', compact('product'));
}
21. Update a Product
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
22. Delete a Product
public function destroy(Product $product)
{
    $product->delete();

    return redirect()->route('products.index');
}
Project Structure (MVC)
newapp/
│
├── app
│   ├── Http
│   │   └── Controllers
│   │         ProductController.php
│   │
│   └── Models
│         Product.php
│
├── database
│   └── migrations
│         create_products_table.php
│
├── resources
│   └── views
│        └── products
│             index.blade.php
│             create.blade.php
│             edit.blade.php
│             show.blade.php
│
├── routes
│     web.php
│
└── .env
Artisan Commands Summary
# Create Laravel project
composer create-project laravel/laravel newapp "9.4.*"

# Run the application
php artisan serve

# Run default migrations
php artisan migrate

# Create a migration
php artisan make:migration create_products_table

# Create a model
php artisan make:model Product

# Create a controller
php artisan make:controller ProductController

# Create a resource controller (optional)
php artisan make:controller ProductController --resource

# Roll back the last migration
php artisan migrate:rollback

# Refresh all migrations
php artisan migrate:refresh

# Refresh and seed the database
php artisan migrate:fresh --seed

This workflow follows Laravel's standard MVC pattern:

Migration → Database → Model → Controller → Routes → Views → Validation → CRUD Operations (Create, Read, Update, Delete).
