# 9. Partitions

Partitions are one of the most important concepts in Kafka.

In fact, Kafka's:

- Scalability
- Parallel Processing
- High Throughput
- Fault Tolerance

are all built around partitions.

A common interview statement is:

> Kafka scales through partitions.

If you understand partitions thoroughly, topics, producers, consumers, consumer groups, replication, and performance tuning become much easier.

---

# What is a Partition?

## Definition

A Partition is the fundamental storage unit of a Kafka Topic.

A Topic is logically divided into one or more partitions.

---

## Topic Without Partition

```text
Orders Topic

+----------------------+
| Order 1              |
| Order 2              |
| Order 3              |
| Order 4              |
+----------------------+
```

Everything stored together.

---

## Topic With Partitions

```text
Orders Topic

+-------------+
| Partition 0 |
+-------------+

+-------------+
| Partition 1 |
+-------------+

+-------------+
| Partition 2 |
+-------------+
```

Topic data is distributed across partitions.

---

## Example

Topic:

```text
orders
```

Partitions:

```text
Partition 0

Partition 1

Partition 2
```

---

## Internal Structure

Each partition is an append-only log.

```text
Partition 0

Offset 0
Offset 1
Offset 2
Offset 3
Offset 4
```

Records are continuously appended.

---

## Visualization

```text
Orders Topic

Partition 0
----------------
Offset 0
Offset 1
Offset 2

Partition 1
----------------
Offset 0
Offset 1

Partition 2
----------------
Offset 0
Offset 1
Offset 2
Offset 3
```

---

# Why Partitions are Needed?

This is one of the most frequently asked interview questions.

---

## Problem Without Partitions

Imagine:

```text
Orders Topic

100 Million Events Per Day
```

Stored in a single location.

Problems:

```text
CPU Bottleneck

Disk Bottleneck

Network Bottleneck

Consumer Bottleneck
```

---

## Solution

Split the topic into multiple partitions.

---

### Before

```text
Orders Topic

Single Storage Unit
```

---

### After

```text
Orders Topic

Partition 0

Partition 1

Partition 2

Partition 3
```

Load gets distributed.

---

# Benefits of Partitions

---

## 1. Scalability

Kafka can spread partitions across multiple brokers.

---

### Example

```text
Broker 1
Partition 0

Broker 2
Partition 1

Broker 3
Partition 2
```

Storage scales horizontally.

---

## 2. Parallel Processing

Multiple consumers can read simultaneously.

---

Example:

```text
Consumer 1 → Partition 0

Consumer 2 → Partition 1

Consumer 3 → Partition 2
```

---

## 3. Increased Throughput

More partitions:

```text
More Producers

More Consumers

More Throughput
```

---

## 4. Fault Tolerance

Partitions are replicated independently.

---

# Partition Distribution

Kafka distributes partitions across brokers.

---

# Example

Topic:

```text
Orders
```

Partitions:

```text
P0
P1
P2
P3
P4
P5
```

---

Cluster:

```text
Broker 1
Broker 2
Broker 3
```

---

Distribution:

```text
Broker 1

P0
P3
```

```text
Broker 2

P1
P4
```

```text
Broker 3

P2
P5
```

---

## Benefits

### Load Balancing

No broker stores all data.

---

### Resource Utilization

CPU, memory, and disk are distributed.

---

### High Availability

Failure impacts only some partitions.

---

# Partition Scalability

Partitioning is Kafka's primary scaling mechanism.

---

# Horizontal Scaling

Instead of:

```text
Bigger Server
```

Kafka prefers:

```text
More Brokers
```

---

## Example

Initial Setup

```text
Broker 1

Partition 0
Partition 1
```

---

Increased Load

Add Broker 2

```text
Broker 1
Partition 0

Broker 2
Partition 1
```

---

Further Growth

```text
Broker 1 -> P0

Broker 2 -> P1

Broker 3 -> P2

Broker 4 -> P3
```

---

# Throughput Scaling

Example:

One partition handles:

```text
10,000 Messages/Sec
```

---

Four partitions:

```text
40,000 Messages/Sec
```

Approximate throughput increases.

---

# Important Interview Point

More partitions generally increase throughput, but not infinitely.

Too many partitions cause:

```text
Memory Overhead

Metadata Overhead

Leader Election Cost

Rebalancing Cost
```

---

# Partition Ordering

One of the most important Kafka concepts.

---

# Ordering Guarantee

Kafka guarantees ordering:

```text
Within A Partition
```

Only.

---

## Example

Partition 0

```text
Offset 0 → Order Created

Offset 1 → Payment Done

Offset 2 → Shipment Started

Offset 3 → Delivered
```

Order preserved.

---

# Multiple Partitions

```text
Partition 0

Order 1
Order 2
```

```text
Partition 1

Order 3
Order 4
```

Global ordering is NOT guaranteed.

---

# Example

Producer sends:

```text
Order1
Order2
Order3
Order4
```

Distribution:

```text
P0 → Order1
P0 → Order3

P1 → Order2
P1 → Order4
```

Consumer may receive:

```text
Order1
Order2
Order4
Order3
```

Across partitions.

---

# How to Preserve Ordering?

Use the same key.

Example:

```text
Customer ID = 101
```

All records with key:

```text
101
```

go to the same partition.

---

Result:

```text
Customer Event 1

Customer Event 2

Customer Event 3
```

Order maintained.

---

# Partition Assignment

Partition assignment determines where records are stored.

---

# Producer Side Assignment

When a producer sends a record:

```java
producer.send(record)
```

Kafka decides:

```text
Which Partition?
```

---

# Assignment Approaches

---

## 1. Explicit Partition

Developer specifies partition.

```java
new ProducerRecord<>(
    "orders",
    2,
    key,
    value
);
```

---

Record always goes to:

```text
Partition 2
```

---

## 2. Key-Based Partitioning

Most common approach.

---

Example:

```java
Key = CustomerId
```

Kafka computes:

```text
hash(key) % partitionCount
```

---

Example

```text
hash(101) % 3 = 2
```

Partition:

```text
P2
```

---

Benefits:

```text
Consistent Routing

Ordering Preservation
```

---

## 3. Round Robin

No key provided.

---

Kafka distributes evenly.

Example:

```text
Message 1 → P0

Message 2 → P1

Message 3 → P2

Message 4 → P0
```

---

Benefits:

```text
Load Balancing
```

---

# Consumer Partition Assignment

Partitions must also be assigned to consumers.

---

Example

Topic:

```text
P0
P1
P2
P3
```

Consumer Group:

```text
C1
C2
```

Assignment:

```text
C1 → P0, P1

C2 → P2, P3
```

---

# Assignment Rules

---

## One Partition

Can only be consumed by:

```text
One Consumer
```

within the same consumer group.

---

## One Consumer

Can consume:

```text
Multiple Partitions
```

---

# Rebalancing

Rebalancing is a critical Kafka concept.

---

# What is Rebalancing?

Rebalancing is the process of redistributing partitions among consumers.

---

## Why Rebalancing Happens?

---

### New Consumer Joins

Before:

```text
C1 → P0,P1,P2,P3
```

---

After:

```text
C1 → P0,P1

C2 → P2,P3
```

---

### Consumer Leaves

Before:

```text
C1 → P0,P1

C2 → P2,P3
```

---

C2 crashes.

After:

```text
C1 → P0,P1,P2,P3
```

---

### Topic Partitions Change

New partitions added.

---

### Consumer Restart

Triggers rebalance.

---

# Rebalancing Process

```text
Consumer Change
      ↓
Stop Consumption
      ↓
Partition Reassignment
      ↓
Resume Consumption
```

---

# Problem with Rebalancing

During rebalance:

```text
Consumption Paused
```

Temporary delay occurs.

---

# Cooperative Rebalancing

Modern Kafka supports:

```text
Incremental Cooperative Rebalancing
```

Benefits:

```text
Less Downtime

Faster Recovery

Reduced Disruption
```

---

# Partition Strategy

Choosing the right partition strategy is crucial.

---

# Strategy 1: Round Robin

---

## Description

Messages distributed evenly.

---

Example

```text
Msg1 → P0

Msg2 → P1

Msg3 → P2

Msg4 → P0
```

---

## Advantages

```text
Balanced Load
```

---

## Disadvantages

```text
Ordering Lost
```

---

# Strategy 2: Key-Based Partitioning

---

## Description

Same key always goes to same partition.

---

Example

```text
Customer101 → P1

Customer101 → P1

Customer101 → P1
```

---

## Advantages

```text
Ordering Preserved
```

---

## Disadvantages

Possible partition hotspots.

---

# Strategy 3: Custom Partitioner

---

## Description

Developer creates custom logic.

---

Example

```java
class CustomPartitioner
implements Partitioner
```

---

Routing logic:

```text
VIP Customers → P0

Regular Customers → P1,P2,P3
```

---

# Partition Count Selection Strategy

One of the most common architecture questions.

---

## Too Few Partitions

Problems:

```text
Limited Throughput

Limited Parallelism
```

---

## Too Many Partitions

Problems:

```text
Memory Usage

Metadata Overhead

Long Rebalancing

Leader Election Cost
```

---

## General Guidelines

Small Applications:

```text
3–6 Partitions
```

---

Medium Applications:

```text
12–50 Partitions
```

---

Large Systems:

```text
100+ Partitions
```

Depends on workload.

---

# Real-World Example

E-commerce System

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

Key:

```text
CustomerId
```

Routing:

```text
Customer101 → P1

Customer102 → P3

Customer103 → P0
```

Benefits:

```text
Ordering Preserved

Load Distributed

Scalable Consumers
```

---

# Interview Questions

## What is a Partition?

A partition is the fundamental storage unit of a Kafka topic.

---

## Why are partitions needed?

To achieve:

- Scalability
- Parallel Processing
- High Throughput
- Fault Tolerance

---

## Does Kafka guarantee ordering?

Yes, but only within a partition.

---

## Can one partition be consumed by multiple consumers in the same consumer group?

```text
No
```

One partition can be assigned to only one consumer within a consumer group.

---

## What is Rebalancing?

Redistribution of partitions among consumers when group membership changes.

---

## How does Kafka decide a partition for a record?

Using:

- Explicit Partition
- Key-Based Hashing
- Round Robin
- Custom Partitioner

---

## What happens if a consumer group has more consumers than partitions?

Some consumers remain idle.

Example:

```text
Partitions = 3

Consumers = 5
```

Assignment:

```text
C1 → P0

C2 → P1

C3 → P2

C4 → Idle

C5 → Idle
```

---

## What is the best partitioning strategy?

Most production systems use:

```text
Key-Based Partitioning
```

because it balances scalability and ordering guarantees.

---

# Summary

Partitions are the foundation of Kafka scalability.

They provide:

```text
Parallel Processing
High Throughput
Load Distribution
Fault Tolerance
Horizontal Scaling
```

Key concepts include:

- Partition Distribution
- Partition Ordering
- Partition Assignment
- Rebalancing
- Partition Strategies

A topic is merely a logical container, but partitions are where Kafka actually stores and processes data.

```text
Topic
   |
Partitions
   |
Offsets
   |
Records
```

Understanding partitions thoroughly is essential before moving to:

- Offsets
- Producers
- Consumers
- Consumer Groups
- Replication
- Kafka Performance Tuning