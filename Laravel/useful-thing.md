# Some random and useful things to know

## Tinker

Tinker is a tool in php artisan who allow us to run php code in our project via the console.   
Run tinker : `php artisan tinker`   
Notice : to exit, press ctr + c   

### Basics

You can make the more basic things as `2 + 2`, tinker will return 4   
Or ` 4 > 1 ? 'yes' : 'no'`, tinker will return yes   

### Add to db

You can make more complexe thing like add something to db   
Admitting you have a model Assignement :   
`$assignement = new App\Models\Assignement;`   
`$assignement->body = 'Finish school work';`   
`$assignement->save();`   
And now you have created an assignement in the db

### Query db

You can also make query to the db. Some examples :     
`App\Models\Assignement::all();`   
`App\Models\Assignement::where('completed', false)->get();`  

### Run function of model
    
`$assignement = App\Models\Assignement::first();`   
`$assignement->complete();`   
Notice : this function pass the complete field to true  