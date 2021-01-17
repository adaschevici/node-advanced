+++
title="Libuv"
date=2021-01-16T15:12:53+01:00
draft=false
weight=2
+++

{{< lazy-image image="libuv_architecture.png" lightbox=true />}}

## Event Loop

### Timers, Promises, Next Ticks

1. Execution order 

Timers: 
setTimeout() setInterval() setImmediate()
https://blog.insiderattack.net/timers-immediates-and-process-nexttick-nodejs-event-loop-part-2-2c53fd511bb3 

process.nextTick
promises
https://blog.insiderattack.net/promises-next-ticks-and-immediates-nodejs-event-loop-part-3-9226cbe7a6aa 
async/await?

//TODO 

livecoding example including setTimeout, setImmediate, promises

Discussion about output

1. 

## I/O

## Thread Pool
http://docs.libuv.org/en/v1.x/threadpool.html#threadpool 

libuv provides a threadpool which can be used to run user code and get notified in the loop thread. This thread pool is internally used to run all file system operations, as well as getaddrinfo and getnameinfo requests.

Its default size is 4, but it can be changed at startup time by setting the UV_THREADPOOL_SIZE environment variable to any value (the absolute maximum is 1024).

UV_THREADPOOL_SIZE 

//TODO 

livecoding example showing example of running crypto 2/4/6 times; benchmark using apache benchmark 

livecoding example showing example of http requests; benchmark using autocannon

Discussion about cpu intensive operations vs I/O operations

## Best Practices

dns.lookup() vs dns.resolve*()