# Form Validation

## Table of contents

* [The controller](#The-controller) 
* [The view](#The-view) 

## The controller

The data from the form will pass in the validator function to check if is valid or not

### Method 1 

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

### Method 2 (cleanest)

```php
public function store() {

    $validatedData = request()->validate([
        'title' => ['required', 'min:3', 'max:255'],
        'excerpt' => 'required',
        'body' => 'required',
    ]);

    Article::create($validatedData);

    return redirect('/article');
}
```

Note : passing `$validatedData` to the create function work because `$validatedData` contains an array.
It's quicker than to repeat ourselves with :
```php
Article::create([
    'title' => request('title'),
    'body' => request('body')
]);
```

### Method 3 (cleanest + shortest)

```php
public function store() {

    Article::create(request()->validate([
        'title' => ['required', 'min:3', 'max:255'],
        'excerpt' => 'required',
        'body' => 'required',
    ]));

    return redirect('/article');
}
```

### Re-use the validation part

When all the form field are the same it seems overkill to repeat this same part of code so we can isolate it in a controller's function.

The function : 

```php
protected function validateArticle() {
    return request()->validate([
        'title' => ['required', 'min:3', 'max:255'],
        'excerpt' => 'required',
        'body' => 'required',
    ]);
}
```

Call the funciton : `Article::create($this->validateArticle());`

## The view

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

### Keep correct data when form fail

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