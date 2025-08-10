---
layout: default
title: High-Level Architecture
parent: Interview Framework
nav_order: 5
---

# Step 5: High-Level Architecture
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition

Identify major components and how they interact, before diving into details. This creates the foundation for deeper discussions about specific parts of your system.

## Why It Matters

A clear architecture diagram sets the foundation for deep dives and trade-off discussions. It shows you can think in terms of systems, not just individual services.

## Generic System Flow

Most systems follow this pattern (role-based, not product-specific):

Client → Entry Layer → Application Layer → Data Layer → Observability

## Core Layers Explained

### Client Layer
**Who/what consumes the system**: web browsers, mobile apps, IoT devices, external APIs

**Considerations:**
- Versioning & backward compatibility
- Offline mode & sync strategies  
- Security (TLS, certificate pinning)
- Platform-specific optimizations

**Real-world example:**
Slack's API maintains backward compatibility for years, letting enterprise customers avoid forced upgrades.

### Entry Layer (API Gateway / Reverse Proxy)
**Handles:**
- Authentication & authorization (JWT, OAuth2, mTLS)
- Rate limiting & quotas
- Routing requests to the right service
- Canary and blue/green deployments
- API versioning
- SSL termination

**Real-world example:**
Netflix's Zuul handles traffic shaping, routing, and dynamic filtering for hundreds of microservices.

### Application Layer
**Stateless services implementing business logic**

**Key principles:**
- Enables horizontal scaling (any instance can handle any request)
- State stored in databases or caches, not in-memory
- Service-oriented or microservices architecture
- Clear separation of concerns

**Real-world example:**
Netflix uses EVCache (Memcached) to store user session data, enabling any service instance to serve any user.

### Data Layer
**Storage and retrieval systems**

**Components:**
- **SQL DBs** for relational data (PostgreSQL, MySQL)
- **NoSQL DBs** for scale/flexible schema (Cassandra, DynamoDB)
- **Object stores** for large files (S3, GCS)
- **Caches** for fast access (Redis, Memcached)
- **Search engines** for full-text search (Elasticsearch)

**Scaling approaches:**
- Replication, sharding, partitioning
- Read replicas for read-heavy workloads
- CQRS (Command Query Responsibility Segregation)

**Real-world example:**
Instagram shards MySQL by user ID to spread writes evenly and avoid hot spots.

### Async / Streaming Layer
**Decouples producers from consumers**

**Benefits:**
- Absorbs spikes, smooths load
- Supports background processing
- Enables event-driven architecture
- Improves fault tolerance

**Technologies:**
- Message queues (RabbitMQ, SQS)
- Event streaming (Kafka, Kinesis)
- Pub/sub systems (Google Pub/Sub)

**Real-world example:**
Uber uses Kafka for trip events, allowing billing, ETA updates, and analytics to process asynchronously.

### Observability Layer
**Monitor, debug, and understand system behavior**

**Components:**
- **Metrics** (latency, throughput, error rates)
- **Logging** (structured, centralized)
- **Tracing** (distributed tracing across services)
- **Alerting** (on-call rotation, escalation)

**Real-world example:**
Google SREs use "Golden Signals" — latency, traffic, errors, saturation — as standard monitoring dimensions.

## What to Draw

1. **Start simple**: Client → API → DB
2. **Add layers gradually**: Load balancer, cache, queue
3. **Show data flow**: Arrows indicating request/response paths
4. **Label key technologies**: "Redis cache", "PostgreSQL", "Kafka"

## What to Say

> "Let me sketch out the major components and how they interact. I'll start with the core flow and then add layers for scale and reliability."

## Common Patterns by Use Case

### Read-Heavy Systems (News Feed, Content)

Client → CDN → Load Balancer → App Servers → Cache → Read Replicas

### Write-Heavy Systems (Analytics, Logging)

Client → Load Balancer → App Servers → Message Queue → Workers → Database

### Real-Time Systems (Chat, Gaming)

Client ↔ WebSocket Gateway → Message Broker → App Services → Database

## Architecture Evolution

**Phase 1**: Monolith + CDN + Database  
**Phase 2**: Service-oriented, add caching  
**Phase 3**: Microservices, event-driven  
**Phase 4**: Multi-region, global scale

## Common Mistakes to Avoid

- **Over-architecting early**: Don't start with microservices
- **Missing observability**: Always include monitoring/logging
- **Ignoring data flow**: Show how data moves through the system
- **No scaling hooks**: Don't paint yourself into a corner

---

**Next:** [Deep Dive Components](deep-dive-components.html)