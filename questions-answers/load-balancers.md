---
layout: default
title: Load Balancers
parent: Questions & Answers
nav_order: 1
---

# Load Balancers
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## What are the most common load balancing algorithms? ##

### Basic Algorithms ###
**Round Robin**: Requests are distributed sequentially across servers. Simple and fair when all servers have similar capacity. Most load balancers start here as a default.

**Weighted Round Robin**: Servers get different numbers of requests based on their assigned weights. Useful when servers have different capacities (e.g. some have more CPU / memory).

**Least connections**: Routes new requests to the server currently handling the fewest active connections. Works well for applications where request processing times vary significantly.

**Weighted Least Connections**: Combines least connections with server weights, accounting for both current load and server capacity.

### Advanced Algorithms ###
**Least Response Time**: Routes to the server with the lowest average response time and fewest active connections. More sophisticated but requires the load balancer to track response times.

**Random**: Surprisingly effective for many workloads. Simple to implement and performs well when you have many servers. Less predictable than round robin but often performs similarly in practice.

**IP Hash**: Uses a hash of the client's IP address to consistently route them to the same server. Provides session affinity without requiring session storage.

### Modern / Intelligent Algorithms ###
**Consistent Hashing**: Popular in distributed systems and CDNs. When servers are added / removed, only a small portion of requests need to be remapped. Used by systems such as Amazon's DynamoDB and many caching layers.

**Power of Two Choices**: Randomly selects two servers and routes to the one with fewer connections. Surprisingly effective - performas almost as well as least connections but with much less overhead.

**Adaptive / Machine Learning**: Some modern load balancers use real-time metrics (CPU usage, memory, response times, queue depths) to make intelligent routing decisions.

### What You'll See in Practice ###
**Application Load Balancers**: (AWS ALB, nginx): Often default to round robin with options for least connections and various hash-based methods.

**Service meshes**: (Istio, Linkerd): Commonly use round robin or least connections, with increasingly sophisticated options like circuit breakers and outlier detection.

**CDNs and Reverse Proxies**: Frequently use geographic routing combined with consistent hashing or weighted round robin.

**Database Load Balancers**: Often use connection-based algorithms since DB connections are long-lived and expensive.

### Algorithms Selection Factors ###
The choice depends on:
**Request uniformity**: If requests take similar time, round robin works great.
**Server heterogeneity**: If servers have different capacities, weighted algorithms are essential.
**Session requirements**: If you need sticky sessions, hash-based or consistent hashing.
**Observability needs**: More sophisticated algorithms require more monitoring data.
**Scale**: Simple algorithms perform better at very high scales.

**Most common in practice**: Round robin and least connections cover the majority of use cases. Many systems start with round robin and only move to more complex algorithms when they have specific peformance requirements or constraints.

The trend in modern systems is toward more intelligent, metrics-driven approaches, but the fundamentals haven't changed - you're still trying to distribute load efficiently while maintaining good user experience.

## Follow-up Question - Load Balancing for Databases? ##
**Question**:
The prior question was related to load balancing; how is that relevant to databases?

**Answer**:

### Database Load Balancing Use Cases ###
**Read Replicas**: This is the most common scenario. You have one primary database handling writes and multiple read replicas. A load balancer distributes read queries across the replicas to improve read performance and availability.

**Database Clustering**: Systems such as MySQL Cluster, PostgresSQL with Postgres-XL, or Oracle RAC have multiple nodes that can handle reads and writes. Load balancers distribute connections across these nodes.

**Connection Pooling**: Database connections are expensive to establish. Load balancers can maintain connection pools and distribute lcient connections efficiently across multiple database instances.

**Geographic Distribution**: Multi-region database setups where you want to route users to their nearest database instance for better latency.

### Why Database Load Balancing is Different ###
Unique characteristics compared to web application load balancing:
**Connection stickiness**: Database connections are stateful and long-lived, unlike stateless HTTP requests.
**Read / write splitting**: You often need intelligent routing based on query type.
**Transaction consistency**: Some load balancers need to understand database transactions to route related queries to the same instance.

## Long-lived Server-to-Database Connections ##
**Question**: When we're talking about long-lived database connections in the context of a web application, we're talking about the server's connection to its database(s), rather than a client's connection to the server, correct?

**Answer**:
Yes - an important distinction.

### The Connection Layers ###
**Client to Web Server**: These are typically short-lived HTTP connections. E.g. a user's browser makes a request, gets a response, and the connection often closes (or gets reused briefly and then closed). Even with HTTP keep-alive, these connections are transient.

**Web server to database**: These are long-lived connections. Your application server establishes connections to the database(s) it needs and keeps them open for extended periods of time to avoid the (high) overhead of constantly connecting and disconnecting.

### Why Web Server -> Database Connections are Long-Lived ###
**Connection overhead**: Establishing a database connection involves authentication, SSL handshakes, and protocol negotiation. This is expensive to do for every query.

**Connection pooling**: Most web apps use connection pools, i.e. they maintain a set of open database connections (say, 10-50) that can be reused across different user requests.

**Stateful Nature**: Database connections often maintain session state, prepared statements, temporary tables, etc. that you want to preserve across multiple operations.


## Load Balancers at Different Layers of the Internet ##
**Question**:
What is the difference between load balancers that operate at different layers of the internet, e.g. Layer 7 vs. Layer 4? Which are more common in different use cases?

**Answer**:
The key difference lies in how much of the network traffic they inspect and understand, which affects their capabilities and performance characteristics.

### Layer 4 (Transport Layer) Load Balancers ###
**What they see**: IP addresses, ports, and basic TCP / UDP connection information. They operate at the transport layer and make routing decisions based on network-level data.

**How they work**: They typically use NAT (Network Address Translation) or direct server return. The load balancers sees the initial connection request and forwards packets to a chosen backend server, often without inspecting the actual application data.

**Capabilities**:
* Route based on source / destination IP and port
* TCP / UDP protocol handling
* Connection-based load balancing algorithms
* SSL passthrough (encrypted traffic passes through unchanged)

**Performance**: Very fast and low-latency since they don't need to parse application protocols. Can handle millions of connections with minimal processing overhead.

### Layer 7 (Application Layer) Load Balancers ###
**What they see**: Full HTTP/HTTPS requests, including headers, URLs, cookies, and request bodies. They understand application protocols.

**How they work**: They terminate the client connection, parse the HTTP request, make a routing decision, then establish a new connection to the backend server. This is called "proxying."

**Capabilities**:
* Route based on URL paths, HTTP headers, cookies, request methods
* SSL termination and re-encryption
* Content-based routing (e.g. ``/api/*`` goes to API servers, ``/static/*`` goes to CDN)
* Request modification (adding / removing headers)
* Caching, compression, rate limiting
* Health checks based on application responses

**Performance**: Higher latency and resource usage due to protocol parsing and connection termination, but still very capable of handling high loads.

### Common Use Cases ###
#### Layer 4 is more common for: ####
**Database load balancing**: You just need to distribute TCP connections to database servers - no need to inspect SQL queries.

**Simple web server load balancing**: When all servers handle the same content and you just want to distribute connections.

**Gaming / streaming applications**: Low-latency requirements where protocol inspection would add unwanted overhead.

**Network-level services**: DNS servers, VPN endpoitns, or any service where you're just moving packets around.

**High-throughput scenarios**: When you need maximum performance and don't need application intelligence.

#### Layer 7 is more common for: ####
**Microservices architectures**: Routing ``/users/*`` to user service, ``/orders/*`` to order service, etc.
**API Gateway**: Need to inspect HTTP methods, authenticate requests, rate limit by user, etc.
**Modern web applications**: Content-based routing, A/B testing, canary deployments.
**SSL termination**: When you want to decrypt traffic once at the load balancer rather than on every backend server.
**Multi-tenant applications**: Routing based on subdomain or tenant identifier in headers.

#### Hybrid Approaches ####
Many modern load balancers can operate in both modes:
**AWS ALB vs. NLB**: Application load balancer (layer 7) vs Network load balancer (layer 4).
**nginx**: Can do simple TCP / UDP proxying (layer 4) or full HTTP proxying (layer 7)
**Cloud provider load balancers**: Often let you choose the mode based on your needs

#### The Reality in Practice ####
**Layer 7 has become increasingly common as applications have grown more complex.** Microservices, API-first architectures, and the need for intelligent routing have made Layer 7 features essential for many use cases.

**Layer 4 remains crucial** for high-performance, low-latency scenarios and for protocols other than HTTP / HTTPS.

Many architectures use both: Layer 4 for the initial traffic distribution and Layer 7 for application-specific routing within each service cluster.

### Follow-up: What about AWS API Gateway and similar technologies? ###
**Question**:
What about AWS API Gateway? It seems to offer Layer 7 features, e.g. proxying based on API endpoint and request validation against a schema, but it has to be connected to a layer 4 Network Load Balancer (usually through a VPC link)?

**Answer**:
This is great example of how modern cloud architectures layer different types of load balancing for different purposes.

### Why This Layered Approach? ###
**AWS API Gateway** is indeed a Layer 7 service. It understands HTTP, can route based on paths, validates request schemas, handles authentication, rate limiting, etc. But it's a **managed service** that needs to connect to resources in your VPC.

**The VPC Link + NLB pattern** exists because:
1. **Network isolation**: Your backend services are in a private VPC, but API Gateway is a public AWS service.
2. **Connection efficiency**: NLB provides a stable, high-performance entry point into your VPC.
3. **Health checking**: NLB handles health checks and failover for your backend instances.
4. **Protocol translation**: API Gateway handles HTTP / REST loic, NLB handles the actual TCP connections to your services.

#### The Flow Looks Like: ####
```
Client Request 
→ API Gateway (Layer 7: authentication, validation, routing) 
→ VPC Link 
→ Network Load Balancer (Layer 4: connection distribution)
→ Backend Services (EC2, containers, etc.)
```

#### This is not redundant because: ####
**Different responsibilities**:
* API Gateway: API management, security, throttling, request / response transformation
* NLB: Efficient connection distribution, health checking, high availability within your VPC
**Different scaling characteristics**:
* API Gateway: Managed scaling, handles internet-facing concerns
* NLB: Optimized for high throughput, low-latency connections to your actual compute resources
**Network boundaries**:
* API Gateway operates in AWS's public network
* NLB operates within your private VPC network

#### Alternative Patterns ####
You could also see:
**API Gateway --> ALB**: for more Layer 7 features within the VPC
**Direct integration** with Lambda (no load balancer required)
**API Gateway --> ELB Classic** in older architectures

This layered approach is actually quite common in cloud architecture - you often have multiple levels of load balancing serving different purposes rather than tryingto solve everything with a single load balancer. Each layer handles the concerns it's best suited for.

### Traditional Pattern vs. API Gateways ###
Modern cloud architectures break from the traditional OSI model flow.

### Traditional Network Flow ###
In a classic network setup, you'd expect:
```
Server A Layer 7 → Layer 6 → 5 → 4 → 3 → 2 → 1 (physical transmission)
→ Server B Layer 1 → 2 → 3 → 4 → 5 → 6 → Layer 7
```

This is the "proper" way the OSI model describes network communication, i.e. you go up and down the full stack.

#### The AWS API Gateway --> NLB "Roller Coaster" ####
But with API Gateway --> NLB, you get this weird flow:
```
Client HTTP Request 
→ API Gateway (Layer 7 processing: auth, validation, routing)
→ Makes a NEW HTTP request to NLB 
→ NLB (Layer 4 processing: TCP connection distribution)
→ Backend Service (Layer 7 again: actual application logic)
```

This is a "roller coaster" that technically violates the traditional layered approach. This request gets fully processed at Layer 7 (API Gateway) and then gets re-packed and sent down to Layer 4 (NLB), then back up to Layer 7 (your application).

#### Why This Happens in Cloud Architectures ####
This pattern exists because these aren't really "layers" in the traditional sense - they're **separate services** with different responsibilities:
* **API Gateway**: A managed proxy service that happens to operate at Layer 7
* **NLB**: A managed load balancer that happens to operate at Layer 4

Each service terminates the connection, processes it according to its function, then makes a new outbound connection. They're not transparent network devices - they're application services that understand and modify the traffic.

#### This Pattern is Everywhere in Modern Systems ####
You see similar "layer violations" in:
* **Service meshes**: (Istio sidecars doing Layer 7 -> Layer 4 -> Layer 7)
* **CDNs**: (Edge servers processing at Layer 7, then routing through Layer 4 infrastructure)
* **Reverse proxies** chained together
* **API Gateways -> Application load balancers --> service discovery**

The traditional OSI model assumes you're moving through network infrastructure, but modern distributed systems are really chains of application services that each make their own networking decisions.