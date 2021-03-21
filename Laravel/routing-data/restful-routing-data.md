# Restful routing and data management

## The Seven Restful Controller Actions

The seven actions are : 
* index : render list of ressources
* create : show a view to create new resource
* store : persist the new ressource
* show : render a single ressource
* edit : show a view to edit new resource
* update : persist the edited ressource
* destroy : delete the ressource

You can generate it with a command : `php artisan make:controlle ProjectController -r`   

You can also create the model in the same time : `php artisan make:controlle ProjectController -r -m Project`   

## Rest Routing

### Methods

* `GET /articles`  or  `GET /article/:id`
* `POST /article`
* `PUT /article/:id` 
* `DELETE /article/:id`
