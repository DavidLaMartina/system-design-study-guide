---
layout: default
title: Estimate Scale
parent: Interview Framework
nav_order: 2
---

# Step 2: Estimate Scale
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition ##

Back-of-the-envelope math to determine expected load, storage needs, and growth.

## Why It Matters ##

Without scale estimates, your design decisions are guesses. This step is where you justify architecture choices with numbers.

## What to Do in the Interview ##
* Estimate traffic (DAU -> requests / day -> request / sec)
* Separate reads vs. writes (may be a huge difference)
* Estimate data size per request
* Multiply to get storage growth per day / month / year
* Apply a peak factor (often 5-10x average load)

## What to Say ##
"Let's run some quick scale estimates, so we can match the architecture to the load profile."

## Key terms explained ##
* RPS / QPS: Requests per second / queries per second
* Throughput: Data processed per unit time
* Peak factor: How much higher peak traffic is compared to average

## Example calculations **
* 20M DAU × 15 requests/day = 300M requests/day.
* 300M / 86,400 ≈ 3.47k RPS average.
* Assume peak = 5× average → ~17.35k RPS.
* Each request writes 2KB → ~600GB/day storage growth.

## Real-world Example ##
Amazon retail teams model traffic for Black Friday & Cyber Monday as ~10x normal daily load to ensure systems autoscale to meet that peak without human intervention.

## What can go wrong if skipped ##
* Wrong database type (choosing a single-node SQL DB when you need distributed NoSQL, for instance).
* No plan for sudden spikes, which leads to outages
* Overpaying for capacity you never use

**Next:** [Define Core Entities](define-core-entities.html)