
High Availability (HA) is one of Kafka's most important design goals.

Kafka is used in mission-critical systems such as:

```text
Banking Systems

Payment Platforms

Stock Trading Systems

E-Commerce Applications

Fraud Detection Systems

Telecommunication Platforms
```

In such systems:

```text
Downtime Is Expensive
```

and often unacceptable.

Kafka achieves High Availability through:

```text
Replication

ISR (In-Sync Replicas)

Leader Election

Failover

Multi-Broker Clusters

Disaster Recovery Strategies
```

---

# What is High Availability?

## Definition

High Availability (HA) is the ability of a system to remain operational and accessible even when components fail.

---

Goal:

```text
Minimal Downtime

Continuous Operation

Fault Tolerance
```

---

# Example

Without HA:

```text
Broker Failure
     ↓
Application Stops
```

---

With HA:

```text
Broker Failure
     ↓
Automatic Recovery
     ↓
Application Continues
```

---

# Availability Calculation

Availability is often expressed as:

```text
Percentage Uptime
```

---

Examples:

| Availability | Downtime Per Year |
|-------------|------------------|
| 99% | ~3.65 Days |
| 99.9% | ~8.7 Hours |
| 99.99% | ~52 Minutes |
| 99.999% | ~5 Minutes |

---

Kafka clusters often target:

```text
99.99%

or

99.999%
```

---

# High Availability Architecture

```text
Producer
     ↓
Kafka Cluster
     ↓
Consumer
```

---

HA requires:

```text
Multiple Brokers

Replication

Automatic Failover
```

---

# Building Blocks of Kafka High Availability

Kafka HA is built on:

```text
Replication

ISR

Leader Election

Failover

Multi-Broker Clusters

Disaster Recovery
```

---

# Replication

Replication is Kafka's primary high availability mechanism.

---

# What is Replication?

Replication means maintaining multiple copies of the same partition on different brokers.

---

# Example

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

Replication Factor:

```text
3
```

---

Copies:

```text
Broker-1 → Leader

Broker-2 → Follower

Broker-3 → Follower
```

---

Architecture:

```text
            Partition-0

      Broker-1 (Leader)
          /       \
         /         \
        /           \
Broker-2         Broker-3
(Follower)       (Follower)
```

---

# Why Replication?

Without replication:

```text
Broker Failure
      ↓
Data Unavailable
```

---

With replication:

```text
Broker Failure
      ↓
Replica Takes Over
```

---

# Replication Factor

Configuration:

```properties
replication.factor
```

---

Common values:

| RF | Description |
|-----|------------|
| 1 | No Redundancy |
| 2 | Basic Redundancy |
| 3 | Production Standard |
| 5+ | Large Critical Systems |

---

Production Recommendation:

```text
RF = 3
```

---

# Replication Workflow

Producer sends message:

```text
Producer
    ↓
Leader
    ↓
Followers
```

---

Followers replicate data.

---

Once synchronization occurs:

```text
Message Durable
```

---

# Benefits

---

## Fault Tolerance

---

## Data Durability

---

## Automatic Recovery

---

## High Availability

---

# ISR (In-Sync Replica)

ISR is a critical component of Kafka HA.

---

# What is ISR?

ISR stands for:

```text
In-Sync Replica
```

---

ISR contains replicas that are fully synchronized with the leader.

---

# Example

Partition:

```text
Leader → Broker-1

Follower → Broker-2

Follower → Broker-3
```

---

All synchronized:

```text
ISR = {1,2,3}
```

---

# ISR Architecture

```text
Leader
   ↓
Follower A
   ↓
Follower B
```

---

All caught up:

```text
Inside ISR
```

---

# Replica Lag Scenario

Broker-3 becomes slow.

---

State:

```text
Leader → Broker-1

Follower → Broker-2

Slow Replica → Broker-3
```

---

ISR becomes:

```text
ISR = {1,2}
```

---

Broker-3 removed from ISR.

---

# Why ISR Matters?

Only ISR members can become leaders.

---

Benefits:

```text
Prevents Data Loss

Ensures Consistency

Supports Safe Failover
```

---

# Minimum ISR

Configuration:

```properties
min.insync.replicas
```

---

Example:

```properties
min.insync.replicas=2
```

---

With:

```text
RF=3
```

---

At least:

```text
2 Replicas
```

must acknowledge writes.

---

# Recommended Configuration

```properties
acks=all

min.insync.replicas=2

replication.factor=3
```

---

# Failover

Failover is Kafka's automatic recovery mechanism.

---

# What is Failover?

Failover is the process of automatically switching workload from a failed component to a healthy component.

---

# Example

Before failure:

```text
Leader → Broker-1
```

---

Broker-1 crashes.

---

After failover:

```text
Leader → Broker-2
```

---

Applications continue running.

---

# Failover Process

```text
Failure
   ↓
Detection
   ↓
Leader Election
   ↓
Traffic Redirected
   ↓
Normal Operation
```

---

# Failover Example

Initial State:

```text
Broker-1 → Leader

Broker-2 → Follower

Broker-3 → Follower
```

---

Broker-1 fails.

---

Controller detects failure.

---

New state:

```text
Broker-2 → Leader

Broker-3 → Follower
```

---

# Failover Characteristics

---

## Automatic

---

## Fast

---

## Transparent To Applications

---

## Minimal Downtime

---

# Types of Failover

---

## Broker Failover

---

## Controller Failover

---

## Network Failover

---

# Multi Broker Setup

A multi-broker cluster is essential for High Availability.

---

# What is Multi Broker Setup?

A Kafka cluster containing multiple brokers.

---

# Single Broker Cluster

```text
Broker-1
```

---

Problem:

```text
Single Point Of Failure
```

---

If broker fails:

```text
Cluster Down
```

---

# Multi Broker Cluster

```text
Broker-1

Broker-2

Broker-3
```

---

Benefits:

```text
Replication

Failover

Load Distribution
```

---

# Production Architecture

```text
Producer
     ↓
 -----------------
| Broker-1        |
| Broker-2        |
| Broker-3        |
 -----------------
     ↓
Consumer
```

---

# Why Multiple Brokers?

---

## Better Availability

---

## Better Scalability

---

## Better Fault Tolerance

---

## Better Storage Capacity

---

# Recommended Broker Count

Development:

```text
1 Broker
```

---

Testing:

```text
3 Brokers
```

---

Production:

```text
3+ Brokers
```

---

Large Scale:

```text
5+

10+

20+
```

---

# Broker Distribution Example

Topic:

```text
Orders
```

---

Partition-0:

```text
Leader → Broker-1
```

---

Partition-1:

```text
Leader → Broker-2
```

---

Partition-2:

```text
Leader → Broker-3
```

---

Benefits:

```text
Balanced Load
```

---

# Disaster Recovery (DR)

Disaster Recovery protects against large-scale failures.

---

# What is Disaster Recovery?

Disaster Recovery is the ability to recover Kafka operations after catastrophic events.

---

Examples:

```text
Datacenter Failure

Cloud Region Failure

Natural Disaster

Massive Network Outage
```

---

# Why Disaster Recovery?

Replication protects:

```text
Broker Failure
```

---

But not:

```text
Entire Datacenter Failure
```

---

# Disaster Recovery Architecture

```text
Primary Cluster
      ↓
Replication
      ↓
Secondary Cluster
```

---

# Multi-Datacenter Setup

```text
Datacenter-A
      ↓
Replication
      ↓
Datacenter-B
```

---

Benefits:

```text
Geographic Redundancy
```

---

# Active-Passive DR

Most common strategy.

---

Architecture:

```text
Primary Cluster
       ↓
Replicates Data
       ↓
Backup Cluster
```

---

Normal:

```text
Primary Handles Traffic
```

---

Failure:

```text
Backup Activated
```

---

# Active-Active DR

Both clusters process traffic.

---

Architecture:

```text
Cluster-A
     ↔
Cluster-B
```

---

Benefits:

```text
Higher Availability

Better Resource Utilization
```

---

Challenges:

```text
Complex Synchronization
```

---

# MirrorMaker 2

Kafka's built-in disaster recovery tool.

---

# What is MirrorMaker 2?

A Kafka tool used to replicate topics between clusters.

---

Architecture:

```text
Cluster-A
     ↓
MirrorMaker 2
     ↓
Cluster-B
```

---

Uses:

```text
Disaster Recovery

Migration

Geo Replication
```

---

# Recovery Objectives

Important DR metrics.

---

## RTO

Recovery Time Objective.

---

Question:

```text
How Fast Can We Recover?
```

---

Example:

```text
15 Minutes
```

---

# RPO

Recovery Point Objective.

---

Question:

```text
How Much Data Can Be Lost?
```

---

Example:

```text
0 Messages
```

---

# HA Best Practices

---

## Use Replication Factor = 3

---

## Configure Min ISR ≥ 2

---

## Use ACK = all

---

## Disable Unclean Leader Election

---

## Use Multiple Brokers

---

## Monitor ISR

---

## Monitor Replication Lag

---

## Monitor Broker Health

---

## Use MirrorMaker For DR

---

## Test Failover Regularly

---

# High Availability Configuration Example

```properties
replication.factor=3

min.insync.replicas=2

acks=all

unclean.leader.election.enable=false
```

---

This is one of the most common production-grade HA configurations.

---

# Common Interview Questions

## What is High Availability in Kafka?

The ability of Kafka to remain operational despite failures.

---

## What provides High Availability in Kafka?

```text
Replication

ISR

Leader Election

Failover
```

---

## What is the recommended replication factor?

```text
3
```

---

## What is ISR?

In-Sync Replicas that are fully synchronized with the leader.

---

## Why is ISR important?

Only ISR members can safely become leaders.

---

## What is failover?

Automatic transfer of leadership from a failed broker to a healthy broker.

---

## Why are multiple brokers required?

To eliminate single points of failure.

---

## What is Disaster Recovery?

Recovery from large-scale failures such as datacenter outages.

---

## What is MirrorMaker 2?

Kafka's cluster-to-cluster replication tool used for disaster recovery and geo-replication.

---

## What are RTO and RPO?

```text
RTO → Recovery Time Objective

RPO → Recovery Point Objective
```

---

# Summary

Kafka High Availability ensures continuous operation even during failures.

Core Topics:

- Replication
- ISR
- Failover
- Multi Broker Setup
- Disaster Recovery

Replication:

```text
Multiple Copies Of Data

Fault Tolerance

Durability
```

ISR:

```text
Synchronized Replicas

Safe Leader Election
```

Failover:

```text
Automatic Recovery

Leader Replacement

Minimal Downtime
```

Multi-Broker Setup:

```text
No Single Point Of Failure

Load Distribution

Scalability
```

Disaster Recovery:

```text
Cross-Cluster Replication

Geo-Redundancy

Datacenter Protection
```

Recommended Production Configuration:

```properties
replication.factor=3
min.insync.replicas=2
acks=all
unclean.leader.election.enable=false
```

Benefits:

```text
High Availability

Fault Tolerance

Data Durability

Minimal Downtime

Business Continuity
```

Kafka achieves high availability through replication, ISR management, automatic failover, multi-broker deployments, and disaster recovery strategies, making it suitable for mission-critical distributed systems that require continuous operation and strong reliability guarantees.