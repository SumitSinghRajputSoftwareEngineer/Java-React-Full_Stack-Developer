# 10. Offsets

Offsets are one of the most important concepts in Kafka.

A common interview statement is:

> Kafka does not track which messages are consumed. Consumers track their progress using Offsets.

Offsets enable:

- Message Tracking
- Consumer Recovery
- Fault Tolerance
- Replay Processing
- Exactly Once and At Least Once Processing

Without offsets, Kafka consumers would not know where to continue reading data.

---

# What is Offset?

## Definition

An Offset is a unique sequential number assigned to every record within a partition.

It represents the position of a record inside a partition.

---

## Simple Example

Partition:

```text
Partition 0

Offset 0 → Order Created

Offset 1 → Payment Completed

Offset 2 → Shipment Started

Offset 3 → Delivered
```

---

## Visualization

```text
Partition 0

+----------+-------------------+
| Offset   | Message           |
+----------+-------------------+
| 0        | Order Created     |
| 1        | Payment Done      |
| 2        | Shipment Started  |
| 3        | Delivered         |
+----------+-------------------+
```

---

## Important Characteristics

### Unique Within a Partition

Offsets are unique only inside a partition.

---

Example:

```text
Partition 0

Offset 0
Offset 1
Offset 2
```

---

```text
Partition 1

Offset 0
Offset 1
Offset 2
```

Same offset numbers can exist in different partitions.

---

### Sequential

Offsets increase continuously.

```text
0
1
2
3
4
5
```

---

### Immutable

Once assigned:

```text
Offset Never Changes
```

---

### Automatically Generated

Kafka generates offsets automatically.

Developers cannot manually assign offsets.

---

# Why Offsets Are Needed?

Imagine Kafka without offsets.

---

Consumer reads:

```text
Order Created
Payment Done
Shipment Started
```

Consumer crashes.

---

Question:

```text
Where Should Consumer Restart?
```

Impossible to know.

---

Offsets solve this problem.

---

Example:

```text
Processed Until Offset 200
```

Consumer restarts from:

```text
Offset 201
```

---

# Offset Example

```text
Partition 0

Offset 100
Offset 101
Offset 102
Offset 103
Offset 104
```

Consumer processed:

```text
100
101
102
```

Stored offset:

```text
102
```

Next read:

```text
103
```

---

# Offset Management

Offset Management means:

```text
Tracking
Saving
Recovering
Updating Offsets
```

---

# Who Manages Offsets?

Not Kafka Broker.

Not Producer.

---

Offsets are managed by:

```text
Consumer Group
```

---

# Consumer Progress

```text
Partition 0

Offset 100
Offset 101
Offset 102
Offset 103
```

---

Consumer processed:

```text
100
101
102
```

Current offset:

```text
102
```

---

Consumer restart:

```text
Resume From 103
```

---

# Offset Lifecycle

```text
Read Record
      ↓
Process Record
      ↓
Commit Offset
      ↓
Store Offset
      ↓
Recover Later
```

---

# Offset Storage

Kafka stores committed offsets internally.

---

# Where Are Offsets Stored?

Internal Kafka Topic:

```text
__consumer_offsets
```

---

## Why Internal Topic?

Benefits:

```text
Fault Tolerance

Replication

Durability

Scalability
```

---

# Example

Consumer Group:

```text
order-group
```

Partition:

```text
Partition 0
```

Current position:

```text
Offset 1000
```

Stored in:

```text
__consumer_offsets
```

---

# Internal Storage Example

```text
Group = order-group

Topic = orders

Partition = 0

Offset = 1000
```

---

# Offset Recovery

Consumer crashes.

---

Restart:

```text
Read Stored Offset

Resume Processing
```

---

No messages lost.

---

# Auto Commit

Kafka can automatically commit offsets.

---

# What is Auto Commit?

Kafka periodically commits offsets automatically.

---

Configuration:

```properties
enable.auto.commit=true
```

---

Default:

```properties
enable.auto.commit=true
```

---

# Auto Commit Flow

```text
Poll Records
      ↓
Process Records
      ↓
Kafka Commits Automatically
```

---

# Commit Interval

Configuration:

```properties
auto.commit.interval.ms=5000
```

Default:

```text
5 Seconds
```

---

# Example

Consumer reads:

```text
Offset 100
Offset 101
Offset 102
```

---

After 5 seconds:

```text
Commit Offset 102
```

---

# Advantages

### Easy to Use

Minimal code.

---

### Fast Development

Good for simple applications.

---

# Disadvantages

Major interview topic.

---

## Data Loss Scenario

```text
Read Message
      ↓
Auto Commit Happens
      ↓
Application Crashes
```

Message may never be processed.

---

## Duplicate Processing

Possible during failures.

---

# Auto Commit Example

```java
props.put(
    "enable.auto.commit",
    "true"
);
```

---

# Manual Commit

Most production systems use manual commit.

---

# What is Manual Commit?

Application explicitly decides:

```text
When Offset Should Be Committed
```

---

Configuration:

```properties
enable.auto.commit=false
```

---

# Manual Commit Flow

```text
Read Message
      ↓
Process Message
      ↓
Successful?
      ↓
Commit Offset
```

---

# Advantages

### Better Reliability

Commit only after processing.

---

### Greater Control

Application decides commit timing.

---

### Reduced Data Loss

Safer than auto commit.

---

# Manual Commit Methods

---

## commitSync()

Synchronous commit.

---

Example:

```java
consumer.commitSync();
```

---

Flow:

```text
Commit
   ↓
Wait For Success
```

---

Advantages:

```text
Reliable
```

---

Disadvantages:

```text
Slower
```

---

## commitAsync()

Asynchronous commit.

---

Example:

```java
consumer.commitAsync();
```

---

Flow:

```text
Send Commit
      ↓
Continue Processing
```

---

Advantages:

```text
Faster
```

---

Disadvantages:

```text
Possible Commit Failure
```

---

# Sync vs Async Commit

| Feature | commitSync() | commitAsync() |
|----------|-------------|---------------|
| Speed | Slower | Faster |
| Reliability | Higher | Lower |
| Blocking | Yes | No |
| Production Usage | Common | Common for High Throughput |

---

# Offset Reset

Offset reset determines where a consumer starts reading if no committed offset exists.

---

# Configuration

```properties
auto.offset.reset
```

---

Possible Values:

```text
earliest

latest

none
```

---

# earliest

Read from beginning.

---

Example

Partition:

```text
0
1
2
3
4
5
```

Consumer starts:

```text
Offset 0
```

---

Configuration:

```properties
auto.offset.reset=earliest
```

---

# latest

Read only new messages.

---

Existing:

```text
0
1
2
3
4
5
```

Consumer starts:

```text
Offset 6
```

---

Configuration:

```properties
auto.offset.reset=latest
```

---

# none

No offset found:

```text
Throw Exception
```

---

Configuration:

```properties
auto.offset.reset=none
```

---

# Interview Question

What happens when a new consumer group starts?

Depends on:

```properties
auto.offset.reset
```

---

# Offset Retention

Committed offsets are not stored forever.

---

# What is Offset Retention?

How long Kafka keeps committed offsets.

---

Configuration:

```properties
offsets.retention.minutes
```

---

Default:

```text
10080 Minutes
```

Equivalent:

```text
7 Days
```

---

# Example

Consumer Group inactive:

```text
8 Days
```

Kafka removes stored offsets.

---

When consumer restarts:

```text
Offset Not Found
```

Then:

```properties
auto.offset.reset
```

determines behavior.

---

# Offset Retention Flow

```text
Commit Offset
      ↓
Consumer Inactive
      ↓
Retention Expires
      ↓
Offset Deleted
```

---

# Offset Tracking

Offset tracking means monitoring consumer progress.

---

# Why Track Offsets?

To know:

```text
How Much Data Processed
How Much Data Remaining
Consumer Health
Consumer Lag
```

---

# Consumer Position

Example:

```text
Current Offset = 500
```

---

# Log End Offset

Latest record:

```text
Offset = 1000
```

---

# Consumer Lag

Formula:

```text
Lag =
Latest Offset
-
Current Offset
```

---

Example:

```text
1000 - 500
```

Result:

```text
Lag = 500
```

---

# Visualization

```text
Partition

Current Offset = 500

Latest Offset = 1000

Lag = 500
```

---

# Why Lag Matters?

Large lag indicates:

```text
Consumer Slow
```

---

Potential reasons:

```text
Heavy Processing

Insufficient Consumers

Network Issues

Broker Issues
```

---

# Monitoring Consumer Lag

---

## Command Line

```bash
kafka-consumer-groups.sh \
--describe \
--group order-group \
--bootstrap-server localhost:9092
```

---

Example Output

```text
CURRENT-OFFSET : 500

LOG-END-OFFSET : 1000

LAG : 500
```

---

# Offset Commit Strategies

---

## Strategy 1: Auto Commit

```text
Easy
Less Reliable
```

---

## Strategy 2: Commit Per Message

```text
Reliable
Slower
```

---

## Strategy 3: Commit Per Batch

```text
Most Common
Balanced Approach
```

---

## Strategy 4: Commit After Business Transaction

```text
High Reliability
Production Grade
```

---

# Real-World Example

Order Processing System

---

Messages:

```text
Order 101

Order 102

Order 103
```

---

Flow:

```text
Read Order 101
      ↓
Save To Database
      ↓
Success
      ↓
Commit Offset
```

---

If application crashes before commit:

```text
Order Reprocessed
```

This provides:

```text
At-Least-Once Delivery
```

---

# Common Offset Problems

---

# Problem 1: Duplicate Processing

Cause:

```text
Crash Before Commit
```

---

Result:

```text
Same Message Processed Again
```

---

# Problem 2: Message Loss

Cause:

```text
Commit Before Processing
```

---

Result:

```text
Message Skipped
```

---

# Problem 3: Consumer Lag

Cause:

```text
Consumer Too Slow
```

---

# Problem 4: Expired Offsets

Cause:

```text
Retention Expired
```

---

# Best Practices

---

## Use Manual Commit in Production

```properties
enable.auto.commit=false
```

---

## Commit After Successful Processing

---

## Monitor Consumer Lag

---

## Understand auto.offset.reset

---

## Use Batch Commit for High Throughput

---

## Ensure Idempotent Processing

Handle duplicates safely.

---

# Interview Questions

## What is an Offset?

A unique sequential identifier representing a record's position within a partition.

---

## Are Offsets Unique Across the Entire Topic?

```text
No
```

They are unique only within a partition.

---

## Where are committed offsets stored?

```text
__consumer_offsets
```

internal Kafka topic.

---

## What is Auto Commit?

Kafka automatically commits offsets at configured intervals.

---

## What is Manual Commit?

Application explicitly commits offsets after successful processing.

---

## What is the difference between commitSync() and commitAsync()?

| commitSync() | commitAsync() |
|-------------|--------------|
| Blocking | Non-Blocking |
| More Reliable | Faster |
| Waits for Response | Doesn't Wait |

---

## What does auto.offset.reset do?

Determines where a consumer starts when no committed offset exists.

Values:

```text
earliest
latest
none
```

---

## What is Consumer Lag?

Difference between:

```text
Latest Offset
-
Current Offset
```

---

## Why is Consumer Lag Important?

It indicates whether consumers are keeping up with incoming data.

---

# Summary

Offsets are Kafka's mechanism for tracking consumer progress.

They provide:

```text
Recovery
Replay
Reliability
Fault Tolerance
Progress Tracking
```

Key concepts include:

- Offset Management
- Offset Storage
- Auto Commit
- Manual Commit
- Offset Reset
- Offset Retention
- Offset Tracking
- Consumer Lag

Core flow:

```text
Read Record
      ↓
Process Record
      ↓
Commit Offset
      ↓
Store Offset
      ↓
Resume Later
```

Understanding offsets is essential before learning:

- Consumers
- Consumer Groups
- Delivery Semantics
- Transactions
- Exactly-Once Processing
- Kafka Streams

because offsets are the foundation of reliable message consumption in Kafka.