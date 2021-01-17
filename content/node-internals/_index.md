+++
title= "Node Internals"
chapter = true
date= 2021-01-16T15:03:41+01:00
draft= false
pre = "<b>1. </b>"
weight = 1 
+++

## How does Node.js work under the hood? 

Node.js relies on a collection of libraries and tools which eventually are aggregated into the high performant runtime. 

### Libraries

1. v8 — high-performance JavaScript compilation and execution
1. Libuv — event-driven asynchronous I/O
1. c-ares — DNS operations
1. llhttp - parser for HTTP messages
1. OpenSSL - implementation of cryptographic functions
1. zlib - for fast compression and decompression

### Tools

1. npm - share and manage node packages
1. gyp - meta-build system: a build system that generates other build systems
1. gtest - testing and mocking framework


