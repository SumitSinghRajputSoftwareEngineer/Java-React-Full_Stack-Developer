
> KRaft is one of the most important changes in Kafka's history.
>
> Before KRaft, Kafka relied on ZooKeeper for metadata management and cluster coordination.
>
> With KRaft, Kafka became completely self-managed and no longer requires ZooKeeper.

---

# What is KRaft?

## Definition

KRaft stands for:

```text
Kafka Raft Metadata Mode
```

It is Kafka's built-in metadata management and consensus mechanism that replaces ZooKeeper.

---

## Official Goal

KRaft allows Kafka to:

```text
Manage Metadata
Elect Leaders
Coordinate Brokers
Maintain Cluster State
```

without requiring ZooKeeper.

---

## Simple Definition

Think of KRaft as:

```text
ZooKeeper Functionality
+
Built Directly Into Kafka
```

---

## Before KRaft

```text
Producer
    |
Kafka Broker
    |
ZooKeeper
```

Kafka depended on an external system.

---

## After KRaft

```text
Producer
    |
Kafka Broker
    |
KRaft Controller
```

Everything is handled internally.

---

## Important Point

KRaft does NOT replace:

```text
Topics
Partitions
Replication
Producers
Consumers
```

It only replaces:

```text
ZooKeeper Responsibilities
```

---

# Why ZooKeeper Was Removed?

As Kafka adoption increased, ZooKeeper became a major bottleneck.

---

# Problem 1: Additional Infrastructure

Old Architecture:

```text
Kafka Cluster
+
ZooKeeper Cluster
```

Administrators had to manage both.

---

## Example

```text
3 Kafka Brokers
+
3 ZooKeeper Nodes
```

Total:

```text
6 Servers
```

---

# Problem 2: Operational Complexity

Operations teams needed to handle:

```text
ZooKeeper Upgrades
ZooKeeper Monitoring
ZooKeeper Recovery
ZooKeeper Backup
```

---

# Problem 3: Metadata Bottleneck

Every metadata update involved:

```text
Kafka
   |
ZooKeeper
```

Additional network communication.

---

## Example

Topic Creation:

```text
Broker
   |
ZooKeeper
   |
Metadata Updated
```

---

# Problem 4: Scalability Issues

Large Kafka clusters contain:

```text
Thousands of Brokers

Millions of Partitions
```

ZooKeeper struggled to scale efficiently.

---

# Problem 5: Controller Dependency

Kafka controller relied heavily on ZooKeeper.

---

Example:

```text
ZooKeeper Slow
      ↓
Controller Slow
      ↓
Cluster Slow
```

---

# Problem 6: Startup Dependency

Old Startup Sequence:

```text
Start ZooKeeper
      ↓
Start Kafka
```

---

Desired:

```text
Start Kafka Only
```

---

# Problem 7: Metadata Propagation Delays

Metadata changes required synchronization through ZooKeeper.

This increased latency.

---

# Kafka Improvement Goals

Kafka developers wanted:

```text
Simpler Deployment
Higher Scalability
Better Performance
Lower Operational Cost
```

Result:

```text
KRaft
```

---

# KRaft Architecture

---

# High-Level Architecture

```text
          Kafka Cluster

      +------------------+
      | Controller Nodes |
      +------------------+
               |
    --------------------------
    |           |            |
 Broker1     Broker2     Broker3
```

---

## Major Components

### Controllers

Manage metadata.

---

### Brokers

Store and serve data.

---

### Controller Quorum

Maintains cluster consistency.

---

### Metadata Log

Stores cluster metadata.

---

# Architecture Overview

```text
Producer
    |
Broker
    |
Controller Quorum
    |
Metadata Log
```

---

# Types of Nodes in KRaft

KRaft introduces two roles.

---

# Controller Node

Responsible for:

```text
Metadata Management
Leader Election
Cluster Coordination
```

---

## Responsibilities

### Topic Management

```text
Create Topic
Delete Topic
Update Topic
```

---

### Broker Registration

```text
Broker Joined
Broker Left
```

---

### Leader Election

```text
Leader Failed
New Leader Elected
```

---

### Metadata Replication

Maintain metadata consistency.

---

# Broker Node

Responsible for:

```text
Store Records
Serve Producers
Serve Consumers
Replicate Data
```

---

# Deployment Models

---

## Dedicated Controller Mode

Controllers and brokers run separately.

```text
Controller1
Controller2
Controller3

Broker1
Broker2
Broker3
```

Recommended for production.

---

## Combined Mode

One node acts as both:

```text
Broker
+
Controller
```

---

Example:

```text
Node1
Broker + Controller
```

Usually used for development.

---

# KRaft Cluster Architecture

---

## Example

```text
Controller Quorum

Controller1
Controller2
Controller3

        |
        V

Kafka Brokers

Broker1
Broker2
Broker3
Broker4
```

---

## Why Multiple Controllers?

To avoid:

```text
Single Point of Failure
```

---

# Controller Quorum

---

## What is Controller Quorum?

A Controller Quorum is a group of controller nodes responsible for managing Kafka metadata.

---

## Definition

It is similar to:

```text
ZooKeeper Ensemble
```

but implemented using Kafka itself.

---

## Example

```text
Controller 1
Controller 2
Controller 3
```

---

## Why Quorum?

To achieve:

```text
Fault Tolerance
Consistency
High Availability
```

---

## Quorum Rule

Majority must be available.

---

### Example

3 Controllers

```text
Controller1 ✓
Controller2 ✓
Controller3 ✗
```

Cluster continues.

---

### Example

```text
Controller1 ✗
Controller2 ✗
Controller3 ✓
```

Majority unavailable.

Cluster metadata operations stop.

---

# Raft Consensus Algorithm

KRaft uses:

```text
Raft Consensus Protocol
```

instead of ZooKeeper's coordination mechanism.

---

## Purpose

Ensures:

```text
Metadata Consistency
Leader Election
Fault Tolerance
```

---

# Raft Components

---

## Leader Controller

One controller becomes leader.

---

Example:

```text
Controller1 = Leader
```

---

Responsibilities:

```text
Accept Metadata Changes
Replicate Metadata
Manage Cluster State
```

---

## Follower Controllers

```text
Controller2
Controller3
```

Replicate metadata.

---

# Metadata Replication

```text
Leader Controller
        |
    Metadata
        |
   -------------
   |           |
Follower    Follower
```

---

# Controller Election

---

## Failure Scenario

```text
Controller1 Fails
```

---

## Election Flow

```text
Controller1 Down
       ↓
Raft Election
       ↓
Controller2 Becomes Leader
```

---

## Benefits

Fast and reliable failover.

---

# Metadata Management

---

# What is Metadata?

Metadata is information about Kafka resources.

---

Examples:

```text
Topics
Partitions
Leaders
Replicas
ISR
Brokers
Configurations
ACLs
```

---

# ZooKeeper Approach

Metadata stored inside:

```text
ZooKeeper
```

---

# KRaft Approach

Metadata stored inside:

```text
Kafka Metadata Log
```

---

# Metadata Log

KRaft stores metadata as an append-only log.

---

Example:

```text
Create Topic Orders

Topic Created Event
```

---

```text
Broker Joined

Broker Registration Event
```

---

Everything becomes a metadata record.

---

# Metadata Flow

```text
Metadata Change
       |
Controller Leader
       |
Metadata Log
       |
Replicated To Followers
```

---

# Metadata Log Example

```text
Offset 0

Broker Added
```

```text
Offset 1

Topic Created
```

```text
Offset 2

Leader Changed
```

---

Like normal Kafka data logs, metadata is also stored as a log.

---

# Broker Registration in KRaft

---

## Startup Flow

```text
Broker Starts
      |
Registers With Controller
      |
Metadata Updated
      |
Broker Active
```

---

Unlike ZooKeeper:

```text
No External Registration Needed
```

---

# Leader Election in KRaft

---

## Partition Leader Election

Still exists.

---

Example:

```text
Partition 0

Leader -> Broker1

Followers ->
Broker2
Broker3
```

---

## Failure Scenario

```text
Broker1 Fails
```

---

## KRaft Flow

```text
Controller Detects Failure
         ↓
Leader Election
         ↓
Metadata Updated
```

---

## Difference

ZooKeeper no longer participates.

---

# KRaft vs ZooKeeper

| Feature | ZooKeeper | KRaft |
|----------|------------|---------|
| External System | Required | Not Required |
| Architecture | Kafka + ZooKeeper | Kafka Only |
| Metadata Storage | ZooKeeper | Metadata Log |
| Consensus | ZooKeeper Protocol | Raft |
| Deployment Complexity | High | Lower |
| Scalability | Limited | Better |
| Startup Steps | Multiple | Single |
| Operational Cost | Higher | Lower |
| Failure Recovery | Slower | Faster |
| Metadata Performance | Lower | Better |
| Maintenance | Complex | Simpler |

---

# Architecture Comparison

## Legacy Kafka

```text
Producer
     |
Broker
     |
ZooKeeper
```

---

## Modern Kafka

```text
Producer
     |
Broker
     |
Controller Quorum
```

---

# Benefits of KRaft

---

# 1. No ZooKeeper Dependency

Old:

```text
Kafka + ZooKeeper
```

New:

```text
Kafka Only
```

---

# 2. Simpler Deployment

Fewer servers.

Fewer moving parts.

---

# 3. Better Scalability

Designed for:

```text
Large Clusters
Millions of Partitions
```

---

# 4. Better Metadata Performance

Metadata changes processed faster.

---

# 5. Lower Operational Cost

No need to:

```text
Install ZooKeeper
Monitor ZooKeeper
Upgrade ZooKeeper
```

---

# 6. Faster Controller Elections

Raft provides efficient leader elections.

---

# 7. Better Fault Tolerance

Metadata replicated across controller quorum.

---

# 8. Easier Cloud Deployments

Managed services become simpler.

---

# 9. Unified Architecture

Everything managed inside Kafka.

---

# 10. Future-Proof Design

Kafka development now focuses entirely on KRaft.

ZooKeeper support has been removed from modern releases.

---

# Typical KRaft Production Setup

```text
Controller Quorum

Controller1
Controller2
Controller3

       |
       V

Kafka Brokers

Broker1
Broker2
Broker3
Broker4
Broker5
```

---

# KRaft Startup Process

---

## Step 1

Generate Cluster ID

```bash
kafka-storage.sh random-uuid
```

---

## Step 2

Format Storage

```bash
kafka-storage.sh format
```

---

## Step 3

Start Controllers

```bash
kafka-server-start.sh
```

---

## Step 4

Start Brokers

```bash
kafka-server-start.sh
```

---

## Step 5

Cluster Ready

```text
No ZooKeeper Required
```

---

# Interview Questions

## What is KRaft?

KRaft (Kafka Raft Metadata Mode) is Kafka's built-in metadata management system that replaces ZooKeeper.

---

## Which consensus algorithm does KRaft use?

```text
Raft Consensus Algorithm
```

---

## What is a Controller Quorum?

A group of controller nodes responsible for metadata management and cluster coordination.

---

## Why was ZooKeeper removed?

Because it introduced:

- Operational Complexity
- Metadata Bottlenecks
- Scalability Limitations

---

## Where is metadata stored in KRaft?

Inside Kafka's:

```text
Metadata Log
```

---

## Does Kafka 4.x require ZooKeeper?

```text
No
```

ZooKeeper has been completely removed.

---

## What are the benefits of KRaft?

- Simpler Deployment
- Better Scalability
- Better Performance
- Lower Operational Cost
- No External Dependencies

---

# Summary

KRaft is the modern architecture for Kafka cluster coordination and metadata management.

It replaces ZooKeeper by introducing:

```text
Controller Quorum
Raft Consensus
Metadata Log
Internal Metadata Replication
```

Modern Kafka clusters now operate as:

```text
Kafka Brokers
       +
Controller Quorum
```

without any ZooKeeper dependency.

KRaft provides:

- Simpler Architecture
- Better Scalability
- Faster Metadata Operations
- Lower Maintenance
- Improved Fault Tolerance

and is the foundation of all modern Kafka deployments.