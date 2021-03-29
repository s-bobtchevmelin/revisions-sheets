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

## Database notification

### Create the table

To create the notification table run : `php artisan notifications:table`    
Then, run : `php artisan migrate`