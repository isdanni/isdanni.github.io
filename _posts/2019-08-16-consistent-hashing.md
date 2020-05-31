---
layout: single
title: "Consistent Hashing: tradeoffs & how-to in Redis"
author: "Danni"
toc: true
toc_sticky: true
tags:
  - Redis
  - Distributed System
---

## What is Hashing?

> **Merriam-Webster**: ***noun***: "chopped meat mixed with potatoes and browned"; ***verb***: “to chop (as meat and potatoes) into small pieces.”

So basically, hashing is the mapping between data object.(general terms) The input and output values do not need to be the same type.

**hash collision**: more than one input being maped to the same hash result. (the infamous [Hash Collision Attack](https://learncryptography.com/hash-functions/hash-collision-attack))

## Simple hash in Redis?

To ensure the high availability and improve readability, we can simply do the [replication](https://www.digitalocean.com/community/tutorials/how-to-configure-redis-replication-on-ubuntu-16-04) in Redis so that it can form the `Master-Master` or `Master-Slave`; Building clusters to split read/write in data operations. Similar to the database, when the data is too large, we create new db/tables. 

![High Availability](/assets/images/post/redis/redis-labs.png)*High Availability in Redis [Source: Redis Labs][i1]*

One example, if the key is the image name and the value is the file path, 
- to search for a certain image on Redis server takes traversal time(for each server)
- If we use the plain hash, `hash(file-name.png) % num(server)`, we can directly go to the one we need but there are certain problems -> when the server name changes, every cache location changes too.

```shell
hash(1.png)%2  -> hash(1.png)%3 = ?
```

## Consistent Hashing

Consistent hashing still uses module method. But instead of moding the server number, it mods the `2^32`, which takes the entire hashing space as a `clock-wise circle`, starting from the `0` node.

- First, hash each server(using IP/server name/...)
- For each file,use sane hash function to hash the key to get the hash value on the circle, walking clock-wise, the first server encoutered should be its designated server.

![Consistent Hashing](/assets/images/post/redis/consistent-hashing.png)*Consistent Hashing in Redis [Source: Redis Labs][i2]*

##### Fault Tolerance
If one node down or more node added, we simply need to update a small portion of file mapping while the majority stay unborthered.

##### Weighted Hosts

This happends when we have more (or less) load to one server as to the rest. Possible reasons are **un-evenly distributed nodes**(/the few nodes).

For this situation, we can adopt **virtual nodes** that still map back to the original node, like "Node A#1", "Node A#2", "Node A#3". IN practice, it's common to set the number of virtual nodes larger than 32 so that the even distribution with few nodes is guaranteed.

### Reference

- [Consistent Hashing: Algorithmic Tradeoffs](https://medium.com/@dgryski/consistent-hashing-algorithmic-tradeoffs-ef6b8e2fcae8)
- [什么是一致性Hash算法？](https://zhuanlan.zhihu.com/p/34985026)