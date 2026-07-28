---
name: webisters-routing
description: Define and inspect routes in a Webisters project
---

Routes are defined in `boot/routes.php` and inspected with `route:list`.

## Defining routes

```php
use Framework\Routing\RouteCollection;

App::router()->serve(null, static function (RouteCollection $routes) : void {
    $routes->namespace('App\Controllers', [
        $routes->get('/', 'Home::index', 'home.index'),
        $routes->get('/users', 'Users::index', 'users.index'),
        $routes->get('/users/{id}', 'Users::show', 'users.show'),
        $routes->post('/users', 'Users::store', 'users.store'),
        $routes->put('/users/{id}', 'Users::update', 'users.update'),
        $routes->delete('/users/{id}', 'Users::destroy', 'users.destroy'),
    ]);
    $routes->notFound(static fn () => respond_not_found());
});
```

## Route methods

`get`, `post`, `put`, `patch`, `delete` — all take `($path, $action, $name)`.

Action format: `'ControllerClass::method'` (namespace set by `$routes->namespace(...)`).

## Listing routes

```bash
php webisters route:list
```

## Named routes

```php
App::router()->getNamedRoute('users.show', ['id' => 42]); // /users/42
```

## Router config

`config/router.php`:

```php
return [
    'default' => [
        'files' => [BOOT_DIR . 'routes.php'],
        'auto_options' => false,
        'auto_methods' => false,
    ],
];
```
