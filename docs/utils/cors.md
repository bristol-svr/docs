# Handling CORS
<!-- The `Colstonjs` package simplifies Cross-Origin Resource Sharing (CORS) handling in web applications. Here's how you can use the `CORS` class for effective CORS management. -->
Colstonjs package specifies a simple `interface` by which _Cross Origin Resource Sharing_ (CORS) can be handled within a web application specifically when building APIs.

We have a simple implementation that handles the use case for most of the functionalities involving `cors` handling.

To add this utility module, we first install the package `@colstonjs/cors` in our project.

```bash
bun add @colstonjs/cors
```

## Basic Setup

Without passing in a configuration option for our CORS handler, we rely on the simple default implementation.

```ts
import Colston from 'colstonjs';
import cors from '@colstonjs/cors'; // Or import { cors } from 'colstonjs/utils';

const app = new Colston({ env: 'development' });

// use the default cors middleware
app.use(cors());

app.listen(8000);
```

## Advance CORS Configuration

We can further configure the CORS handler by passing a configuration options to further fine tune the behaviour of our CORS handler.


```ts 
interface CorsOptions {
  headers?: string | string[] | undefined;
  origin?: string | string[] | RegExp | (origin: string, callback: (err: Error | null, allow?: boolean | undefined) => void) => void;
  methods?: string | string[];
  preflightContinue?: boolean;
  optionsSuccessStatus?: number;
  credentials?: boolean;
  allowedHeaders?: string | string[];
  exposedHeaders?: string | string[];
  maxAge?: number | string;
}

import Colston from 'colstonjs';
import cors from 'colstonjs/cors'; // Or import { cors } from 'colstonjs/utils';

const app = new Colston({ env: 'development' });

const whitelist = ['http://localhost:5500', 'http://example.com'];
const corsOptions: CorsOptions = {
  origin: function (origin: string, callback: Function) {
    if (whitelist.indexOf(origin) !== -1) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  }
}

// use the enhanced cors middleware
app.use(cors(corsOptions));

app.listen(8000);
```
This `cors` utility library is a typescript port of the [cors](https://www.npmjs.com/package/cors) npm package fully adapted internally to work with Colstonjs.

## Custom Implementation

If the above implementations doesn't cover the entire use case of the application, a custom CORS handler can be implemented and passed in as a middleware function.

```ts
import { Context, Next } from '@colstonjs/cors';

// cors.ts
export const cors = (options?: object) => {
  return (ctx: Context, next: Next) => {
    // Set headers to allow all origins
    ctx.header.set('Access-Control-Allow-Origin', '*');
    // Set allowed methods
    ctx.header.set('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS');
    // Set allowed headers
    ctx.header.set('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept');

    // Check if it's a preflight request
    if (ctx.req.method === 'OPTIONS') {
      // End preflight request
      return ctx.status(200).head();
    } else {
      // Move to the next middleware
      next();
    }
  }
}
```

The above custom `cors` handler implementation can then be imported and used as shown below
```ts
// server.ts
import Colston from 'colstonjs';
import { cors } from './cors';

const app = new Colston();

app.use(cors())

app.listen(8000);
```