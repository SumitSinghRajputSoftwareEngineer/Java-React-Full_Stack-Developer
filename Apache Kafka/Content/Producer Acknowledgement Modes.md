
Producer Acknowledgements (ACKs) are one of the most important Kafka concepts because they directly affect:

- Reliability
- Durability
- Throughput
- Latency
- Data Loss Risk

A very common Kafka interview question is:

> What is the difference between ACK=0, ACK=1, and ACK=all?

Understanding ACK modes is essential for designing reliable Kafka systems.

---

# What is an Acknowledgement (ACK)?

## Definition

An Acknowledgement (ACK) is a response sent by Kafka Broker to the Producer indicating whether a record has been successfully received and stored.

---

## Basic Flow

```text
Producer
    |
Send Record
    |
Kafka Broker
    |
ACK Response
    |
Producer
```

---

## Why ACKs Matter?

Without acknowledgements:

```text
Producer Cannot Know
Whether Data Was Stored
```

---

Example:

```text
Producer
    |
Send Order Event
    |
Network Failure
```

Questions:

```text
Was Message Stored?
Was Message Lost?
Should Producer Retry?
```

ACKs answer these questions.

---

# Kafka Replication Reminder

Before understanding ACK modes, remember Kafka replication.

---

Example:

Replication Factor = 3

```text
Partition 0

Leader  -> Broker 1

Follower -> Broker 2

Follower -> Broker 3
```

---

Producer always writes to:

```text
Leader Partition
```

---

Followers replicate data from leader.

---

# ACK = 0

---

## Definition

Producer does NOT wait for any acknowledgement from Kafka.

---

Configuration:

```java
props.put("acks","0");
```

---

## Workflow

```text
Producer
     |
Send Record
     |
Continue Immediately
```

---

Broker response:

```text
No ACK Returned
```

---

## Visualization

```text
Producer
     |
Send
     |
Broker

(No ACK)
```

---

## Timeline

```text
Producer Sends Message
        |
Immediately Continues
```

---

# Example

Producer sends:

```text
Order Created
```

---

Possible Scenarios

### Success

```text
Message Stored Successfully
```

Producer doesn't know.

---

### Failure

```text
Broker Down
Network Failure
Disk Failure
```

Producer still doesn't know.

---

# Advantages

---

## Lowest Latency

No waiting.

---

## Maximum Throughput

Producer continues immediately.

---

## Minimal Network Overhead

No response processing.

---

# Disadvantages

---

## Highest Risk of Data Loss

Producer never verifies delivery.

---

## No Reliability Guarantee

Message may disappear silently.

---

## No Delivery Confirmation

Producer has zero visibility.

---

# Typical Use Cases

Rare in production.

Possible scenarios:

```text
Metrics Collection

Log Aggregation

Monitoring Events

Temporary Telemetry Data
```

where losing some messages is acceptable.

---

# ACK = 1

---

## Definition

Producer waits for acknowledgement from the Leader Partition only.

---

Configuration:

```java
props.put("acks","1");
```

---

## Workflow

```text
Producer
     |
Send Record
     |
Leader Stores Record
     |
ACK Returned
```

---

## Visualization

```text
Producer
     |
Send
     |
Leader Broker
     |
ACK
     |
Producer
```

---

Followers may still be replicating.

---

# Example

Replication Factor = 3

```text
Leader -> Broker1

Follower -> Broker2

Follower -> Broker3
```

---

Flow

```text
Producer
     |
Leader Stores Record
     |
ACK Sent
     |
Followers Replicate Later
```

---

# Failure Scenario

This is a favorite interview question.

---

## Scenario

```text
Producer Sends Record
       |
Leader Stores Record
       |
ACK Returned
       |
Leader Crashes
       |
Followers Not Updated Yet
```

---

Result:

```text
Message Lost
```

because followers never received it.

---

# Advantages

---

## Good Performance

Only waits for leader.

---

## Reasonable Reliability

Much better than ACK=0.

---

## Common Default Choice

Historically used frequently.

---

# Disadvantages

---

## Possible Data Loss

Leader failure can cause loss.

---

## Replication Not Guaranteed

Followers may not yet have data.

---

# Typical Use Cases

```text
Web Applications

Moderate Reliability Systems

General Business Events
```

where small data loss risk is acceptable.

---

# ACK = all (-1)

---

## Definition

Producer waits until the leader and all required replicas acknowledge the record.

---

Configuration

```java
props.put("acks","all");
```

or

```java
props.put("acks","-1");
```

Both are identical.

---

## Workflow

```text
Producer
     |
Leader Receives Record
     |
Followers Replicate Record
     |
ACK Returned
```

---

## Visualization

```text
Producer
     |
Send
     |
Leader
     |
Replicate
     |
Followers
     |
ACK
     |
Producer
```

---

# Example

Replication Factor = 3

```text
Broker1 -> Leader

Broker2 -> Follower

Broker3 -> Follower
```

---

Flow

```text
Producer
      |
Leader Writes Record
      |
Follower Replication
      |
ISR Confirms
      |
ACK Returned
```

---

# What Does "all" Really Mean?

A common misunderstanding.

---

It does NOT mean:

```text
Every Replica In Cluster
```

---

It means:

```text
All ISR Replicas
```

---

# ISR Reminder

ISR = In-Sync Replica

Example:

```text
Broker1 -> Leader

Broker2 -> ISR

Broker3 -> ISR
```

---

ACK returned after ISR replication.

---

# Failure Scenario

---

Producer sends:

```text
Order Event
```

---

Leader receives.

Followers replicate.

ACK returned.

---

Leader crashes.

---

Result:

```text
No Data Loss
```

because followers already contain the record.

---

# Advantages

---

## Highest Reliability

Strongest durability.

---

## Lowest Data Loss Risk

Preferred for critical systems.

---

## Recommended for Production

Especially for business transactions.

---

## Required for Exactly-Once Processing

Used with idempotent producers and transactions.

---

# Disadvantages

---

## Higher Latency

Waits for replication.

---

## Lower Throughput

More coordination required.

---

# min.insync.replicas Relationship

Important interview topic.

---

Configuration:

```properties
min.insync.replicas=2
```

---

Replication Factor:

```text
3
```

---

ACK=all requires:

```text
At Least 2 ISR Replicas
```

to acknowledge.

---

# Scenario

ISR:

```text
Broker1
Broker2
```

Alive.

---

Broker3:

```text
Down
```

---

ACK still succeeds.

---

# Another Scenario

Only:

```text
Broker1
```

Alive.

---

Required:

```text
min.insync.replicas=2
```

---

Result:

```text
Write Fails
```

to protect durability.

---

# Reliability Comparison

---

# Reliability Ranking

```text
ACK=0
   ↓
ACK=1
   ↓
ACK=all
```

---

## ACK=0

Reliability:

```text
Very Low
```

---

Data Loss Risk:

```text
Very High
```

---

## ACK=1

Reliability:

```text
Medium
```

---

Data Loss Risk:

```text
Moderate
```

---

## ACK=all

Reliability:

```text
Very High
```

---

Data Loss Risk:

```text
Minimal
```

---

# Reliability Table

| ACK Mode | Leader Confirmation | Replica Confirmation | Data Loss Risk |
|-----------|--------------------|----------------------|---------------|
| 0 | No | No | Very High |
| 1 | Yes | No | Medium |
| all | Yes | Yes (ISR) | Lowest |

---

# Performance Comparison

Performance behaves opposite to reliability.

---

# Performance Ranking

```text
ACK=0
   ↓
ACK=1
   ↓
ACK=all
```

---

## ACK=0

Performance:

```text
Highest
```

---

Latency:

```text
Lowest
```

---

## ACK=1

Performance:

```text
Good
```

---

Latency:

```text
Medium
```

---

## ACK=all

Performance:

```text
Lower
```

---

Latency:

```text
Highest
```

---

# Performance Table

| ACK Mode | Latency | Throughput |
|-----------|----------|------------|
| 0 | Lowest | Highest |
| 1 | Medium | High |
| all | Highest | Lower |

---

# Reliability vs Performance Tradeoff

---

## ACK=0

```text
Fastest
Least Reliable
```

---

## ACK=1

```text
Balanced
```

---

## ACK=all

```text
Most Reliable
Slowest
```

---

# Real-World Examples

---

# Example 1: System Metrics

Events:

```text
CPU Usage
Memory Usage
Health Checks
```

---

Recommended:

```text
ACK=0
```

Some data loss acceptable.

---

# Example 2: Website Activity Tracking

Events:

```text
Page Views
User Clicks
Ad Impressions
```

---

Recommended:

```text
ACK=1
```

Balance between speed and reliability.

---

# Example 3: Banking Transactions

Events:

```text
Money Transfer

Account Debit

Account Credit
```

---

Recommended:

```text
ACK=all
```

Data loss unacceptable.

---

# Example 4: E-commerce Orders

Events:

```text
Order Created

Payment Completed

Refund Issued
```

---

Recommended:

```text
ACK=all
```

Business-critical events.

---

# Best Practices

---

# Use ACK=all for Production

Most enterprise Kafka systems use:

```java
props.put("acks","all");
```

---

# Combine ACK=all with Replication

Recommended:

```text
Replication Factor = 3
```

---

# Configure min.insync.replicas

Example:

```properties
min.insync.replicas=2
```

---

Provides stronger durability.

---

# Use Idempotent Producers

```java
enable.idempotence=true
```

---

Prevents duplicate messages during retries.

---

# Avoid ACK=0 for Critical Data

Never use for:

```text
Payments

Orders

Transactions

Financial Data
```

---

# Monitor ISR Health

ACK=all depends on healthy ISR replicas.

---

# Production Recommendation

```java
acks=all

enable.idempotence=true

retries=Integer.MAX_VALUE

min.insync.replicas=2

replication.factor=3
```

---

# Interview Questions

## What is ACK in Kafka?

An acknowledgement sent by Kafka broker confirming receipt and storage of a record.

---

## What does ACK=0 mean?

Producer does not wait for any acknowledgement.

---

## What does ACK=1 mean?

Producer waits for acknowledgement from the leader partition only.

---

## What does ACK=all mean?

Producer waits for acknowledgement from all ISR replicas.

---

## Which ACK mode is the safest?

```text
ACK=all
```

---

## Which ACK mode is the fastest?

```text
ACK=0
```

---

## Can ACK=1 lose data?

```text
Yes
```

If the leader crashes before followers replicate the data.

---

## Why is ACK=all more reliable?

Because records are replicated to ISR replicas before acknowledgement is returned.

---

## What is the relationship between ACK=all and min.insync.replicas?

ACK=all succeeds only if the required number of ISR replicas defined by `min.insync.replicas` successfully acknowledge the write.

---

# Summary

Producer Acknowledgement Modes determine the balance between:

```text
Reliability
Durability
Latency
Throughput
```

Kafka supports three ACK modes:

| ACK Mode | Reliability | Performance |
|-----------|-------------|-------------|
| ACK=0 | Lowest | Highest |
| ACK=1 | Medium | High |
| ACK=all (-1) | Highest | Lower |

General rule:

```text
ACK=0  -> Speed First
ACK=1  -> Balanced
ACK=all -> Reliability First
```

For most production systems:

```java
acks=all
enable.idempotence=true
replication.factor=3
min.insync.replicas=2
```

is considered the recommended configuration because it provides strong durability and minimizes the risk of data loss.