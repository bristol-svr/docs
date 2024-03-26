# WebSocket

Colstonjs supports WebSocket which is an abstraction on the [Bun native WebSocket](//bun.sh/docs/api/websockets) implementation which on the other hand is based on the [µWebSockets](//github.com/uNetworking/uWebSockets) implementation.

Colstonjs also support the routing of WebSockets as well as providing specialized handlers to handle the events on a specific socket route.


## Registering a WebSocket

```ts
import Colston from '@colstonjs/core';

const app = new Colston();

app.ws('/ping', {
  message<T>(ws: ServerWebSocket<T>, msg: string | Uint8Array) {
    ws.send('Pong!!!');
  }
});

app.listen(8000, () => console.log(':listening'));
```

The above snippet registers a WebSocket handler on a `/ping` route with a `message` event handler which is responsible for handling an message received from the WebSocket client.

A simple WebSocket client to handle the above registered socket on the server on the `route` path is shown in the snippet below

```ts
const socket = new WebSocket('ws://localhost:8000/ping'); // assuming the server is listening on `localhost:8000`

socket.addEventListener('open', () => { socket.send('from client') });
socket.addEventListener('message', (event) => { console.log('from server', event.data) });
socket.addEventListener('close', (event) => { console.log('from server', event.code) });
```

The Colstonjs WebSocket has some predefined handler functions which are designed to handle all the possible socket events.

```ts
...
app.ws('/{route}', {
  // core handlers
  open?(): IWebSocketResponse,
  message?(ws: ServerWebSocket<T>, msg: string | Uint8Array): IWebSocketResponse,
  close?(): IWebSocketResponse,
  drain?(): IWebSocketResponse,
  ping(data: Buffer): IWebSocketResponse,
  pong(data: Buffer) IWebSocketResponse,

  // special handler that executes before any core handler
  // specifally useful for auth check and pre-execution processing
  before?(ctx: Context): Promise<Context> | Context,

  /**
   * special `hanlder` function that sets the upgrade data,
   * this is used for passing data to the websocket server.
   * see the `ws` chat-room example for how it can be used.
   */
  upgrade?(ctx?: Context): Record<string, any>
})
...
```

The `before()` is a special handler that is specific to Colstonjs and is executed on each socket call to the server and it runs before any of the core handlers executes, as the name implies, it is useful for authentication and authorization checks and also for pre-handler execution processing etc.

The `upgrade()` is also a special handler that is responsible for passing the return data (must be an object) to the WebSocket server so it can be accessible every other place within the handler functions. The `chat-room` example illustrates how this can be used to customize the upgrade data of the WebSocket server.

For a more practical application of the WebSocket functionality, checkout the example directory in the Github source for the WebSocket specific [examples](//github.com/bristol-svr/examples/tree/master/ws).