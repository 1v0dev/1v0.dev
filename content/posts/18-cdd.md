---
title: "What is Contract-Driven Development"
date: 2023-02-28T09:42:17+02:00
cover: /posts/images/18-cdd-cover.jpeg
tags:
- Contract-Driven
- Methodology
keywords:
- Contract-Driven
- Development
- OpenAPI
---

# Overview

With the prevalence of microservices today, it is important to define how the interaction between the different components of an application happen. When you have a monolith application that is easy - the method signatures and interfaces define a rigid structure and any errors due to changes are caught immediately at the build/compile step. For microservices, we don't have such an enforcement of correct communication - any error surface during integration testing which is often too late and slows down development. To fix that we use a methodology called Contract-Driven Development. It emphasizes the use of **contracts** to enforce the behavior and interaction between the different microservices.

# Contracts

The contracts used in Contract-Driven Development are usually text documents written in YAML or JSON that follow some existing specification like [Open API](https://spec.openapis.org/oas/latest.html)

Here is a small example of Open API specification for a hello world service:
```yaml
openapi: 3.0.0
info:
  title: Hello World API
  version: 1.0.0
paths:
  /hello:
    get:
      summary: Returns a simple greeting message
      responses:
        '200':
          description: OK
          content:
            text/plain:
              schema:
                type: string
      operationId: helloWorld
```

Once you have the contract defined the other developers/teams can use this to verify their communication with the service and run automated test during development catching any errors far sooner than usual.

# Tools

[Specmatic](https://specmatic.in/) is a great tool to compliment the Contract-Driven Development approach. It lets you treat the contracts like executable tests.You can run them by using their command line tool to test if all communication between services will be correct once deployed to the real evironment