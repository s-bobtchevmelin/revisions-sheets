# Form Handling

## Table of contents

* [Creation form](#Creation-form)   
* [Edition form](#Edition-form)   

## Creation form

### The route

```php
Route::post('/article', [ArticlesController::class, 'store']);
Route::get('/article/create', [ArticlesController::class, 'create']);
```

The first one handle the form, the second one render the form

### The form

```php
<form method="POST" action="/article">
    @csrf
    <div>
        <label for="title">Title</label>
        <input type="text" name="title" id="title">
    </div>

    <div>
        <label for="body">Body</label>
        <textarea name="body" id="body"></textarea>
    </div>

    <button type="submit">Submit</button>
</form>
```

The action is the route where to go to handle the form.   
`@csrf` is use to secure the form submit from exterior attacks, without it you might encounter an  error 419.

### The controller's method

Those methods create a new article in the database thanks to the data submit by the form  

#### Method 1 

```php
public function store() {
    $article = new Article();
    $article->title = request('title');
    $article->body = request('body');

    $article->save();

    return redirect('/article');
}
```

#### Method 2 (cleanest)

```php
public function store() {

    Article::create([
        'title' => request('title'),
        'body' => request('body')
    ]);

    return redirect('/article');
}
```
For this method to work you need to had a `fillable` variable in your model. If not you should get an `error mass assignement` or something.

In the model : `protected $fillable = ['title', 'body'];`

Notice : instead of `fillable` you can just had `protected $guarded = [];` but that's not very safe.
 

## Edition form

### The controller method

You can use the `method 1` from  `Creation form` or we can use a cleanest method : 
```php
public function store() {

    $article->update([
        'title' => request('title'),
        'body' => request('body')
    ]);

    return redirect('/article/' . $article->id);
}
```

### With POST

To make a edition form, you just add to add a value attribut on the form fields and set the values with the data of the ressource to edit   
```php
<form method="POST" action="/article">
    @csrf
    <div>
        <label for="title">Title</label>
        <input type="text" name="title" id="title" value="{{ $article->title }}">
    </div>

    <div>
        <label for="body">Body</label>
        <textarea name="body" id="body" value="{{ $article->body }}"></textarea>
    </div>

    <button type="submit">Submit</button>
</form>
```

### With PUT

The route : `Route::put('/article/{article}', [ArticlesController::class, 'update'])`   

The form :   
```php
<form method="POST" action="/article/{{ $article->id }}">
    @csrf
    @method('PUT')
    
    ... form fields ...
</form>
```
The browse onnly understand GET or POST method so to tell Laravel we want a PUT we have to trick the browser with `@method('PUT')` (that create an hidden input to pass the info.)

Notice : this trick also work for PATCH and DELETE, just replace the PUT in `@method` by the method you want.  
