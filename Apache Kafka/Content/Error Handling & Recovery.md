# 52. Error Handling & Recovery

In distributed systems, failures are inevitable.

Kafka applications must be designed to handle:

```text
Network Failures

Broker Failures

Consumer Failures

Serialization Errors

Database Failures

Invalid Messages
```

Without proper error handling and recovery mechanisms:

```text
Message Loss

Infinite Processing Loops

Data Corruption

Application Crashes
```

can occur.

---

# What is Error Handling & Recovery?

## Definition

Error Handling & Recovery refers to the techniques used to detect, manage, isolate, retry, and recover from failures during Kafka message processing.

---

# Goals of Error Handling

```text
Prevent Message Loss

Recover Automatically

Maintain Reliability

Support Fault Tolerance

Enable Monitoring
```

---

# Error Handling Architecture

```text
Producer
    ↓
Kafka Topic
    ↓
Consumer
    ↓
Processing
    ↓
Success / Failure
```

---

If failure occurs:

```text
Retry
   ↓
Success
```

or

```text
Retry Exhausted
       ↓
DLT / DLQ
```

---

# Common Failure Types

## Temporary Failures

Usually recoverable.

Examples:

```text
Database Timeout

Network Glitch

Service Unavailable
```

---

## Permanent Failures

Usually not recoverable.

Examples:

```text
Invalid JSON

Missing Required Fields

Corrupted Data
```

---

## Infrastructure Failures

Examples:

```text
Broker Down

Disk Failure

Cluster Outage
```

---

# Retry Mechanisms

Retry is the first line of defense against transient failures.

---

# What is Retry?

Retry means reprocessing a failed message after a delay.

---

# Why Retry?

Many failures are temporary.

Example:

```text
Consumer
    ↓
Database Down
```

---

Few seconds later:

```text
Database Available
```

---

Message can now be processed successfully.

---

# Retry Flow

```text
Consume Message
      ↓
Failure
      ↓
Retry
      ↓
Success
```

---

# Retry Strategies

Kafka applications commonly use:

```text
Immediate Retry

Fixed Delay Retry

Exponential Backoff Retry

Retry Topics
```

---

# Immediate Retry

Retries immediately after failure.

---

Example:

```text
Attempt 1 → Fail

Attempt 2 → Retry Immediately
```

---

Advantages:

```text
Simple
```

---

Disadvantages:

```text
Can Overload Systems
```

---

# Fixed Delay Retry

Uses a constant delay.

---

Example:

```text
Retry Every 5 Seconds
```

---

Flow:

```text
Attempt 1
    ↓
Wait 5 Seconds
    ↓
Attempt 2
```

---

Advantages:

```text
Predictable

Simple
```

---

# Exponential Backoff Retry

Delay increases after every retry.

---

Example:

```text
Retry 1 → 1 sec

Retry 2 → 2 sec

Retry 3 → 4 sec

Retry 4 → 8 sec
```

---

Advantages:

```text
Reduces System Pressure

Improves Stability
```

---

# Retry Topic Pattern

Recommended for production.

---

Architecture:

```text
Orders Topic
      ↓
Failure
      ↓
Orders-Retry
      ↓
Retry Consumer
      ↓
Success
```

---

Benefits:

```text
Non-Blocking

Scalable

Fault Tolerant
```

---

# Spring Kafka Retry Example

```java
@RetryableTopic(
        attempts = "5")
@KafkaListener(
        topics = "orders")
public void consume(
        String message){

}
```

---

# Retry Best Practices

```text
Use Exponential Backoff

Limit Retry Attempts

Monitor Retry Topics

Avoid Infinite Retries
```

---

# Dead Letter Queue (DLQ)

A DLQ stores messages that cannot be processed successfully.

---

# What is DLQ?

Dead Letter Queue is a queue that receives failed messages after all retry attempts are exhausted.

---

Purpose:

```text
Prevent Consumer Blocking

Preserve Failed Messages

Support Investigation
```

---

# DLQ Flow

```text
Message
   ↓
Processing
   ↓
Failure
   ↓
Retry
   ↓
Failure
   ↓
DLQ
```

---

# Why DLQ?

Without DLQ:

```text
Consumer May Retry Forever
```

---

Result:

```text
Consumer Stuck
```

---

DLQ prevents this issue.

---

# DLQ Example

```text
orders
```

Failed messages:

```text
orders-dlq
```

---

# Benefits

```text
No Data Loss

Operational Visibility

Failure Analysis

Recovery Support
```

---

# Typical DLQ Contents

Failed message plus metadata:

```text
Original Payload

Topic

Partition

Offset

Error Reason
```

---

# DLQ Recovery

Operations team may:

```text
Fix Data

Replay Messages

Reprocess Events
```

---

# Dead Letter Topic (DLT)

In Kafka, the DLQ concept is implemented using a topic.

---

# What is DLT?

Dead Letter Topic is a Kafka topic that stores permanently failed messages.

---

# DLQ vs DLT

| DLQ | DLT |
|------|------|
| Generic Messaging Concept | Kafka Implementation |
| Queue Based Systems | Topic Based Systems |
| Message Queue | Kafka Topic |

---

# DLT Architecture

```text
Orders Topic
      ↓
Consumer
      ↓
Failure
      ↓
Orders-DLT
```

---

# Typical Naming

```text
orders
```

DLT:

```text
orders-dlt
```

---

# Spring Kafka DLT Example

```java
@DltHandler
public void processDlt(
        String message){

    System.out.println(
            "Failed: "
            + message);
}
```

---

# DLT Message Flow

```text
Topic
 ↓
Consumer
 ↓
Failure
 ↓
Retry
 ↓
Failure
 ↓
DLT
```

---

# Why DLT Matters?

Benefits:

```text
No Message Loss

Consumer Stability

Audit Trail

Easy Troubleshooting
```

---

# DLT Monitoring

Monitor:

```text
DLT Message Count

DLT Growth Rate

Repeated Failures
```

---

# Poison Pill Messages

One of the most common Kafka production problems.

---

# What is a Poison Pill Message?

A message that consistently fails processing and can never be successfully consumed.

---

# Characteristics

```text
Always Fails

Blocks Processing

Causes Infinite Retries
```

---

# Example

Consumer expects:

```json
{
  "id": 101
}
```

---

Received:

```json
INVALID_JSON
```

---

Result:

```text
Deserialization Failure
```

every time.

---

# Poison Pill Flow

```text
Consume Message
       ↓
Failure
       ↓
Retry
       ↓
Failure
       ↓
Retry
       ↓
Failure
```

---

Potential outcome:

```text
Infinite Loop
```

---

# Common Causes

---

## Invalid JSON

```json
INVALID_JSON
```

---

## Schema Mismatch

Expected:

```json
{
  "name":"John"
}
```

Received:

```json
{
  "username":"John"
}
```

---

## Corrupted Data

---

## Missing Fields

---

## Unsupported Versions

---

# Detecting Poison Pills

Indicators:

```text
Repeated Retries

Same Offset Failing

Growing Consumer Lag

Repeated Exceptions
```

---

# Handling Poison Pills

Recommended flow:

```text
Failure
   ↓
Retry Few Times
   ↓
Move To DLT
```

---

# Best Practices

```text
Use Schema Validation

Use DLT

Limit Retries

Monitor Failure Rates
```

---

# Exception Handling

Exception handling controls how Kafka applications respond to failures.

---

# Why Exception Handling?

Without exception handling:

```text
Consumer Crash

Application Failure

Message Loss
```

---

# Exception Handling Goals

```text
Graceful Recovery

Logging

Retries

DLT Routing
```

---

# Common Kafka Exceptions

---

## SerializationException

Occurs during serialization.

---

Example:

```text
Java Object
      ↓
JSON Conversion Failure
```

---

## DeserializationException

Occurs during deserialization.

---

Example:

```text
Invalid JSON
```

---

## TimeoutException

Occurs when broker response is delayed.

---

Example:

```text
Network Slow
```

---

## RetriableException

Temporary failure.

---

Examples:

```text
Leader Election

Network Glitch
```

---

Can be retried safely.

---

## Non-Retriable Exception

Permanent failure.

---

Examples:

```text
Invalid Data

Schema Violation
```

---

Should be routed to DLT.

---

# Exception Handling Flow

```text
Consume
   ↓
Exception
   ↓
Classify
```

---

Retriable:

```text
Retry
```

---

Non-Retriable:

```text
DLT
```

---

# Spring Kafka Error Handler

```java
@Bean
public DefaultErrorHandler
errorHandler() {

    return new DefaultErrorHandler();
}
```

---

# Fixed Retry Handler

```java
@Bean
public DefaultErrorHandler
errorHandler() {

    return new DefaultErrorHandler(
        new FixedBackOff(
            2000L,
            3));
}
```

---

Meaning:

```text
Retry 3 Times

2 Second Delay
```

---

# Custom Exception Handling

```java
try {

    processMessage();

}
catch(Exception ex){

    log.error(
        "Processing Failed",
        ex);
}
```

---

# Global Error Handling Strategy

```text
Retry Temporary Errors
      ↓
Send Permanent Failures To DLT
      ↓
Alert Operations Team
```

---

# Recovery Mechanisms

Recovery strategies include:

```text
Retries

DLQ

DLT

Replay Messages

Manual Reprocessing
```

---

# Replay Strategy

Failed messages from DLT:

```text
Fix Issue
      ↓
Replay Message
      ↓
Original Topic
```

---

# End-to-End Recovery Flow

```text
Kafka Topic
      ↓
Consumer
      ↓
Failure
      ↓
Retry
      ↓
Failure
      ↓
DLT
      ↓
Investigation
      ↓
Replay
```

---

# Production Architecture

```text
Orders Topic
      ↓
Consumer
      ↓
Retry Topic
      ↓
DLT
      ↓
Operations Team
```

---

# Best Practices

## Retry Mechanisms

```text
Use Exponential Backoff

Avoid Infinite Retries

Limit Retry Attempts
```

---

## DLT

```text
Always Use DLT

Store Error Metadata

Monitor DLT Growth
```

---

## Poison Pills

```text
Validate Input

Use Schema Registry

Move Poison Pills To DLT
```

---

## Exception Handling

```text
Separate Retriable Errors

Separate Non-Retriable Errors

Log Properly
```

---

# Common Interview Questions

## What is a Retry Mechanism?

A process of reattempting failed message processing after a delay.

---

## What is a Dead Letter Queue (DLQ)?

A queue that stores messages that failed all retry attempts.

---

## What is a Dead Letter Topic (DLT)?

Kafka's implementation of a Dead Letter Queue using a Kafka topic.

---

## What is a Poison Pill Message?

A message that repeatedly fails processing and cannot be consumed successfully.

---

## Why are Poison Pill messages dangerous?

They can cause infinite retries, consumer lag, and processing blockage.

---

## What is the difference between a retriable and non-retriable exception?

| Retriable | Non-Retriable |
|------------|--------------|
| Temporary Failure | Permanent Failure |
| Retry Recommended | Send To DLT |
| Network Issue | Invalid Data |

---

## Why use Exponential Backoff?

To reduce load on dependent systems during repeated failures.

---

## What should happen after retries are exhausted?

The message should be moved to a DLT for investigation and recovery.

---

## How do you handle failed messages in production?

```text
Retry

DLT

Monitoring

Replay Mechanism
```

---

# Summary

Error Handling & Recovery is essential for building reliable Kafka systems.

Core Topics:

- Retry Mechanisms
- Dead Letter Queue (DLQ)
- Dead Letter Topic (DLT)
- Poison Pill Messages
- Exception Handling

Retry Mechanisms:

```text
Immediate Retry

Fixed Delay Retry

Exponential Backoff

Retry Topics
```

Dead Letter Queue:

```text
Stores Failed Messages

Prevents Consumer Blocking
```

Dead Letter Topic:

```text
Kafka-Based DLQ

Supports Recovery

Preserves Messages
```

Poison Pill Messages:

```text
Invalid Data

Repeated Failures

Require Isolation
```

Exception Handling:

```text
Retriable Exceptions

Non-Retriable Exceptions

Recovery Logic
```

Production Recommendations:

```text
Use Retry Topics

Use Exponential Backoff

Always Configure DLT

Monitor Failure Rates

Validate Schemas

Implement Replay Mechanisms
```

A robust error handling and recovery strategy ensures high availability, reliability, fault tolerance, and operational stability in Kafka-based event-driven systems.