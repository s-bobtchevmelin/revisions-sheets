# Database connexion

## Edit the .env

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=nameOfTheDatabase
DB_USERNAME=myUsername
DB_PASSWORD=myPassword
```

## Create the db

Enter in mysql : `mysql -u myUsername -p` afer that command we'll ask the password so type myPassword   
In mysql create de db : `create database nameOfTheDatabase;`   
If you want to add table, column and data, use phpMyadmin, adminer...
Notice : when creating a table remember to make it plurial. Example : if tou want your table to be named cat, you have to named it cats

## Without Eloquent

### In the controller

```php
public function show($id) {
        
    $seal = \DB::table('seal')->where('id', $id)->first();

    if (!$seal) {
        abort(404);
    }

    return view('seal', ['seal' => $seal]);
}
```

### In the view

```html
<p>A seal : {{ $seal->name }}</p>
```

## With Eloquent

### Create model

`php artisan make:model MyModel`   

The model created can be found at /app/Models and looks like : 
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Seal extends Model
{
    use HasFactory;
}
```

### In the controller

```php
public function show($id) {
    $seal = Seal::where('id', $id)->firstOrFail();
    return view('seal', ['seal' => $seal]);
}
```

Notice : `firstOrFail()` is a cleaner way to do an `->first()` and after a `abort(404)`

### Generating multiple files

With eloquent, you can generate multiple files when running the command for create a model.   
You can find the complete list with : `php artisan help make:model`

Some examples :    
I want to generate a controller and a migration with my model : `php artisan make:model MyModel -mc`   
I want to generate a controller with my model : `php artisan make:model MyModel -c`

### Function in model

In model you can add some function, for example : 
```php
public function complete() {
    $this->completed = true;
    $this->save();
}
```
This fonction modify the field complete of the model and pass it to true 


## Model Binding

### With Primary key

Model binding allow us to pass a model as argument of a controller's method, it'll get us the ressources depending the id pass in the route

The route : `Route::get('/article/{article}', [ArticlesController::class, 'show']);`

The controller : 
```php
public function show(Article $article) {
    // Before we use this but we do the exact same thing with the model binding
    // $article = Article::find($id);
    return view('article.article-details', ["article" => $article]);
}
```

### With any others fields

The previous point works only with primary key (id) if you want to use a slug or a title (for example) instead of an id, you can overwrite the method `getRouteKeyName()`

```php
 public function getRouteKeyName()
{
    return 'title';
}
```

Now you can type in your URL that : `http://127.0.0.1:8000/article/My first article` and it'll work !