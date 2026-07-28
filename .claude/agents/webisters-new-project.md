---
name: webisters-new-project
description: Scaffold a new Webisters project (app, api, one, or site) using the webisters CLI
---

Scaffold a new Webisters project using the `webisters` CLI tool.

## Usage

```
webisters new-app <name>
webisters new-api <name>
webisters new-one <name>
webisters new-site <name>
```

Or the grouped form:

```
webisters new app <name>
webisters new api <name>
webisters new one <name>
webisters new site <name>
```

## Options

- `--no-install` - scaffold without running `composer install`
- `--with-install` - run `composer install` automatically without prompting
- `--dry-run` - preview what would be created without writing files

## Project types

| Type | Command | Description |
|---|---|---|
| App | `new-app` | Full MVC project with controllers, models, views, routing |
| API | `new-api` | API-focused project, no view layer |
| One | `new-one` | Single-file style project |
| Site | `new-site` | Static site project |

## After scaffolding

1. `cd <name>`
2. If you used `--no-install`, run `composer install`
3. Start the dev server: `php webisters start`
4. Open http://localhost:8000

## Prerequisites

- PHP >= 8.2 with extensions: `intl`, `sodium`, `gd`, `mysqli`, `curl`, `fileinfo`, `json`, `simplexml`, `dom`, `libxml`, `zip`, `openssl`
- Composer 2.x
- `webisters/webisters` installed globally: `composer global require webisters/webisters`
- PATH configured (Windows): `composer global exec webisters setup`, then open a new terminal
