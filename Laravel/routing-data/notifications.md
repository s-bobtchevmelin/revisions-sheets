# Notifications

## Table of contents

* [Basics](#Basics)
* [Database notification](#Database-notification)
* [](#)


## Basics

### Create a notification

`php artisan make:notification MyNotification`   
We can see a new folder named `Notifications` in ou `app` directory. It contains all of our notifications.

### Use the notification

To use notification, we can do it in 2 ways : 

* `Notification::send(Request::user(), new MyNotification());`   => best for notify a collection of user
* `Request::user()->notify(new MyNotification());`   => best for notify a single of user

### Pass parameter to the __contruct()

* When calling the notification, you can pass a parameter : `Notification::send(Request::user(), new MyNotification(20));`
* In the notification :
```php
protected $amount;

public function __construct($amount)
{
    $this->amout = $amount;
}
```


## Database notification

### Create the table

To create the notification table run : `php artisan notifications:table`    
Then, run : `php artisan migrate`

### Config

To specify you want database notification, go to your notification and edit the `via` method :

* You can add both mail and datatbase
```php
public function via($notifiable)
{
    return ['mail', 'database'];
}
```

* Or you can just add database

```php
public function via($notifiable)
{
    return ['database'];
}
```

In the database, the data field (in the table) correspond to the content of the `toArray()` method, by default it sets to an empty array (`[]`), you can edit it to put any data you want.    
Example :     
```php
public function toArray($notifiable)
{
    return [
        'name' => 'Sarah'
    ];
}
```

### Display notifications

* In the controller, pass the notifications to the view
```php
public function show()
{
    return View::make('notifications.show', ['notifications' => Auth::user()->notifications]);
}
```
Notice : you can also send `->unreadNotifications` instead of `->notifications`

* In the view you can display the notif with a basic foreach :
```php
@foreach($notifications as $notification)
    <p>{{ $notification->type }}</p>
@endforeach
```

* To display notification data : 
```php
@foreach($notifications as $notification)
    <p>{{ $notification->data['dataKey'] }}</p>
@endforeach
```

### Mark as read / unread

To mark a notification has read : 

```php
public function show()
{
    Auth::user()->unreadNotifications->markAsRead();

    return View::make('notifications.show', ['notifications' => Auth::user()->notifications]);
}
```
