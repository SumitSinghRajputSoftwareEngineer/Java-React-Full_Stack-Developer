
Consumer Groups are one of Kafka's most powerful features.

They enable:

```text
Scalability
Fault Tolerance
Parallel Processing
Load Balancing
High Throughput
```

Without Consumer Groups, Kafka would not be able to process millions of messages efficiently.

A very common interview statement is:

> Kafka achieves horizontal scaling on the consumer side using Consumer Groups.

---

# What is Consumer Group?

## Definition

A Consumer Group is a collection of one or more consumers working together to consume records from a topic.

---

## Simple Example

Topic:

```text
orders
```

Consumer Group:

```text
order-group
```

Contains:

```text
Consumer 1

Consumer 2

Consumer 3
```

---

## Visualization

```text
Consumer Group

+------------+
| Consumer 1 |
+------------+

+------------+
| Consumer 2 |
+------------+

+------------+
| Consumer 3 |
+------------+
```

---

## Group ID

Every Consumer Group has a unique identifier.

Example:

```java
props.put(
    "group.id",
    "order-group"
);
```

---

Consumers with the same:

```text
group.id
```

belong to the same Consumer Group.

---

# Example

Consumer A:

```java
group.id=order-group
```

Consumer B:

```java
group.id=order-group
```

Same group.

---

Consumer C:

```java
group.id=payment-group
```

Different group.

---

# Important Rule

Within a Consumer Group:

```text
Each Partition
Can Be Assigned To
Only One Consumer
```

---

This prevents duplicate processing.

---

# Why Consumer Groups?

Without Consumer Groups Kafka would face serious scalability issues.

---

# Problem Without Consumer Groups

Topic:

```text
orders
```

Partitions:

```text
P0
P1
P2
P3
```

---

Single Consumer:

```text
Consumer 1
```

---

Processing:

```text
P0
P1
P2
P3
```

all handled by one consumer.

---

Problems:

```text
Slow Processing

CPU Bottleneck

Limited Throughput
```

---

# Solution: Consumer Groups

Add more consumers.

---

Example:

```text
Consumer 1

Consumer 2

Consumer 3

Consumer 4
```

---

Now processing occurs in parallel.

---

Benefits:

```text
Higher Throughput

Parallel Processing

Better Resource Utilization
```

---

# Consumer Group vs Independent Consumers

---

## Same Consumer Group

Topic:

```text
orders
```

Consumers:

```text
C1
C2
```

Group:

```text
order-group
```

---

Records distributed.

---

```text
Message A -> C1

Message B -> C2
```

---

No duplication.

---

## Different Consumer Groups

Topic:

```text
orders
```

Groups:

```text
order-group

analytics-group
```

---

Each group receives all records.

---

Example:

```text
Order Event
```

Consumed by:

```text
Order Service

Analytics Service
```

independently.

---

# Visualization

```text
orders Topic
       |
       |
+----------------+
| Order Group    |
+----------------+
       |
       V
Processes Orders

+----------------+
| Analytics Group|
+----------------+
       |
       V
Processes Analytics
```

---

# Group Coordinator

One of the most important internal Kafka concepts.

---

# What is Group Coordinator?

A special Kafka broker responsible for managing a Consumer Group.

---

Responsibilities:

```text
Group Membership

Heartbeats

Partition Assignment

Rebalancing

Offset Coordination
```

---

# Visualization

```text
Consumer 1
       |
Consumer 2
       |
Consumer 3
       |
       V
Group Coordinator
```

---

# How Coordinator Is Selected?

Kafka chooses a broker automatically.

---

Based on:

```text
group.id
```

---

Consumer does not choose coordinator.

Kafka handles it internally.

---

# Coordinator Responsibilities

---

## Register Consumers

Tracks active consumers.

---

## Monitor Heartbeats

Detect failures.

---

## Trigger Rebalancing

When group changes.

---

## Assign Partitions

Determines ownership.

---

## Manage Membership

Tracks joins and leaves.

---

# Consumer Group Architecture

Understanding the architecture is a common interview topic.

---

# High-Level Architecture

```text
Kafka Topic
      |
Partitions
      |
Consumer Group
      |
Consumers
```

---

# Example

Topic:

```text
orders
```

Partitions:

```text
P0
P1
P2
P3
```

---

Consumer Group:

```text
C1
C2
```

---

Assignment:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

# Architecture Diagram

```text
Orders Topic

P0
P1
P2
P3

      |
      V

Consumer Group

C1 -> P0,P1

C2 -> P2,P3
```

---

# Internal Components

---

## Consumers

Process records.

---

## Group Coordinator

Manages group.

---

## Assigned Partitions

Partitions owned by consumers.

---

## Heartbeat Mechanism

Tracks health.

---

## Offset Storage

Tracks progress.

---

# Partition Assignment

Partition assignment is the process of distributing partitions among consumers.

---

# Why Assignment Is Needed?

Topic:

```text
P0
P1
P2
P3
```

Consumers:

```text
C1
C2
```

Kafka must decide:

```text
Who Reads What?
```

---

# Assignment Rules

---

## One Partition

Can belong to:

```text
Only One Consumer
```

inside a group.

---

## One Consumer

Can own:

```text
Multiple Partitions
```

---

# Example

Partitions:

```text
P0
P1
P2
P3
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

# Example 2

Partitions:

```text
6
```

Consumers:

```text
3
```

Assignment:

```text
C1 -> P0,P1

C2 -> P2,P3

C3 -> P4,P5
```

---

# Partition Assignment Strategies

Kafka supports several strategies.

---

## Range Assignor

Assigns consecutive partitions.

---

Example:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

## Round Robin Assignor

Distributes evenly.

---

Example:

```text
C1 -> P0,P2

C2 -> P1,P3
```

---

## Sticky Assignor

Attempts to minimize partition movement during rebalances.

---

## Cooperative Sticky Assignor

Modern preferred approach.

Reduces downtime.

---

# Rebalancing

One of Kafka's most important concepts.

---

# What is Rebalancing?

Rebalancing is the process of redistributing partitions among consumers.

---

# Why Rebalancing Happens?

---

## New Consumer Joins

Before:

```text
C1 -> P0,P1,P2,P3
```

---

After:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

## Consumer Leaves

Before:

```text
C1 -> P0,P1

C2 -> P2,P3
```

---

C2 crashes.

---

After:

```text
C1 -> P0,P1,P2,P3
```

---

## Consumer Restart

Triggers rebalance.

---

## Topic Partition Change

New partitions added.

---

# Rebalance Workflow

```text
Consumer Change
       ↓
Pause Consumption
       ↓
Revoke Partitions
       ↓
Reassign Partitions
       ↓
Resume Consumption
```

---

# Drawback of Rebalancing

During rebalance:

```text
Consumption Stops
```

temporarily.

---

Can increase latency.

---

# Cooperative Rebalancing

Modern Kafka introduced:

```text
Incremental Cooperative Rebalancing
```

---

Benefits:

```text
Less Downtime

Faster Recovery

Reduced Partition Movement
```

---

# Scaling Consumers

Consumer Groups provide horizontal scalability.

---

# Example

Topic:

```text
orders
```

Partitions:

```text
4
```

---

Initially:

```text
Consumer 1
```

---

Processes:

```text
P0,P1,P2,P3
```

---

High traffic arrives.

---

Add consumers.

---

New setup:

```text
C1 -> P0

C2 -> P1

C3 -> P2

C4 -> P3
```

---

Result:

```text
Parallel Processing
```

---

# Throughput Improvement

Example:

Single Consumer:

```text
10,000 Messages/Sec
```

---

Four Consumers:

```text
40,000 Messages/Sec
```

Approximate scaling.

---

# Important Limitation

Consumer count cannot exceed useful partition count.

---

Example

Partitions:

```text
3
```

Consumers:

```text
5
```

---

Assignment:

```text
C1 -> P0

C2 -> P1

C3 -> P2

C4 -> Idle

C5 -> Idle
```

---

Extra consumers do no work.

---

# Scaling Formula

Maximum Parallelism:

```text
Number Of Partitions
```

---

Example:

```text
Partitions = 20
```

Maximum active consumers:

```text
20
```

---

# Load Distribution

Consumer Groups automatically distribute workload.

---

# Example

Topic:

```text
8 Partitions
```

Consumers:

```text
4
```

---

Assignment:

```text
C1 -> P0,P1

C2 -> P2,P3

C3 -> P4,P5

C4 -> P6,P7
```

---

# Benefits

---

## Balanced Processing

---

## Better CPU Usage

---

## Higher Throughput

---

## Reduced Bottlenecks

---

# Visualization

```text
Partitions

P0 P1 P2 P3 P4 P5 P6 P7

         ↓

Consumers

C1 C2 C3 C4
```

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

---

Consumer Group:

```text
order-processing-group
```

---

Consumers:

```text
6 Instances
```

---

Assignment:

```text
2 Partitions Per Consumer
```

---

Benefits:

```text
High Throughput

Fault Tolerance

Easy Scaling
```

---

# Consumer Group Best Practices

---

## Use Multiple Partitions

Enables scaling.

---

## Monitor Consumer Lag

---

## Use Cooperative Sticky Assignor

Modern recommendation.

---

## Avoid Frequent Rebalances

They reduce throughput.

---

## Match Consumer Count To Partition Count

---

## Use Meaningful Group IDs

Example:

```text
order-group

payment-group

analytics-group
```

---

# Interview Questions

## What is a Consumer Group?

A collection of consumers working together to consume data from a topic.

---

## Why are Consumer Groups needed?

To provide:

```text
Scalability

Parallel Processing

Fault Tolerance
```

---

## What is a Group Coordinator?

A Kafka broker responsible for managing consumer group membership, heartbeats, partition assignments, and rebalances.

---

## Can two consumers in the same group read the same partition?

```text
No
```

A partition can be assigned to only one consumer within a group.

---

## Can two different consumer groups read the same topic?

```text
Yes
```

Each group receives its own copy of the data stream.

---

## What is Rebalancing?

Redistribution of partitions among consumers when group membership changes.

---

## What triggers a rebalance?

- Consumer Join
- Consumer Leave
- Consumer Crash
- Topic Partition Change
- Consumer Restart

---

## What is the maximum number of active consumers?

Equal to:

```text
Number Of Partitions
```

---

## What happens if consumers exceed partitions?

Extra consumers remain idle.

---

## How does Kafka achieve horizontal scaling?

By distributing partitions across consumers within a Consumer Group.

---

# Summary

Consumer Groups are the foundation of Kafka scalability.

They provide:

```text
Parallel Processing
Load Balancing
Fault Tolerance
Horizontal Scaling
```

Core concepts include:

- Consumer Group
- Group Coordinator
- Consumer Group Architecture
- Partition Assignment
- Rebalancing
- Scaling Consumers
- Load Distribution

Complete Flow:

```text
Topic
   ↓
Partitions
   ↓
Consumer Group
   ↓
Partition Assignment
   ↓
Consumers
   ↓
Processing
```

Key rule:

```text
One Partition
      ↓
One Consumer
(Within Same Group)
```

Consumer Groups allow Kafka to process massive volumes of data efficiently while maintaining scalability, reliability, and fault tolerance.