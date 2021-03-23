# Authentication

## Table of contents

* [Set up](#Set-up)
* [Middleware](#Middleware)
* [Access current user in blade](#Access-current-user-in-blade)
* [Reset password](#Reset-password)

## Set up

As see in installation.md, you need to install larvel ui : `composer require laravel/ui --dev`   

Then, you have to choose the the you want : bootstrap, vue or react.   
After run the command : `php artisan ui vue --auth`   
Notice : here I select the vue as type, it's why there is vue.   

Finally run `npm install`

For compile, run : `npm run dev`

Notice : if the css on login and register are broken. I resolve it by adding a missing folder in `/public` : go to `/public` and check if the css folder is here, if not create it and had a `app.css` file in it. Then run `npm run dev`.

/!\ Now we can access to a login and a register page which work. If I try to register a new user, it works ! I also can login or logout !

## Middleware

Middleware are very usefull to set route restrictions, we can find them at `/app/Http/Middleware`

### Creation

### Usage

We can use them in 2 differents place :

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

## Access current user in blade

### Display user info

We can access to the current user in blade easily :

```php
<p>Hello {{ Auth::user()->name }}</p>
```

Notice : sometime we can see `<p>Hello {{ auth()->user()->name }}</p>`, that's the same thing

### Condition user info

* Sometime we want to show something if a user is connected but show something else is no user connected

```php
@auth
    <h1>Hello {{ Auth::user()->name }}</h1>
@else
    <h1>Hello unkown user</h1>
@endauth
```

Notice : We can also use a `@if @else` if we want but `@auth` is cleaner

* Sometime we may want the inverse
```php
@guest
    <h1>Hello unkown user</h1>
@else
    <h1>Hello {{ Auth::user()->name }}</h1>
@endguest
```

## Reset password

The function to reset the password is already done, the only thing we have to do is configure the email notification.   
If not using nptification yet you can send to the log :

In `.env` change `MAIL_MAILER` to log : 

```
MAIL_MAILER=log
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=null
MAIL_FROM_NAME="${APP_NAME}"
```

Now you recive the email in your lof file (at /storage/logs)