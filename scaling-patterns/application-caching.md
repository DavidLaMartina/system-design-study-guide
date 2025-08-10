---
layout: default
title: Application-level Caching
parent: Scaling Patterns
nav_order: 3
---

## How Redis Works ##

### Architecture ###
**Redis is a separate server process** and thus doesn't use the same RAM as your application. It typically runs on an entirely separate server from your server instances, so it isn't coupled with any specific instance. Its state might be considered "global" relative to your entire system.

```
Application Server 1  ──┐
Application Server 2  ──┼──→ Redis Server (dedicated instance)
Application Server 3  ──┘     ├── RAM: 16GB+ for data storage
                               ├── CPU: Handles requests
                               └── Disk: Persistence (optional)
```

### How Redis Uses Memory ###
**Primary Storage: RAM**
* All data stored in Redis server's RAM for fast access (ms response times)
* RAM is the "working set" - what you're actively reading / writing
* In contrast, RAM is simply used for caching and buffering purposes in a traditional DB.
**Persistence Options**
* RDB: Periodic snapshots saved to disk
* AOF: Append-only file logs every write operation
* Both: hybrid approach for max durability
* None: Pure in-memory (data lost on restart)

### Common Redis Deployment Patterns ###
**1. Cache Layer**
```
App → Redis (check cache) → PostgreSQL (if cache miss)
```
* Redis holds frequently accessed data
* 8-64 GB RAM typical
* Data can abe regenerated from primary DB

**2. Session Store**
```
Load Balancer → App Server 1 ──┐
                App Server 2 ──┼──→ Redis (session data)
                App Server 3 ──┘
```
* Enables stateless application servers
* Session data persists across app server failures

**3. Primary Database**
```
App → Redis (only storage)
```

### Memory Management ###
**What happens when Redis runs out of RAM?**
* Eviction policies: LRU, LFU, random, or refuse new writes
* Max memory setting: configure mem limit
* Monitoring: Watch memory usage to avoid OOM kills
**Typical sizings**
* small cache: 2-8 GB
* medium cache: 16-64 GB
* large cache: 100+ GB (Redis cluster)

### Redis Cluster for Scale ###
When data doesn't fit one server:
```
App ──┬──→ Redis Node 1 (keys: A-F)
      ├──→ Redis Node 2 (keys: G-M)
      └──→ Redis Node 3 (keys: N-Z)
```
* Automatic sharding across nodes
* Each node has its own RAM
* Client library handles routing

### Real-World Example: Instagram ###
Instagram uses Redis for:
* User sessions: 8GB Redis cluster
* Feed caching: 100GB+ distributed across multiple Redis instances
* Counter storage: like counts, follower counts
* Deployment: Separate Redis servers from application servers

### Cost Implications ###
Why not put everything in Redis?
* RAM is expensive: ~10 cost of SSD storage
* Limited capacity: Largest single Redis instance ~500GB - 1TB
* Persistence overhead: snapshots can impact performance
When to use Redis
* Data you access frequently (hot data)
* Sub-millisecond response requirements
* Simple data structures (strings, lists, sets, hashes)
This is why most systems use Redis as a **cache layer or specialized database** rather than replacing their primary database entirely. It's about using the right tool for each access pattern.