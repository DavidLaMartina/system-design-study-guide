---
layout: default
title: SQL vs. NoSQL
parent: Questions & Answers
nav_order: 2
---

# SQL vs. NoSQL
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## SQL vs. NoSQL - Does it Really Matter ##
**Question**: Astute interviewers and interviewees often say that, given today's NoSQL systems available, the distinction between SQL vs. NoSQL isn't all that important - **at least not when it comes to creating the basic design of a system**. How true is that?

**Answer**:
### Where the distinction matters less ###
**At the service boundary level**, this is correct. Whether your user service uses PostgresSQL or DynamoDB, the rest of your system primarily cares about the API contract, i.e. "Give me user data for ID X." The internal storage mechanism becomes an implementation detail.

**For basic CRUD operations**, many modern NoSQL systems have converged toward SQL-like interfaces. DynamoDB has PartiQL, MongoDB has aggregation pipelines that feel SQL-ish, and Cassandra has CQL. The query patterns aren't all that different from simple SQL operations.

**Horizontal Scaling** is now available in both camps. You can partition PostgresSQL, and many NoSQL systems handle partitioning automatically. The scaling story isn't as black-and-white as it used to be.

### Where the Distinction Matters Significantly ###
**Data modeling fundamentally differs.** In SQL systems, you normalize data and rely on joins. In NoSQL, you often denormalize and duplicate data. This affects:
* How you structure your domain objects
* Update patterns (updating in one place vs. many)
* Query performance characteristics
* Storage efficiency

**Transaction capabilities vary drastically**. If your system needs multi-record ACID transactions across different entities, this heavily influences your architecture. You might need to:
* Design around single-document or row updates
* Implement saga patterns for distributed transactions
* Accept eventual consistency
* Use event sourcing approaches

**Query flexibility impacts service design**. SQL systems excel at ad-hoc queries and complex analytics, while many NoSQL systems require you to know your access patterns upfront. This affects:
* Whether you need separate OLAP systems
* How you handle reporting requirements
* Service API design (exposing more specific endpoints vs. flexible querying)

**Consistency models shape your architecture**. Strong consistency vs. eventual consistency isn't just a database choice; it ripples through your entire system design, affecting caching strategies, API semantics, and user experience.

### The Reality Check ###
In practice, the choice often **does** influence overall system design because:
1. **Access patterns drive architecture** - NoSQL systems that require knowing queries upfront lead to different service decompositions.
2. **Consistency requirements** affect everything from caching to user workflows.
3. **Operational characteristics** (backup, monitoring, debugging) influence your DevOps and observability architecture.

The astute observation is probably this: both SQL and NoSQL systems can solve most problems, but the **path** to the solution and the trade-offs you make along the way are quite different. A well-designed system abstracts these differences where possible, but the underlying choice still influences many architectural decisions.
The distinction matters less for what you can build, but it matters significantly for how you build it.

## Follow-up: Can NoSQL systems provide any degree of ACID compliance? ##
**ACID refresher**
* **Consistency**: The database moves from one valid state to another valid state. All integrity constraints and business rules are maintained.
* **Isolation**: Concurrent transactions don't intefere with each other. Different isolation levels (read uncommitted, read committed, repeatable read, serializable) provide different guarantees about what one transaction can see from others.
* **Durability**: Once a transaction commits, the changes persist even if the system crashes immediately afterward.

**Question**:
To what degree can NoSQL systems provide ACID compliance?

**Answer**:
### NoSQL ACID Capabilities by Category ###

**Document databases**:
**MongoDB** has made significant strides here:
* **Single-document operations**: Fully ACID compliant
* **Multi-document transactions**: Full ACID support within a replica set (since 4.0) and across shards (since 4.2)
* **Limitations**: Cross-shard transactions have performance overhead and some operational complexity.

**CouchDB / Couchbase**:
* Single-document ACID compliance
* Limited multi-document transaction support

**Column-Family / Wide-Column**
**Cassandra**:
* **Atomicity**: Only at the row level (single partition key)
* **Consistency**: Tunable consistency levels, but not traditional ACID consistency
* **Isolation**: Limited - no multi-row transactions
* **Durability**: Yes, with configurable replication
**HBase**:
* Single-row ACID compliance
* No multi-row transactions

**Key-value Stores**
**DynamoDB**:
* **Single-item operations**: Fully ACID
* **Transactions**: Supports ACID transactions across up to 100 items
* **Conditional operations**: Provides consistency guarantees
**Redis**:
* Single-operation atomicity
* MULTI/EXEC provides atomicity for command sequence
* Limited durability (depends on persistence configuration)

**Graph Databases**
**Neo4j**:
* Full ACID compliance, including complex multi-node transactions
* One of the strongest ACID stories in NoSQL

**Multi-Model/NewSQL**
* **Amazon Aurora**: SQL interface with NoSQL-like scaling, full ACID
* **Google Spanner**: Globally distributed with full ACID compliance
* **FaunaDB**: Designed specifically for ACID compliance across distributed systems

### The Nuanced Reality ###
**Single-entity operations**: Most modern NoSQL systems provide ACID guarnatees here. This covers a surprising number of use cases.
**Multi-entity transactions**: This is where things get complex. The CAP theorem creates fundamental trade-offs:
* Systems prioritizing availability / partition tolerance (e.g. Cassandra) sacrifice consistency.
* Systems prioritizing consistency (such as some MongoDB configurations) may sacrifice availability.
**Distributed ACID is expensive** Even systems that support multi-document / multi-partition transactions often have significant performance penalties compared to single-entity operations.

### Practical Implications ###
Many successful NoSQL deployments work around ACID limitations through:
**Design patterns**
* Embedding related data in single documents or rows
* Event sourcing and CQRS
* Saga patterns for long-running business processes
* Idempotent operations
**Hybrid approaches**
* Using SQL databases for transaction-heavy workflows
* NoSQL for read-heavy, eventually consistent data
* Event streams to maintain consistency across systems
**Application-level consistency**
* Implementing business rules in application code
* Using external coordination services
* Accepting eventual consistency for non-critical operations

**The bottom line**: Modern NoSQL systems can provide ACID guarantees, but often with more constraints or performance trade-offs than traditional RDBMS. The choice depends heavily on whether your use case can work within those constraints or if the scalability benefits justify the added complexity.

## Follow-up: What sharding capabilities are available with each option (SQL and NoSQL)? ##