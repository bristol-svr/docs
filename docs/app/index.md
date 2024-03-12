# Getting Started
Colstonjs provides a simple and elegant way to build web applications. To get started, you first need to install the main component, `colstonjs`.

## Installation
Use Bun to install `colstonjs`:

```bash
$ bun add colstonjs
```

## Initializing Your Application
<!-- rephrase -->
Once installed, you can set up your application by importing and running the `Colston` class module from `colstonjs`.
This module takes an optional configuration object where you specify essential settings for your application, such as the routes prefix, `port`, `env` etc.

### Example:
Create an entry point file (e.g., `index.ts`) and add the following code:

```ts
import Colston, { Context } from '@colstonjs/core';

type IServerOptions = {
    // Specify your application configurations here
    env?: 'development' | 'production',
    port?: number,
    hostname?: string,
    unix?: string,
    /**
     * prefix all route with a string
     * @example
     * - without prefix
     * route -> /notes
     *
     * - with prefix '/api/v1'
     * route -> /api/v1/notes
     */
    prefix?: string,
    ignoreTrailingSlash?: boolean, // '/note/'' -> '/note'
    prefix?: string,
    maxRequestBodySize?: number,
    /**
     * This sets `OPENSSL_RELEASE_BUFFERS` to 1.
     * It reduces overall performance but saves some memory.
     * @default false
     */
    lowMemoryMode?: boolean,
    keyFile?: string,
    certFile?: string,
    passphrase?: string,
    caFile?: string,
    dhParamsFile?: string,

    tls?: {
        key?: TLSKey;
        cert?: TLSCert;
        ca?: TLSCa;
        passphrase?: TLSPassphrase;
        dhParamsFile?: TLSDhParamsFile;
    };
};

const app: Colston = new Colston(options?: IServerOptions);
```

A simple route can be defined on the `app` instance as follows:

```ts
app.get('/', (ctx: Context) => {
    return ctx.text('This is a simple GET route');
});
```

Also, we can create a route using the `Router` class module from `colstonjs`

```ts
import { Router, Context } from '@colstonjs/core';

interface TRouteOptions {
    prefix?: string;
    ignoreTrailingSlash?: boolean;
}

const router = new Router(options?: TRouteOptions);

router.get('/note', (ctx: Context) => {
    return ctx.text('This is a GET route from a Router instnace');
});
```

## Application Instance Cache
This is a simple javascript `Map()` data structure that can hold a key-value entries attached to the `app: Colston` instance.

```ts
...
const app: Colston = new Colston(options?: IServerOptions);

// set a value using a key
app.set(key: string, value: string): void;

// check if the key exists
app.has(key: string): boolean;

// get the value of a specific cache key
app.get(key: string): string | number | any
...
```

#### Examples

Setting and retrieving different cache entries

```ts
// set properties to cache
app.set('@age', 50);
app.set('@name', 'jane doe');

// check if a key exists in the cache
app.has('@age');        // true
app.has("@name");       // true

// retrieve the value stored in a given cache key
app.get('@age');        // 50
app.get('@name');       // jane doe

// set object
app.set('@data', JSON.stringify({ name: 'jane doe', age: 50 }));

// get object
app.get('@data')        // '{"name":"jane doe","age":50}'
                        // JSON.parse('{"name":"jane doe","age":50}')
```

## Error Handling
Errors are handled internally by Colston, however the error handler can be overridden by a custom error handler.

#### Example
```ts
import Colston, { Context } from '@colstonjs/core';

const app: Colston = new Colston({ env: 'development' });

// a broken route
app.get("/error", (ctx) => {
    throw new Error('This is a broken route');
});

// Custom error handler
app.error = function (error: Error) {
    console.debug('This is an error...');
    const err = JSON.stringify(error);
    // return custom error here
    return new Response(JSON.stringify(
        new Error(error.message || "An error occurred" + err);
    ), { status: 500 });
}
```

Another way to pass in a custom error handler is through the `serverOption` configuration

```ts
import Colston, { Context, ServerOptions } from '@colstonjs/core';

const serverOptions: ServerOptions = {
    env: 'development',
    error: (error: Error) => {
        console.debug('This is an error');
        const err = JSON.stringify(error);
        // return custom error here
        return new Response(JSON.stringify(
            new Error(error.message || "An error occurred" + err);
        ), { status: 500 });
    }
    ...
}

const app: Colston = new Colston(serverOptions);
...

app.start(8000);
```

<!-- rephrase -->
This minimal setup is all you need to get your Colstonjs application running.
As you develop your application, you can expand the configuration to include additional options and features provided by Colstonjs.
