+++
title = "How Node Testing Works"
date = 2021-01-26T21:12:36+01:00
draft = false
weight = 1
+++

## Know the tools
{{< lazy-image image="knowledge.gif" lightbox=false />}}

There are a bunch of libraries for testing in node:

1. __runners__ i.e. scripts for running tests (e.g. `mocha`, `jest`)
2. __adjacent utilities__ i.e. assertion libraries (e.g. `chai`)
3. __mocking__ while this can be lobbed in with adjacent utilities it wouldn't do it justice because mocking is a big
   part of unittests. It helps you write faster tests by faking all the slow bits. (e.g. `moxios`)
4. __integration test libraries__ these are perfect for testing end to end flow. (e.g. `supertest`)
5. __coverage reports__ these are the tools that tell you how much of your code is covered by tests. This is the right
   approach to test your app. You want to get coverage reports to measure what you've automated (e.g. `jest`, `nyc`)
6. __manual testing__ you can also always use tools like `postman` and `swagger` to document and manually test your
   endpoints. This is not great but provides you with a good way to get a sense of how the api works. This is e2e
   testing.

There are quite a few libraries that you can use and most times you will want to stick with one in order to not have
subtle errors in your tests. A common situation would be when you have different versions of assertion library.

## What to use
There is no silver bullet when it comes to testing and in the end it's all about the fans. You want to choose a library
that fits in well with the rest of your code. For example if you're using React with Jest, most likely, you may want to
try and use that into your node app as well.
`Jest` and `mocha` are both great choices for testing your node api. You can even mix and match, but be careful when you
do as stated previously you may step into a world of hurt if you have your dependencies mixed up.

## Did you use TDD?

If you use TDD from the get go your app will be in very good shape and adding new and tested features. The reason for
this is because you code will already be robust and decoupled in order for you to have implemented the tests in the
first place.

What you need to do to achieve this is to keep functional units small, use dependency injection and personally I would
recommend making your code functional when you find ways to do so. I'm not a huge advocate of functional code but I have
found that you can test it easier and since JS does not have classes in a way that classical OOP languages understand.

## The first stop towards tests

We need to refactor the current code in order to make it more testable. There are a few sections that we need to look
at. `app.js` and `bin/www`. These are the entrypoints in our app so that is where we will begin our refactor.

There are two chunks of module init, one is for routes and the other for the db that we probably want to extract into
more reusable chunks of code. This can be easily done, but the order of initialization logic needs to be preserved.

```js
const createError = require("http-errors");
const express = require("express");
const cookieParser = require("cookie-parser");
const logger = require("morgan");
const mongoose = require("mongoose");
const passport = require("passport");

require("dotenv").config();

function setupDb(app) {
  const mongoURI = process.env.MONGO_URI;
  const mongoPort = process.env.MONGO_PORT;
  const mongoUser = process.env.MONGO_USER;
  const mongoPass = process.env.MONGO_PASSWORD;
  const mongoDbName = process.env.MONGO_DB_NAME;
  const option = {
    socketTimeoutMS: 30000,
    keepAlive: true,
    useNewUrlParser: true,
    useUnifiedTopology: true,
  };

  (async () => {
    try {
      await mongoose.connect(
        `mongodb://${mongoUser}:${mongoPass}@${mongoURI}:${mongoPort}/${mongoDbName}?authSource=${mongoDbName}`,
        option
      );
    } catch (error) {
      console.log("Failed to connect to mongodb");
    }
  })();

  require("./models/book");
  require("./models/image");
  require("./models/rating");
  require("./services/cache");
}

function setupRoutes(app) {
  require("./services/oauth/passport");
  const indexRouter = require("./routes/index");
  app.use("/", indexRouter);
  require("./routes/user")(app);
  require("./routes/book")(app);
}

function createApp() {
  setupDb();

  const app = express();

  app.use(passport.initialize());
  app.use(passport.session());
  app.use(logger("dev"));
  app.use(express.json());
  app.use(express.urlencoded({ extended: false }));
  app.use(cookieParser());

  setupRoutes(app);
  // catch 404 and forward to error handler
  app.use(function (req, res, next) {
    next(createError(404));
  });

  // error handler
  app.use(function (err, req, res, next) {
    // set locals, only providing error in development
    res.locals.message = err.message;
    res.locals.error = req.app.get("env") === "development" ? err : {};

    // render the error page
    res.status(err.status || 500);
    res.json({ msg: "error" });
  });
  return app;
}

const app = createApp();

module.exports = app;
```

Once we have something like this we can start writing the tests. We will look at doing that next.
