---
name: webisters-make
description: Generate controllers, models, and views inside a Webisters project using make:* commands
---

Generate boilerplate files inside an existing Webisters project.

## Commands

```bash
php webisters make:controller <Name>
php webisters make:controller Admin/Users   # nested under app/Controllers/Admin/

php webisters make:model <Name>
php webisters make:model Auth/User          # nested under app/Models/Auth/

php webisters make:view <path/name>
php webisters make:view users/index         # creates app/Views/users/index.php
```

## Notes

- Run from the project root (where `App.php` lives)
- Nested paths use `/` as separator on all platforms
- The command errors if the file already exists - it will not overwrite

## Typical workflow

```bash
php webisters make:controller Users
php webisters make:model User
php webisters make:view users/index
php webisters make:view users/show
```

Then register routes in `boot/routes.php`:

```php
$routes->get('/users', 'Users::index', 'users.index');
$routes->get('/users/{id}', 'Users::show', 'users.show');
$routes->post('/users', 'Users::store', 'users.store');
```
