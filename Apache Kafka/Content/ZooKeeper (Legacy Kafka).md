
> **Important Note**
>
> ZooKeeper was a critical component of Kafka architecture until Kafka 3.x.
>
> Starting from Kafka 4.x, Kafka no longer requires ZooKeeper and uses **KRaft (Kafka Raft Metadata Mode)** instead.
>
> However, ZooKeeper remains one of the most frequently asked Kafka interview topics because many organizations still run Kafka clusters that depend on ZooKeeper.

---

# What is ZooKeeper?

## Definition

Apache ZooKeeper is a distributed coordination service used to manage and coordinate distributed systems.

It provides centralized management for:

- Configuration Management
- Service Discovery
- Leader Election
- Cluster Coordination
- Distributed Synchronization

---

## Simple Definition

Think of ZooKeeper as:

```text
Cluster Manager
```

for distributed systems.

It keeps track of:

```text
Which Broker Is Alive?
Who Is Leader?
Which Topics Exist?
Which Partitions Exist?
```

---

## Why ZooKeeper Exists?

Imagine a Kafka cluster:

```text
Broker 1
Broker 2
Broker 3
Broker 4
```

Questions arise:

```text
Who is currently alive?
Who is leader?
Who failed?
Who joined?
```

ZooKeeper maintained all this information.

---

## Real World Analogy

Imagine a school.

Students:

```text
Broker 1
Broker 2
Broker 3
```

Principal:

```text
ZooKeeper
```

The principal knows:

```text
Who is present?
Who is absent?
Who is class monitor?
```

Similarly, ZooKeeper knows cluster state.

---

# Why Kafka Used ZooKeeper?

Before KRaft existed, Kafka required a centralized coordination service.

---

## Problems Kafka Needed to Solve

### Broker Coordination

```text
Broker Joined
Broker Left
Broker Failed
```

---

### Leader Election

```text
Leader Failed
Need New Leader
```

---

### Metadata Storage

```text
Topics
Partitions
Replicas
ISR
```

---

### Configuration Management

```text
Broker Configurations
Topic Configurations
```

---

### Cluster Membership

```text
Active Brokers
Inactive Brokers
```

---

ZooKeeper solved all these problems.

---

# Kafka with ZooKeeper

```text
               ZooKeeper
                    |
     ----------------------------------
     |               |                |
 Broker1         Broker2         Broker3
```

---

## Startup Sequence

```text
Start ZooKeeper
      ↓
Start Brokers
      ↓
Brokers Register Themselves
      ↓
Kafka Cluster Ready
```

---

## Without ZooKeeper

Old Kafka versions:

```text
Kafka Cannot Start
```

because ZooKeeper was mandatory.

---

# ZooKeeper Responsibilities

ZooKeeper performed multiple responsibilities inside Kafka.

---

# 1. Broker Registration

When a broker starts:

```text
Broker 1 Started
```

It registers itself in ZooKeeper.

---

## Example

```text
Broker 1
Broker 2
Broker 3
```

ZooKeeper knows all active brokers.

---

# 2. Cluster Membership

ZooKeeper continuously tracks:

```text
Active Brokers
Dead Brokers
New Brokers
```

---

## Example

```text
Broker 2 Crashes
```

ZooKeeper detects failure immediately.

---

# 3. Leader Election

One of ZooKeeper's most important responsibilities.

---

Example:

```text
Partition 0

Leader -> Broker 1

Followers ->
Broker 2
Broker 3
```

If Broker 1 fails:

```text
ZooKeeper Elects New Leader
```

---

# 4. Metadata Storage

ZooKeeper stores Kafka metadata.

Examples:

```text
Topics
Partitions
Leaders
Replicas
ISR
Configurations
```

---

# 5. Configuration Management

Stores:

```text
Broker Configurations
Topic Configurations
ACLs
```

---

# 6. Controller Election

Kafka Cluster has one Controller Broker.

ZooKeeper elects it.

---

Example:

```text
Broker 2
```

becomes controller.

---

Responsibilities:

```text
Leader Election
Partition Management
Cluster Coordination
```

---

# 7. Failure Detection

ZooKeeper detects:

```text
Broker Crash
Network Failure
Disconnected Broker
```

---

# ZooKeeper Architecture

---

## High-Level Architecture

```text
          ZooKeeper Ensemble

         ZK1
          |
    ---------------
    |             |
   ZK2           ZK3

          |
     Kafka Cluster
```

---

## ZooKeeper Ensemble

A ZooKeeper cluster is called an:

```text
Ensemble
```

---

Example:

```text
ZooKeeper1
ZooKeeper2
ZooKeeper3
```

---

## Why Multiple ZooKeepers?

Single ZooKeeper:

```text
Single Point Of Failure
```

---

Multiple ZooKeepers:

```text
Fault Tolerance
High Availability
```

---

## Recommended Setup

```text
3 Nodes
5 Nodes
7 Nodes
```

Always odd numbers.

---

# ZooKeeper Data Model

ZooKeeper stores data as:

```text
Tree Structure
```

similar to a filesystem.

---

Example:

```text
/
├── brokers
├── topics
├── controller
├── configs
```

---

## ZNodes

ZooKeeper data units are called:

```text
ZNodes
```

Similar to folders/files.

---

Example:

```text
/cluster
/brokers
/topics
```

---

# Leader Election Using ZooKeeper

One of the most important Kafka interview topics.

---

# Why Leader Election?

Each partition must have:

```text
One Leader
```

to handle reads/writes.

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

# Failure Scenario

```text
Broker1 Crashes
```

Current leader unavailable.

---

## What Happens?

### Step 1

ZooKeeper detects broker failure.

---

### Step 2

Controller receives notification.

---

### Step 3

Controller selects a new leader.

---

### Step 4

Metadata updated.

---

### Step 5

Consumers and producers continue.

---

## Election Flow

```text
Leader Fails
      ↓
ZooKeeper Detects Failure
      ↓
Controller Notified
      ↓
New Leader Selected
      ↓
Metadata Updated
```

---

# Controller Election

ZooKeeper also elects the Kafka controller.

---

## Controller Responsibilities

```text
Leader Election
Partition Assignment
Metadata Updates
```

---

Example:

```text
Broker2
```

becomes controller.

---

If controller fails:

```text
ZooKeeper Elects New Controller
```

---

# Metadata Management

Metadata is information about the Kafka cluster.

---

# What Metadata Was Stored?

---

## Brokers

```text
Broker IDs
Broker Addresses
Broker Status
```

---

## Topics

```text
Orders
Payments
Inventory
```

---

## Partitions

```text
Partition 0
Partition 1
Partition 2
```

---

## Leaders

```text
Partition 0 Leader
Partition 1 Leader
```

---

## Replicas

```text
Replica Locations
Replication Factor
```

---

## ISR

```text
In Sync Replicas
```

---

## ACLs

```text
Permissions
Authorization Rules
```

---

# Metadata Example

```text
Orders Topic

Partition 0

Leader -> Broker1

Replicas ->
Broker2
Broker3

ISR ->
Broker1
Broker2
Broker3
```

Stored inside ZooKeeper.

---

# ZooKeeper Limitations

As Kafka adoption grew, ZooKeeper became a bottleneck.

---

# Limitation 1: Additional System to Manage

Running Kafka required:

```text
Kafka Cluster
+
ZooKeeper Cluster
```

---

Operational complexity increased.

---

# Limitation 2: Scaling Issues

Large clusters generated massive metadata.

Example:

```text
Thousands Of Brokers
Millions Of Partitions
```

ZooKeeper struggled at scale.

---

# Limitation 3: Metadata Bottleneck

Every metadata update required:

```text
Kafka
      ↓
ZooKeeper
```

Additional network overhead.

---

# Limitation 4: Controller Dependency

Controller heavily depended on ZooKeeper.

---

## Failure Scenario

```text
ZooKeeper Slow
      ↓
Controller Slow
      ↓
Cluster Instability
```

---

# Limitation 5: Operational Complexity

Administrators needed to manage:

```text
ZooKeeper Backups
ZooKeeper Monitoring
ZooKeeper Upgrades
ZooKeeper Recovery
```

---

# Limitation 6: Startup Complexity

Old Startup Process:

```text
Start ZooKeeper
      ↓
Start Kafka
```

---

Modern systems preferred:

```text
Start Kafka Only
```

---

# Limitation 7: High Metadata Traffic

Large clusters continuously generated:

```text
Leader Changes
Broker Registrations
Topic Changes
```

ZooKeeper became overloaded.

---

# Why ZooKeeper Was Removed?

Kafka developers wanted:

```text
Simpler Architecture
Higher Scalability
Better Performance
```

---

# Problems Solved by Removing ZooKeeper

---

## Simplified Deployment

Old:

```text
Kafka + ZooKeeper
```

New:

```text
Kafka Only
```

---

## Better Scalability

Supports:

```text
Millions Of Partitions
Large Clusters
```

more efficiently.

---

## Reduced Operational Cost

No separate ZooKeeper management.

---

## Faster Metadata Operations

Metadata stays inside Kafka itself.

---

## Better Fault Tolerance

Metadata replicated through Kafka's own consensus mechanism.

---

# KRaft Replaces ZooKeeper

KRaft stands for:

```text
Kafka Raft Metadata Mode
```

---

## Old Architecture

```text
Producer
      |
Kafka Broker
      |
ZooKeeper
```

---

## New Architecture

```text
Producer
      |
Kafka Broker
      |
KRaft Controller
```

---

# ZooKeeper vs KRaft

| Feature | ZooKeeper | KRaft |
|----------|-----------|--------|
| External System | Required | Not Required |
| Complexity | High | Lower |
| Scalability | Limited | Higher |
| Metadata Storage | ZooKeeper | Kafka |
| Deployment | More Complex | Simpler |
| Startup | Two Systems | One System |
| Maintenance | More Effort | Less Effort |
| Performance | Lower | Better |

---

# Legacy Kafka Startup

---

## Step 1

Start ZooKeeper

```bash
bin/zookeeper-server-start.sh config/zookeeper.properties
```

---

## Step 2

Start Kafka

```bash
bin/kafka-server-start.sh config/server.properties
```

---

## Shutdown

Stop Kafka:

```bash
bin/kafka-server-stop.sh
```

---

Stop ZooKeeper:

```bash
bin/zookeeper-server-stop.sh
```

---

# Interview Questions

## What is ZooKeeper?

A distributed coordination service used by older Kafka versions for metadata management, leader election, broker coordination, and cluster management.

---

## Why did Kafka use ZooKeeper?

To handle:

- Leader Election
- Metadata Storage
- Broker Registration
- Controller Election
- Cluster Coordination

---

## What is a ZooKeeper Ensemble?

A ZooKeeper cluster consisting of multiple ZooKeeper servers.

---

## What are ZNodes?

ZooKeeper's data storage units, similar to files/directories.

---

## Why was ZooKeeper removed?

Because it introduced:

- Operational Complexity
- Scalability Issues
- Metadata Bottlenecks

and Kafka introduced KRaft as a replacement.

---

## Does Kafka 4.x Require ZooKeeper?

```text
No
```

Kafka 4.x uses KRaft mode.

---

# Summary

ZooKeeper was the backbone of Kafka cluster coordination for many years.

Its primary responsibilities included:

- Broker Registration
- Cluster Membership
- Leader Election
- Controller Election
- Metadata Management
- Failure Detection

Although ZooKeeper helped Kafka become successful, it eventually became a scalability and operational bottleneck.

To overcome these limitations, Kafka introduced:

```text
KRaft (Kafka Raft Metadata Mode)
```

which completely removes the dependency on ZooKeeper and stores metadata directly within Kafka itself.

The next topic, **KRaft Mode (Modern Kafka)**, explains how Kafka now performs metadata management, leader election, and cluster coordination without ZooKeeper.