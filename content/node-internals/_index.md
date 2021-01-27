+++
title= "Node Overview"
date= 2021-01-16T15:03:41+01:00
draft= false
pre = "<b>1. </b>"
weight = 1 
+++

## Understanding Node

Node.js is an open source project designed to help you write JavaScript programs that talk to networks, file systems or other I/O (input/output, reading/writing) sources. That's it! It is just a simple and stable I/O platform that you are encouraged to build modules on top of.

At a lower level, node can be described as a tool for writing two major types of programs:

- Network programs using the protocols of the web: HTTP, TCP, UDP, DNS and SSL
- Programs that read and write data to the filesystem or local processes/memory

What is an "I/O based program"? Here are some common I/O sources:

- Databases (e.g. MySQL, PostgreSQL, MongoDB, Redis, CouchDB)
- APIs (e.g. Twitter, Facebook, Apple Push Notifications)
- HTTP/WebSocket connections (from users of a web app)
- Files (image resizer, video editor, internet radio)

## The Node Way

“Every programming platform has its own philosophy, a set of principles and guidelines that are generally accepted by the community, or an ideology for doing things that influence both the evolution of the platform and how applications are developed and designed.”

Mario Casciaro & Luciano Mammino. “Node.js Design Patterns.”

- **Small Core**: Node.js has a small core group of modules (aka node core) while leaving the rest to the 'userland' (npm modules); this encourages a strong & healthy ecosystem culture while making the core faster and more maintainable

- **Small modules**: smallest building block for creating reusable libraries and applications; small refers to both size and functionality and it's rooted in the Unix philosophy:

  "Small is beautiful"
  "Make each program do one thing well"

Smaller modules are more reusable, easier to understand and use, simpler to test and maintain. The implementation of "Don't Repeat Yourself".

Complexity via composition. Separating development across the ecosystem requires some careful design considerations. In Node.js, the wide adoption of ecosystem modules led to favoring Composition rather than Inheritance to avoid having to know the internals of modules & de reactive to changes.

- **Small Surface Area**: each module should be exposing a minimal set of functionality through its API; modules created to be used rather than extended. In practice, this means preferring to expose functions instead of classes, and being careful not to expose any internals to the outside world.

## How does Node.js work under the hood?

Node.js relies on a collection of libraries and tools which eventually are aggregated into the high performant runtime.

### Libraries

1. v8 — high-performance JavaScript compilation and execution
1. Libuv — event-driven asynchronous I/O
1. c-ares — DNS operations
1. llhttp - parser for HTTP messages
1. OpenSSL - implementation of cryptographic functions
1. zlib - for fast compression and decompression

{{< lazy-image image="node.png" lightbox=true />}}

### Tools

1. npm - share and manage node packages
1. gyp - meta-build system: a build system that generates other build systems
1. gtest - testing and mocking framework

## Ecosystem overview

At its core, node is just a tool used for managing I/O across file systems and networks, and it leaves other more fancy functionality up to third party modules. Here are some of the most useful tools you might consider using:

### Web frameworks

- [Express](https://github.com/expressjs/express) - Web application framework, providing a robust set of features for building single and multi-page, and hybrid web applications.
- [Hapi](https://github.com/hapijs/hapi) - Framework for building applications and services.
- [Fastify](https://github.com/fastify/fastify) - Fast and low overhead web framework.
- [Nest](https://github.com/nestjs/nest) - Angular-inspired framework for building efficient and scalable server-side apps.

### Database

- Drivers
  - [PostgreSQL](https://github.com/brianc/node-postgres) - PostgreSQL client. Pure JavaScript and native libpq bindings.
  - [MySQL](https://github.com/mysqljs/mysql) - MySQL client.
    - [MongoDB](https://github.com/mongodb/node-mongodb-native) - MongoDB driver.
- ODM / ORM
  - [Sequelize](https://github.com/sequelize/sequelize) - Multi-dialect ORM. Supports PostgreSQL, SQLite, MySQL, and more.
    - [Mongoose](https://github.com/Automattic/mongoose) - Elegant MongoDB object modeling.

### Testing

- [Jest](https://github.com/facebook/jest) - Painless JavaScript testing.
- [Puppeteer](https://github.com/GoogleChrome/puppeteer) - Headless Chrome.
- [Playwright](https://github.com/microsoft/playwright) - Headless Chromium, WebKit, and Firefox with a single API.

- [debug](https://github.com/visionmedia/debug) - Tiny debugging utility.
- [Passport](https://github.com/jaredhanson/passport) - Simple, unobtrusive authentication.
- [Benchmark.js](https://github.com/bestiejs/benchmark.js) - Benchmarking library that supports high-resolution timers and returns statistically significant results.
- [nodemon](https://github.com/remy/nodemon) - Monitor for changes in your app and automatically restart the server.
- [Strapi](https://github.com/strapi/strapi) - Content Management Framework (headless-CMS) to build powerful APIs.
- [dotenv](https://github.com/motdotla/dotenv) - Load environment variables from .env file.

Node.js is so awesome that we have an awesome-awesome list for it! [awesome-awesome-nodejs](https://github.com/bnb/awesome-awesome-nodejs)

The master awesome list (awesome-node.js)[https://github.com/sindresorhus/awesome-nodejs]

## Use cases

Open ended discussion :)
