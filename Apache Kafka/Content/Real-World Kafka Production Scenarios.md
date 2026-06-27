
This section covers real production issues that Kafka engineers frequently encounter.

These scenarios are extremely important for:

```text
Production Support Engineers

Backend Developers

Senior Engineers

Kafka Administrators

System Design Interviews
```

Most Kafka interview questions for experienced professionals are based on production incidents rather than theoretical concepts.

---

# Why Learn Production Scenarios?

In production, problems are rarely:

```text
What is a Topic?

What is a Partition?
```

Instead they are:

```text
Why is consumer lag increasing?

Why are duplicate messages appearing?

Why is ordering broken?

Why did a broker crash?

Why are consumers rebalancing continuously?
```

Understanding these scenarios separates a beginner from an experienced Kafka engineer.

---

# Duplicate Messages

One of the most common Kafka production issues.

---

## Problem

Consumers receive the same message multiple times.

Example:

```text
Order #1001 Processed Twice
```

Result:

```text
Double Payment

Duplicate Emails

Incorrect Analytics
```

---

## Example Scenario

Producer sends:

```json
{
  "orderId":1001
}
```

---

Network timeout occurs.

Producer thinks:

```text
Message Failed
```

---

Producer retries.

Kafka already stored the message.

Result:

```text
Duplicate Messages
```

---

# Root Causes

## Producer Retries

```properties
retries=3
```

---

Producer may resend messages.

---

## Consumer Reprocessing

Consumer:

```text
Processes Message
```

but crashes before:

```text
Offset Commit
```

---

After restart:

```text
Same Message Reprocessed
```

---

## Rebalancing

Consumer ownership changes.

Offsets may be reprocessed.

---

## Transaction Failures

Incomplete transaction handling.

---

# Detection

Check:

```text
Duplicate Order IDs

Duplicate Event IDs

Duplicate Business Actions
```

---

# Solutions

## Enable Idempotent Producer

```properties
enable.idempotence=true
```

---

Prevents duplicate writes.

---

## Use Transactions

```properties
transactional.id=payment-service
```

---

Supports Exactly Once Semantics.

---

## Implement Idempotent Consumers

Example:

```text
Store Processed Event IDs
```

---

If already processed:

```text
Skip Event
```

---

## Use Unique Event IDs

Example:

```json
{
  "eventId":"abc123"
}
```

---

# Interview Question

### How do you prevent duplicate messages in Kafka?

Answer:

```text
Enable Idempotent Producer

Use Transactions

Implement Idempotent Consumers

Use Unique Event IDs
```

---

# Message Loss

The most critical Kafka problem.

---

## Problem

Producer successfully sends message.

Consumer never receives it.

---

Result:

```text
Lost Orders

Lost Payments

Missing Audit Logs
```

---

# Example

Producer sends:

```json
{
  "paymentId":101
}
```

---

Broker crashes before replication completes.

Message disappears.

---

# Root Causes

## ACK=0

```properties
acks=0
```

---

Producer never waits for acknowledgement.

---

## ACK=1

Leader acknowledges.

Followers have not replicated yet.

Leader crashes.

Data may be lost.

---

## Replication Factor = 1

No backup copy exists.

---

## Unclean Leader Election

Out-of-sync replica becomes leader.

---

Data may disappear.

---

## Log Retention Misconfiguration

Messages deleted earlier than expected.

---

# Detection

Check:

```text
Producer Logs

Broker Logs

Offset Gaps

Missing Business Records
```

---

# Prevention

## Use ACK=all

```properties
acks=all
```

---

## Replication Factor >= 3

```text
Recommended Production Value
```

---

## Enable ISR Protection

```properties
min.insync.replicas=2
```

---

## Disable Unclean Leader Election

```properties
unclean.leader.election.enable=false
```

---

# Interview Question

### How do you avoid message loss?

Answer:

```text
ACK=all

Replication Factor >=3

ISR Protection

Disable Unclean Election
```

---

# Consumer Lag

The most monitored Kafka metric.

---

## What is Consumer Lag?

Difference between:

```text
Latest Offset

Consumer Offset
```

---

Formula:

```text
Lag = Latest Offset - Consumed Offset
```

---

# Example

Partition:

```text
Latest Offset = 5000
```

Consumer:

```text
Current Offset = 4000
```

---

Lag:

```text
1000 Messages
```

---

# Symptoms

```text
Delayed Processing

Slow Dashboards

Stale Data
```

---

# Root Causes

## Slow Consumer

Processing slower than incoming messages.

---

## Too Few Consumers

```text
100 Partitions

2 Consumers
```

---

## Database Bottlenecks

Consumer waits for:

```text
Database

REST API

External Service
```

---

## Large Messages

Increase processing time.

---

# Detection

Monitor:

```text
Consumer Lag
```

using:

```text
Prometheus

Grafana

Burrow
```

---

# Solutions

## Add More Consumers

```text
Horizontal Scaling
```

---

## Increase Partitions

More parallelism.

---

## Optimize Business Logic

Reduce processing time.

---

## Batch Processing

Process multiple records together.

---

# Interview Question

### Consumer lag keeps increasing. What would you do?

Answer:

```text
Check Processing Speed

Check Database Latency

Scale Consumers

Increase Partitions
```

---

# Rebalancing Issues

A major source of production instability.

---

# Problem

Consumers repeatedly rebalance.

---

Symptoms:

```text
Processing Pauses

Lag Increases

Throughput Drops
```

---

# Example

Consumer crashes.

---

Group changes:

```text
Consumer Joined

Consumer Left
```

---

Rebalance triggered.

---

# Root Causes

## Frequent Consumer Restarts

---

## Long Processing Time

Consumer misses heartbeat.

---

## Configuration Problems

```properties
session.timeout.ms
```

too small.

---

## Network Instability

Heartbeats lost.

---

# Detection

Logs show:

```text
Rebalance Started

Partition Revoked

Partition Assigned
```

---

# Solutions

## Static Membership

```properties
group.instance.id=node1
```

---

Reduces unnecessary rebalances.

---

## Cooperative Rebalancing

```properties
partition.assignment.strategy=
CooperativeStickyAssignor
```

---

Minimizes partition movement.

---

## Increase Session Timeout

```properties
session.timeout.ms=30000
```

---

## Reduce Processing Time

Avoid long-running tasks in poll loop.

---

# Interview Question

### How do you reduce Kafka rebalancing?

Answer:

```text
Static Membership

Cooperative Rebalancing

Stable Consumers

Proper Timeout Settings
```

---

# Broker Crash

One of the most serious production incidents.

---

# Problem

Kafka broker becomes unavailable.

---

# Example

Broker:

```text
Broker-2
```

crashes.

---

Hosted partitions:

```text
Leader Partitions
```

become unavailable.

---

# What Happens?

Controller detects failure.

---

Leader election starts.

---

Follower promoted:

```text
New Leader
```

---

Traffic resumes.

---

# Root Causes

## Hardware Failure

---

## Disk Full

---

## JVM Out Of Memory

---

## Network Issues

---

## OS Crash

---

# Detection

Monitor:

```text
Broker Health

JMX Metrics

System Logs
```

---

# Recovery

## Automatic Leader Election

Kafka promotes ISR member.

---

## Restart Broker

---

## Verify ISR Health

---

## Verify Replication

---

# Prevention

```text
Replication Factor >=3

Multiple Brokers

Monitoring

Capacity Planning
```

---

# Interview Question

### What happens when a broker crashes?

Answer:

```text
Controller Detects Failure

Leader Election Occurs

ISR Replica Becomes Leader

Consumers Continue Processing
```

---

# Slow Consumers

A very common production problem.

---

# Problem

Consumer cannot process records fast enough.

---

# Symptoms

```text
Lag Increasing

Backlog Growing

Delayed Processing
```

---

# Example

Incoming:

```text
10,000 Messages/Second
```

---

Consumer handles:

```text
2,000 Messages/Second
```

---

Backlog grows.

---

# Root Causes

## Slow Database

---

## External API Calls

---

## Heavy Computation

---

## Large Messages

---

## Single Consumer

---

# Solutions

## Increase Consumers

---

## Increase Partitions

---

## Use Batch Processing

---

## Optimize Queries

---

## Introduce Caching

---

# Interview Question

### How do you troubleshoot slow consumers?

Answer:

```text
Measure Processing Time

Check Database Calls

Check API Latency

Scale Consumers
```

---

# High Throughput Systems

A common architecture challenge.

---

# Problem

System must handle:

```text
Millions Of Messages Per Second
```

---

# Example

E-Commerce Platform:

```text
Orders

Payments

Inventory

Notifications
```

---

Millions of daily events.

---

# Design Considerations

## Large Partition Count

Example:

```text
100 Partitions
```

---

## Multiple Brokers

Example:

```text
10 Brokers
```

---

## Consumer Groups

Parallel processing.

---

## Compression

```properties
compression.type=zstd
```

---

## Batching

```properties
linger.ms=10
batch.size=65536
```

---

# Throughput Optimization

Producer:

```text
Compression

Batching

Async Send
```

---

Consumer:

```text
Batch Processing

Parallel Processing
```

---

Broker:

```text
SSD Storage

Large Page Cache

Fast Network
```

---

# Interview Question

### How would you design Kafka for 1 million messages per second?

Answer:

```text
Many Partitions

Multiple Brokers

Compression

Batching

Consumer Groups
```

---

# Event Ordering Problems

One of the most misunderstood Kafka issues.

---

# Problem

Messages arrive out of expected order.

---

# Example

Expected:

```text
Order Created

Order Paid

Order Shipped
```

---

Received:

```text
Order Paid

Order Created

Order Shipped
```

---

Business logic breaks.

---

# Root Causes

## Multiple Partitions

Kafka guarantees ordering only:

```text
Within A Partition
```

---

## Different Keys

Messages routed differently.

---

## Multiple Producers

Concurrent publishing.

---

## Retry Scenarios

Retries may affect perceived ordering.

---

# Detection

Check:

```text
Partition IDs

Keys

Producer Logs
```

---

# Solutions

## Use Same Key

Example:

```text
orderId=1001
```

---

All events go to same partition.

---

## Maintain Partition Affinity

---

## Avoid Random Keys

When ordering matters.

---

## Use Single Producer For Ordered Workflows

When strict ordering is required.

---

# Example

Good:

```json
{
  "orderId":1001
}
```

---

Partition Key:

```text
1001
```

---

All order events stay ordered.

---

# Interview Question

### Does Kafka guarantee ordering?

Answer:

```text
Yes

But Only Within A Single Partition
```

---

# Production Incident Troubleshooting Framework

When Kafka issues occur:

---

## Step 1

Identify Symptom

```text
Lag

Duplicates

Data Loss

Rebalancing
```

---

## Step 2

Collect Metrics

```text
Consumer Lag

ISR

Broker Health

Throughput
```

---

## Step 3

Check Logs

```text
Producer Logs

Consumer Logs

Broker Logs
```

---

## Step 4

Identify Root Cause

```text
Configuration

Network

Application

Infrastructure
```

---

## Step 5

Apply Fix

```text
Scale

Optimize

Reconfigure
```

---

## Step 6

Add Monitoring

Prevent future incidents.

---

# Common Interview Questions

## Why do duplicate messages happen?

Because of retries, consumer restarts, offset issues, or transaction failures.

---

## How do you prevent duplicates?

```text
Idempotent Producer

Transactions

Idempotent Consumers
```

---

## What causes message loss?

```text
ACK=0

Replication Factor=1

Broker Failure

Unclean Election
```

---

## How do you reduce consumer lag?

```text
More Consumers

More Partitions

Optimize Processing
```

---

## Why does rebalancing occur?

```text
Consumer Join

Consumer Leave

Partition Changes
```

---

## What happens when a broker crashes?

```text
Leader Election

ISR Promotion

Processing Continues
```

---

## How do you preserve event ordering?

```text
Use Same Key

Same Partition

Partition Affinity
```

---

# Summary

Most Kafka production incidents fall into these categories:

- Duplicate Messages
- Message Loss
- Consumer Lag
- Rebalancing Issues
- Broker Crash
- Slow Consumers
- High Throughput Systems
- Event Ordering Problems

Critical Production Configurations:

```properties
acks=all

enable.idempotence=true

replication.factor=3

min.insync.replicas=2

unclean.leader.election.enable=false
```

Most Important Monitoring Metrics:

```text
Consumer Lag

ISR Count

Broker Availability

Disk Usage

Throughput

Latency

Rebalance Frequency
```

If you can confidently troubleshoot these eight scenarios, you will be prepared for most real-world Kafka production incidents and senior-level Kafka interview discussions.