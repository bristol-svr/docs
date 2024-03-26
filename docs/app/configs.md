# Configurations

Colstonjs allow for a wide range of configuration and customization using built in configuration for the server and also custom utility functions.

## Server Configuration
The server accepts a configuration object that specifies the mode, the security policies, route prefixes etc. Below are some of the configurable server options

```ts
export interface IServerOptions {
	env?: 'development' | 'production';
	port?: number;
	hostname?: string;
	unix?: string;
	prefix?: string;
	ignoreTrailingSlash?: boolean;
	eTag?: boolean;
	maxRequestBodySize?: number;
	error?<T extends ErrorLike, U extends TErrorResponse>(error: T): U;
	/**
	 * This sets `OPENSSL_RELEASE_BUFFERS` to 1.
	 * It reduces overall performance but saves some memory.
	 * @default false
	 */
	lowMemoryMode?: boolean;
	websocket?: {
		maxPayloadLength?: number; // default: 16 * 1024 * 1024 = 16 MB
		idleTimeout?: number; // default: 120 (seconds)
		backpressureLimit?: number; // default: 1024 * 1024 = 1 MB
		closeOnBackpressureLimit?: boolean; // default: false
		sendPings?: boolean; // default: true
		publishToSelf?: boolean; // default: false

		perMessageDeflate?: | boolean | {
			compress?: boolean | WebSocketCompressor;
			decompress?: boolean | WebSocketCompressor;
		};
	};

	tls?: {
		keyFile?: string;
		certFile?: string;
		passphrase?: string;
		caFile?: string;
		dhParamsFile?: string;
  }
};
```

*`ignoreTrailingSlash: [boolean]`* signals the router to ignore all trailing slashes when matching the paths against the registered routes or to strictly include it during path matching. The `ignoreTrailingSlash` IServerOptions defaults to `false`, if not provided.<br/>

**Examples**
```ts
import Colston, { Router, Context } from '@colstonjs/core';

const router = new Router();
cosnt app = new Colston({ ignoreTrailingSlash: true });

// to match these routes we need to call `/v1/notes` from the client,
// the trailing `/` will be ignore, else we have to call `/v1/notes/`
app.get('/notes/', (ctx: Context) => {...});
router.get('/notes/', (ctx: Context) => {...});
```

For a complete explanation of some of these interfaces, take a look at the Bunjs HTTP server options reference [here](//bun.sh/docs/api/http#bun-serve)

## Router configuration
Colston also allow a very minimal configuration of the Router class object to specify how the routing feature functions.

*`prefix: [string]`* Signals the router if it should prefix all the route in this router instance with the provided prefix string.

**Examples**
```ts
import { Router, Context } from '@colstonjs/core';

const router = new Router({ prefix: '/v1' });

// to match this route we need to call `/v1/notes` from the client
router.get('/notes', (ctx: Context) => {...});
```
