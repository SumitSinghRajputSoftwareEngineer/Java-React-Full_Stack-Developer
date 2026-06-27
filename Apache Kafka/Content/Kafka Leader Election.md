
Leader Election is one of Kafka's most critical fault-tolerance mechanisms.

It ensures that when a broker fails, Kafka can automatically promote another replica to become the new leader and continue serving producers and consumers.

Without leader election:

```text
Broker Failure
      ↓
Partition Unavailable
      ↓
Service Outage
```

With leader election:

```text
Broker Failure
      ↓
New Leader Selected
      ↓
Processing Continues
```

---

# Why Leader Election Is Needed?

Consider a Kafka cluster:

```text
Broker1
Broker2
Broker3
```

---

Topic:

```text
orders
```

Partition:

```text
P0
```

Replication Factor:

```text
RF = 3
```

---

Replica Distribution:

```text
Broker1 -> Leader

Broker2 -> Follower

Broker3 -> Follower
```

---

Visualization:

```text
P0

Broker1 (Leader)

Broker2 (Follower)

Broker3 (Follower)
```

---

All:

```text
Reads
Writes
```

go through:

```text
Broker1
```

---

Now Broker1 crashes.

---

Without leader election:

```text
P0 Unavailable

Producers Fail

Consumers Fail
```

---

Kafka solves this using:

```text
Leader Election
```

---

# What is Leader Election?

## Definition

Leader Election is the process of selecting a new leader replica when the current leader becomes unavailable.

---

# Goal

Maintain:

```text
High Availability

Fault Tolerance

Continuous Processing
```

---

# High-Level Flow

```text
Leader Fails
      ↓
Controller Detects Failure
      ↓
Eligible Replica Selected
      ↓
New Leader Assigned
      ↓
Clients Continue
```

---

# Important Rule

Kafka elects leaders from:

```text
ISR
(In-Sync Replicas)
```

whenever possible.

---

Reason:

```text
Avoid Data Loss
```

---

# Leader Election Process

This is the exact sequence Kafka follows.

---

# Initial State

```text
Partition P0

Broker1 -> Leader

Broker2 -> Follower

Broker3 -> Follower
```

---

ISR:

```text
Broker1

Broker2

Broker3
```

---

# Step 1: Broker Failure

Leader crashes.

---

Example:

```text
Broker1 Down
```

---

State:

```text
Leader Unavailable
```

---

# Step 2: Controller Detects Failure

Kafka Controller receives:

```text
Broker Failure Event
```

---

# Step 3: Select New Leader

Controller checks:

```text
ISR List
```

---

Available ISR:

```text
Broker2

Broker3
```

---

# Step 4: Elect Leader

Controller selects:

```text
Broker2
```

---

# Step 5: Update Metadata

Cluster metadata updated.

---

New state:

```text
Broker2 -> Leader

Broker3 -> Follower
```

---

# Step 6: Notify Brokers

Metadata propagated.

---

# Step 7: Resume Processing

Producers and consumers reconnect.

---

Flow:

```text
Broker Failure
       ↓
Controller Detects
       ↓
Select ISR Replica
       ↓
Elect Leader
       ↓
Update Metadata
       ↓
Continue Processing
```

---

# Visualization

Before Failure:

```text
Broker1 -> Leader

Broker2 -> Follower

Broker3 -> Follower
```

---

After Failure:

```text
Broker2 -> Leader

Broker3 -> Follower
```

---

# Election Requirements

For a replica to become leader:

```text
Replica Must Be In ISR
```

---

Reason:

```text
Data Consistency
```

---

# Example

Leader Offset:

```text
100
```

Follower Offset:

```text
100
```

Eligible.

---

Follower Offset:

```text
60
```

Not eligible.

---

# Preferred Leader Election

A very common interview topic.

---

# What is Preferred Leader?

When a partition is created, Kafka chooses a preferred leader.

---

Usually:

```text
First Replica
```

in the replica list.

---

Example:

```text
Replica List:

Broker1
Broker2
Broker3
```

---

Preferred Leader:

```text
Broker1
```

---

# Scenario

Initially:

```text
Broker1 -> Leader
```

---

Broker1 crashes.

---

Election:

```text
Broker2 -> Leader
```

---

Later:

```text
Broker1 Returns
```

---

Question:

```text
Should Broker1 Become Leader Again?
```

---

Preferred Leader Election says:

```text
Yes
```

---

Kafka can move leadership back to the preferred leader.

---

# Why Preferred Leader Election?

Benefits:

```text
Balanced Load

Predictable Leadership

Even Broker Utilization
```

---

# Example

Without preferred election:

```text
Broker2 Continues As Leader
```

for a long time.

---

Broker2 may become overloaded.

---

Preferred election restores:

```text
Original Distribution
```

---

# Visualization

Original:

```text
Broker1 -> Leader
```

---

Failure:

```text
Broker2 -> Leader
```

---

Recovery:

```text
Broker1 -> Leader
```

---

# Automatic Leader Rebalancing

Kafka supports:

```properties
auto.leader.rebalance.enable=true
```

---

Purpose:

```text
Move Leadership Back
To Preferred Leaders
```

---

# Preferred Leader Election Command

Legacy administrative operation:

```bash
kafka-leader-election.sh
```

---

Example:

```bash
kafka-leader-election.sh \
--bootstrap-server localhost:9092 \
--election-type preferred
```

---

# Unclean Leader Election

One of the most important advanced Kafka concepts.

---

# Normal Leader Election

Kafka chooses leader from:

```text
ISR
```

only.

---

Example:

```text
ISR:

Broker2
Broker3
```

---

Leader:

```text
Broker2
```

---

Safe.

---

# Problem Scenario

Leader:

```text
Broker1
```

---

Followers:

```text
Broker2

Broker3
```

---

Suppose:

```text
Broker2 Out Of Sync

Broker3 Out Of Sync
```

---

ISR:

```text
Broker1 Only
```

---

Now:

```text
Broker1 Crashes
```

---

ISR becomes:

```text
Empty
```

---

Question:

```text
Who Becomes Leader?
```

---

# Option 1: No Election

Result:

```text
Partition Unavailable
```

---

No data loss.

---

But downtime.

---

# Option 2: Unclean Leader Election

Kafka selects:

```text
Out Of Sync Replica
```

as leader.

---

Example:

```text
Broker2
```

---

Processing resumes.

---

But:

```text
Recent Data May Be Lost
```

---

# Definition

Unclean Leader Election is the election of a replica that is not part of ISR.

---

# Example

Leader Offset:

```text
100
```

---

Follower Offset:

```text
85
```

---

Follower becomes leader.

---

Offsets:

```text
86-100
```

may disappear.

---

# Configuration

```properties
unclean.leader.election.enable=true
```

---

# Advantages

---

## Better Availability

---

## Faster Recovery

---

## Less Downtime

---

# Disadvantages

---

## Possible Data Loss

---

## Data Inconsistency

---

## Violates Durability

---

# Production Recommendation

```properties
unclean.leader.election.enable=false
```

---

Most organizations prefer:

```text
Data Safety
```

over

```text
Availability
```

---

# Clean vs Unclean Election

| Feature | Clean Election | Unclean Election |
|----------|---------------|------------------|
| Leader Source | ISR | Any Replica |
| Data Loss | No | Possible |
| Availability | Lower | Higher |
| Consistency | Strong | Weaker |
| Production Recommendation | Yes | Usually No |

---

# Controller Role

Leader election is coordinated by the Kafka Controller.

---

# What is Controller?

The Controller is a special broker responsible for managing cluster state.

---

Responsibilities:

```text
Leader Election

Broker Monitoring

Partition Management

Metadata Updates
```

---

# Controller in ZooKeeper Mode

Legacy Kafka:

```text
ZooKeeper
```

selected the controller.

---

Cluster:

```text
Broker1

Broker2

Broker3
```

---

ZooKeeper elects:

```text
Broker2
```

as controller.

---

# Controller in KRaft Mode

Modern Kafka:

```text
KRaft Controller Quorum
```

elects controllers.

---

No ZooKeeper required.

---

# Controller Responsibilities

---

## Detect Broker Failures

---

## Trigger Leader Elections

---

## Update Metadata

---

## Notify Brokers

---

## Manage Partitions

---

# Visualization

```text
Controller
      ↓
Detect Failure
      ↓
Choose Leader
      ↓
Update Cluster
```

---

# Metadata Updates

After leader election:

Controller updates:

```text
Partition Metadata

Leader Information

ISR Information
```

---

Then informs:

```text
All Brokers

All Clients
```

---

# Broker Failure Scenarios

Understanding these scenarios is critical for interviews.

---

# Scenario 1: Leader Broker Failure

Initial:

```text
Broker1 -> Leader

Broker2 -> Follower

Broker3 -> Follower
```

---

Broker1 crashes.

---

Controller:

```text
Elect Broker2
```

---

Result:

```text
Broker2 -> Leader
```

---

No downtime beyond election duration.

---

# Scenario 2: Follower Broker Failure

Initial:

```text
Broker1 -> Leader

Broker2 -> Follower

Broker3 -> Follower
```

---

Broker3 crashes.

---

Result:

```text
Leader Remains Same
```

---

No election required.

---

ISR becomes smaller.

---

# Scenario 3: Multiple Follower Failures

Initial:

```text
Leader

Follower

Follower
```

---

Followers fail.

---

Leader survives.

---

Result:

```text
No Election
```

---

But replication protection decreases.

---

# Scenario 4: Entire ISR Failure

Example:

```text
Leader Crashes

ISR Empty
```

---

Possible outcomes:

```text
Partition Unavailable
```

or

```text
Unclean Leader Election
```

depending on configuration.

---

# Scenario 5: Preferred Leader Returns

Initial:

```text
Broker1 -> Preferred Leader
```

---

Failure:

```text
Broker2 -> New Leader
```

---

Recovery:

```text
Broker1 Returns
```

---

Preferred election:

```text
Broker1 Becomes Leader Again
```

---

# Election Timeline Example

```text
10:00 Broker1 Leader

10:01 Broker1 Crash

10:01 Controller Detects Failure

10:01 Broker2 Elected

10:01 Metadata Updated

10:01 Clients Resume
```

---

# Best Practices

---

## Use RF=3

---

## Use ACK=all

---

## Configure min.insync.replicas

Example:

```properties
min.insync.replicas=2
```

---

## Keep Unclean Election Disabled

```properties
unclean.leader.election.enable=false
```

---

## Monitor ISR Shrink Events

---

## Enable Preferred Leader Rebalancing

```properties
auto.leader.rebalance.enable=true
```

---

## Monitor Controller Health

---

# Interview Questions

## What is Leader Election?

The process of selecting a new leader replica when the current leader becomes unavailable.

---

## Who performs Leader Election?

```text
Kafka Controller
```

---

## From where is a new leader selected?

```text
ISR
(In-Sync Replicas)
```

---

## Why only ISR replicas are eligible?

To avoid data loss and maintain consistency.

---

## What is a Preferred Leader?

The originally designated leader replica, usually the first replica in the replica list.

---

## What is Preferred Leader Election?

The process of moving leadership back to the preferred leader after recovery.

---

## What is Unclean Leader Election?

Election of a replica that is not part of ISR.

---

## Why is Unclean Leader Election risky?

Because it may cause data loss.

---

## What is the recommended setting for unclean leader election?

```properties
unclean.leader.election.enable=false
```

---

## What is the role of the Controller?

Managing leader elections, broker failures, metadata updates, and partition state.

---

## What happens when a follower broker fails?

Usually nothing major.

Leader continues serving traffic.

---

## What happens when a leader broker fails?

Controller elects a new leader from ISR.

---

# Summary

Kafka Leader Election is the mechanism that keeps Kafka highly available during broker failures.

Core Topics:

- Leader Election Process
- Preferred Leader Election
- Unclean Leader Election
- Controller Role
- Broker Failure Scenarios

Election Flow:

```text
Leader Failure
      ↓
Controller Detects
      ↓
Select ISR Replica
      ↓
Elect New Leader
      ↓
Update Metadata
      ↓
Resume Processing
```

Key Principles:

```text
Leader Chosen From ISR

Preferred Leaders Improve Balance

Unclean Election Improves Availability

Controller Manages Elections
```

Production Recommendation:

```properties
replication.factor=3
acks=all
min.insync.replicas=2
unclean.leader.election.enable=false
auto.leader.rebalance.enable=true
```

Together, these configurations provide:

```text
High Availability
Fault Tolerance
Strong Consistency
Minimal Data Loss
Reliable Failover
```

which are the foundations of Kafka's distributed architecture.