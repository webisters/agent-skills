# Webisters Framework — Agent Instructions

This file is loaded automatically by OpenAI Codex and compatible agents.

## Project type

PHP 8.2+ MVC application using the `webisters/framework` package. Entry class is `App` (project root `App.php`), which extends `Framework\MVC\App`.

## Directory layout

- `App.php` — project App class, extends `Framework\MVC\App`
- `boot/` — bootstrap files: `init.php`, `constants.php`, `helpers.php`, `app.php`, `routes.php`
- `app/Controllers/` — `App\Controllers\*`, extend `Framework\MVC\Controller`
- `app/Models/` — `App\Models\*`, extend `Framework\MVC\Model`
- `app/Views/` — PHP view templates
- `app/Commands/` — console commands
- `config/` — one PHP file per service, each returns `['default' => [...]]`
- `public/index.php` — HTTP entry point
- `storage/` — writable runtime files

## Service container

```php
App::database()   App::router()    App::request()   App::response()
App::session()    App::cache()     App::view()       App::logger()
App::mailer()     App::validation() App::language()  App::console()
App::migrator()   App::antiCsrf()
```

All accept an optional `$instance = 'default'` name.

## Routing

```php
// boot/routes.php
App::router()->serve(null, static function (RouteCollection $routes) : void {
    $routes->namespace('App\Controllers', [
        $routes->get('/', 'Home::index', 'home.index'),
    ]);
    $routes->notFound(static fn () => respond_not_found());
});
```

## Conventions

- All PHP files: `<?php declare(strict_types=1);`
- Namespaces: `App\Controllers`, `App\Models`, `App\Commands`
- Config files return `['default' => [...]]`; env values via `env('key', 'default')`

## Commands

```bash
php webisters start                    # dev server
php webisters route:list               # list routes
php webisters make:controller <Name>   # scaffold controller
php webisters make:model <Name>        # scaffold model
php webisters make:view <path/name>    # scaffold view
vendor/bin/phpunit                     # run tests
vendor/bin/php-cs-fixer fix            # coding standard
```

## Testing

Tests live under `tests/` with namespace `Tests\`. PHPUnit config is `phpunit.xml.dist`. Run `vendor/bin/phpunit` from the project root.

## Do not modify

- `vendor/` — managed by Composer
- `boot/constants.php` — path constants, do not hand-edit
- The license header comment block at the top of each PHP file (managed by php-cs-fixer)
