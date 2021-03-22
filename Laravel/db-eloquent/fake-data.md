# Fake data

## Factories

### Basics

Factories are fake data for our model, by default the user's factory is generate. Those file can be find in /database/factories.

Example of the user's factory : 
```php
public function definition()
{
    return [
        'name' => $this->faker->name,
        'email' => $this->faker->unique()->safeEmail,
        'email_verified_at' => now(),
        'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
        'remember_token' => Str::random(10),
    ];
}
```

To call this factory, you can make `App\Models\User::factory()->create();` (in tinker for example)     
Notice : when calling the factory creation method, you can overwrite some field, for example : `App\Models\User::factory()->create(['title' => 'An overwrite title']);`

### Create a factory

The command : `php artisan make:factory ArticleFactory`

In the factory we need to create the field

```php
public function definition()
{
    return [
        'user_id' => \App\Models\User::factory(),
        'title' => $this->faker->sentence,
        'excerpt' => $this->faker->sentence,
        'body' => $this->faker->paragraph,
    ];
}
```

For the foreign key (`user_id`) you can create a new item (here, an user) for each new creation with : `\App\Models\User::factory()`    