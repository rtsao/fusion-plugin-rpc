# fusion-plugin-rpc

[![Build status](https://badge.buildkite.com/5165e82185b13861275cd0a69f29c2a13bc66dfb9461ee4af5.svg?branch=master)](https://buildkite.com/uberopensource/fusion-plugin-rpc)

Fetch data on the server and client with an RPC style interface.

If you're using React/Redux, you should use [`fusion-plugin-rpc-redux-react`](https://github.com/fusionjs/fusion-plugin-rpc-redux-react) instead of this package.

---

### Installation

```
yarn add fusion-plugin-rpc
```

---

### Example

```js
// src/main.js
import React from 'react';
import App from 'fusion-react';
import RPC from 'fusion-plugin-rpc';
import fetch from 'unfetch';

// Define your rpc methods server side
const handlers = __NODE__ && {
  getUser: async (args, ctx) => {
    return {some: 'data' + args};
  },
};

export default () => {
  const app = new App(<div></div>);

  const Api = app.plugin(RPC, {handlers, fetch});
  app.plugin((ctx, next) => {
    Api.of(ctx).request('getUser', 1).then(console.log) // {some: 'data1'}
  });

  return app;
}
```

---

### API

```js
const Api = app.plugin(RPC, {handlers, fetch, EventEmitter});
```

- `handlers: Object<(...args: any) => Promise>` - Server-only. Required. A map of server-side RPC method implementations
- `fetch: (url: string, options: Object) => Promise` - Browser-only. Required. A `fetch` implementation
- `EventEmitter` - Server-only. Optional. An event emitter plugin such as [fusion-plugin-universal-events](https://github.com/fusionjs/fusion-plugin-universal-events)

##### Instance methods

```js
const instance = Api.of(ctx)
```

- `instance.request(method: string, args: any)` - make an rpc call to the `method` handler with `args`.

If on the server, this will directly call the `method` handler with `(args, ctx)`.

If on the browser, this will `POST` to `/api/${method}` endpoint with JSON serialized args as the request body. The server will then deserialize the args and call the rpc handler. The response will be serialized and send back to the browser.

### Testing

The package also exports a mock rpc plugin which can be useful for testing. For example:

```js
import {mock as MockRPC} from 'fusion-plugin-rpc';
app.plugin(mock, {
  handlers: {
    getUser: (args) => {
      return {
        mock: 'data',
      }
    }
  }
});
```
