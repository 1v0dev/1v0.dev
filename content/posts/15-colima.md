---
title: "Great Software: Colima"
date: 2023-01-19
mermaid: false
tags:
- GreatSoftware
- MacOS
- Colima
- Docker
keywords:
- macos
- colima
- docker
---
[Colima](https://github.com/abiosoft/colima) is a simple way to run Docker or Kubernetes locally on your Mac. It is very light and takes little resources on my M1 machine.

<!--more-->

You can install it with Homebrew `brew install colima`. Then you just need to start it with `colima start` and you have a running docker environment to deploy and test containers in.

Starting kubernetes is very simple too, just use `colima start --kubernetes`

