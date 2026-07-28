---
name: webisters-config-env
description: Configure services and environment variables in a Webisters project
---

Each service has a config file under `config/` returning `['default' => [...]]`. Environment values come from `.env.php` at the project root.

## .env.php structure

```php
<?php
$_ENV['ENVIRONMENT'] = 'development'; // or 'production'

// Database
$_ENV['database.default.config.username'] = 'myuser';
$_ENV['database.default.config.password'] = 'secret';
$_ENV['database.default.config.schema']   = 'myapp';
$_ENV['database.default.config.host']     = 'localhost';
$_ENV['database.default.config.port']     = 3306;

// Cache (Redis example)
$_ENV['cache.default.handler'] = 'redis';
$_ENV['cache.default.host']    = '127.0.0.1';

// Logger
$_ENV['logger.default.destination'] = __DIR__ . '/storage/logs/app.log';
```

## Reading env values in config files

```php
// config/database.php
return [
    'default' => [
        'config' => [
            'username' => env('database.default.config.username', 'root'),
            'password' => env('database.default.config.password', 'password'),
            'schema'   => env('database.default.config.schema', 'app'),
        ],
    ],
];
```

The `env($key, $default)` helper reads from `$_ENV`.

## Multiple named instances

Any service supports multiple named instances:

```php
// config/cache.php
return [
    'default' => ['handler' => 'redis', ...],
    'session' => ['handler' => 'memcached', ...],
];

// Usage
App::cache('session')->set('key', 'value');
```

## Available config files

| File | Service accessor |
|---|---|
| `config/database.php` | `App::database()` |
| `config/router.php` | `App::router()` |
| `config/session.php` | `App::session()` |
| `config/cache.php` | `App::cache()` |
| `config/logger.php` | `App::logger()` |
| `config/email.php` | `App::mailer()` |
| `config/language.php` | `App::language()` |
| `config/console.php` | `App::console()` |
