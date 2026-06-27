# 13. Producer Partitioning Strategies

Partitioning is one of the most important concepts in Kafka Producers.

Whenever a Producer sends a record to a Topic, Kafka must answer:

```text
Which Partition Should Receive This Record?
```

The logic used to determine the destination partition is called:

```text
Partitioning Strategy
```

Partitioning directly affects:

- Ordering
- Throughput
- Load Balancing
- Scalability
- Consumer Parallelism
- Performance

A poor partitioning strategy can create:

```text
Hot Partitions
Uneven Load
Poor Throughput
Consumer Bottlenecks
```

while a good strategy ensures:

```text
Balanced Distribution
Ordering Guarantees
Efficient Processing
```

---

# Why Partitioning Matters?

Consider:

```text
Orders Topic

Partition 0
Partition 1
Partition 2
```

Producer sends:

```text
Order1
Order2
Order3
Order4
```

Kafka must decide:

```text
Where Should Each Record Go?
```

---

# Without Partitioning Logic

Problems:

```text
Uneven Load

Lost Ordering

Poor Scalability
```

---

# With Partitioning Logic

Benefits:

```text
Balanced Traffic

Consistent Routing

Better Performance
```

---

# Partition Selection Overview

Kafka supports multiple partitioning strategies:

```text
Round Robin

Key-Based

Sticky

Custom
```

---

# Round Robin Partitioning

---

## What is Round Robin Partitioning?

Round Robin distributes records sequentially across partitions.

---

## Goal

Provide:

```text
Even Distribution
```

across all partitions.

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
```

---

Messages:

```text
M1
M2
M3
M4
M5
M6
```

---

Distribution:

```text
M1 → P0

M2 → P1

M3 → P2

M4 → P0

M5 → P1

M6 → P2
```

---

# Visualization

```text
Message 1 ---> P0

Message 2 ---> P1

Message 3 ---> P2

Message 4 ---> P0

Message 5 ---> P1

Message 6 ---> P2
```

---

# Benefits

---

## Excellent Load Balancing

Records spread evenly.

---

Example:

```text
1000 Messages

≈ 333 Per Partition
```

---

## Better Resource Utilization

All brokers participate.

---

## High Throughput

Workload distributed equally.

---

# Drawbacks

---

## No Ordering Across Related Records

Example:

```text
Customer 101 Event1 → P0

Customer 101 Event2 → P1

Customer 101 Event3 → P2
```

Ordering lost.

---

## Not Suitable for Entity-Based Processing

Entities like:

```text
Customer

Account

Order

User
```

may become scattered.

---

# Typical Use Cases

Suitable when:

```text
Ordering Not Important
```

Examples:

```text
Metrics Collection

System Logs

Telemetry Data

Monitoring Events
```

---

# Key-Based Partitioning

Most commonly used strategy in production.

---

# What is Key-Based Partitioning?

Records with the same key always go to the same partition.

---

# Goal

Provide:

```text
Ordering
+
Predictable Routing
```

---

# How It Works?

Kafka calculates:

```text
hash(key) % numberOfPartitions
```

---

# Example

Partitions:

```text
P0
P1
P2
```

---

Key:

```text
CustomerId = 101
```

---

Calculation:

```text
hash(101) % 3
```

Result:

```text
Partition 2
```

---

All future records:

```text
Customer 101
```

go to:

```text
Partition 2
```

---

# Visualization

```text
Customer 101
      |
      V
Partition 2

Customer 101
      |
      V
Partition 2

Customer 101
      |
      V
Partition 2
```

---

# Ordering Guarantee

Since all records go to same partition:

```text
Event 1
Event 2
Event 3
Event 4
```

Order preserved.

---

# Example

Customer Events:

```text
Profile Created

Address Updated

Order Placed

Payment Completed
```

All routed to:

```text
Partition 1
```

---

Ordering maintained.

---

# Benefits

---

## Ordering Preservation

Kafka guarantees order within partition.

---

## Consistent Routing

Same key always goes to same partition.

---

## Consumer Simplicity

Consumer receives related events together.

---

## Widely Used

Most enterprise systems use this approach.

---

# Drawbacks

---

## Hot Partitions

Some keys may generate excessive traffic.

---

Example:

```text
Customer 999999
```

creates:

```text
Millions Of Events
```

---

All events go to one partition.

---

Result:

```text
Partition Hotspot
```

---

# Typical Use Cases

```text
Orders

Payments

Bank Accounts

Customers

Users

Inventory
```

---

# Sticky Partitioning

Introduced to improve producer throughput.

---

# What is Sticky Partitioning?

When records do NOT have keys, Kafka temporarily chooses one partition and keeps sending records to it until a batch is full.

Then it switches to another partition.

---

# Why Introduced?

Traditional round robin:

```text
M1 → P0

M2 → P1

M3 → P2
```

created many small batches.

---

Small batches:

```text
More Network Calls

Lower Throughput
```

---

Sticky partitioning improves batching.

---

# Example

Partitions:

```text
P0
P1
P2
```

---

Producer selects:

```text
P0
```

---

Records:

```text
M1 → P0

M2 → P0

M3 → P0

M4 → P0
```

---

Batch becomes full.

---

Switch:

```text
P1
```

---

Next records:

```text
M5 → P1

M6 → P1

M7 → P1
```

---

# Visualization

```text
Batch 1

P0
M1
M2
M3
M4

Batch 2

P1
M5
M6
M7
```

---

# Benefits

---

## Larger Batches

Better network utilization.

---

## Fewer Requests

Reduced overhead.

---

## Higher Throughput

Improved producer performance.

---

## Better Compression

Large batches compress better.

---

# Drawbacks

---

## Temporary Uneven Distribution

Short-term imbalance may occur.

---

However:

```text
Overall Distribution Remains Balanced
```

---

# Default Behavior

Modern Kafka Producer uses:

```text
Sticky Partitioner
```

for records without keys.

---

# Typical Use Cases

```text
Logs

Metrics

Telemetry

Analytics Events
```

where no key exists.

---

# Custom Partitioning

---

## What is Custom Partitioning?

Developer creates a custom algorithm to determine partitions.

---

Used when business requirements cannot be satisfied by default strategies.

---

# Example Requirement

VIP Customers:

```text
Partition 0
```

Regular Customers:

```text
Partition 1,2,3
```

---

Default Kafka partitioning cannot do this.

---

Custom partitioner required.

---

# Creating Custom Partitioner

Implement:

```java
org.apache.kafka.clients.producer.Partitioner
```

---

Example:

```java
public class CustomPartitioner
implements Partitioner {

}
```

---

# Main Method

```java
public int partition(...)
```

---

Kafka calls this method for every record.

---

# Example Logic

```java
if(customerType.equals("VIP"))
{
    return 0;
}
else
{
    return 1;
}
```

---

# Example Distribution

```text
VIP Customer
      |
      V
Partition 0

Regular Customer
      |
      V
Partition 1
```

---

# Benefits

---

## Business-Specific Routing

---

## Complete Control

---

## Advanced Optimization

---

# Drawbacks

---

## More Complexity

---

## Maintenance Overhead

---

## Risk of Load Imbalance

---

# Real-World Examples

---

## Geographic Routing

```text
India → P0

USA → P1

Europe → P2
```

---

## Priority Routing

```text
High Priority → P0

Low Priority → P1
```

---

## Customer Tier Routing

```text
Gold Customers → P0

Silver Customers → P1

Bronze Customers → P2
```

---

# Partition Selection Logic

Understanding Kafka's partition selection flow is important.

---

# Scenario 1: Explicit Partition Provided

Producer:

```java
new ProducerRecord<>(
    topic,
    2,
    key,
    value
)
```

---

Result:

```text
Always Partition 2
```

---

Highest priority.

---

# Scenario 2: Key Present

Producer:

```java
new ProducerRecord<>(
    topic,
    customerId,
    value
)
```

---

Kafka:

```text
hash(key)
      ↓
mod(partitionCount)
      ↓
Partition Selected
```

---

# Scenario 3: No Key

Kafka uses:

```text
Sticky Partitioner
```

(Modern Kafka)

---

# Complete Selection Logic

```text
Explicit Partition?
        |
       Yes
        |
Use Given Partition
        |
       End

        No
        |
Key Present?
        |
       Yes
        |
Hash(Key)
        |
Partition
        |
       End

        No
        |
Sticky Partitioner
        |
Partition
```

---

# Partitioning Strategy Comparison

| Strategy | Ordering | Load Balancing | Performance |
|-----------|-----------|----------------|-------------|
| Round Robin | No | Excellent | Good |
| Key-Based | Yes | Good | Good |
| Sticky | No | Good | Excellent |
| Custom | Depends | Depends | Depends |

---

# Real-World Strategy Selection

---

## Metrics System

Recommended:

```text
Sticky Partitioning
```

---

## Banking System

Recommended:

```text
Key-Based Partitioning
```

Key:

```text
AccountId
```

---

## E-Commerce

Recommended:

```text
Key-Based Partitioning
```

Key:

```text
CustomerId
```

or

```text
OrderId
```

---

## Log Collection

Recommended:

```text
Sticky Partitioning
```

---

## Business-Specific Routing

Recommended:

```text
Custom Partitioner
```

---

# Best Practices

---

## Prefer Key-Based Partitioning

For business events.

---

## Use Meaningful Keys

Examples:

```text
CustomerId

AccountId

OrderId

UserId
```

---

## Avoid Hot Partitions

Monitor key distribution.

---

## Use Sticky Partitioning

When no key exists.

---

## Keep Custom Partitioners Simple

Complex logic can create bottlenecks.

---

## Test Distribution

Always verify partition balance.

---

# Interview Questions

## What is partitioning in Kafka?

The process of selecting which partition receives a record.

---

## How does Kafka choose a partition?

Priority order:

```text
Explicit Partition
      ↓
Key-Based Hashing
      ↓
Sticky Partitioner
```

---

## What is Round Robin Partitioning?

Sequential distribution of records across partitions.

---

## What is Key-Based Partitioning?

Records with the same key always go to the same partition.

---

## How does Kafka calculate partition for a key?

```text
hash(key) % numberOfPartitions
```

---

## What is Sticky Partitioning?

Kafka temporarily sticks to one partition to build larger batches and improve throughput.

---

## Which partitioning strategy preserves ordering?

```text
Key-Based Partitioning
```

because all records with the same key go to the same partition.

---

## Why was Sticky Partitioning introduced?

To:

```text
Improve Batching

Reduce Network Calls

Increase Throughput
```

---

## When should Custom Partitioners be used?

When business-specific routing requirements cannot be handled by Kafka's default strategies.

---

# Summary

Producer Partitioning Strategies determine where records are stored inside a topic.

Kafka supports:

```text
Round Robin

Key-Based

Sticky

Custom
```

Selection flow:

```text
Explicit Partition
      ↓
Key Present?
      ↓
Hash(Key)
      ↓
Sticky Partitioner
```

Most production systems use:

```text
Key-Based Partitioning
```

because it provides:

```text
Ordering
Consistency
Predictable Routing
```

while modern Kafka automatically uses:

```text
Sticky Partitioning
```

for records without keys to maximize producer throughput and batching efficiency.