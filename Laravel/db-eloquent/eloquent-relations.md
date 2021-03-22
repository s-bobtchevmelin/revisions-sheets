# Eloquent Relations

hasMany   
hasOne   
belongsTo   
belongsToMany   

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

The relation

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