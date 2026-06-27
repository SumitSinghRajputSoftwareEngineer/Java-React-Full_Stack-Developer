
Message Delivery Semantics define the guarantees Kafka provides regarding how many times a message will be delivered from Producer to Consumer.

This is one of the most important Kafka topics because every distributed system must answer:

```text
Can Messages Be Lost?

Can Messages Be Duplicated?

Can Messages Be Delivered Exactly Once?
```

Kafka provides three delivery guarantees:

```text
At Most Once

At Least Once

Exactly Once
```

Each guarantee offers a different balance between:

```text
Reliability
Performance
Complexity
Cost
```

---

# Why Message Delivery Semantics Matter?

Imagine a banking application.

Producer sends:

```text
Transfer ₹10,000
```

---

Possible problems:

### Message Lost

```text
Money Not Transferred
```

---

### Message Delivered Twice

```text
Money Debited Twice
```

---

### Message Delivered Exactly Once

```text
Correct Transaction
```

---

Message Delivery Semantics determine which of these outcomes are possible.

---

# Message Delivery Lifecycle

```text
Producer
     ↓
Kafka Broker
     ↓
Consumer
```

---

Failures can occur at any point:

```text
Network Failure

Producer Failure

Broker Failure

Consumer Failure

Offset Commit Failure
```

---

Kafka semantics define how these failures are handled.

---

# At Most Once

---

# What is At Most Once?

At Most Once means:

```text
Message Delivered
Zero Or One Time
```

---

Possible outcomes:

```text
Delivered Once

OR

Lost
```

---

Duplicates are not possible.

---

# Guarantee

```text
No Duplicates

Possible Data Loss
```

---

# Workflow

```text
Receive Message
       ↓
Commit Offset
       ↓
Process Message
```

---

Notice:

```text
Offset Committed
Before Processing
```

---

# Failure Scenario

Consumer receives:

```text
Order Created
```

---

Consumer commits offset.

---

Before processing:

```text
Consumer Crashes
```

---

Result:

```text
Message Lost
```

because Kafka believes it was already consumed.

---

# Visualization

```text
Message Received
       ↓
Offset Commit
       ↓
Crash
       ↓
Message Lost
```

---

# Example

Consumer:

```java
commitOffset();
processRecord();
```

---

If crash occurs:

```text
Record Never Processed
```

---

# Advantages

---

## Fastest Processing

No reprocessing.

---

## Lowest Overhead

Minimal coordination.

---

## No Duplicates

Messages never replayed.

---

# Disadvantages

---

## Data Loss Possible

Major drawback.

---

## Not Suitable For Critical Systems

---

# Typical Use Cases

```text
Monitoring

Metrics

Telemetry

System Logs
```

where occasional loss is acceptable.

---

# At Least Once

---

# What is At Least Once?

At Least Once means:

```text
Message Delivered
One Or More Times
```

---

Possible outcomes:

```text
Delivered Once

Delivered Multiple Times
```

---

Messages are never lost.

---

# Guarantee

```text
No Data Loss

Duplicates Possible
```

---

# Workflow

```text
Receive Message
       ↓
Process Message
       ↓
Commit Offset
```

---

Notice:

```text
Offset Commit
After Processing
```

---

# Failure Scenario

Consumer receives:

```text
Payment Event
```

---

Processes successfully.

---

Before offset commit:

```text
Consumer Crashes
```

---

Kafka sees:

```text
Offset Not Committed
```

---

After restart:

```text
Message Reprocessed
```

---

Result:

```text
Duplicate Processing
```

---

# Visualization

```text
Message Received
       ↓
Processed
       ↓
Crash
       ↓
Offset Not Saved
       ↓
Message Reprocessed
```

---

# Example

```java
processRecord();

commitOffset();
```

---

Crash before commit:

```text
Duplicate Processing Possible
```

---

# Advantages

---

## No Message Loss

Most important benefit.

---

## Reliable

Suitable for most business systems.

---

## Simpler Than Exactly Once

---

# Disadvantages

---

## Duplicates Possible

Applications may need deduplication.

---

# Typical Use Cases

```text
Order Processing

Inventory Updates

Notifications

E-Commerce Systems
```

---

# At Least Once Timeline

```text
Message
    ↓
Process
    ↓
Crash
    ↓
Restart
    ↓
Process Again
```

---

Result:

```text
Processed Twice
```

---

# Exactly Once

---

# What is Exactly Once?

Exactly Once means:

```text
Message Delivered
Exactly One Time
```

---

Possible outcomes:

```text
Processed Once
```

Only once.

No loss.

No duplicates.

---

# Guarantee

```text
No Data Loss

No Duplicates
```

---

# Why Is It Difficult?

Distributed systems experience:

```text
Retries

Failures

Restarts

Network Issues
```

---

Preventing both:

```text
Loss
AND
Duplicates
```

is challenging.

---

# Kafka Solution

Exactly Once Semantics (EOS).

---

Achieved using:

```text
Idempotent Producer
+
Transactions
+
Offset Coordination
```

---

# Workflow

```text
Send Message
       ↓
Process
       ↓
Transaction Commit
       ↓
Offset Commit
```

---

Everything succeeds together.

---

# Example

Bank Transfer

```text
Debit Account

Credit Account

Commit Offset
```

---

All operations become:

```text
Single Atomic Transaction
```

---

# If Failure Occurs

Transaction:

```text
Rolled Back
```

---

No duplicates.

No partial updates.

---

# Exactly Once Architecture

```text
Producer
      ↓
Kafka Topic
      ↓
Consumer
      ↓
Process
      ↓
Kafka Transaction
      ↓
Commit
```

---

# Idempotent Producer Role

Prevents duplicate records.

---

Configuration:

```java
enable.idempotence=true
```

---

Kafka uses:

```text
Producer ID

Sequence Numbers
```

---

Duplicates ignored.

---

# Transactional Processing

Producer configuration:

```java
transactional.id=my-tx
```

---

Example:

```java
producer.beginTransaction();

process();

producer.commitTransaction();
```

---

All operations succeed together.

---

# Exactly Once Benefits

---

## No Message Loss

---

## No Duplicate Processing

---

## Highest Reliability

---

## Strong Consistency

---

# Exactly Once Drawbacks

---

## More Complex

---

## Higher Latency

---

## Slightly Lower Throughput

---

## Additional Coordination

---

# Trade-offs

Choosing a delivery guarantee always involves trade-offs.

---

# Reliability Comparison

```text
At Most Once
      ↓
At Least Once
      ↓
Exactly Once
```

Reliability increases.

---

# Performance Comparison

```text
At Most Once
      ↓
At Least Once
      ↓
Exactly Once
```

Performance decreases.

---

# Complexity Comparison

```text
At Most Once
      ↓
At Least Once
      ↓
Exactly Once
```

Complexity increases.

---

# Delivery Semantics Comparison Table

| Feature | At Most Once | At Least Once | Exactly Once |
|-----------|-------------|-------------|-------------|
| Message Loss | Possible | No | No |
| Duplicate Messages | No | Possible | No |
| Reliability | Low | High | Very High |
| Throughput | Highest | High | Lower |
| Complexity | Low | Medium | High |
| Production Usage | Rare | Very Common | Critical Systems |

---

# Consumer Offset Perspective

Understanding offset commit timing is crucial.

---

## At Most Once

```text
Commit Offset
      ↓
Process Message
```

---

Risk:

```text
Data Loss
```

---

## At Least Once

```text
Process Message
      ↓
Commit Offset
```

---

Risk:

```text
Duplicate Processing
```

---

## Exactly Once

```text
Process Message
      ↓
Transaction Commit
      ↓
Offset Commit
```

---

Result:

```text
No Loss

No Duplicates
```

---

# Real World Examples

---

# Example 1: System Monitoring

Events:

```text
CPU Usage

Memory Usage

Disk Metrics
```

---

Recommended:

```text
At Most Once
```

---

Reason:

```text
Occasional Data Loss
Is Acceptable
```

---

# Example 2: Website Analytics

Events:

```text
Page Views

Clicks

Ad Impressions
```

---

Recommended:

```text
At Least Once
```

---

Reason:

```text
Losing Events
Is Worse Than Duplicates
```

---

# Example 3: E-Commerce Orders

Events:

```text
Order Created

Order Shipped

Refund Processed
```

---

Recommended:

```text
At Least Once
```

or

```text
Exactly Once
```

depending on business requirements.

---

# Example 4: Banking Transactions

Events:

```text
Money Transfer

Account Debit

Account Credit
```

---

Recommended:

```text
Exactly Once
```

---

Reason:

```text
No Loss

No Duplicates
```

required.

---

# Example 5: Stock Trading System

Events:

```text
Buy Order

Sell Order

Trade Execution
```

---

Recommended:

```text
Exactly Once
```

---

# Example 6: Logging Platform

Events:

```text
Application Logs
```

---

Recommended:

```text
At Most Once
```

or

```text
At Least Once
```

depending on requirements.

---

# How Kafka Achieves Each Semantic

---

## At Most Once

```text
Auto Commit
Before Processing
```

---

## At Least Once

```text
Manual Offset Commit
After Processing
```

---

## Exactly Once

```text
Idempotent Producer
+
Transactions
+
Transactional Consumers
```

---

# Best Practices

---

## Use At Least Once For Most Applications

Provides:

```text
Good Reliability

Reasonable Performance
```

---

## Use Exactly Once For Financial Systems

Examples:

```text
Payments

Banking

Trading
```

---

## Avoid At Most Once For Business-Critical Data

---

## Enable Idempotency

```java
enable.idempotence=true
```

---

## Use Transactions When Required

```java
transactional.id=my-tx
```

---

## Design Consumers To Be Idempotent

Even when using:

```text
At Least Once
```

---

# Interview Questions

## What are Kafka Message Delivery Semantics?

Guarantees that define how messages are delivered between Producer and Consumer.

---

## What are the three delivery guarantees?

```text
At Most Once

At Least Once

Exactly Once
```

---

## What is At Most Once?

Messages may be lost but are never duplicated.

---

## What is At Least Once?

Messages are never lost but may be delivered multiple times.

---

## What is Exactly Once?

Messages are processed exactly one time with no loss and no duplication.

---

## Which guarantee does Kafka commonly provide?

```text
At Least Once
```

---

## How is At Least Once achieved?

```text
Process First
Commit Offset Later
```

---

## How is At Most Once achieved?

```text
Commit Offset First
Process Later
```

---

## How does Kafka achieve Exactly Once?

Using:

```text
Idempotent Producer
+
Transactions
+
Offset Coordination
```

---

## Which guarantee is best for banking systems?

```text
Exactly Once
```

---

## Which guarantee offers highest performance?

```text
At Most Once
```

---

# Summary

Message Delivery Semantics define Kafka's reliability guarantees.

Kafka supports:

```text
At Most Once
At Least Once
Exactly Once
```

Comparison:

| Semantic | Data Loss | Duplicates |
|-----------|-----------|------------|
| At Most Once | Possible | No |
| At Least Once | No | Possible |
| Exactly Once | No | No |

General Rule:

```text
At Most Once
      ↓
Fastest
      ↓
Least Reliable

At Least Once
      ↓
Balanced

Exactly Once
      ↓
Most Reliable
      ↓
Most Complex
```

For most production systems:

```text
At Least Once
```

is the preferred choice because it offers strong reliability with manageable complexity, while:

```text
Exactly Once
```

is reserved for mission-critical systems such as banking, payments, financial trading, and inventory management where both message loss and duplicate processing are unacceptable.