# Typesafe REST API Specification (TyRAS) Toolset

| ![Animation of Usage when refactoring protocol](https://raw.githubusercontent.com/ty-ras/.github/main/profile/img/usage.gif) |
| :--------------------------------------------------------------------------------------------------------------------------: |
|      Short animation about how refactoring shape of data moving over HTTP protocol shows up in both server and client.       |

The Typesafe REST API Specification (TyRAS) toolset is a framework for developers to use to define REST APIs in easy and intuitive way while preserving compile- and runtime safety HTTP servers and clients operating on a shared data scheme.
Currently TyRAS contains multiple libraries written in TypeScript, to be used by both HTTP servers and clients.

TyRAS will boost productivity and code maintainability whenever there is need to use HTTP protocol either as a server od a client (or both!).
The productivity boost will come from the fact that TyRAS supplies various pre-defined helpers that allow you to precisely define how the HTTP endpoints behave, while at the same time being easy to use and having sensible defaults.
And the maintainability will stem from the TyRAS making it effortless to maintain [DRY principle](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself): define runtime validators (utilizing `io-ts`, `zod`, or `runtypes` framework) in one place, and the compile-time types and OpenAPI JSON schema definitions (for server projects) will stem from the same runtime validator objects.

To re-cap:
- For servers, TyRAS enables the following:
    - ✨ Define server endpoints using [TS5 decorators](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-0.html#decorators) ([aka ES decorators](https://2ality.com/2022/10/javascript-decorators.html)), keeping the code clear, reusable, and concise. ✨
    - 🛠️ Have _both_ **runtime** _and_ **compile-time** validation for all of the input and output of all of the endpoints. 🛠️
    - 📖 Have _compile-time -safe_ OpenAPI documentation, with TyRAS automatically building necessary JSON schema definitions from validation objects. 📖
- For clients, TyRAS enables the following:
    - ✅ Define server endpoint _calls_ as normal asynchronous functions, simplifying code. ✅
    - 🛠️ Have _both_ runtime _and_ compile-time validation for all of the input and output of all of the endpoint call functions. 🛠️

And if TyRAS is used in both server and client (recommended setup!), one can encapsulate the definitions for HTTP endpoints and all of their inputs and outputs into _one place_, furthermore improving the DRY principle.
This is how `@ty-ras/start` starter template generator behaves if one selects the option that project should host both server and client.

The quickest way to explore how TyRAS works is to run `npx @ty-ras/start@latest` and let it set up a project for you.
Alternatively, there exists [sample repo for CRUD backend](https://github.com/ty-ras/sample-crud) and [another sample repo for microservices](https://github.com/ty-ras/sample-microservices) to give larger and more holistic examples on TyRAS usage.

# Using TyRAS For New Projects
When creating a project which is TyRAS-enabled, simply run the starter template package to get most of the initialization done for you:
```sh
npx @ty-ras/start@latest
```

The starter template package will take care of asking the necessary details, and then initialize the project.
It will try to do so in least invasive way, so that after initialization, one would still need to e.g. install dependencies (using favourite package manager).
The instructions will be in the `REAMDE.md` file which will be part of the newly initialized project.

It is also possible to provide details as command-line arguments, to avoid prompting via stdin.
Please run `npx @ty-ras/start@latest --help` for more information about the command-line arguments.

# Using TyRAS In Existing Projects
To use TyRAS in a project which is already created and initialized, start by making few choices:
- Pick the data validation framework
  - [`io-ts`](https://github.com/gcanti/io-ts),
  - [`zod`](https://github.com/colinhacks/zod), or
  - [`runtypes`](https://github.com/pelotom/runtypes).
  - The support for [`@effect/schema`](https://github.com/Effect-TS/schema) will be coming once `1.x` versions are published.
- For server projects, pick the HTTP server to use:
  - [Node HTTP(S) 1/2 server](https://nodejs.org/dist/latest-v18.x/docs/api/http.html),
  - [Koa](https://github.com/koajs/koa),
  - [Express.JS](https://github.com/expressjs/express), or
  - [Fastify](https://github.com/fastify/fastify).
- For client projects, pick the HTTP request framework to use:
  - [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API),
  - [Node request API](https://nodejs.org/api/http.html), or
  - [Axios](https://github.com/axios/axios).

With the choices made, add the following dependency to your `package.json` file if doing server project:
```json
  // Required for minimal operation
  "@ty-ras/backend-<server>-<data validation>-openapi": "^2.0.0",
  // Optional addons containing various utility methods commonly used in server projects
  "@ty-ras-extras/backend-<data validation>": "^2.0.0",
```

Correspondingly, the client project dependencies will look like this:
```json
  // Required for minimal operation
  "@ty-ras/frontend-<client>-<data validation>": "^2.0.0",
  // Optional addons containing various utility methods commonly used in client projects
  "@ty-ras-extras/frontend-<data validation>": "^2.0.0",
```

Finally, run `npx @ty-ras/start@latest` to some dummy folder, and take a look at the template it creates.
Familiarize yourself with the concepts, and adopt them to your existing project.

Alternatively, instead of running `@ty-ras/start`, one can take a look at [starter template sources](https://github.com/ty-ras/start/tree/main/code/templates/) to get an idea of how the template will look like.
Navigate to `<validation framework>/code` subfolder to see the template contents.