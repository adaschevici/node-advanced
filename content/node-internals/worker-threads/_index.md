+++
title="Worker Threads"
date=2021-01-16T15:36:10+01:00
draft=false
weight=3
+++

## Worker Threads

The `worker_threads` module enables the use of threads that execute JavaScript in parallel. 

```javascript
const worker = require('worker_threads');
```
{{% notice note %}}
Workers (threads) are useful for performing CPU-intensive JavaScript operations. They do not help much with I/O-intensive work. The Node.js built-in asynchronous I/O operations are more efficient than Workers can be.
{{% /notice %}}

https://www.nearform.com/blog/workers-in-node-js-how-to-write-a-sudoku-solving-server/

Workers are originally a feature of the Web, where they have enabled developers to run background tasks without blocking the Browser’s rendering thread for a long time.

Similarly, in Node.js running synchronous JavaScript code for a long time is considered a bad practice, as it keeps the event loop from handling I/O and other HTTP requests that are waiting.

Traditionally, the solution to the issue of CPU-intensive tasks in Node.js has been spawning multiple child processes that handle requests in parallel, managed through process managers like pm2 or the built-in cluster module of Node.js.

Workers do not replace this model, but they do provide an alternative for use cases in which easier communication between the different tasks is desirable. For example, they provide ways to share or transfer typed arrays when that is helpful.

https://blog.insiderattack.net/deep-dive-into-worker-threads-in-node-js-e75e10546b11 


In Node.js, each worker will have its own instance of V8 and Event Loop. However, unlike child processes, Workers can share memory.

### How do they work?

https://www.nearform.com/blog/workers-in-node-js-how-to-write-a-sudoku-solving-server/ 

https://github.com/sebastian-curland/worker-threads-presentation/blob/master/Node_%20from%20single%20thread%20to%20worker%20threads.pdf 


//TODO 
livecoding worker threads

## Tradeoffs 

cost of creating new worker threads vs performance

//TODO 
workout https://repl.it/@dpjayasekara/threadpool 

Use piscina instead https://www.nearform.com/blog/learning-to-swim-with-piscina-the-node-js-worker-pool/ 

threads.js https://github.com/andywer/threads.js 

authentication example

