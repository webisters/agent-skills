---
name: webisters-cache
description: Use when caching in a Webisters project - choosing a driver, configuring it, or calling the cache API. Covers the Webisters Cache 2.0 behaviour that differs from 1.x.
---

# Cache

Reach the service through the container, never by constructing a driver yourself
in application code:

```php
$cache = App::cache();            // the 'default' instance
$other = App::cache('session');   // a named instance from config/cache.php
```

## The API

```php
$cache->set('user.1', ['name' => 'Ada'], 300);   // TTL in seconds
$cache->get('user.1');                            // null when missing or expired
$cache->delete('user.1');
$cache->increment('hits');                        // and decrement()
$cache->remember('report', fn () => build(), 300);  // callback, then TTL
```

`get()` returns `null` for a missing item, so `null` cannot be told apart from a
stored `null`. Store a sentinel if you need that distinction.

A TTL of zero or less describes an item that is already expired, so nothing is
stored and any existing item under that name is cleared. That is the same on
every driver.

## Use the multi-key methods for more than one key

```php
$cache->setMulti(['a' => 1, 'b' => 2], 300);
$cache->getMulti(['a', 'b']);      // ['a' => 1, 'b' => 2]
$cache->deleteMulti(['a', 'b']);
```

Do not write a loop over `get()`. On Redis and Memcached these are one round
trip; a loop is one per key.

## flush() is scoped to the prefix

This changed in Cache 2.0 and is the thing to get right:

```php
$cache->flush();          // clears only THIS instance's items, by prefix
$cache->flushStorage();   // clears the whole storage, ignoring the prefix
```

Use `flush()` unless the intent really is to clear everything sharing the
storage. Never reach for `flushStorage()` just to clear the application's own
cache.

Two caveats worth knowing:

- Without a `prefix` configured there is nothing to scope by, so `flush()`
  clears the storage.
- **Memcached cannot be scoped.** It offers no way to list keys, so `flush()`
  there clears the whole instance and takes the items of anything else sharing
  it. Prefer a prefix plus tags over `flush()` on that driver.

`RedisCache` never clears the whole server. Clearing the selected database is
`flushStorage()`.

## Tags, for invalidating a group

```php
$cache->tags(['posts'])->set('list', $posts, 300);
$cache->tags(['posts'])->get('list');
$cache->tags(['posts'])->flush();   // invalidates everything tagged 'posts'
$cache->flushTags('posts');         // same thing from the base cache
```

Tagged items are reached only through `tags()` with the same tags. A tagged
write costs one extra write the first time a tag is seen and nothing extra on
later writes.

## Locks and counters

```php
if ($cache->lock('import', 30)) {
    try {
        // only one worker gets here
    } finally {
        $cache->unlock('import');
    }
}
```

Always release in a `finally`. `lock()` is `add()`, so it is atomic on every
driver that offers the primitive.

## Drivers

| Driver | Use when |
|---|---|
| `FilesCache` | no cache server available; the default in the project skeletons |
| `RedisCache` | a Redis server is available; set `database` to stay out of the way of others |
| `MemcachedCache` | an existing Memcached pool; note the `flush()` caveat above |
| `ApcuCache` | single server, per-process speed, no sharing between machines |
| `DatabaseCache` | no other store available and a database already exists |
| `ArrayCache` | one request only; useful in tests |
| `NullCache` | caching disabled without changing call sites |

`FilesCache` is the odd one: its `prefix` names a **subdirectory** of
`directory` rather than a key prefix. The subdirectory is created when missing,
as of Cache 2.0. `directory` itself must exist.

```php
// config/cache.php
'default' => [
    'class' => Framework\Cache\FilesCache::class,
    'configs' => ['directory' => STORAGE_DIR . 'cache'],
    'prefix' => null,
],
```

Never point `directory` at a shared location such as the system temp directory
itself: `flush()` and the garbage collector delete what they find under it.

## Serializers

`Serializer::PHP` is the default and round-trips every PHP value. `JSON` decodes
an object to `stdClass`, and `JSON_ARRAY` to an array, so neither round-trips an
associative array as it went in. Pick `PHP` unless another consumer has to read
the stored bytes.
