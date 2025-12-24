---
layout: post
title: "Why Deleting Data Is a Mistake: A Counter-intuitive Guide to Syncing Systems"
date: 2025-12-19 09:00:00 +0800
categories: engineering
tags: [sync, distributed-systems, data-sync, architecture]
excerpt: "Three counter-intuitive truths about data sync: timestamps can't guarantee causality, deletions must be tracked not executed, and operation logs beat final-state snapshots for distributed systems."
---

## 1.0 Introduction: The Deceptively Simple Problem of Keeping Things in Sync

As a developer, you've almost certainly faced this challenge: you build an application that needs to work seamlessly for multiple users or across multiple devices. Everything works perfectly until a user goes offline, makes a change, and comes back online. Suddenly, data is overwritten, lists are in the wrong order, and deleted items magically reappear. Welcome to the deceptively simple problem of data synchronization.

At first glance, keeping data consistent seems straightforward. But the reality is a minefield of complex edge cases, particularly around three core challenges: conflicts, ordering, and deletions. The simple approaches we often reach for first can lead to catastrophic data loss and a frustrating user experience.

The root of the problem is that many of our basic assumptions about data are wrong, at least in a distributed environment. This article will debunk three common myths and introduce the counter-intuitive but essential truths you need to build robust, reliable syncing systems. We'll move beyond simplistic solutions and adopt a practical, engineering-focused mindset for tackling one of software's hardest problems.

## 2.0 Takeaway 1: Timestamps Tell You When, Not Why

The most common and intuitive approach to synchronization is using an updated_at timestamp. The logic is simple: when two versions of a record conflict, pick the one with the newest timestamp. This "last write wins" strategy, common in systems like Firebase, is easy to implement and works perfectly fine for basic scenarios, like updating a user profile form where only the final state matters.

However, this method fails spectacularly the moment the order of operations becomes important. Consider a collaborative music playlist. User A moves a song to the top of the list. At the same time, User B, who is briefly offline, moves the same song to the bottom. When User B reconnects, the server sees their timestamp is 10ms later and accepts their change. The song is now at the bottom of the list. User A's intentional move to the top is completely lost, overwritten by an operation that was causally unrelated.

The fundamental issue is that timestamps cannot guarantee causality. They record the wall-clock time of a modification but have no understanding of the sequence of events or user intent. This is worsened by "clock drift," where client devices have slightly different times, making it impossible to establish an authoritative order of operations. A timestamp records the final state of data, but it is completely blind to the journey it took to get there.

**A timestamp records the final state of data, but it's blind to the journey. To truly sync, you must track the operation, not just the outcome.**

## 3.0 Takeaway 2: To Sync a Deletion, You Can't Actually Delete the Data

Here is one of the most surprising truths of data sync: if you need to synchronize a deletion, you must not actually delete the data from your database. The logical assumption—that when a user deletes a record, you should run a DELETE query—is a direct path to a common bug known as the "resurrection" problem.

Imagine this scenario:

1. Client A is online, deletes a record, and syncs this change to the server. The record is removed from the server's database.
2. Client B has been offline for the last hour and still has the old, undeleted copy of the data.
3. When Client B comes back online, it syncs with the server. It compares its local data with the server's data, sees the record is "missing" on the server, and helpfully re-uploads its "old" copy, bringing the deleted data back to life.

The solution is to treat deletion as an operation or a state change, not an erasure. Instead of removing the row, you mark it with a "tombstone" – a flag or a timestamp (e.g., is_deleted: true or deleted_at: '2023-10-27T10:00:00Z'). This approach is used by robust distributed databases like DynamoDB and Cassandra. The record's ID and metadata are preserved, and the deletion itself becomes a piece of information that can be synced to all clients.

But this raises a critical question: does the deleted data live forever? No. This is where tombstone cleanup, or compaction, comes in. Once the system can verify that all clients have "seen" the deletion (often by tracking a global version number, or "vector clock," across the system within a certain version window), the tombstoned records can be permanently purged. This prevents your database from growing infinitely with historical deletions.

**In a synchronized system, deletion isn't an absence of data; it's a piece of data itself. You must record it as an event, or risk it being forgotten and resurrected.**

## 4.0 Takeaway 3: Order Is an Action, Not a Property

We often think of sort order as an inherent property of data, like a position column. But in a distributed system, you must treat ordering as an action performed by a user. This is where the concept of an "Anchor" becomes necessary to establish an unambiguous history of events.

An Anchor is a monotonically increasing sequence number used to represent the "birth order" of an action. Unlike a timestamp, which records when something happened, an anchor records its place in a causal sequence: Operation A happened, then Operation B happened. This solves the causality problem that timestamps can't. When designing a system, you face a fundamental architectural choice:

* **Server-generated Anchors:** The server is the single source of truth and assigns all anchor values. This is ideal for applications where an authoritative order is paramount and clients are generally online, such as a project management board.
* **Client-generated Anchors:** Each client generates its own anchors. This is essential for applications that must work offline or in a peer-to-peer (P2P) context. The server's role shifts to merging streams of ordered operations from different clients.

You see this concept in systems you already use. A Git commit hash is a powerful anchor; it's cryptographically linked to its parent commit, forming an immutable causal chain. When you move a Trello card, the ordering action is assigned an anchor. The result of that action is a new rank for the card (often a fractional index), placing it between others without requiring an update to every other item in the list.

**Data doesn't have an inherent order; users create order through their actions. To sync ordering correctly, you must log the sequence of actions, not just the final arrangement.**

## 5.0 Conclusion: The Three Pillars of Sync

Building a reliable sync system is less about a single technology and more about a philosophical shift in how we think about data. It's an engineering challenge of managing version, causality, and deletion across a distributed system. By asking the right architectural questions, you can design a system that works.

Start with this decision tree:

* **Does the order of my data matter?** If yes, you need Anchors and a ranking system. If no, a simple Timestamp (updated_at) might be enough.
* **Do I need to sync deletions?** If yes, Tombstones are non-negotiable.
* **Do my clients need to work offline?** If yes, you'll need a Client-generated Anchor model.

Putting it all together, a sync-ready database table often applies all three pillars and looks something like this:

* `id`: The unique identifier for the record.
* `data`: The actual content (e.g., a JSON blob).
* `rank`: A string or float for relative ordering.
* `anchor`: A monotonically increasing number tracking the sequence of operations.
* `updated_at`: A timestamp for knowing the wall-clock time of the last change.
* `deleted_at`: A tombstone flag for synchronized deletions.

**Timestamps solve the version problem, Anchors solve the causality problem, and Tombstones solve the deletion problem.** Now that you know sync is about tracking causality, not just state, how will you approach designing the next feature for your application?
