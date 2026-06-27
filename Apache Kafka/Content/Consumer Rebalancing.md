# 17. Consumer Rebalancing

Consumer Rebalancing is one of the most important Kafka concepts and one of the most frequently asked interview topics.

Rebalancing is the mechanism Kafka uses to redistribute partitions among consumers within a Consumer Group whenever the group membership changes.

Without rebalancing:

```text
Consumer Failures
New Consumers
Partition Changes
```

could leave partitions unprocessed or overloaded.

---

# Why Rebalancing Exists?

Consider:

```text
Topic: orders

Partitions:
P0 P1 P2 P3
```

Consumer Group:

```text
C1
C2
```

Assignment:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

Now:

```text
C2 Crashes
```

Without rebalancing:

```text
P2,P3
```

would never be consumed.

---

Kafka solves this using:

```text
Rebalancing
```

which redistributes ownership.

---

Result:

```text
C1 -> P0,P1,P2,P3
```

---

# What is Rebalancing?

## Definition

Rebalancing is the process of redistributing topic partitions among consumers in a Consumer Group.

---

## Goal

Maintain:

```text
Balanced Workload

Fault Tolerance

Parallel Processing
```

---

## High-Level Flow

```text
Group Change
      ↓
Stop Current Assignment
      ↓
Recalculate Ownership
      ↓
Assign Partitions
      ↓
Resume Consumption
```

---

# Visualization

Before:

```text
Consumer Group

C1 -> P0,P1

C2 -> P2,P3
```

---

After New Consumer Joins:

```text
C1 -> P0

C2 -> P1

C3 -> P2,P3
```

---

Kafka performs:

```text
Rebalance
```

to achieve this.

---

# Why Rebalancing Happens?

Rebalancing occurs whenever Kafka detects a change affecting partition ownership.

---

# Consumer Joins Group

Most common reason.

---

Before:

```text
C1 -> P0,P1,P2,P3
```

---

New Consumer:

```text
C2
```

joins.

---

After:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

# Consumer Leaves Group

---

Before:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

C2 exits gracefully.

---

After:

```text
C1 -> P0,P1,P2,P3
```

---

# Consumer Crash

---

Before:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

C2 crashes.

---

Heartbeats stop.

---

Coordinator detects failure.

---

After:

```text
C1 -> P0,P1,P2,P3
```

---

# Session Timeout

Consumer remains alive but fails to send heartbeats.

---

Example:

```text
Consumer Stuck
Long GC Pause
Network Issue
```

---

Coordinator marks consumer dead.

---

Triggers rebalance.

---

# Topic Partition Change

---

Before:

```text
4 Partitions
```

---

After:

```text
8 Partitions
```

---

Kafka must assign new partitions.

---

Triggers rebalance.

---

# Subscription Change

Consumer subscribes to:

```text
New Topic
```

or unsubscribes from a topic.

---

Triggers rebalance.

---

# Summary of Triggers

| Trigger | Rebalance? |
|----------|-----------|
| Consumer Join | Yes |
| Consumer Leave | Yes |
| Consumer Crash | Yes |
| Heartbeat Failure | Yes |
| Partition Increase | Yes |
| Subscription Change | Yes |

---

# Rebalancing Workflow

Understanding the internal flow is a favorite interview topic.

---

# Step 1

Consumer group change detected.

---

Example:

```text
New Consumer Joined
```

---

# Step 2

Coordinator pauses assignments.

---

# Step 3

Current partitions revoked.

---

Example:

```text
C1 Loses P0,P1
```

temporarily.

---

# Step 4

New assignment calculated.

---

# Step 5

Partitions reassigned.

---

# Step 6

Consumption resumes.

---

# Complete Flow

```text
Consumer Change
       ↓
Coordinator Detects
       ↓
Pause Consumption
       ↓
Revoke Partitions
       ↓
Calculate Assignment
       ↓
Assign Partitions
       ↓
Resume Consumption
```

---

# Impact of Rebalancing

Rebalancing is necessary but expensive.

---

# During Rebalance

Consumers temporarily stop consuming.

---

Result:

```text
Processing Pause
```

---

# Effects

```text
Higher Latency

Reduced Throughput

Temporary Downtime
```

---

Therefore:

```text
Frequent Rebalancing
Is Bad
```

---

# Rebalancing Strategies

Kafka supports multiple assignment and rebalance strategies.

---

Common strategies:

```text
Range Assignor

Round Robin Assignor

Sticky Assignor

Cooperative Sticky Assignor
```

---

These determine how partitions are redistributed.

---

# Range Assignor

Assigns consecutive partitions.

---

Example:

Partitions:

```text
P0 P1 P2 P3
```

Consumers:

```text
C1
C2
```

Assignment:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

# Advantages

Simple.

---

# Disadvantages

May create uneven distribution.

---

# Round Robin Assignor

Distributes partitions evenly.

---

Assignment:

```text
C1 -> P0,P2

C2 -> P1,P3
```

---

# Advantages

Better balance.

---

# Disadvantages

More partition movement during rebalance.

---

# Sticky Assignor

Introduced to reduce partition movement.

---

Goal:

```text
Keep Existing Assignments
Whenever Possible
```

---

Before:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

After consumer joins:

```text
Minimal Changes
```

---

# Advantages

```text
Reduced Movement

Less State Transfer

Faster Recovery
```

---

# Cooperative Sticky Assignor

Modern Kafka recommendation.

---

Kafka Version:

```text
2.4+
```

introduced incremental cooperative rebalancing.

---

Benefits:

```text
Less Downtime

Incremental Changes

Reduced Pauses
```

---

# Eager Rebalancing

Traditional Kafka rebalance mechanism.

---

# What is Eager Rebalancing?

All consumers stop processing and revoke all partitions before reassignment begins.

---

# Workflow

```text
Consumer Change
       ↓
Everyone Stops
       ↓
All Partitions Revoked
       ↓
Reassignment
       ↓
Resume
```

---

# Visualization

Before:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

Consumer joins.

---

Eager Rebalance:

```text
Revoke Everything
```

---

Then:

```text
Assign Again
```

---

# Advantages

Simple implementation.

---

# Disadvantages

Major drawback:

```text
Full Stop-The-World Event
```

---

Effects:

```text
Consumption Pause

Higher Latency

Lower Throughput
```

---

# Example

Even if only:

```text
One Partition Changes
```

all consumers stop.

---

This becomes expensive in large systems.

---

# Cooperative Rebalancing

Modern solution.

---

# What is Cooperative Rebalancing?

Consumers only give up partitions that actually need reassignment.

---

Instead of:

```text
Revoke Everything
```

Kafka performs:

```text
Incremental Changes
```

---

# Workflow

```text
Consumer Change
       ↓
Identify Needed Changes
       ↓
Reassign Selected Partitions
       ↓
Continue Processing
```

---

# Example

Before:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

Consumer joins:

```text
C3
```

---

Only required partitions move.

---

Other partitions continue processing.

---

# Benefits

---

## Minimal Downtime

---

## Faster Rebalancing

---

## Higher Throughput

---

## Less State Movement

---

## Better For Large Systems

---

# Eager vs Cooperative Rebalancing

| Feature | Eager | Cooperative |
|----------|---------|------------|
| Stops All Consumers | Yes | No |
| Revoke All Partitions | Yes | No |
| Downtime | High | Low |
| Throughput Impact | High | Low |
| Partition Movement | High | Low |
| Modern Recommendation | No | Yes |

---

# Static Membership

One of Kafka's most useful features for reducing unnecessary rebalances.

---

# Problem

Suppose:

```text
Consumer Restart
```

occurs.

---

Traditional behavior:

```text
Consumer Leaves
      ↓
Rebalance
      ↓
Consumer Returns
      ↓
Another Rebalance
```

---

Two rebalances.

---

# Solution: Static Membership

Consumer keeps a stable identity.

---

Configuration:

```java
group.instance.id
```

---

Example:

```java
props.put(
    "group.instance.id",
    "consumer-1"
);
```

---

Kafka recognizes:

```text
This Is Same Consumer
```

after restart.

---

# Benefits

---

## Fewer Rebalances

---

## Faster Restarts

---

## Better Stability

---

## Reduced Downtime

---

# Example

Without Static Membership:

```text
Restart
      ↓
Rebalance
```

---

With Static Membership:

```text
Restart
      ↓
Rejoin
      ↓
Often No Rebalance
```

---

# Rebalance Listener

Kafka allows applications to react to rebalance events.

---

# What is Rebalance Listener?

A callback mechanism executed during partition revocation and assignment.

---

Interface:

```java
ConsumerRebalanceListener
```

---

# Why Needed?

Before partition ownership changes:

```text
Save State

Commit Offsets

Cleanup Resources
```

---

After assignment:

```text
Restore State

Initialize Processing
```

---

# Methods

---

## onPartitionsRevoked()

Called before partitions are removed.

---

Example:

```java
@Override
public void onPartitionsRevoked(
    Collection<TopicPartition> partitions)
{
}
```

---

Typical Usage:

```text
Commit Offsets

Flush Data

Save State
```

---

# onPartitionsAssigned()

Called after new partitions are assigned.

---

Example:

```java
@Override
public void onPartitionsAssigned(
    Collection<TopicPartition> partitions)
{
}
```

---

Typical Usage:

```text
Load State

Initialize Resources

Resume Processing
```

---

# Example

```java
consumer.subscribe(
    List.of("orders"),
    new ConsumerRebalanceListener() {

        @Override
        public void onPartitionsRevoked(
            Collection<TopicPartition> partitions)
        {
            consumer.commitSync();
        }

        @Override
        public void onPartitionsAssigned(
            Collection<TopicPartition> partitions)
        {
            System.out.println(
                "Partitions Assigned"
            );
        }
    }
);
```

---

# Rebalance Best Practices

---

## Use Cooperative Sticky Assignor

Recommended for modern Kafka.

---

## Avoid Frequent Consumer Restarts

---

## Tune Heartbeat Settings

---

## Use Static Membership

```java
group.instance.id
```

---

## Implement Rebalance Listener

---

## Commit Offsets Before Revocation

---

## Monitor Rebalance Frequency

Frequent rebalances indicate problems.

---

# Real-World Example

E-Commerce Order Processing

---

Topic:

```text
orders
```

Partitions:

```text
12
```

Consumers:

```text
6
```

---

Consumer crashes.

---

Coordinator detects:

```text
Heartbeat Missing
```

---

Triggers:

```text
Rebalance
```

---

Remaining consumers receive:

```text
Additional Partitions
```

---

Order processing continues.

---

# Interview Questions

## What is Consumer Rebalancing?

Redistribution of partitions among consumers in a Consumer Group.

---

## Why does rebalancing happen?

Because of:

```text
Consumer Join

Consumer Leave

Consumer Crash

Partition Changes

Heartbeat Failure
```

---

## What is Eager Rebalancing?

All consumers stop, revoke all partitions, and then receive new assignments.

---

## What is the major drawback of Eager Rebalancing?

```text
Stop-The-World Pause
```

All consumers temporarily stop processing.

---

## What is Cooperative Rebalancing?

Incremental partition reassignment that minimizes downtime and partition movement.

---

## Which rebalance strategy is recommended today?

```text
Cooperative Sticky Assignor
```

---

## What is Static Membership?

A mechanism that gives consumers a stable identity using:

```java
group.instance.id
```

to reduce unnecessary rebalances.

---

## What is a Rebalance Listener?

A callback interface that allows applications to handle partition revocation and assignment events.

---

## Which methods exist in ConsumerRebalanceListener?

```java
onPartitionsRevoked()

onPartitionsAssigned()
```

---

## Why commit offsets during rebalance?

To prevent duplicate processing and ensure processing progress is preserved.

---

# Summary

Consumer Rebalancing is Kafka's mechanism for redistributing partitions whenever Consumer Group membership changes.

Core Concepts:

- Rebalancing
- Rebalance Triggers
- Rebalancing Strategies
- Eager Rebalancing
- Cooperative Rebalancing
- Static Membership
- Rebalance Listener

Rebalance Flow:

```text
Group Change
      ↓
Coordinator Detects
      ↓
Revoke Partitions
      ↓
Reassign Partitions
      ↓
Resume Processing
```

Modern Kafka recommendation:

```text
Cooperative Sticky Assignor
+
Static Membership
+
Rebalance Listener
```

This combination minimizes downtime, reduces partition movement, improves throughput, and creates highly stable consumer groups in production environments.