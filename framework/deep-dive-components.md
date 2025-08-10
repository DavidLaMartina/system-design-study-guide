---
layout: default
title: Deep Dive Components
parent: Interview Framework
nav_order: 6
---

# Step 6: Deep Dive Components
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition

Zoom in on the most interesting, complex, or risky parts of your architecture. This is where you demonstrate deep technical knowledge and design thinking.

## Why It Matters

Shows you can reason deeply about system internals, not just draw boxes. Interviewers often judge senior candidates more on depth than breadth.

## How to Choose What to Deep Dive

### Follow the Interviewer's Interest
- They might explicitly ask: "Tell me more about the database design"
- Watch for engagement cues when you mention certain components

### Focus on High-Risk Areas
- Single points of failure
- Performance bottlenecks  
- Data consistency challenges
- Complex business logic

### Choose Your Strength Areas
- Pick components where you have real experience
- Areas where you can discuss trade-offs confidently

## Common Deep Dive Topics

### Database Design

**Schema Design:**
- Table structure and relationships
- Indexing strategy
- Normalization vs denormalization

**Scaling Approaches:**
- Read replicas for read-heavy workloads
- Sharding strategies and partition keys
- Horizontal vs vertical partitioning

**Example - Social Media Posts:**
```sql
-- Posts table sharded by user_id
CREATE TABLE posts_shard_1 (
  id BIGINT PRIMARY KEY,
  user_id BIGINT,  -- shard key
  content TEXT,
  created_at TIMESTAMP,
  INDEX idx_user_created (user_id, created_at)
);
```

**Real-world example:**
Facebook's TAO caching system ensures high cache hit rates while keeping graph data consistent with MySQL.

### Caching Strategy ###
**Cache Levels**
* Browser cache (static assets)
* CDN (global content)
* Application cache (API responses)
* Database cache (query results)

**Cache Patterns**
* Cache-aside: App manages cache explicitly
* Write-through: Write to cache and DB simultaneously
* Write-behind: Write to cache, DB updated asynchronously

**Cache Considerations**
* TTL tuning: Balance freshness vs. hit rate
* Eviction policies: LRU, FLU, or custom logic
* Hot key handling: Shard or replicate popular keys
* Cache warning: Preload expected data

**Real-world example**: Twitter caches timelines in Redis. When a user tweets, their followers' cached timelines are invalidated using a fan-out pattern.

### Message Queue Design ###

**Delivery Guarantees**
* **At-most-once**: Fast, but messages can be lost
* **At-least once**: Relable, but duplicates possible
* **Excatly-once**: Complex to implement correctly

**Orering guarantees**
* **Global ordering**: Single partition, limited throughput
* **Partition ordering**: Scales better, order within partition
* **No ordering**: highest througput

**Failure Handling**
* **Retry polcies**: Expontential backoff, max attempts
* **Dead letter queues**: Capture failed messages
* **Circuit breakers**: Stop sending to failing consumers

**Real-world examples**
Uber's Kafka setup uses partition keys based on trip ID to ensure all events for a trip are processed in order.

### Load Balancer Configuration ###
**Algorithms**
* **Round-robin**: simple, works for homogenous servers
* **Least connections**: Better for long-lived connections
* **Weighted**: Route more traffic to powerful servers
* **Consistent hashing**: Minimize redistribution on changes

**Health checks**:
* **Active**: periodically ping servers
* **Passive**: Mark unhealthy based on failed requests
* **Circuit breaker**: Temporarily remove failing servers

**Session Handling**:
* **Sticky sessions**: Route user to same server
* **Session store**: External storage (Redis, DB)
* **Stateless design**: No server affinity needed

### API Rate Limiting ###
**Algorithms**:
* **Token bucket**: Burst allowance with sustained rate
* **Leaky bucket**: Smooth out traffic spikes
* **Fixed window**: Simple but can allow bursts
* **Sliding window**: More accurate rate limiting

**Distributed Rate Limiting**:
* **Centralized**: Single rate limit store (Redis)
* **Distributed**: Each server tracks portion of limit
* **Approximate**: Trade accuracy for performance

## What to Say ##
"Let me dive deeper into [component] since it's critical for [reason]. There are a few key design decision we need to make here..."

### Deep Dive Struture ###
1. State the challenge
2. Present options
3. Compare trade-offs
4. Make recommendations
5. Implementation details: schema, config, monitoring, etc.

### Common Mistakes ###
* Too shallow, e.g. just listing technologies without explaining the reasons for their use
* Overcomplicating: Don't add complexity that doesn't serve requirements
* Ignoring operations: How do you deploy, monitor, debug this?
* No trade-offs: Always explain what you're optimizing for.

**Next:** [Bottlenecks & Scaing](bottlenecks-scaling.html)