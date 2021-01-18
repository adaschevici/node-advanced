+++
title="Worker Threads"
date=2021-01-16T15:36:10+01:00
draft=false
weight=3
+++

## Worker Threads

{{% notice warning %}}
Don't block the event loop
{{% /notice %}}

CPU Intensive tasks

1. Partitioning using setImmediate

If you need to accomplish a more complex task, partitioning becomes cumbersome and is not a good option. Partitioning uses only the Event Loop, and you won't benefit from multiple cores available on your machine.

{{% notice warning %}}
The Event Loop should orchestrate client requests, not fulfill them itself
{{% /notice %}}

1. Offloading using Child Process or Cluster or custom C++ addon

The downside of the offloading approach is that it incurs overhead in the form of communication costs. Only the Event Loop is allowed to see the "namespace" (JavaScript state) of your application. From a Worker, you cannot manipulate a JavaScript object in the Event Loop's namespace. Instead, you have to serialize and deserialize any objects you wish to share. Then the Worker can operate on its own copy of these object(s) and return the modified object (or a "patch") to the Event Loop.

Also, spawning processes is not cheap.

{{% notice warning %}}
Consider distinguishing between CPU-intensive and I/O-intensive
{{% /notice %}}

## Enter Worker Threads

The `worker_threads` module enables the use of threads that execute JavaScript in parallel. It provides concurrency by allowing applications to use multiple isolated JavaScript workers where the communication between workers and the parent worker is provided by Node.

In Node.js, each worker will have its own instance of V8 and Event Loop. However, unlike child processes, Workers can share memory.

```javascript
const worker = require("worker_threads");
```

{{% notice note %}}
Workers (threads) are useful for performing CPU-intensive JavaScript operations. They do not help much with I/O-intensive work. The Node.js built-in asynchronous I/O operations are more efficient than Workers can be.
{{% /notice %}}

Workers are originally a feature of the Web, where they have enabled developers to run background tasks without blocking the Browser’s rendering thread for a long time.

Similarly, in Node.js running synchronous JavaScript code for a long time is considered a bad practice, as it keeps the event loop from handling I/O and other HTTP requests that are waiting.

Traditionally, the solution to the issue of CPU-intensive tasks in Node.js has been spawning multiple child processes that handle requests in parallel, managed through process managers like pm2 or the built-in cluster module of Node.js.

Workers do not replace this model, but they do provide an alternative for use cases in which easier communication between the different tasks is desirable. For example, they provide ways to share or transfer typed arrays when that is helpful.

### How do they work?

Basic example:

```javascript
const { Worker, isMainThread, parentPort } = require("worker_threads");
const crypto = require("crypto");

if (isMainThread) {
  const worker = new Worker(__filename);
  worker.postMessage("Hello");
  worker.on("message", (message) => console.log(`Generated key ${message}`));
} else {
  parentPort.on("message", (password) => {
    crypto.pbkdf2(password, "b", 100000, 512, "sha512", (err, derivedKey) => {
      if (err) {
        parentPort.postMessage("error", err);
      }
      parentPort.postMessage(derivedKey);
    });
  });
}
```

Worker options - sharing data through workerData and setting resource limits

```javascript
const {
  Worker,
  isMainThread,
  parentPort,
  resourceLimits,
  workerData,
} = require("worker_threads");
const crypto = require("crypto");

if (isMainThread) {
  const worker = new Worker(__filename, {
    workerData: "HelloEndava",
    resourceLimits: { maxOldGenerationSizeMb: 5 },
  });
  worker.postMessage("Hello");
  worker.on("message", (message) => console.log(`Generated key ${message}`));
} else {
  console.log(resourceLimits);
  parentPort.on("message", (password) => {
    crypto.pbkdf2(
      password,
      workerData,
      100000,
      512,
      "sha512",
      (err, derivedKey) => {
        if (err) {
          parentPort.postMessage("error", err);
        }
        parentPort.postMessage(derivedKey);
      }
    );
  });
}
```

## Best practices

1. Use worker thread pools

2. Don't use workers for parallelizing I/O operations

## Individual workout

Implement worker threads using a threadpool. You can either create your own pool or use existing ones like `piscina` or `threads.js`.

Resources:
[threadpool from scratch](https://repl.it/@dpjayasekara/threadpool)

[piscina](https://www.nearform.com/blog/learning-to-swim-with-piscina-the-node-js-worker-pool/)

[threads.js](https://github.com/andywer/threads.js)
