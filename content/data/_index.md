+++
title="Data"
date=2021-01-27T20:19:31+01:00
draft=false
pre = "<b>3. </b>"
weight = 3 
+++

## Good Reads app

{{< lazy-image image="mern_stack.png" lightbox=true />}}

- MongoDB - document database
- Express(.js) - Node.js web framework
- React(.js) - a client-side JavaScript framework
- Node(.js) - the premier JavaScript web server

**MongoDB** works extremely well with Node.js, and makes storing, manipulating, and representing JSON data at every tier of your application incredibly easy. It's also one of the most popular databases in the world today. Over the past decade it has established itself as a reliable custodian of database workloads both small and large.

- Databases - an overarching group of smaller collections of data
- Collections - groups of documents. Almost always you want each of these objects to represent one thing.

To run mongo, go in the terminal:

```bash
docker-compose up mongodb
```

In MongoDB Compass, click connect and paste this url:

`mongodb://SUPERUSERNAME:*****@localhost:27017/goodreads-db?authSource=goodreads-db&readPreference=primary&appname=MongoDB%20Compass&ssl=false`

In the Compass terminal (MongoSH Beta) you can run **queries** like:

```bash
show dbs

use goodreads-db

db.books.insertOne({id:9780375700453, isbn:"61120083", isbn13:"9780375700453", authors:"fullstacked.io", year:2021, title:"Thriving in a pandemic", description:"Lore Ipsum"});

db.books.findOne({id:9780375700453});

db.books.count();

db.books.find({year:2020});

db.books.count({year: 2000});

db.books.find({year: 2000}).limit(20).toArray();

db.books.find({
  title: { $ne: "The Visitor" },
  year: 2000,
}).count();

db.books.find({$and: [{ year: { $gte: 2000 } }, { year: { $lte: 2005 } }],})

db.books.find({ year: 2000 }).sort({ title: -1 });

db.books.updateOne(
  { authors: "Simona Cotin" },
  { $set: { title: "Best workshop ever" } }
);

db.books.updateOne(
  {
    authors: "Brian Holt",
    title: "Best DB workshop",
    year: 2021
  },
  {
    $set: {
    authors: "Brian Holt",
    title: "Best DB workshop",
    year: 2021
    id: 9780375700454,
    isbn: "375700460",
    isbn13: "9780375700454",
    },
  },
  {
    upsert: true,
  }
);
db.books.deleteMany({ year: 2020 });


db.books.createIndex({ id: 9780743273560 });
db.books.find({ id: 9780743273560 }).explain("executionStats");
db.books.find({ id: 9780743273560 }).count();
db.books.getIndexes();
```

### MongoDB in our Node app

## Mongoose

Mongoose provides a straight-forward, schema-based solution to model your application data. It includes built-in type casting, validation, query building, business logic hooks and more, out of the box.

```javascript
const mongoose = require("mongoose");
const { Schema } = mongoose;

/**
 * Book Schema
 */
const bookSchema = new Schema({
  id: Number,
  isbn: {
    type: String,
    unique: true,
    trim: true,
    required: true,
  },
  isbn13: {
    type: String,
    unique: true,
    trim: true,
    required: true,
  },
  authors: String,
  year: Number,
  title: String,
  description: String,
});

mongoose.model("Book", bookSchema);
```

### Popular Goodreads app

{{< lazy-image image="popular-good-reads-app.png" lightbox=true />}}
