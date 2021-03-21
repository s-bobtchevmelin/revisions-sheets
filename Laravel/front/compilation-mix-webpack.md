# Asset compilation with Laravel Mix and Webpack

If using Vanilla JS, js and css folder should be in public/     
If using a compilator, js and css (or scss) folder should be in ressources/

## Sass

If using sass, you need to install it : `npm install sass`   
Check if there is a folder named sass at /ressources, if not create it   
Check if there is a file app.scss at /ressources/sass, if not create it   
 
## Asset Management

In webpack-mix.js, you can manage all your assets :    
For add our app.scss :   

```js
mix.js('resources/js/app.js', 'public/js')
    .sass('resources/sass/app.scss', 'public/css');
```

Notice : if you're not using any aditionnal js you can just make that : 

```js
mix.sass('resources/sass/app.scss', 'public/css');
```

## Import in blade

Now you can compilate your sass, you need to import it in your blade template.   
Here we want it in our layout.blade.php :   

```html
<link href="/css/app.css" rel="stylesheet">
```

Notice : you can notice that we don't import our app.scss file but the complilate file (app.css)