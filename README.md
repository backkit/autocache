
# Automatic cache resolver service for backkit

## install

```
npm install --save @backkit/autocache
```

## basics

The main idea behind autocache is that you will never have to worry about cache being present nor you will have to manualy populate the cache.

Each time you want to access your cache, it's there.

It also decouples your cache building logic from the rest of your app, so your code is cleaner, more maintable and more testable.


## Use the cache

```
const c = autocache.get('hello')
await c.data
```

`await c.data` will *always* have a value, if it doesn't it will automatically populate a cache, then return the value.

In some situations, you will actualy have multiple caches with same policy. For example a cache for each user, will have same expiration policy, but different values.

In that case, you can pass an ID to get the right cache ID:

```
const c = autocache.get('hello', 1234)
await c.data
```

## Build the cache

Unlike other cache systems, autocache decouples your cache resolver in a separate module, so you will basicaly never write something like `cache.set(key, value)`

Instead, you create a cache resolver, that is called automaticaly when your cache is missing or expired:

res/autocache/hello.js

```
const BaseResolver = require('backkit-')
class HelloResolver extends  {
  
  constructor({autocache}) {
    autocache.use(this);
  }

  async build(id) {
    return `whatever cache value ${Date.now()}, can be an object or an array of course`;
  }
}

```

## Warm up the cache (cli)

Because building the cache may be long you can prewarm it, before you deploy new version of your app in production for example.

For that purpose you can run your app in cli mode, using autocache as entrypoint:

```
ENTRYPOINT=autocache node index.js warm hello
```

same for individual cache

```
ENTRYPOINT=autocache node index.js warm hello 1234
```

you can also pass a wildcard, but you will have to implement `getIds` method on your cache resolver first in order to make it work

```
// see below for implementation example
ENTRYPOINT=autocache node index.js warm hello *
```

## Warm up the cache (js)

You can do the same programaticaly

```
await autocache.warm('hello') // warms hello
await autocache.warm('hello', 1234) // warms hello 1234
await autocache.warm('hello', '*') // warms all hello caches
```


## Implement wildcard prewarming

```
@todo
```

## Using with a worker queue (bull)

```
@todo
```

## Search the cache (flexsearch)

```
@todo
```

