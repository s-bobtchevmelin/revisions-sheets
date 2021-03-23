# Eloquent Relations

hasMany   
hasOne   
belongsTo   
belongsToMany  

* [Foreign Key](#Foreign-Key) 
* [One to many](#One-to-many) 
    * [Define the relation](#Define-the-relation) 
    * [Access the relation](#Access-the-relation)
    * [Custom relation](#Custom-relation)
* [Many to many](#Many-to-many) 
    * [Create the pivot table](#Create-the-pivot-table)  
    * [Define the relation](#Define-the-relation-1) 

## Foreign Key

### Create a foreign key

To declare a foreign key in a table we can had a lign in our migration : `$table->unsignedBigInteger('user_id');`    
Notice : don't forget to re-run your migration (`php artisan migrate:fresh`)   

### Foreign key constraints

We can had some specifications / constraints to our foreign key :   

```php
$table->foreign('user_id')
    ->references('id')
    ->on('users')
    ->onDelete('cascade');
```

It says : the forein key named `user_id`, refer to the id field (`->references('id')`) of the users table (`->on('users')`) and we want the article with this foreign key to be delete is the user don't exist anymore (`->onDelete('cascade')`)

## One to many

Imagine you have users and articles : a user can have multiple articles and an article can have a single user.

### Define the relation

* User side :
```php
public function articles()
{
    return $this->hasMany(Article::class); // select * from articles where user_id = $id
}
```

* Article side :
```php
public function user() 
{
    return $this->belongsTo(User::class);
}
```

### Access the relation

Now, we can access the user's articles with : `App\Models\User::find(3)->articles`    
Or, we can access the article's user : `App\Models\Articles::find(1)->user`

### Custom relation 

Sometime you don't want the relation method to be called as the table or you don't want to point on the primary key

* Example 1 : we don't want the relation to be named user but author :
```php
public function author() //author instead of user
{
    return $this->belongsTo(User::class, 'user_id');
}
```
We need to precise the key (`user_id`) because if not, laravel would search author_id in the article table. And that field doesn't exist.

* Example 2 : we don't want the relation to point on the primary key :
<!-- TO DO -->

## Many to many

Imagine you have articles and tags : a tag can have multiple articles and an article can have multiple tags.

### Create the pivot table

The table will be named `table1_table2` in alphabetic order and in singular.   
Here we have the tables ``tags` and `articles` so the pivot table will be `article_tag`

First we need to create a migration to create this table : `php artisan make:migration create_article_tag_table`    
The migration should looks like : 

```php
public function up()
{
    Schema::create('article_tag', function (Blueprint $table) {
        $table->id();
        $table->unsignedBigInteger('article_id');
        $table->unsignedBigInteger('tag_id');
        $table->timestamps();

        $table->unique(['article_id', 'tag_id']);

        $table->foreign('article_id')
            ->references('id')
            ->on('articles')
            ->onDelete('cascade');

        $table->foreign('tag_id')
            ->references('id')
            ->on('tags')
            ->onDelete('cascade');
    });
}
```

* We create both foreign keys
* We set this foreign keys has unique
* We add foreign keys constraints

### Define the relation

* Article side : 
```php
public function tags() 
{
    return $this->belongsToMany(Tag::class);
}
```

* Tag side :
```php
public function articles() 
{
    return $this->belongsToMany(Article::class);
}
```

### Attach and validate

* In our form we add a select input for our tags (don't forget to pass the tags to the view)

```php
<div>
    <label for="tags">Tags</label>
    <select name="tags[]" multiple>
        @foreach ($tags as $tag)
            <option value="{{ $tag->id }}">{{ $tag->name}}</option>
        @endforeach
    </select>
    
    @error('title')
        <p>{{ $message }}</p>
    @enderror
</div>
```

Notice : in the attribut name we have `name="tags[]"`, the `[]` mean that the output of the select will be dispaly in an array instead of a string

* In our store method we'll attach the tags selected
```php
public function store() {

    request()->validate([
        'title' => ['required', 'min:3', 'max:255'],
        'excerpt' => 'required',
        'body' => 'required',
        'tags' => 'exists:tags,id'
    ]);

    $article = new Article(request(['title', 'excerpt', 'body']));
    
    $article->user_id = 1; // we do this because we don't have any auth yet
    $article->save();

    $article->tags()->attach(request('tags'));

    return redirect('/article');
}
```

Notice : this time we do the validation in 2 parts because we don't want the tags to be "created" but "attach" and that's 2 differents things.

* Validate tags in case somebody try to add tags that don't exist
```php
request()->validate([
    'title' => ['required', 'min:3', 'max:255'],
    'excerpt' => 'required',
    'body' => 'required',
    'tags' => 'exists:tag,id'
])
```

We had `'tags' => 'exists:tag,id'`, that's mean : tags must exist in the field of of the table tag.