+++
title = "Local Docker"
date = 2021-01-27T20:27:28+01:00
draft = false
weight = 3
+++

## Docker

By now everyone should at least have heard of __docker__. It is a great platform for running and isolating your
services' runtime. It is my favorite part in this course, and since I learned how to use it for local development I have
not looked back.

You don't have to set up local databases, key-value stores, no more of that. Instead you set up `docker-compose` and
docker and you are good to go. The docker engine will deal with pulling in the images and starting your services, now
that is pretty cool...who am I kidding...it's a lot cool.

```yaml
version: "3.1"
services:
  mongodb:
    image: mongo
    ports:
      - "${MONGO_PORT}:27017"
    environment:
      - MONGO_INITDB_DATABASE=goodreads-db
    volumes:
      - ./seed/init-mongo.js:/docker-entrypoint-initdb.d/init-mongo.js:ro
      - ./seed/books.json:/docker-entrypoint-initdb.d/books.json:ro
      - ./seed/images.json:/docker-entrypoint-initdb.d/images.json:ro
      - ./seed/ratings.json:/docker-entrypoint-initdb.d/ratings.json:ro
      - ./seed/users.json:/docker-entrypoint-initdb.d/users.json:ro
      - ./seed/init.sh:/docker-entrypoint-initdb.d/setup-db.sh:ro
      - ./seed/mongo-volume:/data/db
  redis:
    image: redis
    ports:
      - "${REDIS_PORT}:6379"

  rc:
    image: rediscommander/redis-commander:latest
    ports:
      - "8081:8081"
    environment:
      - REDIS_HOSTS=redis
    depends_on:
      - redis

```

This is the docker compose file we have been running with up to now. It is pretty good for local development however if
you want to use it in any publicly available infra you want to use [__docker secure storage__](https://docs.docker.com/engine/swarm/secrets/) for storing secrets.
Another great feature about containers is that we can prepopulate data before the service actually starts.
