+++
title = "Recap"
date = 2021-01-27T20:29:00+01:00
draft = true
weight = 5
+++

## We have looked at how our api can be tested and run

1. Docker is amazing for local development
2. There are many libs out there for testing, we can mix and match but need to be careful about the libraries you use so
   that you don't mixup versions.
3. Mocks are powerful but can become complicated
4. Integration testing is useful and easy to implement with `supertest`
5. `Postman` and `swagger` ar amazing tools for docs based testing

## Workout

1. Add unittests for registration so that login is no longer failing if user is not hard coded
2. Dockerize api and load the connection strings as secrets or environment variables
3. Create a postman collection for documenting endpoints
