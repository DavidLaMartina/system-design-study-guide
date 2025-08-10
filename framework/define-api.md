---
layout: default
title: Define API
parent: Interview Framework
nav_order: 4
---

# Step 2: Estimate Scale
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition ##

To define your API(s) is to define the contract(s) between your system and its users. Oftentime (but not always), the APIs map directly to the functional requirements you've already identified in step 1.

## Why It Matters ##

The API contract serves as the guide to the high-level design and ensures you're meeting the functional requirements you've identified.

## Making a Choice - Your API Protocol ##
In general, default to REST - but do consider your alternatives.

### REST (Representational State Transfer) ###
REST uses HTTP "verbs" (GET, POST, PUT, DELETE) to perform CRUD operations on resources. This is the default choice for most interviews.

### GraphQL ###
Allows clients to specify exactly what data they want to receive, avoiding over-fetching and under-fetching. This may be a good option when you have diverse clients with different data needs.

### RPC (Remote Procedure Call) ###
Action-oriented protocol (such as gRPC) which is faster than REST for service-to-service communication. Use for *internal* APIs when performance is critical.

## Real-world Example - Twitter ##
POST /v1/tweets
body: {
  "text": string
}

GET /v1/tweets/{tweetId} -> Tweet

POST /v1/users/{userId}/follows

GET /v1/feed -> Tweet[]

**Next:** [High Level Architecture](high-level-architecture.html)