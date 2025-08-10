---
layout: default
title: Trade-offs & Alternatives
parent: Interview Framework
nav_order: 8
---

# Step 8: Trade-offs & Alternatives
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition

Discuss other viable designs and why you'd choose one approach over another. This demonstrates flexibility, business awareness, and senior-level judgment.

## Why It Matters

Mid-level engineers might just choose one technology; seniors compare options, justify decisions, and tie choices to business requirements. This is often what separates good from great candidates.

## How to Structure Trade-off Discussions

### 1. Present the Decision Point
> "For the database layer, we have a few viable options..."

### 2. List 2-3 Realistic Alternatives
Don't overwhelm with every possible option—focus on the most relevant choices for your use case.

### 3. Compare Along Key Dimensions
- **Performance**: Latency, throughput, scalability
- **Consistency**: Strong vs eventual consistency guarantees  
- **Availability**: Uptime, fault tolerance, geographic distribution
- **Cost**: Infrastructure, operational overhead, development time
- **Complexity**: Learning curve, operational burden, debugging difficulty

### 4. Tie to Business Requirements
> "Given our SLA requirement of 99.99% availability and global user base, I'd choose..."

### 5. Acknowledge Context Dependency
> "If our requirements change to X, then Y would become the better choice."

## Common Trade-off Categories

### Database Technology Choices

**SQL vs NoSQL**

**SQL (PostgreSQL, MySQL):**
- ✅ Strong consistency, ACID transactions
- ✅ Rich querying with JOIN operations  
- ✅ Mature tooling and expertise
- ❌ Vertical scaling limitations
- ❌ Rigid schema requirements

**NoSQL (Cassandra, DynamoDB):**
- ✅ Horizontal scaling, partition tolerance
- ✅ Flexible schema, high write throughput
- ✅ Built for distributed environments
- ❌ Eventual consistency challenges
- ❌ Limited query capabilities, no JOINs

**Decision framework:**
- Choose SQL for: Complex relationships, transactions, strong consistency needs
- Choose NoSQL for: Massive scale, simple queries, high availability requirements

**Real-world example:**
Netflix uses both—Cassandra for viewing history (scale, availability) and MySQL for billing (consistency, transactions).

### Consistency vs Availability

**Strong Consistency:**
- ✅ Read-your-own-writes guarantees
- ✅ Simpler application logic
- ❌ Higher latency, especially globally
- ❌ Reduced availability during partitions

**Eventual Consistency:**
- ✅ Lower latency, better availability
- ✅ Better partition tolerance
- ❌ Complex conflict resolution
- ❌ Potential for stale reads

**When to choose what:**
- Strong consistency: Financial transactions, inventory systems
- Eventual consistency: Social media feeds, recommendation systems

### Architectural Patterns

**Monolith vs Microservices**

**Monolith:**
- ✅ Simple deployment, debugging, transactions
- ✅ Lower operational overhead initially
- ✅ Easier to change across service boundaries
- ❌ Single point of failure
- ❌ Technology lock-in, scaling limitations

**Microservices:**
- ✅ Independent scaling, technology choices
- ✅ Team autonomy, fault isolation
- ✅ Better for large organizations
- ❌ Network overhead, distributed system complexity
- ❌ Higher operational burden

**Decision factors:**
- Team size (Conway's Law)
- Performance requirements
- Organizational maturity

### Synchronous vs Asynchronous Processing

**Synchronous (REST APIs):**
- ✅ Simple request-response model
- ✅ Immediate error handling
- ✅ Easier to reason about flow
- ❌ Tight coupling between services
- ❌ Cascade failures, higher latency

**Asynchronous (Message Queues):**
- ✅ Loose coupling, better fault tolerance
- ✅ Natural load leveling, scalability
- ✅ Background processing capabilities
- ❌ Complex error handling and retry logic
- ❌ Eventual consistency challenges

### Caching Strategies

**Write-Through vs Write-Behind**

**Write-Through:**
- ✅ Cache always consistent with database
- ✅ Simpler to implement and reason about
- ❌ Higher write latency
- ❌ Cache becomes a bottleneck for writes

**Write-Behind (Write-Back):**
- ✅ Lower write latency, better write performance
- ✅ Batching opportunities for efficiency
- ❌ Risk of data loss if cache fails
- ❌ Complex consistency guarantees

### Build vs Buy Decisions

**Build Custom Solution:**
- ✅ Perfect fit for requirements
- ✅ Full control over roadmap
- ✅ Competitive differentiation
- ❌ Development time and cost
- ❌ Ongoing maintenance burden

**Buy/Use Existing Solution:**
- ✅ Faster time to market
- ✅ Proven reliability and features
- ✅ Vendor support and maintenance
- ❌ Vendor lock-in
- ❌ May not fit perfectly

**Decision framework:**
- Build for: Core differentiators, unique requirements
- Buy for: Commodity functions (auth, payments, monitoring)

## How to Present Alternatives

### Use Specific Examples
Instead of: "We could use NoSQL"  
Say: "We could use Cassandra for its write scalability, but we'd lose transaction guarantees"

### Quantify When Possible
> "PostgreSQL can handle our current 5k QPS, but at 50k QPS we'd need to consider sharding or switching to a distributed database"

### Show Evolution Paths
> "We could start with PostgreSQL for faster development, then migrate to Cassandra when we hit scale limits in 6-12 months"

### Acknowledge Unknown Unknowns
> "This design assumes our read:write ratio stays at 10:1. If that changes significantly, we'd need to revisit the caching strategy"

## Real-World Trade-off Examples

**Instagram (Early Architecture):**
- **Trade-off**: Django monolith vs microservices
- **Choice**: Stayed monolithic longer than expected
- **Reasoning**: Team velocity, simpler operations, fewer people
- **Evolution**: Eventually split by vertical (photos, users, etc.)

**Uber (Storage Evolution):**
- **Trade-off**: PostgreSQL vs Cassandra for geospatial data
- **Choice**: Initially PostgreSQL, migrated to custom solution
- **Reasoning**: PostgreSQL couldn't handle write volume in all cities
- **Learning**: Sometimes you outgrow even your migration target

**Discord (Database Migration):**
- **Trade-off**: MongoDB vs Cassandra for message storage  
- **Choice**: Migrated from MongoDB to Cassandra
- **Reasoning**: Better performance under read-heavy loads
- **Key factor**: Millions of concurrent users reading message history

## What to Say

> "Let me discuss a few alternative approaches and why I'd choose this one for our specific requirements."

> "If the business requirements change to prioritize X over Y, then approach Z becomes more attractive."

> "This design makes these assumptions. If they prove wrong, here's how we'd adapt..."

## Advanced Trade-off Discussions

### Regional vs Global Deployment
- Latency vs complexity
- Data sovereignty requirements
- Cost implications

### Event-Driven vs Request-Response
- Loose coupling vs immediate consistency
- Scalability vs debuggability

### Edge Computing vs Centralized
- Lower latency vs operational complexity
- Data freshness vs availability

---

**Congratulations!** You've completed the full interview framework. Practice applying these 8 steps to different system design problems until the flow becomes natural.

**Next Section:** [Scaling Patterns](../scaling-patterns/)