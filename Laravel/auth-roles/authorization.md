# Authorization

## Table of contents

* [Policies](#Policies)
    * [In AuthServiceProvider.php](#In-AuthServiceProvider.php)
    * [In separate file](#In-separate-file)
    * [Usage in the controller](#Usage-in-the-controller)
    * [Usage in blade](#Usage-in-blade)
    * [Filters](#Filters)
* [Middleware](#Middleware)
    * [Creation](#Creation)
    * [Usage](#Usage)
    * [Middleware and policies](#Middleware-and-policies)


## Policies

### In AuthServiceProvider.php

In `boot()` method : 

```php
Gate::define('update-article', function(User $user, Article $article) {
    return $article->user->is($user);
});
```

Notice : this solution is ok for small projects

### In separate file

Create the policy file : `php artisan make:policy ArticlePolicy`   
You can precise the model with : `php artisan make:policy ArticlePolicy --model=Article`   
If you precise the model, it create automaticaly some methods, you can use them and/or you can add new ones.

Notice : These files can be found at `/app/Policies` 

In the class, the method shoul looks like : 

```php
public function update-article(User $user, Article $article) 
{
    return $article->user->is($user);
}
```

Notice : this solution is best for bigger projects


### Usage in the controller

There's two way :

* With `->authorize()` : `$this->autorize('update-article', $article);`

* With `Gate::` : 
    * `if(Gate::denies('update-article', $article)) { die('nop'); }` : when user hasn't the authorization
    * `if(Gate::allows('update-article', $article)) { return 'yep'; }` : when user has the authorization


### Usage in blade

```php
@can ('update-article', $article)
    <p>You can edit the article</p>
@endcan
```

### Filters

In some case, you wan to authorize all actions for a policy. Most of the time it's the case with admin. You can do this in two way : 

* In `boot()` method from `AuthServiceProvider.php` :
```php
Gate::before(function(User $user) {
    if ($user->isAdmin()) {
        return true;
    }
});
```

* In the policy you want, add this method :
```php
public function before(User $user) {
    if ($user->isAdmin()) {
        return true;
    }
}
```

Notice : here we assume that `->isAdmin()` is an existing method who return true if the user is admin.


## Middleware

Middleware are very usefull to set route restrictions, we can find them at `/app/Http/Middleware`

### Usage

We can use them in 2 differents places :

* In the construct of a controller
```php
public function __construct()
{
    $this->middleware('auth');
}
```

* Sticked to a route (more common)
```php
Route::get('/home', [App\Http\Controllers\HomeController::class, 'index'])->name('home')->middleware('auth');
```

### Middleware and policies

You can also pass policies to middleware :  

* `Route::get('/article', [App\Http\Controllers\ArticleController::class, 'index'])->middleware(can:viewAny, App\Models\Article);`
* `Route::put('/article/{article}', [App\Http\Controllers\ArticleController::class, 'update'])->middleware(can:update, article);`
