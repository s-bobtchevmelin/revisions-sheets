# Events

## Table of contents

* [What's an event ?](#What's-an-event-?)
* [Create an event](#Create-an-event)
* [Call the event](#Call-the-event)
* [Create a listener](#Create-a-listener)

## What's an event ?

If we want to keep it simple, an event is a class who call listener(s). It allows us to not have very long controller's methods and it's can be reusable in other controller methods.   

You can see the list of all events and listers with `php artisan event:list`

## Create an event

`php artisan make:event ProductPurshased`

This command create a file named `ProductPurshased.php` in the `Events` directory

In this file you should have  : 

```php
namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class ProductPurshased
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    /**
     * Create a new event instance.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Get the channels the event should broadcast on.
     *
     * @return \Illuminate\Broadcasting\Channel|array
     */
    public function broadcastOn()
    {
        return new PrivateChannel('channel-name');
    }
}
```

But in fact, all of the broadcasting things are not use in major case so you can take it off if you want (broadcast allow us to pass event to the client side) : 

```php
namespace App\Events;

use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class ProductPurshased
{
    use Dispatchable, SerializesModels;

    /**
     * Create a new event instance.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }
}
```

## Call the event

Thers is two way to call event : 

* `ProductPurshased::dispatch();`

* `event(new ProductPurshased());`

Notice : if you have to pass some arguments in `ProductPurshased` you can do it with : 
* `ProductPurshased::dispatch('someData');` 
* `event(new ProductPurshased('someData'));`


## Create a listener

There's two way to create a listener : 

* `php artisan make:listener SendCoupon` : but you have to manually precise the event you want to attatch to it
* `php artisan make:listener SendCoupon --event ProductPurshased` : precise the event wanted automatically    
Notice : you can also do : `php artisan make:listener SendCoupon -e ProductPurshased`

The listener should looks like : 

```php
namespace App\Listeners;

use App\Events\ProductPurshased;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class SendCoupon
{
    /**
     * Create the event listener.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Handle the event.
     *
     * @param  ProductPurshased  $event
     * @return void
     */
    public function handle(ProductPurshased $event)
    {
        // put the logic here
    }
}
```

## Register event listener

To register yours event listener, go to `/app/Providers/EventServiceProvider.php`

Now there is two want : 

* add your even in `$listen` : 
```php
protected $listen = [
    Registered::class => [
        SendEmailVerificationNotification::class,
    ],
    ProductPurshased::class => [
        SendCoupon::class,
    ],
];
```

* Or you can overwrite the `souldDiscoverEvents()` method. TO do it, had the following code to `/app/Providers/EventServiceProvider.php`

```php
public function shouldDiscoverEvents()
{
    return true;
}
```