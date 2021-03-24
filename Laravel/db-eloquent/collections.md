# Eloquent collections

## Table of contents

* [Basics](#Basics)
* [Collection methods](#Collection-methods)

## Basics

When fetching a single elements for the db, it return a element instance. But when fetching multiples elements it return a collection of elements, it a not a simple wrapper around the elements, it provides us many useful methods.

Imagine we have articles and tags, there is a difference between both of the following code :

* Return a Tag instance

```php
App\Models\Tag::where('name', 'laravel');
```

The `::where()` is building a sql query


* Return an eloquent collection

```php
$tags = App\Models\Article::first()->tags;
$tags->where('name', 'laravel');
```

The `->where()` is a collection method

Notice : you can create a collection manually by doing `collect(['one', 'two', 'three'])`     
You can also put nested array : `collect(['one', 'two', 'three', ['four', 'five'], 'six'])`


## Collection methods

You can chain collection methods. Example : 
```php
$items = collect(['one', 'two', 'two']);

// Hugly display
return $items->filter(function ($item) { return $item === 'two'; })->map(function ($item) { return $item })->last();

// Cleanest display 
return $items->filter(function ($item) { return $item === 'two'; })
            ->map(function ($item) { return $item })
            ->last();
```

### Method params callback

```php
$tags = App\Models\Article::first()->tags;
$tags->first(function ($tag) { return strlen($tag->name) > 5; });
```

This return the first tag where the name's length is big than 5

### Remove array depth

We have this collection : 
```php
collect([
    'one',
    'two',
    'three', 
    [
        'four',
        'five', 
            [
                'six',
                'seven'
            ],
        'eight',
    ],
    'nine'
]);
```

* `->collapse()` : remove array depth but goes one level deep on the collection
The previous collection will become : 
```php
[
    'one',
    'two',
    'three', 
    'four',
    'five', 
        [
            'six',
            'seven'
        ],
    'eight',
    'nine'
]
```

* `->flatten()` : remove array depth but goes to the deepest level of the collection
The previous collection will become : 
```php
[
    'one',
    'two',
    'three', 
    'four',
    'five', 
    'six',
    'seven',
    'eight',
    'nine'
]
```

### Elements modification

* `->filter(function ($item) { return $item % 2 === 0; })` : get all even number and put them in an associative array collection

### Elements display

* `->map(function ($item) { return $item })` : a loop (as for or foreach)

* `->pluck()` : get some particular field(s)   
`$articles->pluck('tags')` : get only the articles' tags field   
`$articles->pluck('tags')->collapse()->pluck('name');` : get the names field of the tags field of the article
The previous example is a bit complicated so we can simplify it : `$articles->pluck('tags.*.name');`
Notice : first we want ot make `$articles->pluck('tags.name');` but this will not work because this is a collection

* `->unique()` : remove duplicate element
Is apply unique on this collection : `collect(['one', 'two', 'two'])` it'll return `['one', 'two']`
