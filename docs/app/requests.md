# Requests

The Colstonjs request object is an abstraction of the standard Web Fetch API Request class object with some additional features to ease the daily use of this framework.

The request object is part of the Colstonjs Context which can be accessed by calling the `requst` property of the Context object.

## Request Body

The Colstonjs request body can be read as a `ArrayBuffer`, `Blob`, `Text` or `Json`

```http
GET /body HTTP/1.1
Host: example.com
Content-Type: application/json

{
    "name": "jane",
    "age": 50
}
```

The above `HTTP/1.1` request can be read from the application as can be seen in the snippet below

```ts
app.get('/body', async (ctx: Context) => {
    // read the request body and return a promise that resolves to text
    await ctx.request.text(); // '{ "name": "jane", "age": "50" }'

    // read the request body and return a promise that resolves to json
    await ctx.request.json(); // { "name": "jane", "age": 50 }

    // read the request body and return a promise that resolves to arrayBuffer
    await ctx.request.arrayBuffer();

    // read the request body and return it as a blob
    await ctx.request.blob();
});
```

## Context Request Object

The `context.request` which is aliased as `ctx.req` is where the entire request object resides. Below is the complete properties of the `Request` object used in `Colstonjs`

```ts
interface BunRequest<T> {
    // index signatures
    [key: string]: any;

    // Contains the request's method (GET, POST, etc.)
    method: string;

    // Request path
    path: string;

    // Contains the associated Headers object of the request.
    headers: Headers & { [key: string]: any };

    // URL path parameters
    params: { [key: string]: any };

    // The URL query parameter(s) for thie current request
    query: { [key: string]: any };

    // A ReadableStream of the body contents.
    body: T;

    // Contains the URL (originalUrl) of the request.
    originalUrl: string;

    // The domain of the request URL
    hostname: string;

    // The client domain or simply the host without the port number if supported.
    host: string;

    // Stores true or false to indicate whether or not the body has been used in a request yet.
    bodyUsed: boolean;

    // Contains the cache mode of the request (e.g., default, reload, no-cache).
    cache: RequestCache;

    // Contains the credentials of the request (e.g., omit, same-origin, include). The default is same-origin.
    credential: RequestCredentials;

    // A string describing the type of content being requested.
    destination: RequestDestination;

    // Contains the subresource integrity value of the request (e.g., sha256-BpfBw7ivV8q2jLiT13fxDYAe2tJllusRSZ273h2nFSE=).
    integrity: string;

    // Bun specific property for determining if the current request is keep-alive enabled
    keepalive: boolean;

    // Contains the mode of the request (e.g., cors, no-cors, same-origin, navigate.)
    mode: RequestMode;

    // Contains the mode for how redirects are handled. It may be one of follow, error, or manual.
    redirect: RequestRedirect;

    // Contains the URL (url) of the request.
    url: string;

    // Contains the referrer of the request (e.g., client).
    referrer: string;

    // Bun specific property
    verb: string;

    // Contains the referrer policy of the request (e.g., no-referrer).
    referrerPolicy: ReferrerPolicy;

    // Returns the AbortSignal associated with the request
    signal: AbortSignal;

    // Complete URL of the origin making the Request (e.g http://127.0.0.1:8000)
    origin: string;

    // The absolute client URL (e.g http://127.0.0.1:8000/api/request)
    href: string;

    // The request protocol used when making a request to the server (e.g http, https, ws, ftp etc.)
    protocol: string;

    // This is client URL origin without the protocol
    host: string;

    // Returns a promise that resolves with an ArrayBuffer representation of the request body.
    arrayBuffer(): Promise<ArrayBuffer>;

    // Returns a promise that resolves with a Blob representation of the request body.
    blob(): Promise<Blob>;

    // Creates a copy of the current Request object.
    clone(): Request;

    // Returns a promise that resolves with a FormData representation of the request body.
    formdata(): Promise<FormData>;

    // Returns a promise that resolves with the result of parsing the request body as JSON.
    json(): Promise<any>;

    // Returns a promise that resolves with a text representation of the request body.
    text(): Promise<string>;
}
```

## Request Headers Object

The request header is object that contains all request header information originating from the client.

The `context.request.headers` is an abstraction on the Web Fetch API request headers with some added bun specific properties.

```ts
interface Headers {
    // append a string onto a header property
    append(key: string, value: string): void;

    // delete a given header property
    delete(name: string): void;

    // get all the header values as an iterable string array
    entries(): IterableIterator<[string, string]>;

    // loop all header key-value pairs
    forEach(callbackfn: (value: string, key: string, parent: Headers) => void, thisArg?: any): void:

    // get a given header property
    get(name: string): string | null;

    // get the set cookie
    getSetCookie(): string[];

    // check if the header has the given string
    has(name: string): boolean;

    // retrieve all the header keys as an iterable string array
    keys(): IterableIterator<string>;

    // the request origin
    origin: string | Function | RegExp;

    // set a given header value to a specified key
    set(name: string, value: string): void
}
```
