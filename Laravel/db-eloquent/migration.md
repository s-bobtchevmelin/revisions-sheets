# Migration

## Create a migration

`php artisan make:migration migrationName`

You can choose the name you want but there is a convention : 

For create table : `php artisan make:migration create_myTable_table`   
For update table : `php artisan make:migration update_myTable_table`   

Migrations can be found at database/migrations

## Create tables and column

When we run the command to create a migration, we have a file with some basics code to create the table : there's an id and a timestamp (add a created at and a updated at) field.   
To create more fields we can do that : 

```php
Schema::create('birds', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->integer('age')->nullable();
    $table->timestamps();
});
```

We see that we had 2 new fiels, name and age.   
The `->nullable()` allow us to not fill the age field

Notice : in the migration file there is also a function to drop the modifications that the migration apply to the db.

## Run a migration

`php artisan migrate`

## Add field to a table

To add field to a table we have to make another migration, for exemple : 

`php artisan make:migration add_color_to_birds_table`

When executing the command, we'll have a new migration file but blank this type : 

```php
/**
 * Run the migrations.
 *
 * @return void
 */
public function up()
{
    Schema::table('birds', function (Blueprint $table) {
        //
    });
}

/**
 * Reverse the migrations.
 *
 * @return void
 */
public function down()
{
    Schema::table('birds', function (Blueprint $table) {
        //
    });
}
```

We have to fill it with the field we want to add : 

```php
/**
 * Run the migrations.
 *
 * @return void
 */
public function up()
{
    Schema::table('birds', function (Blueprint $table) {
        $table->string('color');
    });
}

/**
 * Reverse the migrations.
 *
 * @return void
 */
public function down()
{
    Schema::table('birds', function (Blueprint $table) {
        $table->dropColumn('color');
    });
}
```

When the migration is applied it's better to delete it and add the changes in the migration who create the table (less migrations = faster to run)

## Some others commands

### Rollback

When you want to cancel the last migration, you make a rollback : 

`php artisan migrate:rollback`

### Fresh

For drop all migration and re-run them : `php artisan migrate:fresh`