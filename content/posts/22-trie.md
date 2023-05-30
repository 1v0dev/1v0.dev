---
title: "Trie (prefix tree)"
date: 2023-05-30
mermaid: true
tags:
    - Data Structure
keywords:
  - Data Structure
  - Trie
  - Prefix Tree
---

Recently I had to do a design for a system that had a requirement to search strings in a big unstructured data source. While doing research, I learned about a neat data structure, that is perfect for the use case. Enter Trie!

<!--more-->

# Definition
A trie (comes from _retrieval_) is a tree data structure where each edge represents a letter from the alphabet and each vertex contains a concatenation of all edges in the path to the vertex.

Here is a brief sample:
{{< mermaid >}}
graph TB
A(( )) --> B((a))
B((a)) -- b --> C((ab))
C((ab)) -- c --> D((abc))
C((ab)) -- d --> E((abd))
A(( )) --> B1((b))
B1((b)) -- b --> C1((bb))
B1((b)) -- c --> C2((bc))
{{< /mermaid >}}

# Usages

The trie is perfect for storing search index because the time it takes to traverse the tree depends on the length of the search query and not on how big the dataset it. Generally, you would index the data and then store the tree in memory in order to minimise the search latency. 

It is also useful to store IP addresses for prefix matching or in general any information which you need to search symbol by symbol

# Cons

The main negative about the Trie is that it takes long time to construct because each string (or data) must be inserted one symbol at a time by traversing the tree. 

# Storage

Tree structures are usually implemented with in-memory databases and key-value stores like Redis, MongoDB and Apache Cassandra.