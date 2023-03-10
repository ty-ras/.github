# Typesafe REST API Specification (TyRAS)

The Typesafe REST API Specification (TyRAS) is a project aimed to make it easy and intuitive to create compile- and runtime safe HTTP servers and clients operating on a shared data scheme.
Currently TyRAS contains multiple libraries written in TypeScript, to be used by both HTTP servers and clients.

# Using TyRAS

To use TyRAS in your project, start by making few choices:
- Pick the data validation framework
  - [io-ts](https://github.com/gcanti/io-ts),
  - [zod](https://github.com/colinhacks/zod), or
  - [runtypes](https://github.com/pelotom/runtypes) (not yet implemented, but TyRAS support coming soon!).
- For server projects, pick the HTTP server to use:
  - [Node HTTP(S) 1/2 server](https://nodejs.org/dist/latest-v18.x/docs/api/http.html),
  - [koa](https://github.com/koajs/koa) (not yet implemented, but TyRAS support coming soon!),
  - [expressjs](https://github.com/expressjs/express) (not yet implemented, but TyRAS support coming soon!), or
  - [fastify](https://github.com/fastify/fastify) (not yet implemented, but TyRAS support coming soon!).
- For client projects, pick the HTTP request framework to use:
  - [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API),
  - [Node request API](https://nodejs.org/api/http.html) (not yet implemented, but TyRAS support coming soon!), or
  - [axios](https://github.com/axios/axios) (not yet implemented, but TyRAS support coming soon!).

With the choices made, add the following dependency to your `package.json` file if doing server project:
```json
  // Required for minimal operation
  "@ty-ras/backend-<server>-<data validation>-openapi": "0.13.5",
  // Optional addons containing various utility methods commonly used in server projects
  "@ty-ras-extras/backend-<data validation>": "0.13.5",
```

Correspondingly, the client project dependencies will look like this:
```json
  // Required for minimal operation
  "@ty-ras/frontend-<client>-<data validation>": "0.13.1",
  // Optional addons containing various utility methods commonly used in client projects
  "@ty-ras-extras/frontend-<data validation>": "0.13.2",
```

After running dependency installation with your favorite package manager, it is possible to start the server...
```ts
import * as t from "io-ts";
import * as tyras from "@ty-ras/backend-node-io-ts-openapi";

// Start HTTP server created by tyras.createServer
await tyras.listenAsync(
  // Create simple HTTP server which responds "world!" from "/hello" URL.
  tyras.createServer({
    endpoints: [
      // Just one endpoint
      // Specify URL of the endpoint
      tyras.startBuildingAPI().atURL`/hello`
        // Specify the HTTP method for the endpoint
        .forMethod("GET", {
          stateInfo: undefined,
          validator: tyras.plainValidator(t.undefined),
        })
        // Specify the functionality and output validator for the endpoint
        .withoutBody(
          // Implementation
          () => "world!" as const,
          // Output validation
          tyras.responseBody(t.literal("world!"), false),
          // Metadata additional information (not present in this small code sample)
          {},
        )
        // We are fine with just GET at this URL, create endpoint
        .createEndpoint({}).endpoint,
    ],
  }),
  // Host to listen to
  "0.0.0.0",
  // Port to listen to
  8080,
);

console.log("HTTP server started.");
```

There is more systematic approach also instead of adhoc code above, to define REST API on server side.
While the npx starter template is still not completed, the [basic sample repository](https://github.com/ty-ras/sample-be-fe-monorepo) and [multitenancy sample repository](https://github.com/ty-ras/sample-be-fe-monorepo-multitenant) provide some example on that. 

And on the frontend, it will look something like this:
```ts
// services/backend.ts
import * as tyras from "@ty-ras/frontend-fetch-io-ts";
import * as t from "io-ts";

const apiCall = tyras
  // Bind endpoints to go to this server
  .createAPICallFactory(tyras.createCallHTTPEndpoint("http://localhost:8080"))
  // No header functionality for this simple sample
  .withHeaders({});

// Export the backend object
export default {
  // We have only one endpoint - these can be freely named as seen best
  sayHello: apiCall.makeAPICall({
    // Notice that these match the ones specified in server sample
    method: tyras.METHOD_GET,
    url: "/hello",
    response: tyras.plainValidator(t.literal("world!")),
  }),
};
```
```ts
// some-view.ts
import backend from "services/backend";

const renderMyView = () => {
  ...
  // onClick will be of type
  // () => Promise<tyras.APICallResult<"world!">>
  const onClick = async () => await backend.sayHello();
  ...
};
```
