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

// Cache (Redis example). The driver is a class name, and its own options go
// under 'configs'. There is no 'handler' key.
$_ENV['cache.default.class']   = Framework\Cache\RedisCache::class;
$_ENV['cache.default.configs'] = ['host' => '127.0.0.1', 'port' => 6379];
$_ENV['cache.default.prefix']  = 'app-';

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
use Framework\Cache\MemcachedCache;
use Framework\Cache\RedisCache;

return [
    'default' => [
        'class' => RedisCache::class,
        'configs' => ['host' => '127.0.0.1'],
        'prefix' => 'app-',
    ],
    'session' => [
        'class' => MemcachedCache::class,
        'configs' => ['servers' => [['host' => '127.0.0.1', 'port' => 11211]]],
        'prefix' => 'session-',
    ],
];

// Usage
App::cache('session')->set('key', 'value');
```

### Cache config keys

`App::cache()` reads exactly these, so do not invent others:

| Key | Meaning |
|---|---|
| `class` | the driver class, for example `Framework\Cache\FilesCache::class` |
| `configs` | the driver's own options, passed to its constructor |
| `prefix` | prepended to every key, or a subdirectory on `FilesCache` |
| `serializer` | a `Framework\Cache\Serializer` case or its string value |
| `default_ttl` | seconds, used when a call passes no TTL |
| `logger_instance` | the `config/logger.php` instance to log through |

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
