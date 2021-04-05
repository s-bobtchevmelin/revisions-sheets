# Roles and abilities

## Table of contents

* [Tables](#Tables)
* [Models ](#Models)
* [Usage](#Usage)

## Tables

First we need to create the roles, abilitities and pivots tables, create a migration : `php artisan make:migration create_roles_tables`

In the migration, create the tables and pivots :

```php
public function up()
    {
        Schema::create('roles', function (Blueprint $table) {
            $table->id('id');
            $table->string('name');
            $table->string('label')->nullable();
            $table->timestamps();
        });

        Schema::create('abilities', function (Blueprint $table) {
            $table->id('id');
            $table->string('name');
            $table->string('label')->nullable();
            $table->timestamps();
        });

        Schema::create('ability_role', function (Blueprint $table) {
            $table->primary(['ability_id','role_id']);
            $table->unsignedBigInteger('ability_id');
            $table->unsignedBigInteger('role_id');
            $table->timestamps();

            $table->foreign('ability_id')
                ->references('id')
                ->on('abilities')
                ->onDelete('cascade');

            $table->foreign('role_id')
                ->references('id')
                ->on('roles')
                ->onDelete('cascade');
        });

        Schema::create('role_user', function (Blueprint $table) {
            $table->primary(['role_id','user_id']);
            $table->unsignedBigInteger('role_id');
            $table->unsignedBigInteger('user_id');
            $table->timestamps();

            $table->foreign('role_id')
                ->references('id')
                ->on('roles')
                ->onDelete('cascade');

            $table->foreign('user_id')
                ->references('id')
                ->on('users')
                ->onDelete('cascade');
        });
    }
```

## Models 

Create the models  : `php artisan make:model Role` and `php artisan make:model Ability`

Then, define the relations, here an example, you have to do it for each relation on both side (role and ability + role and user) 
```php
// In Role.php
public function ability() 
{
    return $this->belongsToMany(Ability::class);
}
```

You can also make some usefull methods as : 
```php
// In User.php
public function assignRole($role)
{
    $this->roles()->save($role);
}
```

## Usage

Notice : we assume that `edit-forum` is an ability

### Define the checking

To check if the user have the abilities, we can do a Gate :

```php
Gate::before(function ($user, $ability) {
    if ($user->abilities()->contains($ability)) {
        return true;   
    }
});
```

### Call verification in blade

For calling our verification in blade, use `@can` : 

```php
@can ('edit-forum', $article)
    <p>You can edit the article</p>
@endcan
```

### Call verification in route

For calling our verification in route, use middleware : 

```php
Route::get('/forum/edit', [App\Http\Controllers\ForumController::class, 'edit'])->middleware('can:edit-forum');
```