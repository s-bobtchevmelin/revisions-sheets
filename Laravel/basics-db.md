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