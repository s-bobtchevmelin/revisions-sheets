# Blade

## Layout and sections

A layout file is a view who will be use has a base for our others views.   
It allows us to inject a part of code store in a section in the layout : 

layout.blade.php
```php
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Hello</title>
    </head>
    <body class="antialiased">
        @yield('content')
    </body>
</html>
```

myView.blade.php
```php
@extends('layout')

@section('content')

<h1>Hello</h1>
<p>Welcome to my view</p>

@endsection
```
In the layout (or any parent view) we have a `@yield('contentToInject')` which will receive the content between the `@section('contentToInject')` and the `@endsection`

## Set active menu link

When creating a navbar we would have the correct navbar item highlight. There're few methods : 

```php
<ul>
    <li class="{{ Request::path() === '/' ? 'active' : '' }}"><a href="/">Home</a></li>
    <li class="{{ Request::is('sloth') ? 'active' : '' }}"><a href="/sloth">Sloth</a></li>
    <li class="{{ Request::is('seal*') ? 'active' : '' }}"><a href="/seal/1">Seal</a></li>
</ul>
```

Notice : the active class will apply any css related to it when you are on the page, for example : 
```css
<style>
    .active {
        color: red;
    }
</style>
```
Notice 2 : The * after seal allow the route to have a wildcard.   
