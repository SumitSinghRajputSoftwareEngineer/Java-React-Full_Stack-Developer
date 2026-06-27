# 34. Exactly Once Semantics (EOS)

Exactly Once Semantics (EOS) is one of the most advanced and important features of Apache Kafka.

It guarantees that:

```text
Each Message Is Processed Exactly One Time
```

even in the presence of:

```text
Network Failures

Broker Failures

Producer Failures

Consumer Failures

Retries
```

EOS eliminates:

```text
Duplicate Processing

Duplicate Writes

Data Inconsistency
```

---

# What is Exactly Once Semantics (EOS)?

## Definition

Exactly Once Semantics (EOS) guarantees that a record is processed and written exactly one time, regardless of failures or retries.

---

In other words:

```text
No Data Loss

No Duplicate Processing
```

---

# Message Processing Guarantees

Kafka supports three delivery guarantees:

| Guarantee | Data Loss | Duplicates |
|------------|------------|------------|
| At Most Once | Possible | No |
| At Least Once | No | Possible |
| Exactly Once | No | No |

---

# Example

Suppose an order event is produced:

```text
Order #1001
```

---

Without EOS:

```text
Retry Happens
```

---

Result:

```text
Order #1001

Order #1001
```

---

Duplicate order processing.

---

With EOS:

```text
Order #1001
```

processed only once.

---

# Why EOS?

EOS was introduced to solve real-world data consistency problems.

---

# Problem 1: Producer Retries

Producer sends:

```text
Order Created
```

---

Broker receives message.

---

Acknowledgement lost.

---

Producer retries.

---

Result:

```text
Duplicate Record
```

---

# Problem 2: Consumer Failure

Consumer:

```text
Reads Record

Processes Record

Crashes Before Offset Commit
```

---

After restart:

```text
Same Record Reprocessed
```

---

Result:

```text
Duplicate Processing
```

---

# Problem 3: Stream Processing

Kafka Streams:

```text
Read Event

Update State

Write Result
```

---

Crash occurs after state update.

---

Result:

```text
State Updated

Output Missing
```

---

Data becomes inconsistent.

---

# Problem 4: Multi-Topic Writes

Producer writes:

```text
Topic A

Topic B
```

---

Failure occurs between writes.

---

Result:

```text
Partial Data
```

---

EOS solves all these scenarios.

---

# Why EOS is Important?

Many business systems cannot tolerate duplicates.

---

Examples:

```text
Banking

Payments

Inventory

Order Management

Fraud Detection
```

---

# Banking Example

Without EOS:

```text
Withdraw ₹1000

Retry

Withdraw ₹1000 Again
```

---

Result:

```text
Incorrect Balance
```

---

With EOS:

```text
Only One Withdrawal
```

---

# EOS Architecture

EOS is not a single feature.

It is a combination of multiple Kafka features working together.

---

# High-Level Architecture

```text
Producer
    ↓
Idempotent Producer
    ↓
Transactions
    ↓
Kafka Broker
    ↓
Read Committed Consumer
```

---

# Components of EOS

EOS depends on:

```text
Idempotent Producer

Producer ID (PID)

Sequence Numbers

Transactions

Transaction Coordinator

Read Committed Consumers
```

---

# EOS Architecture Diagram

```text
Producer
     ↓
Idempotence
     ↓
Transaction
     ↓
Kafka Broker
     ↓
Read Committed Consumer
```

---

# EOS Processing Flow

```text
Create Record
       ↓
Assign Sequence Number
       ↓
Send Record
       ↓
Broker Verifies
       ↓
Commit Transaction
       ↓
Consumer Reads
```

---

# Producer Side EOS

Producer side is responsible for preventing duplicate writes.

---

# Problem

Producer sends:

```text
Order Event
```

---

Broker stores it.

---

ACK lost.

---

Producer retries.

---

Without protection:

```text
Duplicate Event Stored
```

---

# Solution: Idempotent Producer

Kafka introduced:

```text
Idempotent Producer
```

---

# What is an Idempotent Producer?

A producer that ensures duplicate retries do not create duplicate records.

---

Configuration:

```properties
enable.idempotence=true
```

---

# Meaning of Idempotent

Executing the same operation multiple times produces the same result.

---

Example:

```text
Write Order #1001
```

---

Retry:

```text
Write Order #1001
```

---

Result:

```text
Only One Record Stored
```

---

# Producer ID (PID)

Each producer receives a unique:

```text
Producer ID
```

---

Example:

```text
PID = 12345
```

---

Used for:

```text
Duplicate Detection

Tracking Writes
```

---

# Sequence Numbers

Each partition receives sequence numbers.

---

Example:

```text
Message 1 → Seq=1

Message 2 → Seq=2

Message 3 → Seq=3
```

---

Broker tracks sequence numbers.

---

# Duplicate Detection

Producer retries:

```text
Seq=2
```

---

Broker sees:

```text
Seq=2 Already Exists
```

---

Result:

```text
Duplicate Ignored
```

---

# Producer EOS Flow

```text
Producer
     ↓
Assign Sequence Number
     ↓
Send Record
     ↓
Broker Checks Sequence
     ↓
Accept / Reject
```

---

# Producer Side Benefits

---

## No Duplicate Writes

---

## Safe Retries

---

## Ordered Delivery

---

## Higher Reliability

---

# Consumer Side EOS

Producer-side EOS prevents duplicate writes.

Consumer-side EOS prevents duplicate processing.

---

# Consumer Problem

Consumer:

```text
Read Record
```

---

Processes it successfully.

---

Crashes before:

```text
Offset Commit
```

---

After restart:

```text
Reads Same Record Again
```

---

Duplicate processing occurs.

---

# Consumer EOS Solution

Use:

```text
Transactions
+
Offset Commit Inside Transaction
```

---

# Transactional Offset Commit

Kafka allows:

```text
Record Processing
```

and

```text
Offset Commit
```

inside the same transaction.

---

# Workflow

```text
Read Record
      ↓
Process Record
      ↓
Write Output
      ↓
Commit Offset
      ↓
Commit Transaction
```

---

Everything succeeds together.

---

# Failure Scenario

Suppose:

```text
Process Record
```

succeeds.

---

System crashes before commit.

---

Transaction aborts.

---

Result:

```text
Offset Not Committed

Output Not Visible
```

---

Record reprocessed safely.

---

# Read Committed Consumers

Consumers should read only committed data.

---

Configuration:

```properties
isolation.level=read_committed
```

---

# What Does It Do?

Consumer ignores:

```text
Aborted Transactions
```

---

Reads only:

```text
Committed Records
```

---

# Read Committed Example

Transaction:

```text
Begin

Write Record

Abort
```

---

Consumer:

```text
read_committed
```

---

Result:

```text
Record Invisible
```

---

# Read Uncommitted Example

Configuration:

```properties
isolation.level=read_uncommitted
```

---

Consumer reads:

```text
Committed Data

Aborted Data
```

---

Not suitable for EOS.

---

# Read Committed vs Read Uncommitted

| Feature | Read Committed | Read Uncommitted |
|----------|---------------|------------------|
| Reads Committed Records | Yes | Yes |
| Reads Aborted Records | No | Yes |
| Supports EOS | Yes | No |

---

# Consumer EOS Flow

```text
Consume Record
      ↓
Process Record
      ↓
Write Result
      ↓
Commit Offset
      ↓
Commit Transaction
```

---

# Kafka Streams EOS

Kafka Streams provides built-in Exactly Once Processing.

---

# Why Kafka Streams Needs EOS?

Stream applications typically:

```text
Read Events

Maintain State

Write Results
```

---

Failures can occur between these steps.

---

Without EOS:

```text
State Updated

Output Missing
```

---

or

```text
Output Written

State Missing
```

---

Inconsistency occurs.

---

# Kafka Streams EOS Architecture

```text
Input Topic
      ↓
Kafka Streams
      ↓
State Store
      ↓
Output Topic
```

---

All operations executed within a transaction.

---

# Kafka Streams EOS Workflow

```text
Read Record
      ↓
Update State Store
      ↓
Write Output
      ↓
Commit Offset
      ↓
Commit Transaction
```

---

Everything succeeds atomically.

---

# Streams EOS Configuration

Kafka Streams supports:

```properties
processing.guarantee=exactly_once_v2
```

---

This is the recommended setting.

---

# Older Version

```properties
processing.guarantee=exactly_once
```

---

Less efficient.

---

# Recommended

```properties
processing.guarantee=exactly_once_v2
```

---

# Exactly Once V2 Improvements

Provides:

```text
Better Performance

Lower Latency

Reduced Coordination

Higher Throughput
```

---

# Streams EOS Example

Input:

```text
Order1

Order2
```

---

State Store:

```text
TotalRevenue=5000
```

---

Output Topic:

```text
Revenue Updated
```

---

If failure occurs:

```text
State

Output

Offsets
```

remain consistent.

---

# EOS Internals

Kafka EOS relies on several mechanisms.

---

# Producer ID (PID)

Unique producer identifier.

---

# Sequence Numbers

Prevent duplicate writes.

---

# Idempotence

Guarantees safe retries.

---

# Transactions

Guarantee atomic operations.

---

# Transaction Coordinator

Tracks transaction state.

---

# Read Committed Consumers

Hide aborted data.

---

# EOS Internal Flow

```text
Producer
     ↓
PID
     ↓
Sequence Number
     ↓
Transaction
     ↓
Broker
     ↓
Read Committed Consumer
```

---

# EOS Failure Scenarios

---

# Producer Crash

Transaction Coordinator:

```text
Aborts Transaction
```

---

No partial writes visible.

---

# Consumer Crash

Offsets remain uncommitted.

---

Safe reprocessing occurs.

---

# Broker Failure

Replication protects data.

---

Transaction state recovered.

---

# Network Failure

Retries occur safely.

---

Duplicates prevented.

---

# EOS Performance Considerations

EOS improves correctness but adds overhead.

---

Additional operations:

```text
Transaction Tracking

Sequence Validation

Transaction Coordination
```

---

# Performance Comparison

| Feature | At Least Once | Exactly Once |
|----------|--------------|--------------|
| Throughput | Higher | Slightly Lower |
| Latency | Lower | Slightly Higher |
| Duplicates | Possible | No |
| Complexity | Lower | Higher |

---

# Real-World Use Cases

---

# Banking Systems

```text
Transfers

Payments

Balances
```

---

# Order Processing

```text
Orders

Invoices

Payments
```

---

# Inventory Management

```text
Stock Updates
```

---

# Fraud Detection

```text
Financial Events
```

---

# Kafka Streams Applications

```text
Aggregations

Joins

Stateful Processing
```

---

# Event Sourcing

```text
Business Events
```

must never be duplicated.

---

# Best Practices

---

## Enable Idempotence

```properties
enable.idempotence=true
```

---

## Use Transactions

---

## Use Read Committed Consumers

```properties
isolation.level=read_committed
```

---

## Use Unique Transactional IDs

---

## Configure EOS for Kafka Streams

```properties
processing.guarantee=exactly_once_v2
```

---

## Keep Transactions Short

---

## Monitor Transaction Coordinator

---

## Test Failure Scenarios

---

# Common Interview Questions

## What is Exactly Once Semantics?

A guarantee that each record is processed exactly one time without duplication or data loss.

---

## Why was EOS introduced?

To eliminate duplicates and maintain consistency during failures and retries.

---

## What are the building blocks of EOS?

```text
Idempotence

Transactions

Read Committed Consumers
```

---

## What is an Idempotent Producer?

A producer that prevents duplicate records during retries.

---

## What is Producer ID (PID)?

A unique identifier assigned to a producer for duplicate detection.

---

## What are Sequence Numbers?

Numbers attached to records that allow brokers to identify duplicates.

---

## Why use isolation.level=read_committed?

To ensure consumers only read committed transactional records.

---

## How does Kafka Streams achieve EOS?

By executing state updates, output writes, and offset commits within a single transaction.

---

## What is exactly_once_v2?

The modern and recommended implementation of Kafka Streams Exactly Once Processing.

---

## Does EOS affect performance?

Yes, slightly, because transaction coordination and validation add overhead.

---

# Summary

Exactly Once Semantics (EOS) is Kafka's strongest processing guarantee.

Core Topics:

- Why EOS?
- EOS Architecture
- Producer Side EOS
- Consumer Side EOS
- Kafka Streams EOS

EOS Components:

```text
Idempotent Producer

Producer ID

Sequence Numbers

Transactions

Transaction Coordinator

Read Committed Consumer
```

Architecture:

```text
Producer
     ↓
Idempotence
     ↓
Transaction
     ↓
Broker
     ↓
Read Committed Consumer
```

Kafka Streams EOS:

```properties
processing.guarantee=exactly_once_v2
```

Producer Side:

```text
PID
+
Sequence Numbers
+
Idempotence
```

Consumer Side:

```text
Transactions
+
Read Committed
```

Benefits:

```text
No Duplicate Processing

No Duplicate Writes

No Data Loss

Consistent State

Atomic Processing
```

Exactly Once Semantics is the foundation of reliable event-driven systems where correctness is more important than maximum throughput, making it critical for banking, payments, inventory management, stream processing, and mission-critical enterprise applications.