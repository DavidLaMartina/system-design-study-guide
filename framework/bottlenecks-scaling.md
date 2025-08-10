## framework/bottlenecks-scaling.md

```markdown
---
layout: default
title: Bottlenecks & Scaling
parent: Interview Framework
nav_order: 7
---

# Step 7: Bottlenecks & Scaling
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition

Identify where your system will break first as load increases, and how to scale past those limitations. This demonstrates foresight and proactive thinking.

## Why It Matters

Shows you understand that systems evolve under pressure and that senior engineers think ahead to prevent outages rather than just react to them.

## Systematic Bottleneck Analysis

### Follow the Request Path
Trace a typical request through your system and identify capacity limits at each stage:

1. **Load balancer**: Connections per second, bandwidth
2. **Application servers**: CPU, memory, concurrent requests  
3. **Database**: IOPS, connections, query complexity
4. **Network**: Bandwidth between services
5. **External dependencies**: Third-party API rate limits

### Use Your Scale Estimates
Reference the numbers from Step 2 to identify breaking points:

> "At 10k RPS, our PostgreSQL instance will hit its connection limit of 100 concurrent connections. We'll need connection pooling or read replicas before then."

## Common Bottleneck Patterns

### Database Bottlenecks

**Read Bottlenecks:**
- **Problem**: Single database can't handle read volume
- **Solutions**: Read replicas, caching, denormalization
- **Example**: Instagram uses read replicas to handle billions of photo metadata queries

**Write Bottlenecks:**
- **Problem**: Single database can't handle write volume
- **Solutions**: Sharding, write-optimized databases, async processing
- **Example**: Twitter shards tweets by user ID to distribute write load

**Hot Spot Bottlenecks:**
- **Problem**: Uneven data distribution overloads one shard
- **Solutions**: Better shard keys, consistent hashing, hot data replication
- **Example**: Celebrity tweets getting millions of interactions

### Application Server Bottlenecks

**CPU Bottlenecks:**
- **Problem**: Complex business logic or data processing
- **Solutions**: Horizontal scaling, caching, async processing
- **Monitoring**: CPU utilization, request latency

**Memory Bottlenecks:**
- **Problem**: Large datasets, memory leaks, inefficient caching
- **Solutions**: Optimize data structures, streaming processing, garbage collection tuning
- **Monitoring**: Memory usage, garbage collection metrics

### Network Bottlenecks

**Bandwidth Limitations:**
- **Problem**: Large payloads, media content, chatty APIs
- **Solutions**: CDN, compression, GraphQL, data pagination
- **Example**: Netflix uses CDN for video delivery to reduce backbone traffic

**Latency Issues:**
- **Problem**: Geographic distance, network hops, slow queries
- **Solutions**: Edge computing, connection pooling, query optimization
- **Example**: Gaming companies use edge servers for sub-50ms response times

### External Dependency Bottlenecks

**Third-Party APIs:**
- **Problem**: Rate limits, downtime, slow responses
- **Solutions**: Caching, retries with backoff, circuit breakers, alternative providers
- **Example**: Payment processors often have strict rate limits

## Scaling Strategies by Bottleneck

### Horizontal Scaling (Scale Out)
**When to use**: CPU, memory, or request throughput limits  
**How**: Add more servers, load balance requests  
**Trade-offs**: Increased complexity, eventual consistency challenges

**Implementation patterns:**
- Stateless application design
- Database connection pooling
- Session state in external store (Redis)

### Vertical Scaling (Scale Up)  
**When to use**: Simple bottlenecks, single-server applications  
**How**: Bigger CPU, more RAM, faster storage  
**Trade-offs**: Hardware limits, single point of failure, cost

### Functional Decomposition
**When to use**: Monolith performance issues, team scaling  
**How**: Split into microservices by business function  
**Trade-offs**: Network overhead, distributed system complexity

**Example**: Split user service, post service, notification service

### Data Partitioning
**When to use**: Database too large for single server  
**How**: Shard by ID, geography, or feature  
**Trade-offs**: Cross-shard queries, rebalancing complexity

**Partitioning strategies:**
- **Horizontal**: Split rows across servers (sharding)
- **Vertical**: Split columns across servers (normalization)
- **Functional**: Split tables by business domain

## Scaling Timeline Example

> "Here's how I see this system evolving:
> 
> **Month 1-6**: Single region, monolith + CDN + read replicas  
> **Month 6-12**: Add caching layer, split into services  
> **Year 2**: Database sharding, multi-region deployment  
> **Year 3**: Event-driven architecture, advanced caching"

## What to Say

> "Let me walk through where this system will likely break first as we scale, and how we'd address each bottleneck."

## Proactive Monitoring

Set up alerts before hitting limits:
- **Database**: Connection pool utilization > 80%
- **CPU**: Average utilization > 70% sustained  
- **Memory**: Heap usage > 85%
- **Disk**: IOPS > 80% of provisioned capacity

## Real-World Examples

**WhatsApp (Messaging Scale):**
- Bottleneck: Message delivery to offline users
- Solution: Prioritized queues, connection management
- Result: 2 billion users with 50 engineers

**Pinterest (Read-Heavy Social):**
- Bottleneck: Board/pin recommendations taking seconds  
- Solution: Precomputed recommendations in Redis
- Result: Sub-100ms recommendation serving

**Dropbox (File Storage):**
- Bottleneck: Metadata queries for large folders
- Solution: Custom metadata store optimized for small files
- Result: Faster than using S3 for everything

## Common Mistakes

- **Premature optimization**: Solving problems you don't have yet
- **Ignoring monitoring**: Can't optimize what you can't measure  
- **Single solution thinking**: Usually need multiple strategies
- **Forgetting operational cost**: Some solutions require significant ops overhead

---

**Next:** [Trade-offs & Alternatives](tradeoffs-alternatives.html)