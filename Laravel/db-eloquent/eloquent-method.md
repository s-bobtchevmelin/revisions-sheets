# Eloquent methods

Notice : you can make -> or :: depending the context  
Examples : 
'::' : `App\Models\Article::all()`    
'->' : `App\Models\Article::latest()->get()`

## Table of contents

* [Get data](#Get-data)   
* [Data specifications](#Data-specifications)  


## Get data

`::all()` : get all elements  

`->get()` : get all elements after some other code (exemple : latest, take...) 

`::first()` or `->first()` : get the first element   

`::latest()` : get all elements, order by desc
* need to use get after : `App\Models\Article::latest()->get()`   
* By default, lastest use created_at, but you can specify any field : `App\Models\Article::latest(publish_at)->get()`   

`::find(id)` : get the element depending the id (or a model)

`::findOrFail(id)` : get the element depending the id but return 404 error if nothing found

`::findMany([id_1, id_2])` : get the elements depending the array of id (or model)

## Data specifications

`::take(nb)` or `->take(nd)` : get the number nb of elements    
* need to use get after : `App\Models\Article::take(2)->get()` 

`->pluck('field')` : use after getting some elements for pick only some field