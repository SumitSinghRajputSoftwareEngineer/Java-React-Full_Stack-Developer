# 4. Kafka Architecture

Kafka Architecture is one of the most important topics for interviews because almost every advanced Kafka concept is built upon it.

Once you understand the architecture, concepts like:

- Producers
- Consumers
- Replication
- ISR
- Leader Election
- Rebalancing
- Performance Optimization

become much easier to understand.

---

# High-Level Architecture

## What is Kafka Architecture?

Kafka Architecture describes how producers, consumers, brokers, topics, partitions, and replication work together to provide:

- High Throughput
- Scalability
- Fault Tolerance
- Durability
- High Availability

---

## High-Level View

```text
                Kafka Cluster

Producer 1
Producer 2
Producer 3
      |
      V

+----------------------------------+
|                                  |
|        Kafka Cluster             |
|                                  |
|  Broker1  Broker2  Broker3       |
|                                  |
+----------------------------------+

      |
      V

Consumer Group A
Consumer Group B
Consumer Group C
```

---

## Simplified Architecture

```text
Producer
    |
    V
Kafka Cluster
    |
    V
Consumer
```

---

## Real Example

```text
Order Service
      |
OrderCreated Event
      |
Kafka
      |
+-----------+------------+
|            |           |
Payment   Inventory  Notification
```

---

# Internal Architecture

Kafka internally consists of several components working together.

---

## Internal Components

```text
Producer
   |
Topic
   |
Partition
   |
Leader Replica
   |
Follower Replica
   |
Consumer
```

---

## Core Building Blocks

### Producer

Produces records.

---

### Topic

Logical grouping.

---

### Partition

Physical storage unit.

---

### Broker

Stores partitions.

---

### Replicas

Copies of partitions.

---

### Consumer

Reads records.

---

### Controller

Manages cluster metadata and leader election.

---

## Internal Workflow

```text
Producer
   |
Partition Selection
   |
Leader Partition
   |
Replication
   |
Consumer Reads
```

---

# Kafka Cluster Architecture

---

## What is a Kafka Cluster?

A Kafka Cluster is a collection of brokers working together.

---

## Example

```text
Kafka Cluster

Broker 1
Broker 2
Broker 3
Broker 4
```

---

## Why Cluster?

Single server limitations:

```text
Storage Limit
CPU Limit
Memory Limit
Network Limit
```

Cluster solves these issues.

---

## Cluster Diagram

```text
             Kafka Cluster

+----------+   +----------+   +----------+
| Broker 1 |   | Broker 2 |   | Broker 3 |
+----------+   +----------+   +----------+
```

---

## Cluster Benefits

### High Availability

Broker failures don't stop Kafka.

---

### Scalability

Add more brokers.

---

### Load Distribution

Traffic gets distributed.

---

### Fault Tolerance

Replication prevents data loss.

---

# Broker Architecture

---

## What is a Broker?

A Broker is a Kafka server responsible for:

- Storing data
- Serving producers
- Serving consumers
- Replication

---

## Broker Components

```text
Broker

Topics
Partitions
Replicas
Network Layer
Storage Layer
Request Handler
```

---

## Broker Responsibilities

### Store Messages

Writes data to disk.

---

### Handle Producer Requests

Accept records.

---

### Handle Consumer Requests

Serve records.

---

### Participate in Replication

Sync followers.

---

### Maintain Metadata

Cluster information.

---

## Example

```text
Broker 1

Topic Orders
  Partition 0

Topic Payments
  Partition 1
```

---

# Topic Architecture

---

## Topic Structure

A Topic is divided into partitions.

---

### Example

```text
Orders Topic

Partition 0
Partition 1
Partition 2
```

---

## Visualization

```text
Orders Topic

+------------+
| Partition0 |
+------------+

+------------+
| Partition1 |
+------------+

+------------+
| Partition2 |
+------------+
```

---

## Why Topics?

Topics separate data.

Example:

```text
orders
payments
shipments
customers
```

---

## Benefits

### Organization

Separate business domains.

---

### Scalability

Each topic can have multiple partitions.

---

### Independent Retention

Different retention policies.

---

# Partition Architecture

---

## What is a Partition?

A partition is the fundamental storage and parallelism unit in Kafka.

---

## Example

```text
Orders Topic

Partition 0
Partition 1
Partition 2
Partition 3
```

---

## Internal Structure

```text
Partition

Offset 0
Offset 1
Offset 2
Offset 3
Offset 4
```

---

## Why Partitions?

### Scalability

More partitions = More throughput.

---

### Parallel Consumption

Multiple consumers process simultaneously.

---

### Fault Tolerance

Replicated individually.

---

## Ordering

Guaranteed only within a partition.

```text
P0

Offset 1
Offset 2
Offset 3
```

Order preserved.

---

# Producer Architecture

---

## Producer Components

```text
Application
      |
Kafka Producer
      |
Serializer
      |
Partitioner
      |
Broker
```

---

## Producer Workflow

### Step 1

Application creates event.

```java
OrderCreatedEvent
```

---

### Step 2

Serializer converts object into bytes.

```text
Java Object
      ↓
Byte Array
```

---

### Step 3

Partitioner selects partition.

```text
Partition 0
Partition 1
Partition 2
```

---

### Step 4

Producer sends record.

---

### Step 5

Leader broker stores record.

---

## Producer Architecture Diagram

```text
Application
      |
Producer
      |
Serializer
      |
Partitioner
      |
Kafka Broker
```

---

# Consumer Architecture

---

## Consumer Components

```text
Consumer
     |
Deserializer
     |
Processing Logic
     |
Offset Commit
```

---

## Consumer Workflow

### Step 1

Poll records.

```java
consumer.poll()
```

---

### Step 2

Deserialize records.

---

### Step 3

Process data.

---

### Step 4

Commit offset.

---

## Consumer Diagram

```text
Kafka
   |
Consumer
   |
Deserialize
   |
Business Logic
   |
Commit Offset
```

---

# Replication Architecture

---

## Why Replication?

Without replication:

```text
Broker Failure
      |
Data Loss
```

---

With replication:

```text
Broker Failure
      |
Replica Takes Over
```

---

## Example

Replication Factor = 3

```text
Partition 0

Leader
Broker1

Follower
Broker2

Follower
Broker3
```

---

## Architecture

```text
            Producer
                |
                V

        Leader Partition
           Broker1
           /      \
          /        \
         V          V

      Broker2    Broker3
      Follower   Follower
```

---

## Replication Flow

```text
Producer
    |
Leader
    |
Followers
```

---

## Important Rule

Producer writes only to leader.

Followers replicate data.

---

# Request Processing Flow

Kafka follows a specific flow when handling requests.

---

# Producer Request Flow

---

## Step 1

Producer creates record.

---

## Step 2

Producer fetches metadata.

```text
Which broker is leader?
```

---

## Step 3

Partition selected.

---

## Step 4

Request sent to leader broker.

---

## Step 5

Leader writes record.

---

## Step 6

Followers replicate record.

---

## Step 7

Acknowledgement returned.

---

## Diagram

```text
Producer
    |
Metadata Request
    |
Leader Broker
    |
Disk Write
    |
Replication
    |
ACK
```

---

# Consumer Request Flow

---

## Step 1

Consumer joins group.

---

## Step 2

Partitions assigned.

---

## Step 3

Consumer polls records.

---

## Step 4

Broker returns records.

---

## Step 5

Consumer processes records.

---

## Step 6

Offset committed.

---

## Diagram

```text
Consumer
     |
Poll Request
     |
Broker
     |
Records
     |
Process
     |
Commit Offset
```

---

# Data Flow Inside Kafka

This is one of the most frequently asked interview topics.

---

## Complete Data Flow

```text
Application
      |
      V
Producer
      |
Serializer
      |
Partitioner
      |
Leader Broker
      |
Disk Storage
      |
Replication
      |
Consumer Poll
      |
Deserializer
      |
Business Logic
```

---

## Detailed Data Flow

### Step 1

Producer creates event.

```json
{
 "orderId":101
}
```

---

### Step 2

Serializer converts to bytes.

---

### Step 3

Partition selected.

Example:

```text
Partition 2
```

---

### Step 4

Record sent to leader.

---

### Step 5

Leader writes record to log file.

```text
Offset 100
Offset 101
Offset 102
```

---

### Step 6

Followers replicate.

---

### Step 7

Acknowledgement returned.

---

### Step 8

Consumer polls data.

---

### Step 9

Consumer processes data.

---

### Step 10

Offset committed.

---

## End-to-End Architecture

```text
                    Kafka Cluster

Producer
    |
    V

+--------------------------------------+
| Broker 1                             |
| Leader Partition                     |
+--------------------------------------+

      |
      | Replication
      V

+--------------------------------------+
| Broker 2                             |
| Follower Replica                     |
+--------------------------------------+

      |
      V

+--------------------------------------+
| Broker 3                             |
| Follower Replica                     |
+--------------------------------------+

      |
      V

Consumer Group

Consumer 1
Consumer 2
Consumer 3
```

---

# Interview Questions

## What are the major components of Kafka Architecture?

- Producer
- Consumer
- Topic
- Partition
- Broker
- Cluster
- Leader
- Follower
- Replication
- Consumer Group

---

## Why does Kafka use partitions?

To achieve:

- Parallelism
- Scalability
- High Throughput

---

## Why does Kafka use replication?

To provide:

- High Availability
- Fault Tolerance
- Data Durability

---

## Does Producer write to Followers?

No.

```text
Producer → Leader Only
```

Followers only replicate.

---

## Can Consumer read from Followers?

By default:

```text
No
```

Consumers read from leaders.

(Some advanced configurations allow follower fetching.)

---

## What happens when Leader fails?

Kafka elects a new leader from the ISR replicas.

---

# Summary

Kafka Architecture is built around:

- Producers
- Consumers
- Topics
- Partitions
- Brokers
- Replication

The core workflow is:

```text
Producer
    ↓
Topic
    ↓
Partition
    ↓
Leader Broker
    ↓
Replication
    ↓
Consumer
```

This architecture enables Kafka to provide:

- High Throughput
- Horizontal Scalability
- Fault Tolerance
- Durability
- Real-Time Event Streaming

Understanding Kafka Architecture thoroughly is essential before moving to Topic Management, Partitions, Producers, Consumers, Replication Internals, and Kafka Storage Internals.