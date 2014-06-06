# Storage [![Build Status](https://travis-ci.org/ask11/storage.png?branch=master)](https://travis-ci.org/ask11/storage)

  Storage is a functional wrapper around [localForage](https://github.com/mozilla/localForage).
  That means it's an asynchronous browser storage with multiple back-ends (IndexedDB, WebSQL, localStorage),
  which is built for a better offline experience.

  The main differences with localForage:

  - batch support
  - error first node-style callbacks, [fixes #55](https://github.com/mozilla/localForage/issues/55)
  - simple API inspired by [yields/store](https://github.com/yields/store)
  - optional callbacks

## Installation

```
$ bower install storage
$ component install ask11/storage
$ npm install ask11-storage --save
```

  Standalone build available as [storage.js](https://github.com/ask11/storage/blob/master/storage.js).

```html
<script src="storage.js"></script>
<script>window.storage('key', fn);</script>
```

## Example

```js
// set
storage('key', 'val', function(err) {});
storage({ key: 'foo', key2: 'val2'}, function(err) {});

// get
storage('key', function(err, val) {});
storage(['key', 'key2'], function(err, all) {}); // all.length == 2

// delete
storage('key', null, function(err) {});
storage(['key1', 'key2', 'key3'], null, function(err) {});
```

  Working with async storage in developer console can be a problem.
  For this case, storage provides optional callbacks, and console.log(value) when needed.

```js
storage.set('foo', 1);
storage.set('bar', 2);
storage.get(['foo', 'bar']);
// => [1 ,2]
storage.del('bar');
storage.count();
// => 1
```

## API

### storage(key, fn)

  Get `key` value.

### storage([key1, key2, ..., keyn], fn)

  Get group of values. Callbacks return array of values for each key.
  If key does not exist, it returns undefined for this position.

### storage(key, val, fn)

  Set `key` to `val`.
  You can store any kind of data, including [blobs](https://hacks.mozilla.org/2014/02/localforage-offline-storage-improved/).

### storage(key, null, fn)

  Delete `key`. Null semantic is inspired by [yields/store](https://github.com/yields/store) and [component/cookie](https://github.com/component/cookie).
  Setting a key to `null` is equivalent to  deleting the key via `storage.del(key)`.

### storage({ key1: val1, key2: val2, key3: val3 }, fn)

  Run a batch operation.
  Simple way to create, update, remove multiple records.

```js
// assume we have 2 records
storage('foo', 7, fn)
storage('bar', ['one', 'two', 'three'], fn);

storage({
  baz: 'val' // create new val
  foo: 1000, // update `foo` value
  bar: null, // remove `bar`
}, function(err) {});
```

### storage([key1, key2, ..., keyn], null, fn)

  Delete a group of keys in one request.

### storage.forage

  It gives you access to the localForage instance.
  You can use it to configure backend.

```js
storage.forage.config({ name: 'my-name' });
if (!window.indexedDB) storage.forage.setDriver('localStorageWrapper');
```

### storage.clear()

  Clear storage.

### storage.count()

  Get the number of records in storage.

### .get() .set() .del()

  If you prefer more explicit API, you can use exposed functions.
  They methods allow optional callbacks.

```js
storage.set('key', 'val', function(err) {});
storage.get('key'); // val
storage.del('key');
```

## Promises

  If you don't like callbacks,
  you can combine storage with [then/promise](https://github.com/then/promise).

```js
var Promise = require('promise');
var orignal = require('storage');
var storage = Promise.denodeify(orignal);

storage('key').then(function(val) {});
```

## License

  Aleksey Kulikov, [MIT](http://ask11.mit-license.org/).
