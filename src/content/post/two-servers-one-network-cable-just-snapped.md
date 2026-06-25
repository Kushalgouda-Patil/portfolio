---
title: "Two Servers. One Network Cable Just Snapped."
description: "What does your database do next? The CAP Theorem explains the unavoidable tradeoff every distributed system must make."
publishDate: "2026-04-01"
tags: ["distributed-systems", "system-design", "computer-science", "technology"]
---

*The CAP Theorem*

Modern distributed systems consist of multiple nodes that work together as if they are a single system. These nodes have to communicate over the network to coordinate the actions and share data.

These systems are prone to network partitions. Nodes may go down, packets get lost and data centers loose connectivity. CAP theorem helps us to decide what happens next.

---

## CAP Theorem

It applies to distributed data stores and states that the distributed data store can provide at most 2 out of 3 guarantees:

- **Consistency**: Every read should receive the most recent write. All clients should see the same data at same time
- **Availability**: Every request receives a (non-error) response, though it may not contain the most recent data.
- **Partition tolerance**: The system continues operating even when network partitions cause some nodes to be unable to communicate with others.

---

## The Core Tradeoff During Network Failures

The 3rd guarantee – Partition tolerance is generally always ensured, since network failures are inevitable. We are forced to pick between Option 1 & Option 2 i.e Consistency and Availability.

#### Option 1: Stay Consistent (CP)

- Reject the request if the data becomes inconsistent.
- System becomes temporarily unavailable due to inconsistencies

#### Option 2: Stay Available (AP)

- Always respond and return even if the data is stale
- Temporary inconsistency is accepted

---

## CP Databases *(Consistency + Partition Tolerance)*

- **ZooKeeper** – distributed coordination, stops serving on partition
- **HBase** – Hadoop-based, blocks writes over consistency
- **MongoDB** (default) – primary-only writes, refuses stale reads
- **Redis** (cluster mode) – strong consistency within a shard

## AP Databases *(Availability + Partition Tolerance)*

- **Cassandra** – tunable consistency, always responds
- **DynamoDB** – eventual consistency by default, high uptime
- **CouchDB** – multi-master replication, syncs later
- **Riak** – designed for availability-first workloads
- **Couchbase** – favors availability during network splits

---

## References

- https://en.wikipedia.org/wiki/CAP_theorem
- https://discord.com/blog/how-discord-stores-billions-of-messages
- https://www.ibm.com/think/topics/cap-theorem