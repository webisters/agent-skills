# Webisters Framework — Project Rules

This file is automatically loaded by Claude Code when working inside a Webisters project.

## What this is

A Webisters project is a PHP 8.2+ MVC application built on the `webisters/framework` metapackage. The entry class is `App` (project root `App.php`), which extends `Framework\MVC\App` — a static service container.

## Directory layout

```
App.php              # extends Framework\MVC\App
boot/
  init.php           # env + error mode
  constants.php      # ROOT_DIR, APP_DIR, CONFIG_DIR, PUBLIC_DIR, STORAGE_DIR, VENDOR_DIR, WEBISTERS_DIR
  helpers.php        # global helper functions
  app.php            # bootstraps the App instance
  routes.php         # route definitions
app/
  Controllers/       # App\Controllers\* — extend Framework\MVC\Controller
  Models/            # App\Models\* — extend Framework\MVC\Model
  Views/             # PHP view templates
  Commands/          # console commands
  Languages/         # language files
config/              # one PHP file per service, each returns ['default' => [...]]
public/index.php     # HTTP entry point
storage/             # writable runtime files (logs, cache, sessions)
```

## Service container

Access services via static methods on `App`:

```php
App::database()      // Framework\Database\Database
App::router()        // Framework\Routing\Router
App::request()       // Framework\HTTP\Request
App::response()      // Framework\HTTP\Response
App::session()       // Framework\Session\Session
App::cache()         // Framework\Cache\Cache
App::view()          // Framework\MVC\View
App::logger()        // Framework\Log\Logger
App::mailer()        // Framework\Email\Mailer
App::validation()    // Framework\Validation\Validator
App::language()      // Framework\Language\Language
App::console()       // Framework\CLI\Console
App::migrator()      // Framework\Database\Extra\Migrator
App::antiCsrf()      // Framework\Session\AntiCSRF
```

All accessors accept an optional `$instance = 'default'` name for multiple named instances.

## Routing

Routes are defined in `boot/routes.php`:

```php
App::router()->serve(null, static function (RouteCollection $routes) : void {
    $routes->namespace('App\Controllers', [
        $routes->get('/', 'Home::index', 'home.index'),
        $routes->post('/users', 'Users::store', 'users.store'),
    ]);
    $routes->notFound(static fn () => respond_not_found());
});
```

## Config

Each service has a config file under `config/` returning an array keyed by instance name:

```php
// config/database.php
return [
    'default' => [
        'config' => [
            'username' => env('database.default.config.username', 'root'),
            'password' => env('database.default.config.password', 'password'),
            'schema'   => env('database.default.config.schema', 'app'),
            'host'     => env('database.default.config.host', 'localhost'),
            'port'     => env('database.default.config.port', 3306),
        ],
    ],
];
```

Environment values come from `.env.php` at the project root.

## Conventions

- All PHP files start with `<?php declare(strict_types=1);`
- Namespaces: `App\Controllers`, `App\Models`, `App\Views`, `App\Commands`
- Controllers extend `Framework\MVC\Controller`
- Models extend `Framework\MVC\Model`
- Use `App::response()->setBody(...)->send()` or return a string from a controller action

## Common commands

Run from the project root:

```bash
php webisters start          # dev server on port 8000
php webisters start 8080     # custom port
php webisters route:list     # list all routes
php webisters make:controller <Name>
php webisters make:model <Name>
php webisters make:view <path/name>
vendor/bin/phpunit           # run tests
vendor/bin/php-cs-fixer fix  # apply coding standard
```

## Local monorepo development

If working inside the Webisters monorepo, use `composer.local.json` (path repositories, symlinked libraries):

```bash
COMPOSER=composer.local.json composer install
```
