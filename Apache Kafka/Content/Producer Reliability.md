
Producer Reliability is one of the most critical Kafka concepts.

In distributed systems, failures are inevitable:

- Network Failures
- Broker Failures
- Producer Crashes
- Leader Elections
- Disk Failures
- Timeouts

Kafka provides multiple mechanisms to ensure data reliability and prevent message loss.

These mechanisms include:

```text
Message Delivery Guarantees
Retries
Idempotent Producer
Duplicate Prevention
Failure Handling
Ordering Guarantees
```

A common interview question is:

> How does Kafka ensure reliable message delivery?

The answer involves understanding all the topics covered in this section.

---

# Why Producer Reliability is Important?

Imagine a banking application:

```text
Transfer ₹10,000
```

Producer sends:

```text
Money Debited Event
```

If the message is lost:

```text
Account Balance Incorrect
```

If duplicated:

```text
Money Debited Twice
```

Both scenarios are unacceptable.

Kafka provides mechanisms to prevent these situations.

---

# Message Delivery Guarantees

Message Delivery Guarantee defines how Kafka ensures message delivery between Producer and Consumer.

---

## Three Delivery Semantics

Kafka supports:

```text
At Most Once

At Least Once

Exactly Once
```

---

# At Most Once

## Definition

Message is delivered:

```text
Zero Or One Time
```

Message loss is possible.

Duplicate delivery is not possible.

---

## Flow

```text
Send Message
      ↓
Commit/ACK
      ↓
Failure
      ↓
Message Lost
```

---

## Example

Producer sends:

```text
Order Created
```

Producer assumes success.

Network failure occurs.

Message never reaches Kafka.

---

Result:

```text
Message Lost
```

---

## Characteristics

### Advantages

```text
Fastest
Low Overhead
```

---

### Disadvantages

```text
Data Loss Possible
```

---

## Use Cases

```text
Metrics

Monitoring

Temporary Logs
```

---

# At Least Once

## Definition

Message is delivered:

```text
One Or More Times
```

No message loss.

Duplicates possible.

---

## Flow

```text
Send Message
      ↓
Failure?
      ↓
Retry
      ↓
Eventually Delivered
```

---

## Example

Producer sends:

```text
Payment Event
```

ACK not received.

Producer retries.

---

Actual situation:

```text
Original Message Stored

Retry Also Stored
```

---

Result:

```text
Duplicate Message
```

---

## Characteristics

### Advantages

```text
No Data Loss
```

---

### Disadvantages

```text
Duplicate Messages Possible
```

---

## Most Common Guarantee

Historically Kafka systems used:

```text
At Least Once
```

---

# Exactly Once

## Definition

Message is delivered:

```text
Exactly One Time
```

No loss.

No duplicates.

---

## Flow

```text
Send
     ↓
Retry If Needed
     ↓
Deduplicate
     ↓
Store Once
```

---

## Achieved Using

```text
Idempotent Producer
+
Transactions
```

---

## Characteristics

### Advantages

```text
Highest Reliability
```

---

### Disadvantages

```text
More Complexity
```

---

## Use Cases

```text
Banking

Payments

Financial Systems

Inventory Systems
```

---

# Delivery Guarantee Comparison

| Guarantee | Loss | Duplicate |
|------------|------|-----------|
| At Most Once | Possible | No |
| At Least Once | No | Possible |
| Exactly Once | No | No |

---

# Retries

Retries are Kafka's primary reliability mechanism.

---

## What is Retry?

When sending fails:

```text
Producer Sends Again
```

---

## Why Retries Are Needed?

Failures occur because of:

```text
Network Issues

Broker Restart

Leader Election

Temporary Errors
```

---

# Example

Producer sends:

```text
Message
```

---

Network issue:

```text
ACK Not Received
```

---

Producer retries.

---

Eventually:

```text
Message Stored
```

---

# Retry Workflow

```text
Producer
      ↓
Send Message
      ↓
Failure
      ↓
Retry
      ↓
Success
```

---

# Configuration

## retries

Defines retry attempts.

---

Example:

```java
props.put(
    "retries",
    3
);
```

---

Modern Kafka recommendation:

```java
Integer.MAX_VALUE
```

---

Example:

```java
props.put(
    "retries",
    Integer.MAX_VALUE
);
```

---

# Retry Backoff

Avoids aggressive retrying.

---

Configuration:

```java
props.put(
    "retry.backoff.ms",
    100
);
```

---

Meaning:

```text
Wait 100 ms
Before Next Retry
```

---

# Retry Risks

Retries can create:

```text
Duplicate Messages
```

---

This led to the introduction of:

```text
Idempotent Producer
```

---

# Idempotent Producer

One of the most important Kafka features.

---

# What is Idempotency?

Idempotency means:

```text
Same Operation
Applied Multiple Times
Produces Same Result
```

---

# Example

Without Idempotency

Producer retries.

---

Broker receives:

```text
Message A

Message A
```

---

Stored twice.

---

Result:

```text
Duplicate Records
```

---

# With Idempotency

Producer retries.

Broker recognizes:

```text
Already Received
```

---

Result:

```text
Stored Only Once
```

---

# Configuration

```java
props.put(
    "enable.idempotence",
    true
);
```

---

# How Idempotent Producer Works?

Kafka assigns:

```text
Producer ID (PID)
```

---

Each message receives:

```text
Sequence Number
```

---

Example

```text
PID = 1001

Sequence = 1
Sequence = 2
Sequence = 3
```

---

Broker tracks:

```text
Last Sequence Number
```

---

Duplicate sequence:

```text
Ignored
```

---

# Visualization

```text
Producer

PID = 10

Message A
Seq=1

Retry
Seq=1

Broker

Seq=1 Stored

Seq=1 Duplicate
Ignored
```

---

# Benefits

---

## Duplicate Elimination

---

## Safer Retries

---

## Exactly-Once Foundation

---

## Strong Reliability

---

# Important Requirements

When enabled:

```text
acks=all
```

is automatically enforced.

---

Kafka also ensures:

```text
retries > 0
```

---

# Duplicate Prevention

Duplicate messages are a major concern in distributed systems.

---

# Why Duplicates Occur?

Most common scenario:

```text
Producer Sends Message
      ↓
Broker Stores Message
      ↓
ACK Lost
      ↓
Producer Retries
```

---

Broker receives:

```text
Same Message Twice
```

---

# Without Idempotency

Result:

```text
Duplicate Records
```

---

# With Idempotency

Broker detects:

```text
Duplicate Sequence Number
```

---

Result:

```text
Second Message Ignored
```

---

# Application-Level Duplicate Prevention

Sometimes applications also implement:

```text
Unique Event ID

Transaction ID

Order ID
```

---

Example:

```json
{
  "eventId":"EVT-1001"
}
```

---

Consumer checks:

```text
Already Processed?
```

---

If yes:

```text
Ignore
```

---

# Producer Failure Handling

Kafka producers must handle failures gracefully.

---

# Common Producer Failures

---

## Network Failure

Example:

```text
Producer
     ↓
Network Broken
     ↓
Broker
```

---

Recovery:

```text
Retry
```

---

## Broker Failure

Leader broker crashes.

---

Recovery:

```text
Leader Election
```

---

Producer automatically reconnects.

---

## Timeout Failure

Producer waits too long.

---

Configuration:

```java
request.timeout.ms
```

---

Example:

```java
props.put(
    "request.timeout.ms",
    30000
);
```

---

# Delivery Timeout

Maximum allowed delivery time.

---

Configuration:

```java
props.put(
    "delivery.timeout.ms",
    120000
);
```

---

Meaning:

```text
120 Seconds
```

---

After timeout:

```text
Send Fails
```

---

# Failure Handling Flow

```text
Send Message
      ↓
Failure
      ↓
Retry
      ↓
Leader Election
      ↓
Reconnect
      ↓
Success
```

---

# Producer Callback Handling

Recommended approach.

---

Example:

```java
producer.send(
    record,
    (metadata, exception) -> {

        if(exception != null)
        {
            // failure handling
        }
    }
);
```

---

# Benefits

```text
Error Visibility

Monitoring

Recovery Logic
```

---

# Message Ordering Guarantees

Another popular interview topic.

---

# Does Kafka Guarantee Ordering?

Answer:

```text
Yes
```

but only under certain conditions.

---

# Ordering Within Partition

Kafka guarantees:

```text
Order Within A Partition
```

---

Example:

```text
Partition 0

Offset 0 -> Created

Offset 1 -> Paid

Offset 2 -> Shipped

Offset 3 -> Delivered
```

---

Consumer reads:

```text
Created
Paid
Shipped
Delivered
```

---

Ordering preserved.

---

# Across Multiple Partitions

Kafka does NOT guarantee:

```text
Global Ordering
```

---

Example:

```text
Partition 0
Order A

Partition 1
Order B
```

---

Consumer order may vary.

---

# Ordering Risk With Retries

Historically:

```text
Retries
+
Multiple In-Flight Requests
```

could break ordering.

---

Example:

```text
Message 1 Fails

Message 2 Succeeds
```

---

Result:

```text
Message 2 Appears First
```

---

# Solution

Modern Kafka uses:

```java
enable.idempotence=true
```

---

and controls:

```java
max.in.flight.requests.per.connection
```

---

Default modern Kafka settings preserve ordering.

---

# Ordering Example

Key-Based Partitioning:

```text
Customer 101
```

All records go to:

```text
Partition 1
```

---

Events:

```text
Created

Updated

Purchased

Refunded
```

---

Order preserved.

---

# Reliability Configuration for Production

A common production-grade setup:

```java
Properties props = new Properties();

props.put(
    "acks",
    "all"
);

props.put(
    "enable.idempotence",
    true
);

props.put(
    "retries",
    Integer.MAX_VALUE
);

props.put(
    "delivery.timeout.ms",
    120000
);

props.put(
    "compression.type",
    "snappy"
);
```

---

# Reliability Best Practices

---

## Use ACK=all

```java
acks=all
```

---

## Enable Idempotency

```java
enable.idempotence=true
```

---

## Allow Retries

```java
retries=Integer.MAX_VALUE
```

---

## Monitor Producer Errors

Use callbacks.

---

## Use Meaningful Keys

Preserve ordering.

---

## Handle Failures Gracefully

Implement retry logic and monitoring.

---

## Use Transactions For Exactly Once

Required for critical systems.

---

# Interview Questions

## What are Kafka delivery guarantees?

```text
At Most Once
At Least Once
Exactly Once
```

---

## What guarantee does Kafka provide by default?

Typically:

```text
At Least Once
```

when retries are enabled and idempotency is not used.

---

## What is an Idempotent Producer?

A producer that prevents duplicate records during retries.

---

## How do you enable idempotency?

```java
enable.idempotence=true
```

---

## Why do duplicates occur?

Because producer may retry when ACK is lost even though the broker already stored the record.

---

## How does Kafka prevent duplicates?

Using:

```text
Producer ID (PID)

Sequence Numbers
```

---

## Does Kafka guarantee message ordering?

Yes, within a partition.

---

## Can Kafka guarantee global ordering across partitions?

```text
No
```

---

## Why are retries important?

They help recover from temporary failures and prevent message loss.

---

## What is required for Exactly Once semantics?

```text
Idempotent Producer
+
Kafka Transactions
```

---

# Summary

Producer Reliability ensures that records are delivered safely even during failures.

Kafka provides:

```text
Delivery Guarantees
Retries
Idempotent Producers
Duplicate Prevention
Failure Recovery
Ordering Guarantees
```

Core Reliability Flow:

```text
Send Message
      ↓
Failure?
      ↓
Retry
      ↓
Idempotent Check
      ↓
Store Once
      ↓
ACK
```

Production-grade reliability typically uses:

```java
acks=all
enable.idempotence=true
retries=Integer.MAX_VALUE
```

These settings provide:

```text
High Durability
Minimal Data Loss
Duplicate Prevention
Ordering Preservation
```

and form the foundation for Kafka's Exactly-Once Processing model.