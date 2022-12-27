---
title: "Cloud Native Patterns - RPC"
date: 2022-11-30
cover: /posts/images/cn-pattern-rpc.png
tags:
- Cloud Native
- Pattern
---

# Description

Remote Procedure Call (RPC) is a pattern that allows distributed applications to execute a code (method or function)
in a remote application the same way they make local calls.

RPC is not new. Developers have been using it for a log time, but the old systems and standards are heavy and limited.
The recent rise in popularity of distributed software lead to rediscovery of the RPC pattern and development of new 
frameworks.

# RPC vs REST
REST is similar to RPC in the general request-response structure of the communication. But there are some key
advantages of RPC:
 - bigger payloads - you can use binary instead of text based format allowing better compression and efficiency
 - contract first approach with centralized schema definition allows better type safety
 - native support for streaming
 - includes more features like encryption, authentication and service discovery (depends on implementation)

In many cases RPC is used to take advantage of those features only where needed and the rest of the inter-services
communication falls back to REST to keep the complexity low.

# When to use
 - High performance communication is critical
 - Need to enforce strict contract-first approach during development
 - Business logic needs to be independent of the underlying network protocol

# When not to use
 - The service needs to connect to multiple application types such as mobile and web
 - You need loose contracts to enable flexibility of consumers

# Popular implementations and frameworks

## gRPC
[https://grpc.io/](https://grpc.io/)

The most popular one and de facto industry standard. Developed by Goodle and uses ProtoBuff by default as message 
payload standard. Native support for health checks, load balancing and authentication.

## Apache Thrift
[https://thrift.apache.org/](https://thrift.apache.org/)

Open source and developed initially by Facebook. Similar to gRPC but uses .thrift files for message payload. Supports
a lot of programming languages.

## Apache Dubbo
[https://dubbo.apache.org/en/](https://dubbo.apache.org/en/)

Java based RPC framework developed by Alibaba. Focused on high performance and throughput. Integrates very well
with Spring framework. Build-in load balancing, service registration and discovery. Supports plugins for extensibility.

## Ice
[https://zeroc.com/products/ice](https://zeroc.com/products/ice)

Open source framework developed by ZeroC. Has proprietary communication protocol which is optimized for communication
over internet. Also uses its own definition language called Slice.

