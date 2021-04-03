# Authorization

## Table of contents

* [Policies](#Policies)
* [Middleware](#Middleware)

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


## Middleware

Middleware are very usefull to set route restrictions, we can find them at `/app/Http/Middleware`

### Creation

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