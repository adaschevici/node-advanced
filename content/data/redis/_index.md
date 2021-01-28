---
title: "Redis"
date: 2021-01-27T20:34:20+01:00
draft: false
---

{{< lazy-image image="redis.png" lightbox=true />}}

Redis (*re*mote *di*ctionary *se*rver) is an open source, in-memory data structure store, used as a database, cache, and message broker.

To run redis locally, execute:

```bash
docker-compose up redis
```

In your browser, go to `http://localhost:8081/` to access Redis Commander.

### Operations

In the terminal, execute:

```bash
SET name "fullstacked.io"
GET name

SET visits 0
INCR visits
INCR visits
INCR visits
DECR visits

MSET node:mongo 1 node:redis 10
MGET node:mongo node:mongo

SET snapchat hi EX 5
EXISTS snapchat

```

### Data Types

- Strings
- Lists
- Sets
- Sorted sets
- Hashes
- HyperLogLogs
- Streams

```bash

//lists
RPUSH notifications:simona "Talk about MongoDB" "Talk about Redis" "LiveCoding"
LRANGE notifications:simona 0 -1

//Hashes
HMSET simonaco:profile title "principal cloud advocate" company "Microsoft" city "London" country "UK"
HGET simonaco:profile city
HGETALL simonaco:profile

//Sets
SADD stonks gme acm tsla dava msft
SMEMBERS stonks

```

### Good Reads App

{{< lazy-image image="node_redis.png" lightbox=true />}}

Let's install redis [Node Redis](https://github.com/NodeRedis/node-redis)

`npm i redis`

Let's import `redis` and initialise client in the `blogRoutes.js`, `/api/blogs` route:

```javascript
const redis = require("redis");
const util = require("util");
client.get = util.promisify(client.get);
const redisUrl = "redis://127.0.0.1:6379";
const client = redis.createClient(redisUrl);

const cachedBooks = await client.get("books");

if (cachedBooks) {
  console.log("SERVING FROM CACHE");
  return res.json(cachedBooks);
}

const books = await Book.find({});
console.log("SERVING FROM DB");

client.set("books", JSON.stringify(books));

res.json(blogs);
```

Let's test this!

Any potential issues with the current code?

## Workout

Implement caching and cleaning of cache for books in progress for each user. Extract cleaning cache in middleware.
