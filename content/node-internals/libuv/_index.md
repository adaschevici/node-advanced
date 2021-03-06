+++
title="Libuv"
date=2021-01-16T15:12:53+01:00
draft=false
weight=2
+++

## libuv

`libuv` is a cross platform support library for <b>asynchronous I/O</b>. It's written in C and it provides abstractions for:

1. Network operations - net module for TCP, dgram module for UDP
1. File System access and operations through the fs module
1. DNS support - part of the system calls for dns resolution come from c-ares but libuv implements some of the operations as well
1. Threading utilities
1. Timers

High level overview of libuv components and what subsystem they relate to:

{{< lazy-image image="libuv_architecture.png" lightbox=true />}}

### Core Concepts

To use libuv effectively, there's a couple concepts we need to understand:

1. The event loop `uv_loop_t`
1. Handles `uv_handle_t`
1. Requests `uv_req_t`
1. Thread pool

## Event Loop

The I/O (or event) loop is the central part of libuv.

{{< lazy-image image="loop_iteration.png" lightbox=true />}}

```C
int uv_run(uv_loop_t* loop, uv_run_mode mode) {
  int timeout;
  int r;
  int ran_pending;

  r = uv__loop_alive(loop);
  if (!r)
    uv__update_time(loop);

  while (r != 0 && loop->stop_flag == 0) {
    uv__update_time(loop);
    uv__run_timers(loop);
    ran_pending = uv__run_pending(loop);
    uv__run_idle(loop);
    uv__run_prepare(loop);

    timeout = 0;
    if ((mode == UV_RUN_ONCE && !ran_pending) || mode == UV_RUN_DEFAULT)
      timeout = uv_backend_timeout(loop);

    uv__io_poll(loop, timeout);

    /* Run one final update on the provider_idle_time in case uv__io_poll
     * returned because the timeout expired, but no events were received. This
     * call will be ignored if the provider_entry_time was either never set (if
     * the timeout == 0) or was already updated b/c an event was received.
     */
    uv__metrics_update_idle_time(loop);

    uv__run_check(loop);
    uv__run_closing_handles(loop);

    if (mode == UV_RUN_ONCE) {
      /* UV_RUN_ONCE implies forward progress: at least one callback must have
       * been invoked when it returns. uv__io_poll() can return without doing
       * I/O (meaning: no callbacks) when its timeout expires - which means we
       * have pending timers that satisfy the forward progress constraint.
       *
       * UV_RUN_NOWAIT makes no guarantees about progress so it's omitted from
       * the check.
       */
      uv__update_time(loop);
      uv__run_timers(loop);
    }

    r = uv__loop_alive(loop);
    if (mode == UV_RUN_ONCE || mode == UV_RUN_NOWAIT)
      break;
  }

  /* The if statement lets gcc compile it to a conditional store. Avoids
   * dirtying a cache line.
   */
  if (loop->stop_flag != 0)
    loop->stop_flag = 0;

  return r;
}
```

```C
static int uv__loop_alive(const uv_loop_t* loop) {
  return uv__has_active_handles(loop) ||
         uv__has_active_reqs(loop) ||
         loop->closing_handles != NULL;
}
```

### How does this translate to Node.js?

{{< lazy-image image="Node_eventloop.png" lightbox=true />}}

### Timers, Promises, Next Ticks

{{< lazy-image image="eventloop.png" lightbox=true />}}

```javascript
const fs = require("fs");

function parseImportedData(err, data) {
  const booksData = JSON.parse(data);
  console.log(JSON.stringify(booksData.meta[0]));
}

function immediately() {
  console.log("Hello from setImmediate");
}
function delayHello() {
  console.log("Hello from setTimeout");
}
function rateLimiter(delay = 1500) {
  /* The 1500 delay will guarantee the script will not exceed Github request
      limit of 1500 per hour. Only increase if you have a higher rate limit */
  return new Promise((resolve) => setTimeout(() => resolve(true), delay));
}

function blockFor500MS() {
  const start = Date.now();
  for (let i = 0; i < 1000000000; i++) {}
  console.log(Date.now() - start);
}

function handlePromise() {
  console.log("promise 1 resolved");
}

function handlePromiseWithNextTick() {
  console.log("promise 2 resolved");
  process.nextTick(() =>
    console.log("next tick inside promise resolve handler")
  );
}

function handleNextTick() {
  console.log("Hello from nextTick");
}

setTimeout(delayHello, 0);
fs.readFile("./data.json", parseImportedData);
blockFor500MS();
console.log("hi!");
setTimeout(delayHello, 0);
rateLimiter.then(handlePromise);
setTimeout(delayHello, 0);
setTimeout(delayHello, 0);

setTimeout(delayHello, 0);
setImmediate(immediately);

process.nextTick(handleNextTick);
Promise.resolve().then(handlePromise);

Promise.resolve().then(handlePromiseWithNextTick);
```

## Thread Pool

http://docs.libuv.org/en/v1.x/threadpool.html#threadpool

libuv provides a threadpool which can be used to run user code and get notified in the loop thread. This thread pool is internally used to run all file system operations, as well as getaddrinfo and getnameinfo requests.

Its default size is 4, but it can be changed at startup time by setting the UV_THREADPOOL_SIZE environment variable to any value (the absolute maximum is 1024).

UV_THREADPOOL_SIZE

```javascript
const crypto = require("crypto");
const NUM_REQUESTS = 2;

const start = Date.now();
for (let i = 0; i < NUM_REQUESTS; i++) {
  crypto.pbkdf2Sync("secret", "salt", 10000, 512, "sha512");
  console.log(`${i}: ${Date.now() - start}`);
}
```

```javascript
const https = require("https");
const NUM_REQUESTS = 8;

const start = Date.now();
for (let i = 0; i < NUM_REQUESTS; i++) {
  https
    .request("https://www.google.com", (res) => {
      res.on("data", () => {});
      res.on("end", () => {
        console.log(`${i} end: ${Date.now() - start}`);
      });
    })
    .end();
}
```

Discussion about cpu intensive operations vs I/O operations

{{% notice warning %}}
The libuv Event Loop is single threaded. The thread pool is only used for file I/O.
{{% /notice %}}

## Best Practices

dns.lookup() vs dns.resolve\*()
