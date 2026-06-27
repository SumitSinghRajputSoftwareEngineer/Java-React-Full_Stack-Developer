# 47. MirrorMaker

MirrorMaker is Kafka's built-in replication solution used to copy data between Kafka clusters.

It is one of the most important tools for:

```text
Disaster Recovery

Cross-Region Replication

Data Migration

Backup Clusters

Geo Replication
```

Without MirrorMaker, organizations would need to build custom replication mechanisms between Kafka clusters.

---

# What is MirrorMaker?

## Definition

MirrorMaker is a Kafka tool that replicates topics and messages from one Kafka cluster to another.

---

Source Cluster:

```text
Cluster-A
```

Destination Cluster:

```text
Cluster-B
```

---

MirrorMaker continuously copies records from:

```text
Source Cluster
      ↓
Target Cluster
```

---

# Why MirrorMaker?

Organizations often need:

```text
Multiple Kafka Clusters

Cross-Datacenter Replication

Cloud Migration

Disaster Recovery
```

---

Example:

```text
Mumbai Kafka Cluster
        ↓
Replication
        ↓
Singapore Kafka Cluster
```

---

Benefits:

```text
Data Redundancy

Business Continuity

High Availability
```

---

# Real-World Use Cases

---

## Disaster Recovery

Maintain backup Kafka clusters.

---

## Multi-Region Applications

Replicate events globally.

---

## Data Migration

Move workloads between clusters.

---

## Analytics

Copy production data to analytics clusters.

---

## Hybrid Cloud

Replicate between on-premise and cloud environments.

---

# MirrorMaker Architecture

```text
Source Cluster
      ↓
MirrorMaker
      ↓
Destination Cluster
```

---

Example:

```text
Producer
    ↓
Cluster-A
    ↓
MirrorMaker
    ↓
Cluster-B
    ↓
Consumer
```

---

# MirrorMaker Workflow

```text
Read From Source Topic
          ↓
Consume Messages
          ↓
Produce Messages
          ↓
Write To Target Topic
```

---

MirrorMaker acts as:

```text
Consumer

and

Producer
```

simultaneously.

---

# How MirrorMaker Works

Step 1:

Consume records from source cluster.

---

Step 2:

Store records temporarily in memory.

---

Step 3:

Produce records to destination cluster.

---

Step 4:

Commit offsets.

---

Flow:

```text
Source Topic
      ↓
Consumer
      ↓
MirrorMaker
      ↓
Producer
      ↓
Destination Topic
```

---

# MirrorMaker Versions

Kafka provides:

```text
MirrorMaker 1 (MM1)

MirrorMaker 2 (MM2)
```

---

# MirrorMaker 1 (Legacy)

Original replication solution.

---

Architecture:

```text
Source Cluster
      ↓
MirrorMaker 1
      ↓
Destination Cluster
```

---

Characteristics:

```text
Simple

Basic Replication

Limited Features
```

---

# Limitations of MirrorMaker 1

---

## No Offset Synchronization

---

## Limited Monitoring

---

## Difficult Management

---

## Poor Multi-Cluster Support

---

## Less Fault Tolerant

---

Because of these limitations:

```text
MirrorMaker 2
```

was introduced.

---

# MirrorMaker 2 (MM2)

MirrorMaker 2 is the modern replication solution.

---

# What is MirrorMaker 2?

MirrorMaker 2 is a Kafka Connect-based replication framework that supports advanced cluster replication capabilities.

---

Introduced in:

```text
Kafka 2.4+
```

---

Built on:

```text
Kafka Connect
```

---

# MirrorMaker 2 Architecture

```text
Source Cluster
       ↓
MirrorSourceConnector
       ↓
Kafka Connect
       ↓
MirrorCheckpointConnector
       ↓
MirrorHeartbeatConnector
       ↓
Destination Cluster
```

---

# Major Components

MirrorMaker 2 contains three important connectors.

---

## MirrorSourceConnector

Replicates topic data.

---

Flow:

```text
Topic-A
    ↓
MirrorSourceConnector
    ↓
Topic-A
```

---

# Responsibility

```text
Copies Messages

Copies Topics

Handles Replication
```

---

## MirrorCheckpointConnector

Replicates consumer group offsets.

---

Purpose:

```text
Consumer Failover
```

---

Without checkpoint synchronization:

```text
Consumers Restart From Wrong Position
```

---

# Responsibility

```text
Offset Synchronization

Consumer Recovery
```

---

## MirrorHeartbeatConnector

Monitors replication health.

---

Purpose:

```text
Detect Connectivity Issues
```

---

Creates heartbeat topics.

---

Responsibilities:

```text
Cluster Health

Replication Monitoring

Connectivity Validation
```

---

# MirrorMaker 2 Architecture Diagram

```text
Source Cluster
       ↓
MirrorSourceConnector
       ↓
MirrorCheckpointConnector
       ↓
MirrorHeartbeatConnector
       ↓
Target Cluster
```

---

# Advantages of MirrorMaker 2

---

## Topic Replication

---

## Offset Synchronization

---

## Better Monitoring

---

## Kafka Connect Integration

---

## Multi-Cluster Support

---

## Fault Tolerance

---

## Scalability

---

# Replication Between Clusters

One of MirrorMaker's primary functions.

---

# What is Cluster Replication?

Copying Kafka topics and messages from one cluster to another.

---

Example:

```text
Cluster-A
      ↓
Replication
      ↓
Cluster-B
```

---

# Replication Flow

```text
Producer
      ↓
Cluster-A
      ↓
MirrorMaker
      ↓
Cluster-B
      ↓
Consumer
```

---

# Replication Types

---

## One-Way Replication

Most common.

---

Architecture:

```text
Cluster-A
      ↓
Cluster-B
```

---

Benefits:

```text
Simple

Reliable

Easy To Manage
```

---

# Example

Production:

```text
Cluster-A
```

---

Backup:

```text
Cluster-B
```

---

# Two-Way Replication

Both clusters replicate to each other.

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
Global Availability
```

---

Challenges:

```text
Loop Prevention

Conflict Resolution
```

---

# Multi-Region Replication

Architecture:

```text
India Cluster
      ↓
Europe Cluster
      ↓
US Cluster
```

---

Benefits:

```text
Global Data Distribution
```

---

# Topic Naming During Replication

MirrorMaker 2 typically prefixes replicated topics.

---

Example:

Source:

```text
orders
```

---

Replicated:

```text
clusterA.orders
```

---

Benefits:

```text
Avoid Topic Conflicts
```

---

# Offset Synchronization

One of MM2's most valuable features.

---

# Why Offset Synchronization?

Consumer groups may fail over to another cluster.

---

Without synchronization:

```text
Consumer Position Lost
```

---

With synchronization:

```text
Consumer Continues Processing
```

from the correct offset.

---

# Example

Source Cluster:

```text
Offset = 5000
```

---

MirrorCheckpointConnector:

```text
Replicates Offset
```

---

Destination Cluster:

```text
Offset = 5000
```

---

Consumer resumes correctly.

---

# Disaster Recovery

Disaster Recovery is the most common MirrorMaker deployment.

---

# Why Disaster Recovery?

A single cluster may fail because of:

```text
Datacenter Outage

Cloud Failure

Network Disaster

Hardware Failure
```

---

# DR Architecture

```text
Primary Cluster
       ↓
MirrorMaker
       ↓
Backup Cluster
```

---

# Normal State

```text
Primary Cluster Active
```

---

Backup cluster:

```text
Receives Replicated Data
```

---

# Failure State

Primary cluster unavailable.

---

Traffic switches to:

```text
Backup Cluster
```

---

Business continues operating.

---

# Active-Passive DR Architecture

```text
Cluster-A (Primary)
        ↓
MirrorMaker
        ↓
Cluster-B (Standby)
```

---

Benefits:

```text
Simple

Reliable

Widely Used
```

---

# Active-Active Architecture

```text
Cluster-A
      ↔
Cluster-B
```

---

Both clusters:

```text
Read

Write

Replicate
```

---

Benefits:

```text
Maximum Availability
```

---

Challenges:

```text
Data Conflicts

Operational Complexity
```

---

# MirrorMaker Failure Handling

MirrorMaker itself can fail.

---

Recovery mechanisms:

```text
Kafka Connect Fault Tolerance

Checkpoint Recovery

Connector Restart
```

---

Benefits:

```text
Automatic Recovery
```

---

# MirrorMaker Scalability

MirrorMaker 2 scales using Kafka Connect.

---

Scale by:

```text
Increasing Connector Tasks

Adding Workers

Distributed Connect Clusters
```

---

Architecture:

```text
Worker-1

Worker-2

Worker-3
```

---

Benefits:

```text
Higher Throughput

Better Reliability
```

---

# MirrorMaker Configuration Example

Source Cluster:

```properties
source.cluster.alias=primary
```

---

Target Cluster:

```properties
target.cluster.alias=backup
```

---

Enable replication:

```properties
primary->backup.enabled=true
```

---

Replicate topics:

```properties
topics=.*
```

---

Meaning:

```text
Replicate All Topics
```

---

# Monitoring MirrorMaker

Important metrics:

```text
Replication Lag

Replication Throughput

Connector Health

Heartbeat Status

Task Failures
```

---

# Common Challenges

---

## Network Latency

---

## Large Topic Volumes

---

## Offset Synchronization Issues

---

## Replication Delays

---

## Topic Configuration Differences

---

# Best Practices

---

## Prefer MirrorMaker 2

---

## Use Kafka Connect Distributed Mode

---

## Monitor Replication Lag

---

## Synchronize Consumer Offsets

---

## Test Failover Procedures

---

## Replicate Critical Topics

---

## Secure Inter-Cluster Traffic

---

## Monitor Heartbeat Topics

---

# MirrorMaker vs Cluster Linking

| Feature | MirrorMaker 2 | Cluster Linking |
|-----------|--------------|----------------|
| Kafka Version | Kafka 2.4+ | Modern Kafka |
| Built On | Kafka Connect | Native Kafka |
| Topic Replication | Yes | Yes |
| Offset Sync | Yes | Limited |
| Operational Complexity | Medium | Lower |
| Deployment Effort | Higher | Lower |
| Multi-Cloud Support | Excellent | Good |

---

# Real-World Example

Global E-Commerce Platform:

Primary:

```text
India Cluster
```

---

Backup:

```text
Singapore Cluster
```

---

Replication:

```text
MirrorMaker 2
```

---

Failure:

```text
India Region Outage
```

---

Recovery:

```text
Consumers Switch To Singapore Cluster
```

---

Business remains operational.

---

# Common Interview Questions

## What is MirrorMaker?

A Kafka tool used to replicate data between Kafka clusters.

---

## Why is MirrorMaker used?

For:

```text
Disaster Recovery

Geo Replication

Cluster Migration

Backup Clusters
```

---

## What are the two versions of MirrorMaker?

```text
MirrorMaker 1

MirrorMaker 2
```

---

## Why was MirrorMaker 2 introduced?

To provide:

```text
Offset Synchronization

Better Monitoring

Kafka Connect Integration

Improved Scalability
```

---

## What are the three main MM2 connectors?

```text
MirrorSourceConnector

MirrorCheckpointConnector

MirrorHeartbeatConnector
```

---

## What does MirrorSourceConnector do?

Replicates topics and messages.

---

## What does MirrorCheckpointConnector do?

Replicates consumer offsets.

---

## What does MirrorHeartbeatConnector do?

Monitors replication health and connectivity.

---

## How does MirrorMaker support Disaster Recovery?

By continuously replicating data to a backup cluster that can take over during failures.

---

## Is MirrorMaker synchronous or asynchronous?

```text
Asynchronous
```

Replication.

---

# Summary

MirrorMaker is Kafka's replication framework used for cluster-to-cluster data replication.

Core Topics:

- MirrorMaker Overview
- MirrorMaker 2
- Replication Between Clusters
- Disaster Recovery

MirrorMaker Functions:

```text
Topic Replication

Message Replication

Offset Synchronization

Cross-Region Replication
```

MirrorMaker Versions:

```text
MM1 (Legacy)

MM2 (Modern)
```

MirrorMaker 2 Components:

```text
MirrorSourceConnector

MirrorCheckpointConnector

MirrorHeartbeatConnector
```

Replication Types:

```text
One-Way Replication

Two-Way Replication

Multi-Region Replication
```

Disaster Recovery Benefits:

```text
Business Continuity

Backup Clusters

Rapid Failover

Reduced Downtime
```

Key Advantages:

```text
High Availability

Geo Replication

Scalability

Fault Tolerance

Cross-Cluster Synchronization
```

MirrorMaker 2 is the recommended approach for Kafka cluster replication, enabling organizations to build resilient, geographically distributed event streaming architectures with strong disaster recovery capabilities and minimal operational downtime.