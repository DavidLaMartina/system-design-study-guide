---
layout: default
title: Redis
parent: Questions & Answers
nav_order: 3
---

# Redis
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## How do multiple Redis instances work? ##

**Question**: With multiple Redis instances at play in a single system, how do they share context, if at all? For example, if I'm using Redis in a URL shortener, how do I avoid collisions with multiple instances?

**Answer**:

**Multi-instance Redis Configurations**
**Redis Cluster**: The formal clustering solution in which data is *automatically* sharded across multiple nodes. Each node holds a subset of the hash slots (16,384 total) and clients automatically route requests to the correct node.
**Master-Replica Setup**: One primary instance handles writes, with read-only replicas. This provides read scaling and failover capability but doesn't distribute write load.
**Independent Instances**: Complete separate Redis instances serving different purposes or applications, with no coordination between them.
**Sharded Setup**: Application-level partitioning in which your code decides which Redis instance to use based on some key logic.

**Handling Collisions and Consistency**
Redis takes several approaches, depending on the setup:
**In Redis Cluster**, collision prevention works through hash slot assignment. Each key is deterministically mapped to one of 16,384 hash slots, and each slot belongs to exactly one master node. This eliminates the possibility of the same key existing on multiple nodes simultaneously.
**For cross-instance operations**, Redis Cluster uses a two-phase commit-like protocol for multi-key operations that span nodes, although this is ilmited. Most multi-key operations must involve keys on the same node.
**Replication uses eventual consistency**: Replicas asynchronously receive updates from masters, so there is a window during which replicas might be behind.

**ACID Properties in Redis**
Redis has limited ACID guarnatees even in insingle-instance scenarios:
* **Atomicity**: Single operations are atomic, and you can use MULTI/EXEC transactions or Lua scripts for multi-operation atomicity.
* **Consistency**: Redis maintains data structure invariants.
* **Isolation**: Operations don't interfere with each other during execution.
* **Durability**: Configurable through RDB snapshots and AOF logging.
**In multi-instance setups**, full ACID across instances becomes more complex and less guaranteed. Redis Cluster sacrifices some consistency for availability and partition tolerance (following CAP theorem). Cross-node transactions are very limited, and you generally can't have ACID transactions spanning multiple Redis nodes.

**Practical Approaches**:
Most applications handle this by:
**1. Designing for single-node transactions** wherever possible.
**2. Using application-level coordination** for cross-instance operations.
**3. Accepting eventual consistency** for replicated data.
**4. Implementing compensation patterns** for distributed operations that need rollback capabilities.

Overall, Redis prioritizes performance and simplicity over strict ACID guarantees in distributed scenarios. If you need strong consistency across multiple instances, you might need additional coordination mechanisms, or you might consider whether Redis / Redis Cluster is right for your use case.


## How does a Redis distributed lock work? ##

**Question**:
A particular [YouTube overview for designing TicketMaster](https://www.youtube.com/watch?v=fhdPyoO6aXI&t=1927s) advises the use of a Redis distributed lock for handling a hypothetical 10-minute TTL (time-to-live) on pre-confirmation ticket reservations, i.e. the first user to book a ticket has 10 minutes between reservation and payment to confirm the booking. How does this actually work in Redis, i.e. how does Redis handle the automatic deletion of entries based on TTL?

**Answer**:
Redis handles TTL-based automatic deletion through several mechanisms working together:

### Redis TTL Implementation ###
**TTL storage**: When you set a TTL on a key (using ``EXPIRE``, ``SETEX``, ``SET key value EX seconds``, etc.), Redis stores the expiration time as an absolute Unix timestamp in milliseconds. This is kept in a separate hash table that maps keys to their expiration times.
**Multiple expiration strategies**: Redis uses a hybrid approach, rather than relying on a single timer mechanism:

#### Active Expiration ####
Redis actively checks for expired keys in two ways:
**Lazy expiration**: When you access a key, Redis first checks whether it's expired before returning it. If expired, it's immediately deleted and the operation proceeds as if the key never existed.
**Active scanning**: Redis periodically scans the expiration hash table (by default, 10 times per second) and deletes expired keys. This prevents expired keys from accumulating in memory indefinitely, even if they're never accessed.

The active scanning algorithm is designed to be adaptive:
* It randoly samples keys from the expiry table.
* If more than 25% of the sampled keys are expired, it continues scanning.
* If fewer than 25% are expired, it stops until the next cycle.
* This prevents CPU spikes when many keys expire simultaneously.

#### For the Ticketmaster Use Case ####
In the distributed lock scenario for ticket reservations, here's how it would work:
```
# User reserves ticket, set lock with 10-minute TTL
SET ticket:123:lock user:456 EX 600 NX

# Redis automatically handles expiration
# After 600 seconds, the lock is automatically removed
```

***Precision Considerations**: Redis TTL has millisescond precision internally, but the cleanup process isn't guaranteed to happen at the exact expiration time. There could be a delay of up to a few seconds in practice, depending on system load and scanning frequency.
**Memory management**: Expired keys are immediately freed from memory when deleted, making this an efficient way to handle temporary reservations without manual cleanup.

#### Potential Issues with this Approach ####
**Clock drift**: In a dstributed Redis setup, clock synchronization between nodes becomes important for consistent TTL behavior.**
**Persistent considerations**: If using Redis persistence (RDB / AOF), expired keys are handled properly during restoration, but there can be edge cases during failover scenarios.
**Race conditions**: The video's approach would need to handle cases in which the TTL expires *exactly* when a user is trying to complete payment. You'd typically implement a grace period or use more sophisticated locking patterns.
**Alternative patterns**: Some systems prefer explicit cleanup (using background jobs) rather than TTL for business-critical operations such as payments, as it provides more control over the expiration process.

The Redis TTL mechanism is quite robust and well-suited for this use-base, but the application logic around it might need to handle the inherent "eventual" nature of the expiration process, i.e. keys expire quickly but not with microsecond precision.