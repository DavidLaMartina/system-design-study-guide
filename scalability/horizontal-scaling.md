---
layout: default
title: Horizontal Scaling
parent: Scalability
nav_order: 1
---

# Horizontal Scaling
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## What is Horizontal Scaling?

Horizontal scaling (scale-out) involves adding more servers to handle increased load, rather than upgrading existing hardware.

## Advantages

- **Cost effective** - Use commodity hardware
- **Fault tolerance** - Failure of one server doesn't bring down the system
- **Geographic distribution** - Servers can be distributed globally

## Challenges

- **Complexity** - More servers mean more complexity
- **Data consistency** - Keeping data in sync across servers
- **Load balancing** - Need to distribute requests efficiently

## Implementation Strategies

### Load Balancers
- Round robin
- Least connections
- Weighted round robin

### Database Considerations
- Read replicas
- Sharding
- Master-slave configurations

## Example: Web Application Scaling