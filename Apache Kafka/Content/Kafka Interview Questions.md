
This section contains the most frequently asked Kafka interview questions ranging from beginner to advanced level, including real-world production support and scenario-based questions.

---

# Beginner Questions

These questions are commonly asked for:

```text
Junior Developer

Associate Developer

Software Engineer

Backend Developer
```

---

## What is Apache Kafka?

Apache Kafka is a distributed event streaming platform used for:

```text
Publishing Messages

Consuming Messages

Real-Time Streaming

Event-Driven Architectures
```

---

## Why was Kafka created?

Kafka was created at :contentReference[oaicite:0]{index=0} to handle:

```text
Large Volumes Of Data

Real-Time Event Processing

Scalable Messaging
```

---

## What are the main components of Kafka?

```text
Producer

Consumer

Topic

Partition

Broker

Consumer Group

Cluster
```

---

## What is a Topic?

A Topic is a logical category where messages are stored.

Example:

```text
orders

payments

notifications
```

---

## What is a Partition?

A Partition is a physical subdivision of a topic.

Used for:

```text
Scalability

Parallel Processing
```

---

## What is an Offset?

Offset is a unique sequence number assigned to every record within a partition.

Example:

```text
Offset 0

Offset 1

Offset 2
```

---

## What is a Producer?

A Producer publishes messages to Kafka topics.

---

## What is a Consumer?

A Consumer reads messages from Kafka topics.

---

## What is a Consumer Group?

A Consumer Group is a collection of consumers working together to consume messages.

---

## Can multiple consumers read the same message?

Yes.

Different consumer groups can consume the same message independently.

---

## What is a Broker?

A Broker is a Kafka server that stores and serves data.

---

## What is a Kafka Cluster?

A group of Kafka brokers working together.

---

## What is Replication?

Replication creates copies of partitions across brokers for fault tolerance.

---

## What is the default port of Kafka?

```text
9092
```

---

## What is the default replication factor?

Kafka has no mandatory default replication factor at the protocol level, but many production environments commonly use:

```text
Replication Factor = 3
```

---

## What is ZooKeeper?

ZooKeeper was previously used for:

```text
Metadata Management

Leader Election

Cluster Coordination
```

---

## What replaced ZooKeeper?

```text
KRaft (Kafka Raft)
```

---

# Intermediate Questions

These questions are frequently asked for:

```text
3+ Years Experience

Backend Developer

Java Developer

Microservices Developer
```

---

## What happens when a Producer sends a message?

Flow:

```text
Producer
   ↓
Partition Selection
   ↓
Leader Partition
   ↓
Replication
   ↓
Acknowledgement
```

---

## How does Kafka decide which partition to use?

Based on:

```text
Message Key

Partitioner

Round Robin

Sticky Partitioner
```

---

## What happens if no key is provided?

Kafka typically uses the sticky partitioner to improve batching efficiency.

---

## What is ISR?

ISR stands for:

```text
In-Sync Replica
```

Replicas fully caught up with the leader.

---

## What is Leader Replica?

The replica responsible for:

```text
Reads

Writes
```

---

## What is Follower Replica?

Replicas that copy data from the leader.

---

## What is Rebalancing?

The process of redistributing partitions among consumers in a consumer group.

---

## Why does Rebalancing happen?

```text
Consumer Joined

Consumer Left

Partition Count Changed
```

---

## What is Consumer Lag?

Difference between:

```text
Latest Offset

Consumer Offset
```

Formula:

```text
Lag = Latest Offset - Consumer Offset
```

---

## What is Log Compaction?

Kafka keeps only the latest value for each key.

---

## Difference Between Retention and Compaction?

| Retention | Compaction |
|------------|------------|
| Removes Old Records | Keeps Latest Record Per Key |
| Time/Size Based | Key Based |

---

## What is ACK=0?

Producer does not wait for acknowledgement.

Fastest but least reliable.

---

## What is ACK=1?

Producer waits for leader acknowledgement.

---

## What is ACK=all?

Producer waits for all ISR acknowledgements.

Most reliable.

---

## What is Idempotent Producer?

Prevents duplicate messages during retries.

---

## What is Exactly Once Semantics?

Guarantees:

```text
No Message Loss

No Duplicate Processing
```

---

## What is Kafka Streams?

A library for stream processing directly on Kafka data.

---

## What is Kafka Connect?

A framework used to move data between Kafka and external systems.

---

# Advanced Questions

Common for:

```text
Senior Developer

Lead Engineer

Architect

Staff Engineer
```

---

## Explain Kafka Internal Storage.

Kafka stores messages in:

```text
Log Files

Segments

Indexes
```

Structure:

```text
Partition
    ↓
Segment Files
    ↓
Index Files
```

---

## What are Segment Files?

Kafka splits logs into smaller files called segments.

Example:

```text
000000000000.log

000000001000.log
```

---

## What are Offset Index Files?

Used to locate records quickly.

---

## Explain Leader Election.

When leader fails:

```text
Controller
      ↓
Select New Leader
      ↓
ISR Member Promoted
```

---

## What is Unclean Leader Election?

Allows out-of-sync replicas to become leader.

Risk:

```text
Data Loss
```

---

## How does Kafka achieve High Throughput?

Through:

```text
Sequential Disk Writes

Batching

Compression

Partitioning

Zero Copy Transfer
```

---

## Why is Kafka faster than traditional messaging systems?

Because Kafka uses:

```text
Append-Only Logs

Sequential Writes

OS Page Cache
```

---

## Explain Kafka Transactions.

Transactions ensure:

```text
Atomic Writes

Exactly Once Processing
```

---

## What is Transaction Coordinator?

Component that manages transaction lifecycle.

---

## Explain EOS Architecture.

Components:

```text
Idempotent Producer

Transactions

Read Committed Consumers
```

---

## Difference Between Kafka and RabbitMQ?

| Kafka | RabbitMQ |
|---------|----------|
| Event Streaming | Message Broker |
| Pull Model | Push Model |
| High Throughput | Flexible Routing |
| Replay Supported | Limited Replay |

---

## Difference Between Kafka and ActiveMQ?

| Kafka | ActiveMQ |
|---------|----------|
| Distributed Log | Traditional Queue |
| High Scalability | Lower Scalability |
| Event Streaming | Message Queuing |

---

## Explain KRaft Architecture.

KRaft replaces ZooKeeper.

Components:

```text
Controller Quorum

Metadata Log

Raft Consensus
```

---

# Scenario-Based Questions

Most important section for experienced candidates.

---

## Scenario 1

### One Consumer Is Slow. What Happens?

Result:

```text
Consumer Lag Increases
```

---

Possible solutions:

```text
Increase Consumers

Increase Partitions

Optimize Processing
```

---

## Scenario 2

### Broker Suddenly Crashes. What Happens?

Flow:

```text
Broker Failure
      ↓
Leader Lost
      ↓
Controller Election
      ↓
New Leader Selected
```

---

Consumers continue processing.

---

## Scenario 3

### One Million Messages Per Second Need To Be Processed.

How would you design?

Answer:

```text
Multiple Brokers

High Partition Count

Consumer Groups

Batch Processing

Compression
```

---

## Scenario 4

### Duplicate Messages Are Appearing.

Possible causes:

```text
Producer Retries

Consumer Reprocessing

Offset Issues
```

---

Solution:

```text
Enable Idempotence

Use EOS

Deduplication Logic
```

---

## Scenario 5

### Consumer Lag Keeps Increasing.

Possible reasons:

```text
Slow Processing

Insufficient Consumers

External Dependency Delays
```

---

Solutions:

```text
Scale Consumers

Increase Partitions

Optimize Business Logic
```

---

## Scenario 6

### Database Is Down While Consuming Messages.

Solution:

```text
Retry

Exponential Backoff

Retry Topics

DLT
```

---

## Scenario 7

### Payment Events Cannot Be Lost.

Recommended:

```text
ACK=all

Replication=3

Idempotent Producer

Transactions

EOS
```

---

## Scenario 8

### Need To Replay Events.

Kafka supports replay because:

```text
Offsets Can Be Reset
```

---

Reset:

```text
earliest
```

---

## Scenario 9

### Topic Has 10 Partitions And 20 Consumers.

How many consumers actively consume?

Answer:

```text
10 Consumers Active

10 Consumers Idle
```

---

Reason:

```text
One Consumer Per Partition
```

within a consumer group.

---

## Scenario 10

### Topic Has 100 Partitions.

How many consumers should be created?

Answer:

```text
Depends On Throughput Requirements
```

Maximum parallelism:

```text
100 Consumers
```

for one consumer group.

---

# Production Support Questions

These are commonly asked in support and operations interviews.

---

## How Do You Check Consumer Lag?

Tools:

```text
Kafka Consumer Groups Command

Burrow

Prometheus

Grafana
```

---

Example:

```bash
kafka-consumer-groups.sh \
--describe
```

---

## How Do You Identify Broker Issues?

Check:

```text
Broker Logs

Disk Usage

CPU Usage

Network Usage
```

---

## How Do You Monitor Kafka?

Metrics:

```text
Consumer Lag

ISR Count

Broker Health

Request Latency

Disk Usage
```

---

## What Happens If ISR Shrinks?

Possible causes:

```text
Network Issues

Slow Replica

Broker Failure
```

---

Risk:

```text
Reduced Fault Tolerance
```

---

## Topic Size Suddenly Increased. What Do You Check?

Check:

```text
Retention Settings

Producer Traffic

Data Duplication
```

---

## Consumers Are Not Receiving Messages. What Would You Verify?

Check:

```text
Topic Name

Consumer Group

Offsets

Network Connectivity

Broker Health
```

---

## How Do You Handle Poison Pill Messages?

```text
Retry Few Times

Move To DLT

Investigate
```

---

## Kafka Disk Usage Is 95%. What Should You Do?

Options:

```text
Increase Storage

Reduce Retention

Add Brokers

Delete Unused Topics
```

---

## How Do You Safely Add Brokers?

Steps:

```text
Add Broker

Start Broker

Reassign Partitions

Verify Replication
```

---

## How Do You Recover From Data Center Failure?

Options:

```text
MirrorMaker 2

Cross Region Replication

Disaster Recovery Cluster
```

---

# Frequently Asked Interview Questions (Top 25)

## What is Kafka?

## What is a Topic?

## What is a Partition?

## What is an Offset?

## What is Consumer Lag?

## What is ISR?

## What is Rebalancing?

## What is ACK=all?

## What is Idempotent Producer?

## What is Exactly Once Semantics?

## What is Kafka Streams?

## What is Kafka Connect?

## What is Log Compaction?

## What is Consumer Group?

## What is Leader Election?

## What is KRaft?

## What is DLT?

## What is CDC?

## What is Outbox Pattern?

## What is Saga Pattern?

## How does Kafka ensure reliability?

## How does Kafka scale?

## How do you monitor Kafka?

## How do you troubleshoot lag?

## How do you design Kafka for payments?

---

# Kafka Interview Cheat Sheet

## Reliability

```text
ACK=all

Replication Factor=3

ISR

Idempotence

Transactions
```

---

## Scalability

```text
Partitions

Consumer Groups

Multiple Brokers
```

---

## Recovery

```text
Retries

Retry Topics

DLT

Replay
```

---

## Monitoring

```text
Lag

ISR

Disk Usage

Throughput

Latency
```

---

## Performance

```text
Batching

Compression

Sticky Partitioner

Sequential Writes
```

---

# Summary

Kafka interview questions generally fall into five categories:

- Beginner Questions
- Intermediate Questions
- Advanced Questions
- Scenario-Based Questions
- Production Support Questions

For experienced Java/Spring/Kafka interviews, the most important areas are:

```text
Consumer Groups

Partitions

Offsets

Rebalancing

Replication

EOS

Transactions

Kafka Streams

Kafka Connect

Error Handling

DLT

CDC

Outbox Pattern

Saga Pattern

Production Troubleshooting
```

If you can confidently answer the scenario-based and production support questions, you will be well prepared for Senior Software Engineer, Lead Engineer, and Kafka-focused backend interviews.