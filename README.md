# @overra/koa-history-api-fallback
Koa v2 implementation of [connect-history-api-fallback](https://github.com/bripkens/connect-history-api-fallback)

[![npm](https://img.shields.io/npm/v/@overra/koa-history-api-fallback.svg?maxAge=3600)]()
[![David](https://img.shields.io/david/dev/overra/koa-history-api-fallback.svg?maxAge=3600)]()

## Introduction

This is a fork of [koa-history-api-fallback](https://github.com/dalhorinek/koa-history-api-fallback) which is a simple rewrite of [connect-history-api-fallback](https://github.com/bripkens/connect-history-api-fallback)

## Usage

The middleware is available through NPM and can easily be added.

```
npm install --save @overra/koa-history-api-fallback
```

Import the library

```javascript
const historyApiFallback = require('koa-history-api-fallback');

const app = new Koa();

app.use(historyApiFallback());
```

To use this with [koa-webpack-middleware](https://github.com/leecade/koa-webpack-middleware) you'll need to do the following.

```javascript
const {devMiddleware, hotMiddleware} = require('koa-webpack-middleware')
const historyApiFallback = require('@overra/koa-history-api-fallback')

// ...
const app = new Koa()
const webpackMiddleware = devMiddleware(compiler, options)

app.use(webpackMiddleware) // serve up webpack content
app.use(historyApiFallback()) // catch any other requests and redirect to /index.html
app.use(webpackMiddleware) // serve up webpack content
```

## Options
You can optionally pass options to the library when obtaining the middleware

```javascript
const middleware = historyApiFallback({});
```

### index
Override the index (default `/index.html`)

```javascript
historyApiFallback({
  index: '/default.html'
});
```

### rewrites
Override the index when the request url matches a regex pattern. You can either rewrite to a static string or use a function to transform the incoming request.

The following will rewrite a request that matches the `/\/soccer/` pattern to `/soccer.html`.
```javascript
historyApiFallback({
  rewrites: [
    { from: /\/soccer/, to: '/soccer.html'}
  ]
});
```

Alternatively functions can be used to have more control over the rewrite process. For instance, the following listing shows how requests to `/libs/jquery/jquery.1.12.0.min.js` and the like can be routed to `./bower_components/libs/jquery/jquery.1.12.0.min.js`. You can also make use of this if you have an API version in the URL path.
```javascript
historyApiFallback({
  rewrites: [
    {
      from: /^\/libs\/.*$/,
      to: function(context) {
        return '/bower_components' + context.parsedUrl.pathname;
      }
    }
  ]
});
```

The function will always be called with a context object that has the following properties:

 - **parsedUrl**: Information about the URL as provided by the [URL module's](https://nodejs.org/api/url.html#url_url_parse_urlstr_parsequerystring_slashesdenotehost) `url.parse`.
 - **match**: An Array of matched results as provided by `String.match(...)`.


### verbose
This middleware does not log any information by default. If you wish to activate logging, then you can do so via the `verbose` option or by specifying a logger function.

```javascript
historyApiFallback({
  verbose: true
});
```

Alternatively use your own logger

```javascript
historyApiFallback({
  logger: console.log.bind(console)
});
```
