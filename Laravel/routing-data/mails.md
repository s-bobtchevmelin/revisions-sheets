# Mails

## Table of contents

* [Raw mail](#Raw-mail)
* [Mailtrap](#Mailtrap)
* [Mailable Classes](#Mailable-Classes)
* [](#)
* [](#)

## Raw mail

Image you have a form where you type your email and it send a mail when submiting : 

### The form

```php
<form action="/contact" method="post">
    @csrf
    <label for="email">Email address</label>
    <input type="text" name="email" id="email">
    @error('email')
        <div>{{ $message }}</div>
    @enderror

    <button type="submit">Email Me !</button>
</form>
```

### The controller method

```php
Request::validate(['email' => ['required', 'email']]);
       
Mail::raw('A beautiful E-mail', function($message) {
    $message->to(Request::get('email'))
            ->subject('Hello');
});
```

## Mailtrap

https://mailtrap.io/

In the .env, modify mailer infos : 

```
MAIL_PORT=2525
MAIL_USERNAME=inboxUsername
MAIL_PASSWORD=inboxPassword
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=laravel.tuto@admin.com
MAIL_FROM_NAME="${APP_NAME}"
```

Username end password can be found in your inbox, at SMTP settings then show credentials


## Mailable Classes

### Create the mail class

`php artisan make:mail ContactMe`   
We can see a new folder named `mail` in ou `app` directory. It contains all of our mails.   

### The mail class

```php
class ContactMe extends Mailable
{
    use Queueable, SerializesModels;

    /**
     * Create a new message instance.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->view('view.name');
    }
}
```

When the mail class is created, it looks like the previous code. We can see it redirect to a view, by default it `view.name` but you have to replace this with the view you want it to be redirect to.

### Make it work

To send the mail you have to do it in the controller :

```php
Mail::to(Request::get('email'))
    ->send(new ContactMe());
```

In the `::to()` you put the eamil address you want to send the mail and in the `->send()` you put the mail class you want to call (depending the mail you want to send)

### Pass data to the mail view

To pass data to the mail view, you have to initalize it int th email class :

```php
class ContactMe extends Mailable
{
    use Queueable, SerializesModels;

    public $username

    /**
     * Create a new message instance.
     *
     * @return void
     */
    public function __construct($username)
    {
        $this->username = $username
    }

    ... some other code ...
}
```

In your controller, pass the $username : 

```php
Mail::to(Request::get('email'))
    ->send(new ContactMe('myBeautifulUsername'));
```

Now you have access to the `$username` in your view using `{{ username }}`

### Customize

Some examples : 

You can change the subject (= object) of the email with  : 
In your mail class
```php
return $this->view('emails.contact-me')
    ->subject('Welcome');
```

## Using markdown mail instead of html

### Create the markdown mail

You can directly create a markdown mail by adding some code to the command : `php artisan make:mail ContactMe --markdown=emails.contactMe`   
Notice : `emails.contactMe` correspond to the path you want the view to be : here we want to create the view named `contactMe` in the emails folder

### Class and template

If you want to use markdown, you have to modify in your mail class `return $this->view('emails.contact-me')` to `return $this->markdown('emails.contact-me')`    
And in your template instead of using html you can use markdown with `@component` : 

```php
@component('mail::message')

# Welcome

This is an email in markdown.

@endcomponent

@component('mail::button', ['url' => 'https://google.fr'])
Google
@endcomponent
```

Notice : 
* there are some others components types, check the doc
* never use indentation in markdown (excpet for nested list), it will broke the css

### Customize

If you want to customize the `@component` or the css of the mail, you have to export a part of the vendor: `php artisan vendor:publish --tag=laravel-mail`   
This will create a vendor folder in your views directory in which you have all the html and the css of the mail components. You can now edit them as you want.   

To change the css file, go to `/conf/mail.php` :    
There is :    
```php
'markdown' => [
    'theme' => 'default',

    'paths' => [
        resource_path('views/vendor/mail'),
    ],
],
```

Change `'theme' => 'default',` to the css file you want. Example : you create a new css file named `styles.css` : `'theme' => 'styles',`