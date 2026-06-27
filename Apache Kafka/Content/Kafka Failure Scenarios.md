
One of Kafka's greatest strengths is its ability to continue operating even when failures occur.

Kafka was specifically designed for:

```text
Fault Tolerance

High Availability

Distributed Processing

Failure Recovery
```

In real-world production environments, failures are inevitable:

```text
Broker Crashes

Consumer Crashes

Producer Failures

Network Issues

Hardware Failures

Disk Failures

Datacenter Outages
```

Kafka provides multiple mechanisms to detect, isolate, and recover from these failures.

---

# What is a Failure Scenario?

## Definition

A failure scenario is any situation where one or more Kafka components stop functioning correctly, potentially affecting message delivery, processing, or storage.

---

# Types of Kafka Failures

Kafka failures generally fall into:

```text
Broker Failure

Consumer Failure

Producer Failure

Network Failure

Storage Failure

Data Loss Scenarios
```

---

# Failure Handling Goals

Kafka aims to provide:

```text
Data Durability

High Availability

Automatic Recovery

Minimal Downtime

Reliable Processing
```

---

# Failure Architecture

```text
Producer
    ↓
Broker
    ↓
Consumer
```

Failures can occur at any layer.

---

# Broker Failure

Broker failure is one of the most important failure scenarios.

---

# What is Broker Failure?

A broker failure occurs when a Kafka broker becomes unavailable.

---

Causes:

```text
Server Crash

Power Failure

Disk Failure

Operating System Failure

JVM Crash

Cloud Instance Failure
```

---

# Example

Cluster:

```text
Broker-1

Broker-2

Broker-3
```

---

Topic:

```text
Orders
```

---

Partition:

```text
Partition-0
```

---

Leader:

```text
Broker-1
```

---

Followers:

```text
Broker-2

Broker-3
```

---

If:

```text
Broker-1 Fails
```

leader becomes unavailable.

---

# How Kafka Detects Broker Failure?

Kafka controllers continuously monitor brokers.

---

Detection mechanisms:

```text
Heartbeats

Controller Communication
```

---

If heartbeat stops:

```text
Broker Marked Offline
```

---

# Broker Failure Recovery

Kafka automatically elects a new leader.

---

Before Failure:

```text
Leader → Broker-1

Followers → Broker-2, Broker-3
```

---

After Failure:

```text
Leader → Broker-2
```

---

Consumers and producers continue operating.

---

# Impact of Broker Failure

With replication:

```text
Minimal Impact
```

---

Without replication:

```text
Partition Becomes Unavailable
```

---

# Best Practices

---

## Replication Factor ≥ 3

---

## Monitor Broker Health

---

## Keep ISR Healthy

---

## Avoid Single Broker Clusters

---

# Consumer Failure

Consumers frequently fail in distributed systems.

---

# What is Consumer Failure?

A consumer failure occurs when a consumer stops processing messages.

---

Causes:

```text
Application Crash

Memory Leak

Server Failure

Network Failure

Deployment Error
```

---

# Example

Consumer Group:

```text
Consumer-1

Consumer-2

Consumer-3
```

---

Topic:

```text
Orders
```

---

Partitions:

```text
P0

P1

P2
```

---

Assignments:

```text
Consumer-1 → P0

Consumer-2 → P1

Consumer-3 → P2
```

---

Consumer-2 crashes.

---

# How Kafka Detects Consumer Failure?

Consumers send:

```text
Heartbeats
```

to the group coordinator.

---

If heartbeat stops:

```text
Consumer Marked Dead
```

---

# Consumer Failure Recovery

Kafka triggers:

```text
Rebalancing
```

---

After Rebalance:

```text
Consumer-1 → P0, P1

Consumer-3 → P2
```

---

Processing continues.

---

# Potential Problems

---

## Duplicate Processing

Possible with:

```text
At-Least-Once Delivery
```

---

## Temporary Pause During Rebalance

---

# Best Practices

---

## Use Static Membership

---

## Tune Heartbeats

---

## Use Cooperative Rebalancing

---

# Producer Failure

Producers may fail before messages reach Kafka.

---

# What is Producer Failure?

Producer failure occurs when the producer application crashes or cannot deliver records.

---

Causes:

```text
Application Crash

Network Issues

Broker Unavailability

Out Of Memory

Container Failure
```

---

# Producer Failure Scenarios

---

## Failure Before Send

Message never reaches Kafka.

---

Result:

```text
Message Lost
```

unless application retries.

---

## Failure During Send

Producer sends request but crashes before receiving acknowledgment.

---

Result:

```text
Unknown Delivery State
```

---

Producer cannot determine whether the message was stored.

---

# Producer Recovery

Kafka provides:

```text
Retries

Acknowledgements

Idempotent Producer

Transactions
```

---

# Recommended Settings

```properties
acks=all

enable.idempotence=true

retries=Integer.MAX_VALUE
```

---

# Producer Failure Example

Without idempotence:

```text
Retry
```

may create:

```text
Duplicate Messages
```

---

With idempotence:

```text
Duplicates Prevented
```

---

# Best Practices

---

## Enable Idempotence

---

## Configure Retries

---

## Use Transactions For Critical Systems

---

# Network Failure

Distributed systems heavily depend on networking.

---

# What is Network Failure?

A network failure occurs when communication between Kafka components is disrupted.

---

Affected communications:

```text
Producer ↔ Broker

Consumer ↔ Broker

Broker ↔ Broker
```

---

# Causes

```text
Network Congestion

Packet Loss

Firewall Issues

Router Failures

Datacenter Connectivity Issues
```

---

# Producer-Broker Network Failure

Example:

```text
Producer Cannot Reach Broker
```

---

Result:

```text
Send Failure
```

---

Recovery:

```text
Retry
```

---

# Consumer-Broker Network Failure

Consumer cannot communicate with broker.

---

Result:

```text
Heartbeat Failure
```

---

Coordinator eventually removes consumer.

---

# Broker-Broker Network Failure

Very important scenario.

---

Example:

```text
Leader Cannot Reach Followers
```

---

Result:

```text
Replication Lag
```

---

May cause:

```text
ISR Shrinkage
```

---

# Network Partition

One of the most dangerous distributed system failures.

---

# What is Network Partition?

Cluster splits into isolated groups.

---

Example:

```text
Broker-1

Broker-2
```

cannot communicate with:

```text
Broker-3

Broker-4
```

---

Result:

```text
Cluster Partitioning
```

---

Kafka relies on:

```text
Controller Quorum (KRaft)

or

ZooKeeper (Legacy)
```

to prevent split-brain scenarios.

---

# Recovery Mechanisms

---

## Retry Requests

---

## Leader Election

---

## ISR Management

---

## Controller Decisions

---

# Data Loss Scenarios

Preventing data loss is a primary Kafka objective.

---

# What is Data Loss?

Data loss occurs when a message that was expected to be stored or processed becomes permanently unavailable.

---

# Common Data Loss Scenarios

---

## ACK=0

Producer does not wait for confirmation.

---

Flow:

```text
Producer Sends Message

Broker Fails

Message Lost
```

---

# Example

```properties
acks=0
```

---

Risk:

```text
High Data Loss Probability
```

---

# ACK=1 Scenario

Producer waits only for leader acknowledgment.

---

Flow:

```text
Leader Stores Message

Producer Receives ACK

Leader Crashes

Followers Not Updated
```

---

Result:

```text
Potential Data Loss
```

---

# Unclean Leader Election

Dangerous configuration.

---

Scenario:

```text
Leader Fails

ISR Unavailable

Out-Of-Sync Replica Becomes Leader
```

---

Result:

```text
Lost Messages
```

---

Configuration:

```properties
unclean.leader.election.enable=true
```

---

Production Recommendation:

```properties
unclean.leader.election.enable=false
```

---

# Replication Factor = 1

No replicas exist.

---

If broker fails:

```text
Partition Lost
```

---

Risk:

```text
Very High
```

---

# Disk Failure

Broker disk becomes corrupted.

---

Without replicas:

```text
Permanent Data Loss
```

---

With replicas:

```text
Recovery Possible
```

---

# How to Prevent Data Loss?

---

## Replication Factor = 3

---

## ACK=all

---

## Min ISR ≥ 2

---

## Idempotent Producer

---

## Transactions

---

## Disable Unclean Leader Election

---

# Recovery Mechanisms

Kafka includes multiple recovery mechanisms.

---

# Leader Election Recovery

When leader fails:

```text
Follower Becomes Leader
```

---

Benefits:

```text
Minimal Downtime
```

---

# Replication Recovery

Follower catches up with leader.

---

Flow:

```text
Leader
   ↓
Follower Synchronization
```

---

# Consumer Recovery

After restart:

```text
Consumer Reads Stored Offset
```

---

Continues from:

```text
Last Committed Offset
```

---

# Producer Recovery

Retries failed sends.

---

Example:

```text
Message Send Failed
```

↓

```text
Retry
```

↓

```text
Message Delivered
```

---

# Transaction Recovery

Kafka transactions support recovery after failures.

---

Scenario:

```text
Producer Crashes Mid-Transaction
```

---

Kafka automatically:

```text
Aborts Transaction
```

---

Consumers never see:

```text
Partial Results
```

---

# Log Recovery

Kafka persists messages on disk.

---

After restart:

```text
Log Segments Recovered
```

---

Kafka rebuilds:

```text
Indexes

Metadata

Offsets
```

---

# Controller Recovery

In KRaft:

```text
Controller Quorum
```

elects a new controller if necessary.

---

Benefits:

```text
Cluster Continues Operating
```

---

# Recovery Architecture

```text
Failure
   ↓
Detection
   ↓
Leader Election
   ↓
Replication Recovery
   ↓
Normal Operation
```

---

# Real-World Failure Example

Scenario:

```text
3 Brokers

RF=3

Min ISR=2

ACK=all
```

---

Broker-1 crashes.

---

Kafka:

```text
Elects New Leader

Maintains Availability

No Data Loss
```

---

Consumers continue reading.

---

Producers continue writing.

---

# Failure Tolerance Matrix

| Failure | Kafka Handles Automatically? | Data Loss Risk |
|----------|-----------------------------|----------------|
| Consumer Failure | Yes | Low |
| Producer Failure | Partially | Medium |
| Broker Failure (RF > 1) | Yes | Low |
| Broker Failure (RF = 1) | No | High |
| Network Failure | Usually | Medium |
| Disk Failure With Replicas | Yes | Low |
| Disk Failure Without Replicas | No | High |

---

# Failure Prevention Checklist

---

## Replication Factor = 3

---

## ACK = all

---

## Min ISR ≥ 2

---

## Enable Idempotence

---

## Use Transactions

---

## Disable Unclean Leader Election

---

## Monitor Broker Health

---

## Monitor Consumer Lag

---

## Monitor Replication Lag

---

## Regularly Test Disaster Recovery

---

# Common Interview Questions

## What happens when a Kafka broker fails?

Kafka elects a new leader from the ISR and continues processing.

---

## How does Kafka detect consumer failure?

Through missed heartbeats to the group coordinator.

---

## What happens after consumer failure?

Kafka performs a rebalance and reassigns partitions.

---

## How can producer failures be handled?

Using retries, acknowledgements, idempotent producers, and transactions.

---

## What is the most dangerous data loss scenario?

Replication factor 1 combined with broker failure.

---

## Why is ACK=all recommended?

Because it provides the strongest durability guarantee.

---

## What is unclean leader election?

Election of an out-of-sync replica as leader, potentially causing data loss.

---

## How can data loss be prevented?

```text
Replication Factor = 3

ACK = all

Min ISR ≥ 2

Idempotent Producer

Transactions
```

---

## What is Kafka's primary recovery mechanism?

```text
Replication + Leader Election
```

---

# Summary

Kafka Failure Scenarios describe how Kafka handles failures while maintaining durability and availability.

Core Topics:

- Broker Failure
- Consumer Failure
- Producer Failure
- Network Failure
- Data Loss Scenarios
- Recovery Mechanisms

Failure Detection Methods:

```text
Heartbeats

Controller Monitoring

ISR Tracking
```

Recovery Mechanisms:

```text
Leader Election

Replication Recovery

Consumer Rebalancing

Producer Retries

Transaction Recovery
```

Major Data Loss Risks:

```text
ACK=0

ACK=1 With Leader Failure

RF=1

Unclean Leader Election

Disk Failure Without Replicas
```

Production Recommendations:

```text
Replication Factor = 3

ACK = all

Min ISR ≥ 2

Enable Idempotence

Use Transactions

Disable Unclean Leader Election
```

Kafka's fault-tolerant architecture enables it to survive broker crashes, consumer failures, producer issues, and many network problems while continuing to provide reliable, durable, and highly available event streaming at scale.