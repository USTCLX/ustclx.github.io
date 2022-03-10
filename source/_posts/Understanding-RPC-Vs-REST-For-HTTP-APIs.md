---
title: Understanding RPC Vs REST For HTTP APIs
date: 2022-02-08 14:26:30
tags:
  - API
  - RESTful
  - RPC
---

最近在用 nestjs 写一个后端应用程序，在设计 API 时选择了在 web app 中常被提起的 RESTful API 形式，但在过程中遇到了一些问题。

- 虽然 RESTful 形式的 API 在很多的教程中被推荐，但是实际的工作中，却也有很多项目的 API 没有遵从 REST 的形式。
- 实际使用 REST 来设计 API 的时候发现了一些难受的地方，比如如何表达批量操作，如何表达文件处理这个请求。

于是咨询了一些同事，查阅了一些资料，原来工作中的一些 API，使用的是 RPC 的形式。在我以前的印象中 RPC 就是远程服务调用，用后端程序和后端程序之间通信的一种方式。
没想到，RPC 也可以是前后端 API 设计的一种形式。在查阅的资料中，这篇[Understanding RPC Vs REST For HTTP APIs](https://www.smashingmagazine.com/2016/09/understanding-rest-and-rpc-for-http-apis/)对我的启发很大。
没有必要拘泥于固定的形式，可以 REST 和 RPC 混合使用。
