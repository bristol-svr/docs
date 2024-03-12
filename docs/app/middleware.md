# Middleware

Colstonjs support both route level middleware as well as app level middleware. App level middleware are the middleware that are called on every request, the route level middleware on the other hand are middleware that are called when a request is made to that specific route.

All middleware functions in Colstonjs has the same structure, basically it's a function that takes in a context (type Context) and an optional next (type Next) as parameters and returns a response or a null

```ts
// middleware definition
function middleware(context: Context, next?: Next): Response | null |  Promise<Response | null>;
```

## Application Level Middleware

This is a middleware which will be called on each request made to the server, one use case can be for logging, modifying all requests coming into the server e.g `cors()` handling, attaching a request id (to identify each request) to every requests etc. To use the application level middleware, we call the `.use()` on the Colston (app) instance object.

```ts
import Colston, { Colston } from 'colston';

function cb(ctx: Colston): Response | null;
new Colston().app(cb): void;
...
```

Below is a simple snippet that show a practical example of how to effectively use the application level middleware

```ts
// logger.ts
export function logger(ctx: Context, next?: Next) {
  const { pathname } = ctx.request;
  console.info([new Date()], ' - - ' + ctx.request.method + ' ' + pathname + ' HTTP 1.1' + ' - ');
  next();
}

// server.ts
import Colston, { Context } from '@colstonjs/core';
import { logger } from './logger';

const app: Colston = new Colston({ env: 'development' });

app.use(logger);  // [2022-07-16T01:01:00.327Z] - - GET / HTTP 1.1 -
app.listen(8000);
```

The `.use()` accepts `k` numbers of middleware functions.

```ts
app.use(fn-1, fn-2, fn-3, ..., fn-k);
```

## Route Level Middleware

Colstonjs on the other hand allows you to add a middleware function in-between the route `path` and the `handler` function. The route level middleware can be used for authentication and authorizing a request, validation of request body, manipulate requests headers etc.

```ts
new Router().get('/', function(ctx: Context, next?: Next): Response | null, handler(ctx: Context): Response);
```

Below is a practical example on how to include a route level middleware into an application.

```ts
// request-id.ts
import crypto from 'crypto';
import { Context, Next } from '@colstonjs/core'

export function requestID(ctx: Context, next?: Next) {
  ctx.request.id = crypto.randomBytes(18).toString('hex');
  next();
}

// server.ts
import Colston, { Context } from '@colstonjs/core';
import { requestID } from './request-id';

const app: Colston = new Colston({ env: 'development' });

app.get('/', requestID, (ctx: Context) => {
  return ctx.status(200).text(`id: ${ctx.request.id}`); // id: 410796b6d64e3dcc1802f290dc2f32155c5b
});

app.listen(8000);
```

Just like the application level middleware, the route level middleware also can have `k` level of middleware functions.

```ts
// server.ts
...
app.get('/', fn-1, fn-2, fn-3, ..., fn-k, (ctx: Context) => {
  return ctx.status(200).text(`id: ${ctx.request.id}`); // id: 410796b6d64e3dcc1802f290dc2f32155c5b
});
...
```

The `next(err?: Error)` function takes in an error-like parameter and should be called for control to move over to the next middleware in line or to the finally route handler.

## The Route Handler
The route handler is a special type of a middleware that finally handles the incoming requests if none of the app level or route level middleware handles the request.

The final request handler has the following signature.

```ts
function handler(context: Context): Response;
```
