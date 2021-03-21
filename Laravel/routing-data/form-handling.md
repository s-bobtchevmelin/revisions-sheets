# Form Handling

## The route

```php
Route::post('/article', [ArticlesController::class, 'store']);
Route::get('/article/create', [ArticlesController::class, 'create']);
```

The first one handle the form, the second one render the form

## Creation form

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

```php
public function store() {
    $article = new Article();
    $article->title = request('title');
    $article->body = request('body');

    $article->save();

    return redirect('/article');
}
```

This method create a new article in the database thanks to the data sublit by the form   

## Edition form

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


## Validation

### The controller

```php
public function store() {

    request()->validate([
        'title' => ['required', 'min:3', 'max:255'],
        'excerpt' => 'required',
        'body' => 'required',
    ]);

    $article = new Article();
    $article->title = request('title');
    $article->excerpt = request('excerpt');
    $article->body = request('body');

    $article->save();

    return redirect('/article');
}
```
The data from the form will pass in the validator function to check if is valid or not

### The view

```php
<form method="POST" action="/article">
    @csrf
    <div>
        <label for="title">Title</label>
        <input type="text" name="title" id="title" required>
        <p>{{ $errors->first('title') }}</p>
    </div>

    ... form fields ...
</form>
```

We can notice two things :    
* A browser validation : `required` is added to the input, it's totally optional.
* A new field `<p>{{ $errors->first('title') }}</p>`, it allow us to display the validator errors thanks to `$errors->first('validation-field-name')` 

### Conditional errors display

A basic way : 
```php
@if ($errors->has('title'))
    <p>{{ $errors->first('title') }}</p>
@endif
```

A better way :
```php
@error('title')
    <p>{{ $errors->first('title') }}</p>
@enderror
```

Notice : we can use `@error() @enderror` inline too : `<p class="@error('title') is-danger @enderror">{{ $errors->first('title') }}</p>` (it applies a class named is-danger if there are error for the title field)

### Keep coreect data when form fail

When the form fail because of unvalid data, all of our field become blank.   
To go througth that mechanism, we can set a value attribut to the data enter before the fail : 

```php
<form method="POST" action="/article">
    @csrf
    <div>
        <label for="title">Title</label>
        <input type="text" name="title" id="title" value="{{ old('title') }}">
    </div>

    ... form fields ...
</form>
```