+++
title = "Implementation Jwt"
date = 2021-01-17T21:02:41+01:00
draft = true
weight = 3
+++

Due to the fact that Node follows the same pattern as general JS the ecosystem is extremely rich and at times  quite
divergent. As Node and Frontend developers with JS you will become quite familiar with javascript fatigue. Every day
more and more frameworks are developed and choosing one can be daunting. Try to choose a library that is well maintained
and perhaps even has some good backing. By far the oldest and most popular is __express__. Other worthy mentions are
[__koa__](https://koajs.com/) and [__hapi__](https://hapi.dev/). Others you may want to check out are [__restify__](http://restify.com/),
[__fastify__](https://www.fastify.io) and [__loopback__](https://loopback.io/)there is even a do-everything framework [__meteor__](https://blog.meteor.com/meteor-1-9-node-js-12-and-new-roadmaps-cb993277d444)

{{< lazy-image image="js-fatigue.jpg" lightbox=false />}}

Plenty of stuff [*out there...*](https://www.toptal.com/nodejs/nodejs-frameworks-comparison)

Most existing libraries will have followed the same patterns you can find in express in order to allow for easy
integration. The things you want to pay special attention to are testability and scalability.

We're going to use express for our api because it is very well suited for most api development needs. It's also very
lean so there is not much noise in the code.

You can write up the app manually or use the `express-generator` package to quickly get set up. The app creator makes
use of a templating language called `pug`(a later version of `jade`). This is not important as most times you will use a
spa library for the frontend however we will not look at that at all.

Our ground zero will be the base repo that we put together. It has a react client and a nice way to stand up the various
parts in the infra. This will prevent you from having to set up `redis`, `mongo` locally and in order to clean up you
can just remove containers.

```bash
cd goodreads-api
git checkout 02-authentication
docker-compose up -d mongodb
npm run dev
```

We have a registration in place but no login so we want to actually implement that. We will use the
[`jsonwebtoken`](https://www.npmjs.com/package/jsonwebtoken) library from Auth0.

We will split the functionality between routes and controllers. This is not a standard MVC architecture and in `nodejs`
the way to implement features is to focus in keeping everything very simple.

We have the register function in `controllers/user.js`. We will add a login method in the same file that will handle
generating the token and signing it.

First we want to grab the data from the request and pass it onto the controller login method. A few things to notice
here is that we pass our data as a `http-only` cookie so it will not be accessible from the client. This means that
every authed client component needs to check the token with the auth endpoint. This depends on your security
requirements.

```js
// routes/user.js
...
  app.post("/auth/login", async function (req, res, next) {
    const { email, password } = req.body;
    try {
      console.log(email, password);
      const token = await login({ email, password });
      if (!token) {
        throw Error("Token is null");
      }
      return res
        .cookie("token", token, { httpOnly: true })
        .sendStatus(200);
    } catch (e) {
      /* handle error */
      return res
        .status(401)
        .json({ msg: "Invalid Email + Password combination" });
    }
  });
...
```

All the business logic will happend in the controller login method. The main idea is to find the user with the specified
email and check the password against it. If everything works as expected what we want is to sign it using the
jsonwebtoken library and send it back to the client. We also want to pass in a payload. Careful though the payload
should not contain any sensitive data.

```js
// controllers/user.js
...
const secret = process.env.JWT_SECRET;
...
exports.login = async function ({ email, password }) {
  try {
    const user = await User.findOne({ email });
    if (!user.comparePassword(password))
      throw Error("Password + Email combination invalid");
    return jwt.sign({ email: user.email, fullName: user.fullName }, secret);
  } catch (e) {
    return null;
  }
};
...
```

The token we used is technically a __`JWS`__ token as it only has the signature as tamper prevention mechanism. There is
also __`JWE`__ which means that the payload is encrypted so even if the cookie is somehow intercepted you have another
layer of protection on top. Encrypting the payload is not a must and __`JWS`__ is sufficiently secure when implemented
correctly.
The way it is protected is that if somebody will try to grab the token from the browser cookies and changes the payload
the signature will no longer match. It is crucial that the `JWT_SECRET` is a strong random key that is cryptographically
complex to reverse engineer.

The nice things about __JWTs__ that makes it a ood choice for authentication:
1. they don't require any server side state
2. they work well with central authentication and authorization
3. they provide good security for your API

A few disadvantages to note:
1. Revoking them requires custom implementation and will require server side storage of some sort for the refresh token
   and verification. Also the refresh token should not be sent across on every request.
