---
layout: default
title: Clarify Requirements
parent: Interview Framework
nav_order: 1
---

# Step 1: Clarify Requirements
{: .no_toc }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition

This step ensures you understand exactly what needs to be built and under what conditions, before you commit to design decisions.

## Why It Matters

Many failures in software architecture happen not because the tech was wrong, but because the team solved the wrong problem or built for incorrect assumptions.

## What to Do in the Interview

### Ask for Functional Requirements
The core capabilities the system must have.

**Example questions:**
- "What are the main user flows we need to support?"
- "Should users be able to edit content after posting?"
- "Do we need real-time notifications?"

### Ask for Non-Functional Requirements  
Performance targets, reliability, compliance needs.

**Example questions:**
- "What's the expected latency budget?"
- "What regions will we serve initially?"
- "Are we prioritizing availability over strict consistency?"

### Identify Explicit Constraints
Technology mandates, deadlines, budgets.

**Example questions:**
- "Are there any technology constraints I should know about?"
- "What's the timeline for launch?"
- "Any compliance requirements?"

### Confirm Growth Expectations
Will usage grow 10× in a year, or is this a niche tool?

## What to Say

> "Before we start sketching out a design, I'd like to confirm the functional and non-functional requirements so I can align the architecture with the business needs."

## Key Terms Explained

**Functional Requirements**: Tangible features (e.g., "users can upload and share photos")

**Non-Functional Requirements (NFRs)**: How well the system does those things (e.g., "photo loads must be under 200ms")

**Constraints**: Boundaries you can't cross (e.g., "must run on AWS GovCloud due to compliance")

**SLA/SLO**: Service Level Agreement / Objective — formal performance commitments

## Real-World Example

When WhatsApp added voice calling, they first confirmed:
- Calls must be end-to-end encrypted
- Latency should not exceed 200ms one-way  
- Must work reliably on 2G connections

These constraints shaped everything from codec choice to server placement.

## Common Follow-up Questions

- "What's the expected latency budget?"
- "What regions will we serve initially?"
- "Are we prioritizing availability over strict consistency?"

## What Can Go Wrong If Skipped

- **Over-engineering**: Building for scale you'll never hit
- **Under-engineering**: Designing for 100k users when you'll have 10M
- **Wrong optimization**: Focusing on latency when the real issue is cost

---

**Next:** [Estimate Scale](estimate-scale.html)