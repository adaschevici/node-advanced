+++
title = "Tools and Code"
date = 2021-01-26T21:13:23+01:00
draft = false
weight = 2
+++

## First tests

This is our first test, very crisp and simple, because the route is in itself really isolated and does not do very much,
but it gives us a taste.

```js
const express = require("express");
const request = require("supertest");
const indexRoute = require("../routes");

const initApp = () => {
  const app = express();
  app.use(indexRoute);
  return app;
};

describe("GET /", () => {
  test("It should fetch root", async () => {
    const app = initApp();
    const theres = await request(app).get("/");
    expect(theres.body).toStrictEqual({ Hello: "World" });
  });
});
```

Let's write something a bit more complex. You will notice the app initialization changes slightly because we are making
use of all the underlying libraries(i.e. middlewares, orm).

```js
const request = require("supertest");
const app = require("../../app");

describe("GET /auth/login", () => {
  test("It should get a bad request if no payload present", async () => {
    const theres = await request(app).post("/auth/login");
    console.log(theres.status);
    expect(theres.status).toStrictEqual(400);
  });
  test("It should get 401 if email password combo is not found", async () => {
    const theres = await request(app).post("/auth/login").type("form").send({
      email: "art",
      password: "password",
    });
    expect(theres.status).toStrictEqual(401);
  });
  test("It should get 200 if correct email and password", async () => {
    const theres = await request(app).post("/auth/login").type("form").send({
      email: "simple@user.com",
      password: "superpassword",
    });
    expect(theres.status).toStrictEqual(200);
  });
});
```

The preinitialization is required and a simple initialization of the app is no longer enough. The fact that the login
requires access to the DB we now need to boot up the `mongo` container and also `redis`, so in order for the tests to
pass you need to boot up the docker containers using `docker-compose up`

## Mocking

So far the test have been making use of the real database and the real redis key-value store. If we want to test in
isolation we need to start mocking the db and also redis.

In order to skip doing a direct call to the database and instead use a mock or stub.
We have already installed `sinon` and `jest` that both have mocking capabilities.

Mock with Jest:
```js
const mockResponse = () => {
  const res = {};
  res.status = jest.fn().mockReturnValue(res);
  res.json = jest.fn().mockReturnValue(res);
  return res;
};
```

Stub with sinon:
```js
const sinon = require('sinon');

const mockResponse = () => {
  const res = {};
  res.status = sinon.stub().returns(res);
  res.json = sinon.stub().returns(res);
  return res;
};
```

For example these two mocks could be really useful for testing middlewares.

Clients on the other hand need to be mocked by constructing a mock system for the api, where you mock all external
dependencies.

A redis client could be mocked as follows however this means that the code may require refactoring to pass in the
redisClient wherever it is being used. Notice that due to the async nature of node this needs to be mocked with async
responses because the real thing is async as well.

```js
const mockRedisClient = {
  setAsync: jest.fn(() => Promise.resolve())
  getAsync: jest.fn(() => Promise.resolve('{}'))
};
```

## Coverage

Normally you want to run tests with coverage in order to understand what is covered by tests and what isn't. The library
that does this most often is [__nyc__](https://github.com/istanbuljs/nyc) however if you are using [jest](https://www.npmjs.com/package/jest) or [tap](https://node-tap.org/) this is already avaliable.
So let's add a new npm script for returning the coverage.

```bash
...
    "test:cov": "jest --coverage -- tests",
...
```
