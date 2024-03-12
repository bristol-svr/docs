# Router
The Colstonjs router is a class that handles all the routing in Colstonjs.

## Handling Routes
Colstonjs simplifies the process of handling different endpoints in your web application. Here’s a guide on setting up and managing routes.

## Setting Up Routes
1. **Initialize a Router instance**: Make a new Router instance.
2. **Register Route path**: Register a route path (pathname), on the router instance object.

```ts
import { Router, type Context } from '@colstonjs/core';

const router: Router = new Router(options?: TRouterOptions);
router.get('/notes', (ctx: Context) => {});
```

- The `router.{method}` registers a handler for the `METHOD` requests matching the provided `/{pathname}` path. In this case, we registered handler function `(ctx: Context) => {}` for a `GET` method on the `/notes` path.
- Colstonjs supports various HTTP methods, including `POST`, `PUT`, `DELETE`, and `PATCH`.

```ts
router.get('/note/:id', (ctx: Context) => {});       // GET a single note object by id path param
router.post('/note', (ctx: Context) => {});          // POST a note object
router.patch('/note', (ctx: Context) => {});         // PATCH a note object
router.put('/note', (ctx: Context) => {});           // PUT a note object
router.delete('/note/:id', (ctx: Context) => {});    // DELETE a note object
```

### Route Configurations
The `new Router(options?: TRouterOptions)` constructor takes in an optional router options object, `ignoreTrailingSlash` and `prefix`

```ts
const options: TRouterOptions = {
    prefix: '/v1',
    ignoreTrailingSlash: true,
}

const router = new Router(options);
```

1.  **ignoreTrailingSlash**
This options is set will ignore all tailing slashes in all the registered routes, e.g `/notes/` -> `/notes`. This option defaults to `true`
2. **prefix**
This option prefixes a static string before the provided route path e.g `prefix: '/v1'` + `/notes` -> `/v1/notes`. This is essential when there is a need to version your APIs

```ts
import { Router, type Context } from 'colston';

const router1 = new Router({ prefix: '/v1' });
const router2 = new Router({ prefix: '/v2' });

// The route can be matched using `/v1/notes`
router1.get('/notes', (ctx: Context) => {
    /** handler v1 endpoint */
});

// The route can be matched using `/v2/notes`
router2.get('/notes', (ctx: Context) => {
    /** handler v2 endpoint */
});
```

It is important to note that the `IServerOptions` configuration also accepts a global `ignoreTrailingSlash` property, if set, it will be used for all the routes. When both the global `ignoreTrailingSlash` from the `IServerOptions` and that from the `TRouterOptions` are set, the property from the `TRouterOptions` take priority as this make more sense since it's specific to a given router instance.

## Request Handler
Handlers are functions that process incoming requests. They accept a single parameter, the current request context and returns a response.
We will look into this in more details in the middleware section.

## Route Patterns
Colstonjs supports both static and dynamic route patterns, including *wildcards and URL parameters. Also, since the each route method (i.e `.get()`, `.post()` etc) returns a router instance, the next router method can be chain onto the previous.

```ts
new Router()
  .get('/note/:id', ctx => {
    // Accessing URL path parameter `id`from `/note/2`
    ctx.req.params.id;    // 2
  })
  .post('/note', ctx => {
    // Accessing URL query parameter `id` and
    // `title` from /note?id=1&title='note-title'
    ctx.req.query.id;     // 1
    ctx.req.query.title;  // note-title
  });
```

See [routington](https://github.com/pillarjs/routington) Github page for more information on dynamic routing patterns.

**Performance Note**: Dynamic patterns (like wildcards and URL parameters) match slower than static ones. Use query parameters where possible for better performance.
