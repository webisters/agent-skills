---
name: webisters-database
description: Configure and use the database service in a Webisters project
---

The database service is `App::database()`, backed by `Framework\Database\Database` (MariaDB/MySQL via `ext-mysqli`).

## Config

`config/database.php`:

```php
return [
    'default' => [
        'config' => [
            'username' => env('database.default.config.username', 'root'),
            'password' => env('database.default.config.password', 'password'),
            'schema'   => env('database.default.config.schema', 'myapp'),
            'host'     => env('database.default.config.host', 'localhost'),
            'port'     => env('database.default.config.port', 3306),
        ],
        'logger_instance' => 'default',
    ],
];
```

Set real values in `.env.php`:

```php
$_ENV['database.default.config.username'] = 'myuser';
$_ENV['database.default.config.password'] = 'secret';
$_ENV['database.default.config.schema']   = 'myapp';
```

## Basic usage

```php
$db = App::database();

$rows = $db->select('SELECT * FROM users WHERE active = ?', [1]);
$db->exec('INSERT INTO users (name, email) VALUES (?, ?)', ['Alice', 'alice@example.com']);
$id = $db->insertId();
$db->exec('UPDATE users SET name = ? WHERE id = ?', ['Bob', 1]);
```

## Multiple instances

```php
// config/database.php
return [
    'default'  => [...],
    'readonly' => ['config' => ['host' => 'replica.example.com', ...]],
];

$replica = App::database('readonly');
```

## Migrations

```bash
php webisters migrate:up
php webisters migrate:down
php webisters migrate:to <version>
php webisters migrate:version
```

Migration classes extend `Framework\Database\Extra\AbstractMigration`.
