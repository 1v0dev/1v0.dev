---
title: "Apache Kafka Basics"
date: 2023-01-17
cover: /posts/images/13-kafka-cover.jpeg
mermaid: false
tags:
- Kafka
- publish/subscribe
---

This post contains a brief overview of the terms and mechanisms of Kafka. Reading this won't make you a Kafka expert, but it will allow you to grasp the basic concepts.

# Overview

[Apache Kafka](https://kafka.apache.org/) is publish/subscribe messaging software designed to handle communication between different systems. It is often described as distributed event streaming platform, and it is used to implement event-driven architectures.

Its benefits are very high performance, reliability and message retention.

# Basic terms

**Message** - basic piece of data transferred and stored by Kafka. A message is simply a binary array and its contents does not have a specific meaning to Kafka.

**Message key** - additional piece of metadata attached to a message. It is a binary array too and its hash is used by Kafka to decide where to process and store the message.

**Message header** - a way to add more metadata to a message in a form of key-value pairs. It's often used to indicate the source of the data and for routing or tracing the message without having to parse it.

**Schema** - while Kafka is not concerned by the message contents, its recommended that they have a structure, called a schema to the content can be easily generated or parsed by the client systems

**Topic** - in Kafka messages are categorised into topics.

**Partitions** - Each topic in Kafka is split into multiple partitions. Each partition can be hosted on different server, which in turn provide redundancy and allow each topic to be horizontally scaled.

# Clients

The clients are the users of the Kafka system. They are two types: **producers** and **consumers**

## Producers

Producers generate messages and push them into Kafka topic.

## Consumers

Consumers subscribe to Kafka topics and receive messages from them. The rule is to have each consumer into a separate thread if they are part of the same application. 

Kafka consumers are usually part of a **consumer group**. When a group is subscribed to a topic, each consumer in it will receive messages from a different subset of the topic's partitions. If there are more consumers in the group than partitions in the topic, some of the consumers will remain idle.

By default, the membership of a consumer in a group is transient. If a consumer leaves the group, its partitions are reassigned. When it joins the group again, a different set of partitions are assigned to it. But if you configure a consumer with unique group instance ID, it becomes a **static member** and its partition assignment is preserved. 

## Rebalance

Moving ownership of a partition from one consumer to another is called **rebalance**. It's important because this mechanism provides Kafka with high availability and scalability but in normal operation a rebalance is undesirable because it disrupts the work.
There are two types of rebalance:
- **Eager** rebalance stops all consumers is a group, remove their partition ownership and assign new partitions.
- **Cooperative** rebalance only involves a small subset of the partitions allowing the consumers to continue processing records for the rest of the partitions. 

# Brokers

A single server in Kafka is called a **broker**. They are designed to operate as a part of a **cluster**. Whiting a cluster, one of the brokers also functions as a **cluster controller**. The brokers also have the ability to limit the rate of messages by using Kafka's **quota mechanism**

# Replicas

Kafka keeps replicas of all its partitions by default in order to guarantee consistency and durability. The number of replicas and the replication mechanism is configurable.

There are two types of partition replicas in Kafka:
- **Leader** replica receives all messages to ensure consistency. Consumers can read either from replica or a follower depending on the configuration
- **Follower** replicas main job is to replicate messages from the leader and replace the reader in case it becomes unavailable. Unless configured otherwise, they don't serve messages to consumers.

# Commits and Offsets

When a consumer asks for messages, Kafka returns only the ones the consumer has not received yet. This means there is a way to track which messages are already received. Instead of Kafka tracking them itself, it has a mechanism which allows the consumers to track what they have received. Kafka allows the consumer to specify an **offset** from the beginning of the message queue, and then recipe only new messages after the offset. The offset can be stored in Kafka itself and there are various mechanisms to **commit** it:

- **Automatic commit** - by default the consumer (or the consumer library) will commit the offset every 5 seconds
- **Manual commit** - a consumer can manually commit the offset at his discretion by using the Kafka Consumer API
- **Asynchronous commit** - the drawback of the manual commit is that the consumer will be blocked until the commit request returns. You can circumvent that blocking by using asynchronous request in the expense of lesser durability.







