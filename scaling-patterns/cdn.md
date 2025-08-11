---
layout: default
title: Content Delivery Networks
parent: Scaling Patterns
nav_order: 1
---

# Content Delivery Networks
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition ##

A network of geographically distributed servers that cache static (and sometimes dynamic) content closer to users' physical locations.

## Problem Solved ##

Without a CDN, every request for a static file (image, CSS, video) goes to the origin server - causing latency for distannt users and extra load on the origin.

### Without a CDN ###
* Every request for static files (images, CSS, videos) goes to origin server
* Users far from origin experience high latency (e.g. 300-500ms+)
* Origin server bandwidth and CPU wasted serving static content
* Single point of failure for all content delivery

### With a CDN ###
* Static content served from edge locations near users
* Origin server only handles dynamic requests and cache misses
* Dramatically improved user experience and reduced infrastructure costs

## How it works ##
* User requests a file
* Request routed to nearest CDN edge node
* If node has file cached, it serves directly (low latency)
* If not cached, node fetches from origin, caches, and serves
```
User (London) requests logo.png
    ↓
CDN Edge (London) - Cache HIT
    ↓
Serve from edge cache (~20ms)

User (Tokyo) requests logo.png  
    ↓
CDN Edge (Tokyo) - Cache MISS
    ↓
Fetch from Origin (US West) - (~200ms)
    ↓
Cache at Tokyo edge + serve to user
    ↓
Next Tokyo user gets cached version (~30ms)
```
```
[Users] → [Edge Locations] → [Regional Caches] → [Origin Server]
   |           |                    |               |
Global      100+ locations      5-10 regions    Your servers
```

* **Edge locations**: Small caches close to users (1-10GB storage)
* **Regional caches**: Larger caches serving multiple edges (100GB - 1TB)
* **Origin**: Your servers providing source of truth

## Content Types & Caching Strategies ##

### Static Assets (High cache hit rate) ###
**Examples**: Images, CSS, JavaScript, fonts, videos


## Common CDN Origin Patterns ##

## Additional Considerations ##

### Proxy Servers ###

**Next:** [Application Caching](application-caching.html)