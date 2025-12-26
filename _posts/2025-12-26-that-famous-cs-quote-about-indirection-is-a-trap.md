---
layout: post
title: "That Famous CS Quote About \"Indirection\" Is a Trap"
date: 2025-12-26
tags: [software-architecture, abstraction, engineering, design-patterns]
image: /assets/images/indirection-abstraction-costs.webp
excerpt: "The famous quote 'All problems in computer science can be solved by another level of indirection' isn't a license to add layers—it's a warning. Every abstraction is a transaction, and you will always pay the price."
---

## Introduction: The Misunderstood Mantra

You've heard the famous computer science aphorism a thousand times: "All problems in computer science can be solved by another level of indirection". It's quoted in architecture meetings and taught in university courses as a foundational truth.

But mention it to a senior engineer, and you'll likely get a cynical laugh. Why? Because they are the ones who bear the daily costs of that "solution." They are the ones who spend hours tracing a single request through a dozen wrappers, deciphering scattered logs, and fighting the performance drag of unnecessary layers.

This quote isn't a license to add layers. It's a warning: every abstraction is a transaction, and you will always pay the price.

<img src="/assets/images/indirection-abstraction-costs.webp" alt="The Hidden Cost of Abstraction - showing the power of indirection, the hidden costs, and the engineer's decision checklist" style="max-width: 100%; height: auto;">

## What Indirection Actually Is

Before we tear it down, let's be clear about what indirection really is. It's not a specific design pattern, an interface, or a pointer. It's a fundamental structure, the most basic building block of abstraction:

A → X → B

Here, X exists for one reason: to prevent A from having to know about the messy, changing details of B. That's it. This simple structure is the common ancestor of many tools we use every day:

* Pointers
* Interfaces and Protocols
* Signaling servers
* Message queues

They are all forms of indirection, acting as the intermediary X to shield one part of a system from another.

## Why Indirection Is So Tempting

So why is this pattern so powerful and universal? Because the root of most problems in computer science is managing change and uncertainty. Indirection is our primary weapon in that fight. Its power comes from three core motivations:

1. **Decoupling**: When you know a component is going to change—a database, a third-party vendor, a network protocol—you introduce a layer to absorb that change. The rest of the system can remain stable.
2. **Late Binding**: Sometimes you can't make a decision until the code is actually running. Indirection allows you to delay the choice of which specific implementation to use based on runtime conditions or environmental factors.
3. **Boundary Crossing**: When systems can't talk directly—because they are in different processes, on different machines, or operate under different trust models—indirection creates the bridge that connects them.

## 1. The Real Enemy: Abstraction Without a Reason

Let's be honest. The deep-seated frustration engineers have with "over-engineering" isn't about abstraction itself. It's about unearned abstraction—layers added without pressure from a real, immediate problem.

These are the common anti-patterns, the costly layers of indirection built on justifications that aren't based on concrete needs:

* Creating an interface-implementation pair when there is no foreseeable second implementation.
* Adding a service or a queue purely for a hypothetical "future use case".
* Structuring code just to make an architecture diagram "look pretty".

Each of these decisions introduces a layer that must be maintained, understood, and debugged. They are architectural debt taken on without a clear return on investment.

**If the change never comes, then this layer of indirection is just pure cost.**

## 2. The True Price: Calculating the Abstraction Tax

While the benefits of indirection are well-known, its costs are often ignored until it's too late. It's helpful to frame these costs as a recurring "tax" on every feature, bug fix, and new team member.

**Cognitive Cost**
Excessive layers make code incredibly difficult to trace. The simple, linear flow of data becomes a maze of calls, events, and queues. This makes onboarding new team members a nightmare and forces even experienced engineers to constantly ask the question, "Where is the data right now?" Every new layer increases the mental overhead required to simply understand the system.

**Debugging Cost**
When a bug occurs, layers of abstraction become layers of obfuscation. That one-line NullPointerException? When it's buried under five layers of generic exception handling, it's no longer a simple bug. It's a day-long archaeological dig through scattered logs. Layers wrap errors, dilute the root cause, and turn straightforward debugging into a distributed forensics investigation.

**Performance Cost**
Indirection is not free. Every layer introduces latency and resource consumption. This isn't theoretical; it happens through concrete mechanisms like data copying between memory buffers, queueing that adds wait times, and context switching that burns CPU cycles. In most applications, this overhead is negligible. But in real-time, media, or firmware systems, this cost can be fatal.

## 3. Case Study in Over-Engineering: OSI vs. TCP/IP

To see the consequences of theoretical purity versus pragmatic reality, look no further than the battle between the OSI and TCP/IP networking models.

The OSI model is the theoretical extreme of clean, layered indirection. Its seven layers were perfectly designed, with each one tasked to "shield the upper layers from the instability of the lower layers." It was a beautiful abstraction.

In contrast, the TCP/IP model that powers the modern internet is messier. It collapses several of the OSI's theoretical layers into more practical ones. TCP/IP isn't a less intelligent model; it's a more honest one. It survived and thrived in the real world precisely because it proved that not every theoretical layer of abstraction is worth its associated cost in performance and complexity.

## 4. A Pragmatist's Guide: To Layer, or Not to Layer?

For engineers in the trenches, theory only goes so far. The decision to add a layer of indirection is a trade-off. Here is a practical checklist to help you make that call.

### Green Flags: When a Layer is Worth the Cost

* **The underlying component changes with high frequency.** You need to swap out a database, vendor, or protocol regularly.
* **You need to cross a significant boundary.** This includes process, network, trust, or device boundaries where direct coupling is impossible or unsafe (e.g., WebRTC signaling and media pipelines).
* **A decision must be delayed until runtime (late binding).** The specific implementation isn't known until the code is actually running.
* **You need to achieve failure isolation.** You need to contain the blast radius of a failing component so it doesn't bring down the entire system.

### Red Flags: When to Avoid Another Layer

* **The code is on a performance-critical "hot path".** Any added overhead will have a significant and direct impact on users.
* **You are working in a real-time system.** Latency and determinism are non-negotiable requirements.
* **There is currently only a single implementation.** You are abstracting for a "maybe," not a "now."
* **High traceability and debuggability are paramount.** The system is complex or critical, and being able to quickly find the root cause of a problem is a top priority.

## Conclusion: The Real Engineering Challenge

Let's return to the original quote: "All problems in computer science can be solved by another level of indirection". After weighing the costs, a more pragmatic interpretation emerges.

**The quote is only true if you are willing and able to pay the price for it.**

The mark of a junior engineer is knowing how to add another layer. The mark of a senior engineer is knowing why you shouldn't—and having the courage to say so.

The truly difficult problem isn't "can we add another layer," but "when should we stop?"
