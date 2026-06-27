
Kafka Transactions are one of the most important features introduced to support:

```text
Exactly Once Processing (EOS)
```

Before transactions were introduced, Kafka could guarantee:

```text
At Most Once

At Least Once
```

but achieving:

```text
Exactly Once
```

was difficult because failures could lead to:

```text
Duplicate Messages

Partial Writes

Inconsistent State
```

Kafka Transactions solve these problems by allowing multiple operations to be executed atomically.

---

# What are Transactions?

## Definition

A Kafka Transaction is a mechanism that allows multiple producer operations to be treated as a single atomic unit of work.

---

Atomic means:

```text
Either Everything Succeeds

OR

Everything Fails
```

---

There is no partial success.

---

# Real-World Example

Imagine a banking application.

Transfer:

```text
Account A → Withdraw ₹1000

Account B → Deposit ₹1000
```

---

If withdrawal succeeds but deposit fails:

```text
Data Inconsistency
```

---

Transactions ensure:

```text
Both Operations Succeed

OR

Both Roll Back
```

---

# Kafka Transaction Example

Suppose a producer writes:

```text
Message 1 → Topic A

Message 2 → Topic B
```

---

Without transactions:

```text
Message 1 Written

Producer Crashes

Message 2 Missing
```

---

Result:

```text
Partial Data
```

---

With transactions:

```text
Message 1

Message 2
```

are committed together.

---

Either:

```text
Both Visible
```

or

```text
Neither Visible
```

---

# Why Kafka Transactions?

Transactions solve several problems.

---

# Problem 1: Duplicate Messages

Producer sends:

```text
Order Created
```

---

Network failure occurs.

---

Producer retries.

---

Result:

```text
Duplicate Order
```

---

# Problem 2: Partial Writes

Producer writes:

```text
Topic A
```

but fails before writing:

```text
Topic B
```

---

Result:

```text
Inconsistent Data
```

---

# Problem 3: Stream Processing Failures

Kafka Streams:

```text
Read Event

Update State

Write Result
```

---

Failure occurs between operations.

---

Result:

```text
State Updated

Output Missing
```

---

Transactions solve these issues.

---

# ACID Properties in Kafka Transactions

Kafka Transactions provide partial ACID guarantees.

---

## Atomicity

All operations succeed or fail together.

---

## Consistency

Data remains consistent.

---

## Isolation

Uncommitted data remains invisible.

---

## Durability

Committed data survives failures.

---

# Transaction Architecture

```text
Producer
     ↓
Transaction Coordinator
     ↓
Kafka Brokers
     ↓
Topics
```

---

# Components

Transactions involve:

```text
Transactional Producer

Transaction Coordinator

Transaction Log

Kafka Brokers

Consumers
```

---

# High-Level Flow

```text
Begin Transaction
       ↓
Send Messages
       ↓
Commit Transaction
```

or

```text
Begin Transaction
       ↓
Failure
       ↓
Abort Transaction
```

---

# Transactional Producer

The producer responsible for creating transactions.

---

# What is a Transactional Producer?

A producer configured with:

```text
transactional.id
```

---

This enables transaction support.

---

# Example Configuration

```java
Properties props =
        new Properties();

props.put(
    ProducerConfig.TRANSACTIONAL_ID_CONFIG,
    "order-tx-1"
);
```

---

# Why Transactional ID?

Kafka must uniquely identify the producer.

---

Example:

```text
Producer Instance A
```

gets:

```text
transactional.id = order-tx-1
```

---

Benefits:

```text
Recovery

Duplicate Prevention

Transaction Tracking
```

---

# Transactional Producer Lifecycle

```text
Create Producer
       ↓
Initialize Transaction
       ↓
Begin Transaction
       ↓
Send Messages
       ↓
Commit / Abort
```

---

# Initialization

Before using transactions:

```java
producer.initTransactions();
```

---

Purpose:

```text
Register Producer

Recover Old Transactions

Initialize State
```

---

# Start Transaction

```java
producer.beginTransaction();
```

---

Now producer enters:

```text
Transaction Mode
```

---

# Send Records

```java
producer.send(record1);

producer.send(record2);
```

---

Messages are:

```text
Buffered

Not Yet Visible
```

to consumers.

---

# Commit Transaction

```java
producer.commitTransaction();
```

---

Messages become:

```text
Visible
```

---

# Abort Transaction

```java
producer.abortTransaction();
```

---

All transaction records discarded.

---

# Transaction Lifecycle Example

```java
producer.initTransactions();

producer.beginTransaction();

producer.send(record1);

producer.send(record2);

producer.commitTransaction();
```

---

Flow:

```text
Begin
   ↓
Send Records
   ↓
Commit
```

---

# Transaction Coordinator

The core component responsible for managing Kafka transactions.

---

# What is Transaction Coordinator?

A broker-side component that manages:

```text
Transaction State

Producer State

Transaction Commit

Transaction Abort
```

---

# Responsibilities

---

## Track Active Transactions

---

## Manage Producer IDs

---

## Commit Transactions

---

## Abort Transactions

---

## Recover Failed Transactions

---

# Architecture

```text
Producer
     ↓
Transaction Coordinator
     ↓
Kafka Topics
```

---

# Internal Transaction Topic

Kafka stores transaction metadata in:

```text
__transaction_state
```

---

This is an internal Kafka topic.

---

Stores:

```text
Transaction State

Producer Information

Commit Status
```

---

# Transaction States

Transactions move through several states.

---

# Empty

No active transaction.

---

# Ongoing

Transaction started.

---

# Prepare Commit

Commit initiated.

---

# Complete Commit

Transaction successfully committed.

---

# Prepare Abort

Abort initiated.

---

# Complete Abort

Transaction aborted.

---

# State Flow

```text
Empty
   ↓
Ongoing
   ↓
Prepare Commit
   ↓
Complete Commit
```

---

or

```text
Empty
   ↓
Ongoing
   ↓
Prepare Abort
   ↓
Complete Abort
```

---

# Producer ID (PID)

Every transactional producer receives:

```text
Producer ID
```

---

Example:

```text
PID = 101
```

---

Used for:

```text
Duplicate Detection

Transaction Tracking
```

---

# Producer Epoch

Each producer instance also has:

```text
Epoch
```

---

Used to prevent:

```text
Zombie Producers
```

---

# Zombie Producer Problem

Old producer instance crashes.

---

New producer starts.

---

Old producer unexpectedly resumes.

---

Without protection:

```text
Duplicate Writes
```

---

Kafka uses:

```text
Producer Epoch
```

to reject stale producers.

---

# Exactly Once Processing

One of Kafka's most important capabilities.

---

# What is Exactly Once Processing?

Guarantee that each message is:

```text
Processed Exactly One Time
```

---

No:

```text
Duplicates

Data Loss
```

---

# Why Is It Hard?

Failures can occur during:

```text
Send

Commit

Retry

Network Issues
```

---

Without EOS:

```text
Duplicates Possible
```

---

# Kafka EOS Components

Exactly Once requires:

```text
Idempotent Producer

Transactions

Read Committed Consumers
```

---

# Architecture

```text
Producer
     ↓
Transaction
     ↓
Kafka
     ↓
Read Committed Consumer
```

---

# Idempotent Producer

Ensures:

```text
Duplicate Sends
```

do not create duplicate records.

---

Configuration:

```java
enable.idempotence=true
```

---

# Read Committed Consumers

Consumers should only read:

```text
Committed Transactions
```

---

Configuration:

```java
isolation.level=read_committed
```

---

# Read Committed vs Read Uncommitted

| Feature | Read Committed | Read Uncommitted |
|----------|---------------|------------------|
| Reads Committed Data | Yes | Yes |
| Reads Aborted Data | No | Yes |
| Supports EOS | Yes | No |

---

# Example

Transaction:

```text
Begin

Send Record

Abort
```

---

Consumer:

```text
read_committed
```

will never see record.

---

# Transaction Lifecycle

A very common interview topic.

---

# Complete Transaction Lifecycle

## Step 1

Create Producer

```java
KafkaProducer producer;
```

---

## Step 2

Initialize Transactions

```java
producer.initTransactions();
```

---

## Step 3

Begin Transaction

```java
producer.beginTransaction();
```

---

State:

```text
ONGOING
```

---

## Step 4

Send Messages

```java
producer.send(...);
```

---

Messages hidden from consumers.

---

## Step 5A

Commit Transaction

```java
producer.commitTransaction();
```

---

State:

```text
COMMITTED
```

---

Messages become visible.

---

# OR

## Step 5B

Abort Transaction

```java
producer.abortTransaction();
```

---

State:

```text
ABORTED
```

---

Messages discarded.

---

# Lifecycle Diagram

```text
Initialize
      ↓
Begin Transaction
      ↓
Send Records
      ↓
Commit
```

---

or

```text
Initialize
      ↓
Begin Transaction
      ↓
Failure
      ↓
Abort
```

---

# Kafka Streams and Transactions

Kafka Streams uses transactions internally.

---

Example:

```text
Read Event
      ↓
Update State
      ↓
Write Output
```

---

All operations occur inside one transaction.

---

Benefits:

```text
Exactly Once Processing
```

---

# Transaction Timeout

Kafka transactions cannot run forever.

---

Configuration:

```properties
transaction.timeout.ms
```

---

If timeout occurs:

```text
Transaction Aborted
```

---

# Transaction Failure Scenarios

---

# Producer Crash

Transaction Coordinator:

```text
Aborts Transaction
```

---

# Broker Failure

Transaction state recovered from:

```text
__transaction_state
```

---

# Network Failure

Producer retries safely.

---

# Transaction Log

Kafka stores transaction metadata inside:

```text
__transaction_state
```

---

Contains:

```text
Transaction ID

Producer ID

State

Epoch
```

---

# Performance Impact

Transactions improve reliability but add overhead.

---

Additional Work:

```text
Transaction Tracking

State Management

Commit Coordination
```

---

# Performance Comparison

| Feature | Non-Transactional | Transactional |
|----------|------------------|--------------|
| Throughput | Higher | Slightly Lower |
| Reliability | Lower | Higher |
| Exactly Once | No | Yes |
| Complexity | Low | Medium |

---

# Real-World Use Cases

---

# Financial Systems

```text
Payments

Transfers

Banking
```

---

# Order Processing

```text
Order Created

Inventory Updated

Payment Processed
```

must remain consistent.

---

# Kafka Streams Applications

```text
State Updates

Output Topics
```

must stay synchronized.

---

# Event Sourcing

Ensure events written exactly once.

---

# Inventory Management

Prevent duplicate inventory updates.

---

# Best Practices

---

## Always Use Unique transactional.id

---

## Enable Idempotence

```properties
enable.idempotence=true
```

---

## Use Read Committed Consumers

```properties
isolation.level=read_committed
```

---

## Keep Transactions Short

---

## Monitor Transaction Coordinator

---

## Configure Proper Timeouts

---

## Use Transactions Only When Needed

---

## Combine Transactions With Kafka Streams EOS

---

# Common Interview Questions

## What is a Kafka Transaction?

A mechanism that allows multiple producer operations to be committed or aborted atomically.

---

## What is a Transactional Producer?

A producer configured with a unique:

```text
transactional.id
```

that supports transactions.

---

## What is the Transaction Coordinator?

A broker-side component that manages transaction states, commits, aborts, and recovery.

---

## Which internal topic stores transaction metadata?

```text
__transaction_state
```

---

## What is Exactly Once Processing?

Guaranteeing that each record is processed exactly one time without duplicates or data loss.

---

## What is required for Exactly Once Semantics?

```text
Idempotent Producer

Transactions

Read Committed Consumers
```

---

## What does commitTransaction() do?

Makes all transaction records visible to consumers.

---

## What does abortTransaction() do?

Discards all records written during the transaction.

---

## What is Producer Epoch?

A version number used to prevent zombie producers.

---

## What is isolation.level=read_committed?

A consumer setting that ensures only committed transactional records are read.

---

# Summary

Kafka Transactions provide atomic, fault-tolerant message processing and form the foundation of Exactly Once Semantics (EOS).

Core Topics:

- What are Transactions?
- Transactional Producer
- Transaction Coordinator
- Exactly Once Processing
- Transaction Lifecycle

Architecture:

```text
Producer
     ↓
Transaction Coordinator
     ↓
Kafka Topics
```

Key Components:

```text
Transactional Producer

Transaction Coordinator

Producer ID

Producer Epoch

Transaction Log
```

EOS Requirements:

```text
Idempotent Producer
+
Transactions
+
Read Committed Consumer
```

Transaction Lifecycle:

```text
Init
 ↓
Begin
 ↓
Send Records
 ↓
Commit / Abort
```

Internal Topic:

```text
__transaction_state
```

Benefits:

```text
Atomic Writes

No Partial Updates

Exactly Once Processing

Consistency

Fault Tolerance
```

Kafka Transactions are essential for financial systems, stream processing applications, event sourcing architectures, and any workload where data consistency and exactly-once guarantees are critical.