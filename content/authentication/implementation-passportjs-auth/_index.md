+++
title = "Implementation Passport Authentication"
date = 2021-01-17T21:02:53+01:00
draft = false
weight = 4
+++

Most apps and services nowadays allow you to login using third party oauth2 services like Google, Facebook, Github,
Gitlab etc. The benefit with this is that you are not forcing your users to signup per se with your app but rather you
are requesting their permission for login via the respective platform's authentication server.

This also gives you some granular level information about the user, like a profile picture, name etc. This makes the
user onboarding more UX friendly. The one downside to it is that the user will need to keep track of the permissions
they have granted previously to apps but this is available in the authorization platform.

Oauth authentication is a pretty cool feature and it used to be quite a pain to implement untill `passportjs` came
along. Now it is more or less straightforward as it has a unified interface for implementing the authentication. The way
it works is through a middleware layer.

[__`passportjs`__](http://www.passportjs.org/docs/) has an extensive pluggable system for authentication built around
all sorts of authentication. Previously we looked at JWT with `http-only` cookies. Let's turn that into user + password
signup and login. For you to get a sense `passportjs` boasts 300 packages as plugins for various authentication
mechanisms. We will have a crash course in some use cases for `passportjs` but the source of truth moving forward should
be the docs mentioned [here](http://www.passportjs.org/docs/).

## Nuff said, let's see some code

Initially what we want to do is add a few extra required packages
{{< highlight json "hl_lines=10-11 14-16" >}}
...
  "dependencies": {
    "bcrypt": "^5.0.0",
    "cookie-parser": "~1.4.4",
    "debug": "~2.6.9",
    "dotenv": "^8.2.0",
    "express": "~4.16.1",
    "http-errors": "~1.6.3",
    "jsonwebtoken": "^8.5.1",
    "lodash": "^4.17.20",
    "moment": "^2.29.1",
    "mongoose": "^5.11.13",
    "morgan": "~1.9.1",
    "passport": "^0.4.1",
    "passport-github2": "^0.1.12",
    "passport-local": "^1.0.0"
  },
...
{{< /highlight >}}

We need to add hooks in the main `app.js` api entry point to load passport.

```javascript
// app.js
...
const passport = require("passport");
...
app.use(passport.initialize());
app.use(passport.session());
...
```

We need to update the user model because we will store extra data, especially later when we will implement social login
via Github later

```js
const userSchema = new Schema(
  {
    email: { type: String, unique: true },
    passwordHash: String,
    passwordResetToken: String,
    passwordResetExpires: Date,
    emailVerificationToken: String,
    emailVerified: Boolean,

    github: String,
    tokens: Array,

    profile: {
      name: String,
      picture: String,
    },
  },
  { timestamps: true }
);

```

The important part with `passport.js` is the middeware. We want to associate a string id to it so that can use it as a
string reference for use later. This is the meat and potatoes in the code that is required for integrating with this
library.

```js
// services/oauth/passport.js
const passport = require("passport");
const { Strategy: LocalStrategy } = require("passport-local");
const _ = require("lodash");
const moment = require("moment");
const jwt = require("jsonwebtoken");

const User = require("../../models/User");

passport.serializeUser((user, done) => {
  done(null, user.id);
});

passport.deserializeUser((id, done) => {
  User.findById(id, (err, user) => {
    done(err, user);
  });
});

passport.use(
  "local-signup",
  new LocalStrategy(
    {
      // by default, local strategy uses username and password, we will override with email
      usernameField: "email",
      passwordField: "password",
      passReqToCallback: true, // allows us to pass back the entire request to the callback
    },
    function (req, email, password, done) {
      // asynchronous
      // User.findOne wont fire unless data is sent back
      process.nextTick(function () {
        // find a user whose email is the same as the forms email
        // we are checking to see if the user trying to login already exists
        User.findOne({ email }, function (err, user) {
          // if there are any errors, return the error
          if (err) return done(err);

          // check to see if theres already a user with that email
          if (user) {
            return done(
              null,
              false,
              console.log("signupMessage", "That email is already taken.")
            );
          } else {
            // if there is no user with that email
            // create the user
            const newUser = new User({
              profile: {
                name: req.body.fullName,
              },
              email,
              passwordHash: password,
            });

            // save the user
            newUser.save(function (err) {
              if (err) throw err;
              return done(null, newUser);
            });
          }
        });
      });
    }
  )
);

passport.use(
  "local-login",
  new LocalStrategy(
    {
      // by default, local strategy uses username and password, we will override with email
      usernameField: "email",
      passwordField: "password",
      passReqToCallback: true, // allows us to pass back the entire request to the callback
    },
    function (req, email, password, done) {
      // callback with email and password from our form

      // find a user whose email is the same as the forms email
      // we are checking to see if the user trying to login already exists
      User.findOne({ email }, function (err, user) {
        // if there are any errors, return the error before anything else
        if (err) return done(err);

        // if no user is found, return the message
        if (!user)
          return done(
            null,
            false,
            console.log("loginMessage", "No user found.")
          ); // req.flash is the way to set flashdata using connect-flash

        // if the user is found but the password is wrong
        if (!user.comparePassword(password))
          return done(
            null,
            false,
            console.log("loginMessage", "Oops! Wrong password.")
          ); // create the loginMessage and save it to session as flashdata

        // all is well, return successful user
        return done(null, user);
      });
    }
  )
);
```

We need to tweak the user routes to make use of the middleware we just added in the passport module.

```js
// routes/user.js
...
  app.post(
    "/auth/register",
    passport.authenticate("local-signup", {
      passReqToCallback: true,
      failureRedirect: "/register", // redirect back to the signup page if there is an error
    }),
    (req, res) => {
      return res.status(200).json({ urlRedirect: "/" });
    }
  );
  app.post("/auth/login", passport.authenticate("local-login"), (req, res) => {
    console.log(req.user);
    return res.status(200).json({ msg: "Successful login" });
  });
...
```

