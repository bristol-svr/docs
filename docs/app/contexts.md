# Context

The context object is a special object that is very specific to Colstonjs, it runs from the first application level middleware to the final route handler. It contains various usual object properties that transmits information throughout the application.

## Context as a Response Object

The context serves as both the request and the response object. The context is basically the response object with an attached `request` or `req` object property meant to completely represent the request.

The context object has (almost) all the necessary properties and methods needed to build a robust web application using Colstonjs.

## Context Signature

The context object has a very simple signature that makes it really easy to work with. Below are all the object properties within the context object.

```ts
interface Context {
  // set status code for the currrent request
  status(code: number): Context;

  // set the status text for the current request
  statusText(text: string): Context;

  // set the request options (incl. headers and statusText) of the current request
  option(options?: ResponseInit): Context;

  // set a header value of the current request using the specified key (if key already exist, it will be overriden)
  setHeader(key: string, value: string): Context

  // the special variable used to transmit a variable or value (primitive of reference) between middleware chains
  locals: Record<string, any>;

  // the HTTP request (Request) object
  req: Request;

  // the HTTP request (Request) object
  request: Request;

  // returns all the HTTP response headers
  headers: HeadersInit;

  // header property use to get, set and append a given header
  header: {
    get(key: string): string;
    set(key: string, value: string): void;
    append(key: string, value: string): void;
  };

  // context/response method to return only the response headers
  head(options?: ResponseInit): Response;

  // method to return the raw response
  raw(body: any, options?: ResponseInit): Response;

  // method to render a view such as html or jsx
  render(view: string, options?: ResponseInit): Response;

  // method to send the response object back to the client
  send(body: object | any): Response;

  // response method to send a generic response to the cleint
  response(o: any, options?: ResponseInit): Response;

  // method to send text responses to the client
  text(text: object | any, options?: ResponseInit): Response;

  // metod to send a json response to the client
  json(body: Record<string, any>, options?: ResponseInit): Response;
}
```

## Sending Responses

Below shows an example on how to use the context object and send basic responses.

```ts
context.status(204).head()                // sends a response with headers only.
context.status(200)text('OK')             // sends a plain text response.
context.status(200).raw([File])           // sends a raw file to the client
context.render('<h1>Hi</h1>', { statusText: 'text/html' })  // sends an HTML response.
context.statusText('text/html').raw('<h1>Hi</h1>', { status: 200 }) // sends an HTML response.
context.option({ status: 200, statusText: 'OK' }).json({ foo: 'bar' })  // sends a JSON response.
context.setHeader('Basic', 'GrVZnV6Y==').send('OK', { statusText: 'text/html' }) // similar to context.response().
```

## Setting and Retrieving Headers

```ts
context.header.set('Basic', 'GrVZnV6Y');
context.header.get('Basic'); // GrVZnV6Y

context.header.append('Basic', '-2uQPt1hIR20==')
context.header.get('Basic'); // GrVZnV6Y-2uQPt1hIR20

context.setHeader('Basic', 'ANQiXvob2Pg=')
context.get('Basic') // ANQiXvob2Pg=

context.header.set('Content-Type', 'application/json')
context.headers(); // { 'Basic': 'ANQiXvob2Pg', 'Content-Type': 'application/json', ... }
```