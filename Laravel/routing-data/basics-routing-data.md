# Laravel Routing and data management

We can find the route file in routes/web.php

## Table of contents

* [Anatomy of a route](#Anatomy-of-a-route)   
* [Route order](#Route-order)  
* [Pass request data to a view in a route](#Pass-request-data-to-a-view-in-a-route)  
* [Pass request data to a view in a controller](#Pass-request-data-to-a-view-in-a-controller) 
* [Restful routing and data management](#Restful-routing-and-data-management)  
* [Named routes](#Named-routes)  


## Anatomy of a route

```php
Route::get('/', function () {
    return view('home');
})
```

Here we declare a route with a get method, the path will be / and we say that this route go to the view named home.  
Notice : Views can be found in ressources/views/  
In the return of the route, you can pass what you want and not just a view. For exemple, we can make `return 'hello world'` and oit will display it on the page.


## Route order

Pay attention to route order, in some case it can be tricky : 

```php
Route::get('/article/{article}', [ArticlesController::class, 'show']);
Route::get('/article/create', [ArticlesController::class, 'create']);
```
Here, it'll check if the route is `/article/{article}` and then if not, it'll pass to `/article/create`.   
But in this case, if you tap in your URL `/article/create` you should have an error, that's normal because the first route has a wildcard which accept anything after `/article` . So it think you want to access an article with an id called 'create'.   

If you want to go through this, just invert both routes : 
```php
Route::get('/article/create', [ArticlesController::class, 'create']);
Route::get('/article/{article}', [ArticlesController::class, 'show']);
```
Now the problem is solved because, it'll check if the route is `/article/create` and then if not, it'll pass to `/article/{article}`


## Pass request data to a view in a route

### Directly in the route from the URL, with request()

The URL : http://127.0.0.1:8000/sloth?name=Sid  

The route :  
```php
Route::get('/sloth', function () {
    $name = request('name');
    return view('sloth', ['name' => $name]);
});
```

The view : `<p>A slot name : {{ $name }}</p>`  
Notice : in the view, we use `{{ $myVar }}` to display  dynamic content. If we pass some js it automatically apply `htmlspecialchars()`.   
If we want to pass througt this and display it raw (without apply `htmlspecialchars()`), we have to use `{!! $myVar !!}`

### With Wildcard

A wildcard is a parameter pass in the URL BUT using laravel instead of html.   
Before thr URL looks like : http://127.0.0.1:8000/elephant?id=1  
Now the URL looks like : http://127.0.0.1:8000/elephant/1

```php
Route::get('/elephant/{id}', function ($id) {

    $elephants = [
        "1" => "Dumbo",
        "2" => "Babar"
    ];

    if(!array_key_exists($id, $elephants)) {
        abort(404, 'This elephant doesn\'t exist');
    }

    return view('elephant', ['elephant' => $elephants[$id]]);
});
```

We pass the wildcare in the callback of the function an now we can use it in our route.  
In this example we see a condition who permit to throw an error 404 if the elephant's id is not in the data.


## Pass request data to a view in a controller

What we did previously isn't a clean way to pass data to a view. We can use controller instead.  


### Create a controller

Create the controller with : `php artisan make:controller catController`   
It create in app/Http/Controllers a file named catController.php   
In the file we have : 
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class catController extends Controller
{
    //
}
```

### Add a route

Add the use statement of the controller : `use App\Http\Controllers\CatController;`

```php
Route::get('/cat/{id}', [CatController::class, 'show']);
```
The second argument correspond to a controller, an @ and method name

### Add a controller method

In the controller we need to create a method, here we names it show   
```php
    public function show($id) {
        $cats = [
            "1" => "Minouche",
            "2" => "Gribouille"
        ];

        if(!array_key_exists($id, $cats)) {
            abort(404, 'This cat doesn\'t exist');
        }

        return view('cat', ['cat' => $cats[$id]]);
    }
```


## Restful routing and data management

### The Seven Restful Controller Actions

The seven actions are : 
* index : render list of ressources
* create : show a view to create new resource
* store : persist the new ressource
* show : render a single ressource
* edit : show a view to edit new resource
* update : persist the edited ressource
* destroy : delete the ressource

You can generate it with a command : `php artisan make:controlle ProjectController -r`   

You can also create the model in the same time : `php artisan make:controlle ProjectController -r -m Project`   

### Methods :

* `GET /articles`  or  `GET /article/:id`
* `POST /article`
* `PUT /article/:id` 
* `DELETE /article/:id`


## Named routes

Give names to our routes is usefull : if don't using it, when our routes change we have to change the route manually in each link. thanks to named routes, we don't have to do this anymore.

## Declare the route's name

```php
Route::get('/article/', [ArticlesController::class, 'index'])->name(article.all);
Route::get('/article/{article}', [ArticlesController::class, 'show'])->name(article.show);
```

## Use named route

```php
    <a href="{{ route('article.all)'}}"></a>
    <a href="{{ route('article.show)', $article->id }}"></a>
    <a href="{{ route('article.show)', $article }}"></a>
```

Notice : for the wildcard, we can pass the article id, or you can pass the article and laravel will fing automatically the id (work only for primary key).