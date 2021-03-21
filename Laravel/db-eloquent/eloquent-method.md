# Eloquent methods

Notice : you can make -> or :: depending the context  
Examples : 
'::' : `App\Models\Article::all()`    
'->' : `App\Models\Article::latest()->get()`


## Get data

`::all()` : get all elements  

`->get()` : get all elements after some other code (exemple : latest, take...) 

`::first()` or `->first()` : get the first element   

`::latest()` : get all elements, order by desc
* need to use get after : `App\Models\Article::latest()->get()`   
* By default, lastest use created_at, but you can specify any field : `App\Models\Article::latest(publish_at)->get()`   

`::take(nb)` or `->take(nd)` : get the number nb of elements    
* need to use get after : `App\Models\Article::take(2)->get()`   

