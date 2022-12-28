---
title: "GitOps"
date: 2022-12-28T10:13:29+02:00
cover: /posts/images/11-gitops-cover.png
tags:
- Cloud Native
- Pattern
- DevOps
---

# Overview

GitOps is Continuous Deployment pattern mostly used for cloud native applications. It uses infrastructure-as-code 
paradigm for deploying applications. The main idea is to have all infrastructure described in a Git (or another 
version control system) repository. When any change in the repository happens the real infrastructure will be
updated automatically. This allows the development team to mange the application deployment easily and 
have versioned history of all deployments.

# Why should I use GitOps?

- Deploy Faster More Often
- Easy and Fast Error Recovery
- Easier Credential Management
- Self-documenting Deployments
- Shared Knowledge in Teams

# Popular implementations and frameworks

## Argo CD

[https://argo-cd.readthedocs.io/en/stable/](https://argo-cd.readthedocs.io/en/stable/)

Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes. Support for multiple config 
management/templating tools. SSO Integration. Rollback/Roll-anywhere to any application configuration committed in 
Git repository

## Flux

[https://fluxcd.io/](https://fluxcd.io/)

Flux is a tool for keeping Kubernetes clusters in sync with sources of configuration (like Git repositories and OCI 
artifacts), and automating updates to configuration when there is new code to deploy. 
Flux is a Cloud Native Computing Foundation (CNCF) project, used in production by various organizations.

## JenkinsX

[JenkinsX](https://jenkins-x.io/)

All In One CI/CD including everything you need to start exploring Kubernetes
Multi-cluster GitOps, Tekton pipelines, Secrets management, Pull Request ChatOps and Preview Environments