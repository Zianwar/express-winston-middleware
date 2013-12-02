Winston Middleware for Express
==============================

[Winston][winston] log wrappers and middleware for [Express][express].

## Usage

You can install `express-winston-middleware` using [NPM][npm]:

```
$ npm install express-winston-middleware
```

From there, you can create a `Log` instance or use the `request` Express
middleware.

### Logger

The `Log` logger class internally creates and wraps a Winston logger. You
can create one with:

```js
var winston = require("winston");
var Log = require("express-winston-middleware").Log;

// Create logger with Console transport and "foo" metadata item.
var log = new Log({
  transports: [
    new (winston.transports.Console)({ json: true })
  ]
}, {
  // Metadata to add to each log response.
  foo: "bar"
});

// Log something.
log.info("Hello World!");
```

which produces the following output:

```js
{
  "date": "2013-12-01T23:29:48.035Z",
  "env": "development",
  "server": {
    "id": "m",
    "pid": 24638,
    "hostName": "titan.local"
  },
  "foo": "bar",
  "level": "info",
  "message": "Hello World!"
}
```

### Express Middleware

The `request` middleware is added to your Express setup like:

```js
var express = require("express");
var app = express(),
var requestLogger = require("express-winston-middleware").request;

/* ... */

// Same options and meta as for the `Log` class.
app.use(logging.createMiddle(requestLogger({
  transports: [
    new (winston.transports.Console)({ json: true })
  ]
}, {
  // Metadata to add to each log response.
  foo: "bar"
})));

```

and produces output for requests like:

```js
{
  "date": "2013-12-01T23:32:54.759Z",
  "server": {
    "id": "m",
    "pid": 24653,
    "hostName": "titan.local"
  },
  "req": {
    "method": "GET",
    "host": "localhost:2000",
    "path": "/",
    "query": ""
  },
  "res": {
    "statusCode": 304
  },
  "foo": "bar",
  "level": "info",
  "message": "request"
}
```

## API

* [`request(opts, baseMeta)` - Express request middleware](#-request-opts-basemeta-express-request-middleware)
* [`error(opts)` - Express error middleware](#-error-opts-express-error-middleware)
* [`uncaught(opts)` - Global uncaught exception handler](#-uncaught-opts-global-uncaught-exception-handler)
* [`Log(opts, baseMeta)` - Logger class.](#-log-opts-basemeta-logger-class-)
* [`Log.addMeta(meta)`](#-log-addmeta-meta-)
* [`Log.addReq(req)`](#-log-addreq-req-)
* [`Log.addRes(res)`](#-log-addres-res-)
* [`Log.addRes(err)`](#-log-addres-err-)

### `request(opts, baseMeta)` - Express request middleware

Creates a middleware function using base metadata. Integration:

```
app.use(winMid.request({
  transports: [ new (winston.transports.Console)({ json: true }) ]
}, { foo: "bar" }));
```

Once integrated, a logger will be attached to the response locals,
and available as `res.locals._log`.

### `error(opts)` - Express error middleware

Creates a middleware function for Express. Integration:

```
app.use(winMid.error({
  transports: [ new (winston.transports.Console)({ json: true }) ]
}));
```

### `uncaught(opts)` - Global uncaught exception handler

Creates a handler function for any uncaught exception. Integration:

```
process.on("uncaughtException", winMid.uncaught({
  transports: [ new (winston.transports.Console)({ json: true }) ]
}));
```

**Note**: Terminates process at end.

### `Log(opts, baseMeta)` - Logger class.

Wraps Winston logger with additional functionality.

```
var log = new winMid.Log({
  transports: [ new (winston.transports.Console)({ json: true }) ]
}, { foo: "bar" }));
```

### `Log.addMeta(meta)`

Add arbitrary meta to all subsequent log statements.

### `Log.addReq(req)`

Add request to meta.

### `Log.addRes(res)`

Add response to meta.

### `Log.addRes(err)`

Add error to meta.

## Contributions

Please see the [Contributions Guide](./CONTRIBUTING.md) for how to help out
with the plugin.

## Licenses
All code is Copyright 2013 Ryan Roemer.
Released under the [MIT](./LICENSE.txt) License.

[winston]: https://github.com/flatiron/winston
[express]: http://expressjs.com/
[npm]: https://npmjs.org/package/express-winston-middleware
