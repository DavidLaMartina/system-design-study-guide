---
layout: default
title: Define Core Entities
parent: Interview Framework
nav_order: 3
---

# Step 3: Define Core Entities
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition ##

Identify the core entities of your system (different from the functional components). Essentially, these are the entities your API will exchange, and which your system will persist in a data model.

## Why It Matters ##

An understanding of the core entities allows you to define terms, understand the data central to your design, and provides a foundation upon which you can build your APIs and high-level design.

## What to D (and ask) in the Interview ##
* Who are the actors in the system, and do they overlap?
* What are the resources (nounss) necessary to satisfy the functional requirements?
* Start with a minimal list, and build upon it as necessary
* Use good names - [one of the hardest problems in computer science](https://www.martinfowler.com/bliki/TwoHardThings.html).

## What to Say ##

"Let's define the core entities of our system, according to the requirements and use case."

## Real-world Example - TWitter ##
* User
* Tweeet
* Follow

## What can go wrong if skipped ##

If you're not clear on the core entities of the system, your data model and set of APIs will be incomplete, and you may miss key functional components in your high-level design.