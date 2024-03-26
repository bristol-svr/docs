# Background

Bun is the latest and arguably the fastest javascript and typescript runtime environment, similar to node and deno. Bun uses the faster and less bloated JSC (JavaScriptCore) engine unlike node and deno which is based on the v8 browser engine.

Bun is written in a low-level manual memory management programming language called ZIG.

Bun supports ~90% of the native nodejs APIs including `fs`, `http`, `path` etc. and also distribute it's packages using npm hence both yarn and npm CLI tools are supported in bun.

Colstonjs on the other hand is a fast, minimal and highly configurable typescript based web framework (inspired by ExpressJs and FastifyJs) for building high performance APIs. Colstonjs is currently built for bunjs.

## Getting Started with Colstonjs

### Prerequisite
Bun is required to run Colstonjs hence it needs to be installed on your development machine.

### Installation
To install bun, head over to the official [website](http://bun.sh) and follow the installation instructions.

To install `@colstonjs/core` run

```bash
# Initialize a new Bun project
$ bun init

# Install Colstonjs core package
$ bun add @colstonjs/core
```
#### _NOTE_
_Although colstonjs is distributed under npm, colstonjs is only available for bun, node and deno are not currently supported._

### Usage
Once the setup is complete, configure the entry point of your application.

1. **Modify the Entry Point File (`index.ts`):**
   Update the content of `index.ts` to import the Colstonjs package and initialize a server options.

```ts
// index.ts
import Colston, { Context } from '@colstonjs/core';

// Initialize server options
const serverOptions = {
   port: 8000,
   env: 'development',
   ...
};
```

2. **Create Colstonjs app Instance:**
   Next, update the content of `index.ts` to instantiate the Colstonjs server instance.

```ts
// initialize app with server options
const app: Colston = new Colston(serverOptions);
```
3. **Create a simple GET route**
   Attach a simple get route on the app instance as follows

```ts
// register a route path
app.get('/', (ctx: Context) => {
   return ctx.status(200).text('Hello Colstonjs');
});
```

To allow our app to listen to incoming requests, we have to call the `listen()` method with an optional
port number and callback function. 

   ```ts
   // app.listen<T extends number | Function>(port?: T, cb?: Function): Server;
   app.listen();
   ```

The `listen()` method is an overloaded method that can take any of the form
   ```ts
   // takes default port `3000` if serverOptions.port is not provided as well
   app.listen();

   // takes only port number
   app.listen(8000);

   // takes only a callback function
   app.listen(() => { console.log(':listening'); });

   // takes both port number and a callback function
   app.lsiten(8000, () => { console.log(':listening'); });
   ```

#### _NOTE_
_`port` number can be passed to the `app` through the server options or as the first and only argument of the `listen()` method. If the the port number is passed as part of the server options and also in the `listen()` method, then the port number passed to the `listen()` takes precedence. If neither is provided, then the app will default to port `3000`_

_`callback` method is immediately invoked once the connection is successfully established and the application is ready to accept requests._

The complete **index.ts**
   ```ts
   import Colston, { Context } from '@colstonjs/core';

   // Initialize server options
   const serverOptions = {
      port: 8000,
      env: 'development',
      ...
   };

   // initialize app with server options
   const app: Colston = new Colston(serverOptions);

   // register a route path
   app.get('/', (ctx: Context) => {
      return ctx.status(200).text('Hello Colstonjs');
   });

   // register app to a listener
   app.listen(8000, () => { console.log(':listening'); });
   ```


### Running Our Application

To launch the application, execute the following command

```bash
# Start the server using the entry point file
bun index.ts

# Or starting the app in watch mode
bun --watch index.ts

# Alternatively, you can use the shorthand command 
# this only works if your entry file is named `index.ts | index.js`
bun .
```

### Next Steps

<!-- rephrase -->
You have successfully created a basic Colstonjs application! As you continue, the documentation will introduce you to more advanced concepts and capabilities of the Colstonjs framework.