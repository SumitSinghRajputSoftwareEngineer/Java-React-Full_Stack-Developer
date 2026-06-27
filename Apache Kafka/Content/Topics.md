
Topics are one of the most fundamental concepts in Kafka.

Everything in Kafka revolves around Topics because:

- Producers write data to Topics
- Consumers read data from Topics
- Partitions belong to Topics
- Replication occurs at Topic partitions
- Retention policies are configured on Topics

Without Topics, Kafka cannot organize or manage data.

---

# What is a Topic?

## Definition

A Topic is a logical category or stream of records where Kafka stores messages.

Think of a Topic as a container that groups similar events together.

---

## Simple Analogy

Database Analogy:

```text
Database
    |
  Table
```

Kafka Analogy:

```text
Kafka Cluster
      |
    Topic
```

---

## Example

E-commerce application:

```text
orders
payments
shipments
customers
inventory
```

Each represents a separate topic.

---

## Topic Example

### orders Topic

```text
Order Created
Order Updated
Order Cancelled
Order Delivered
```

---

### payments Topic

```text
Payment Initiated
Payment Completed
Payment Failed
Refund Processed
```

---

## Visualization

```text
Orders Topic

+----------------------+
| Order Created        |
+----------------------+

| Order Updated        |
+----------------------+

| Order Cancelled      |
+----------------------+
```

---

## Producer and Consumer Relationship

```text
Producer
    |
    V
Orders Topic
    |
    V
Consumer
```

---

## Multiple Consumers

```text
Orders Topic
      |
+-----+-----+-----+
|     |     |     |
Billing Analytics Email
```

Same topic can be consumed by multiple applications.

---

## Important Characteristics

### Topics are Logical

A topic itself does not physically store data.

Actual storage happens inside partitions.

---

### Topics are Distributed

Topics are split into partitions.

---

### Topics are Durable

Data is persisted to disk.

---

### Topics Support Retention

Messages remain available even after consumption.

---

# Topic Naming Conventions

Naming conventions become extremely important in production systems.

---

# Why Naming Conventions Matter?

Without standards:

```text
Orders
orders
ORDERS
OrderTopic
Order_Data
```

Chaos occurs.

---

## Recommended Convention

Use:

```text
lowercase
```

and

```text
hyphen-separated
```

---

## Good Examples

```text
orders

customer-events

payment-transactions

inventory-updates

user-notifications
```

---

## Bad Examples

```text
Orders

ORDERS

OrderData

MyTopic

Topic1
```

---

# Naming Strategies

---

## Business Domain Based

```text
orders

payments

inventory

shipments
```

---

## Service Based

```text
order-service

payment-service

customer-service
```

---

## Event Based

```text
order-created

order-updated

payment-completed
```

---

## Environment Based

```text
dev-orders

qa-orders

prod-orders
```

---

## Multi-Tenant Naming

```text
client-a-orders

client-b-orders
```

---

# Naming Best Practices

### Use Lowercase

```text
orders
```

---

### Avoid Spaces

Bad:

```text
customer orders
```

Good:

```text
customer-orders
```

---

### Avoid Special Characters

Bad:

```text
orders@2025
```

Good:

```text
orders-2025
```

---

### Keep Names Meaningful

Bad:

```text
t1
```

Good:

```text
payment-transactions
```

---

# Topic Creation

Topics can be created manually or automatically.

---

# Manual Topic Creation

Most production systems create topics manually.

---

## Command

```bash
kafka-topics.sh \
--create \
--topic orders \
--bootstrap-server localhost:9092 \
--partitions 3 \
--replication-factor 1
```

---

## Explanation

```text
orders
```

Topic name.

---

```text
partitions=3
```

Three partitions.

---

```text
replication-factor=1
```

One replica copy.

---

# Verify Topic Creation

```bash
kafka-topics.sh \
--list \
--bootstrap-server localhost:9092
```

---

Output:

```text
orders
payments
inventory
```

---

# Describe Topic

```bash
kafka-topics.sh \
--describe \
--topic orders \
--bootstrap-server localhost:9092
```

---

Example Output

```text
Topic: orders

PartitionCount:3

ReplicationFactor:3
```

---

# Automatic Topic Creation

Kafka can automatically create topics.

---

Configuration:

```properties
auto.create.topics.enable=true
```

---

Example

Producer sends to:

```text
orders
```

Topic doesn't exist.

Kafka automatically creates it.

---

# Why Production Disables Auto Creation

```properties
auto.create.topics.enable=false
```

Reasons:

- Prevent accidental topics
- Better governance
- Better configuration control

---

# Topic Configuration

Every topic has configurable settings.

---

# View Topic Configuration

```bash
kafka-configs.sh \
--describe \
--entity-type topics \
--entity-name orders
```

---

# Important Topic Configurations

---

## Retention Time

```properties
retention.ms
```

---

Example

```properties
retention.ms=604800000
```

7 Days.

---

## Retention Size

```properties
retention.bytes
```

---

Example

```properties
retention.bytes=1073741824
```

1 GB.

---

## Cleanup Policy

```properties
cleanup.policy
```

Values:

```text
delete
compact
```

---

## Compression

```properties
compression.type
```

---

Example

```properties
compression.type=gzip
```

---

## Maximum Message Size

```properties
max.message.bytes
```

---

Example

```properties
max.message.bytes=1048576
```

1 MB.

---

# Topic Retention

One of Kafka's most important features.

---

# What is Retention?

Retention determines:

```text
How Long Messages Stay In Kafka
```

---

## Traditional Queue

```text
Consume Message
       ↓
Delete Message
```

---

## Kafka

```text
Consume Message
       ↓
Keep Message
```

---

# Time-Based Retention

---

Example

```properties
retention.ms=604800000
```

7 Days.

---

Flow:

```text
Day 1 Message
Day 2 Message
Day 3 Message
...
Day 8
```

Day 1 message removed.

---

# Size-Based Retention

---

Example

```properties
retention.bytes=10GB
```

When storage exceeds:

```text
10 GB
```

Old messages are deleted.

---

# Benefits

### Event Replay

Consumers can re-read messages.

---

### Auditing

Historical records available.

---

### Recovery

Failed consumers can recover.

---

# Topic Deletion

---

# Delete Topic

```bash
kafka-topics.sh \
--delete \
--topic orders \
--bootstrap-server localhost:9092
```

---

## What Happens?

```text
Topic Metadata Removed

Partitions Removed

Data Deleted
```

---

## Verify

```bash
kafka-topics.sh --list
```

Topic disappears.

---

# Production Consideration

Deletion is dangerous because:

```text
Data Loss
```

may occur permanently.

---

Often disabled:

```properties
delete.topic.enable=false
```

---

# Topic Compaction

One of Kafka's most powerful features.

---

# What is Log Compaction?

Compaction keeps only the latest value for each key.

---

## Normal Topic

```text
Key=A Value=10

Key=A Value=20

Key=A Value=30

Key=A Value=40
```

All records retained.

---

## Compacted Topic

```text
Key=A Value=40
```

Older versions removed.

---

# Why Use Compaction?

Maintains:

```text
Latest State
```

instead of:

```text
Complete History
```

---

# Example

Customer Updates

```text
Customer1 -> Name=A

Customer1 -> Name=B

Customer1 -> Name=C
```

---

After compaction:

```text
Customer1 -> Name=C
```

---

# Enable Compaction

```properties
cleanup.policy=compact
```

---

# Typical Use Cases

### Customer Profiles

---

### User Preferences

---

### Configuration Data

---

### Cache Rebuilding

---

### State Management

---

# Topic Partitioning

Topics are divided into partitions.

---

# Why Partition Topics?

Without partitions:

```text
Single Storage Unit
```

Limited scalability.

---

With partitions:

```text
Orders Topic

Partition 0

Partition 1

Partition 2
```

---

# Visualization

```text
Orders Topic

+-------------+
| Partition 0 |
+-------------+

+-------------+
| Partition 1 |
+-------------+

+-------------+
| Partition 2 |
+-------------+
```

---

# Benefits

### Scalability

More partitions = more throughput.

---

### Parallel Processing

Multiple consumers work simultaneously.

---

### Fault Tolerance

Each partition replicated separately.

---

# Example

Orders Topic

```text
Partition 0

Offset 0
Offset 1
Offset 2
```

---

```text
Partition 1

Offset 0
Offset 1
Offset 2
```

---

# Ordering

Guaranteed only within a partition.

---

Example:

```text
Partition 0

Order1
Order2
Order3
```

Order preserved.

---

Not guaranteed across partitions.

---

# Internal Topics

Kafka creates some topics automatically.

These are called:

```text
Internal Topics
```

---

# Why Internal Topics Exist?

Kafka itself requires topics for internal operations.

---

# __consumer_offsets

Most important internal topic.

---

Purpose:

```text
Store Consumer Offsets
```

---

Example

```text
Consumer Group A

Partition 0

Current Offset = 100
```

Stored in:

```text
__consumer_offsets
```

---

# Benefits

Consumer restart:

```text
Resume From Offset 100
```

---

# __transaction_state

Used for:

```text
Kafka Transactions
Exactly Once Processing
```

---

Stores:

```text
Transaction Metadata
```

---

# Kafka Streams Internal Topics

Kafka Streams creates internal topics.

---

Examples:

```text
app-store-changelog

app-repartition-topic
```

---

Purpose:

```text
State Recovery

Repartitioning

Fault Tolerance
```

---

# Internal Topic Naming

Usually begins with:

```text
__
```

or

```text
application-name-
```

---

# View Internal Topics

```bash
kafka-topics.sh \
--list \
--bootstrap-server localhost:9092
```

---

Example

```text
orders

payments

__consumer_offsets

__transaction_state
```

---

# Production Topic Design Best Practices

---

## Separate Business Domains

Good:

```text
orders

payments

inventory
```

---

Bad:

```text
all-events
```

---

## Use Meaningful Names

---

## Configure Retention Carefully

---

## Choose Proper Partition Count

---

## Enable Replication

```text
Replication Factor = 3
```

---

## Avoid Excessive Partitions

Too many partitions increase:

```text
Memory Usage
Metadata Overhead
Leader Elections
```

---

# Interview Questions

## What is a Topic?

A logical category or stream of records where Kafka stores messages.

---

## Do Topics Physically Store Data?

No.

Topics are logical constructs.

Actual data is stored in partitions.

---

## Can Multiple Consumers Read the Same Topic?

```text
Yes
```

Using different consumer groups.

---

## What is Retention?

The duration or size limit that determines how long Kafka keeps messages.

---

## What is Topic Compaction?

A cleanup policy that keeps only the latest record for each key.

---

## What is the Difference Between Delete and Compact Cleanup Policies?

### Delete

Removes old messages based on retention.

---

### Compact

Keeps latest value per key.

---

## What is __consumer_offsets?

An internal Kafka topic used to store consumer group offsets.

---

## Are Internal Topics Created Automatically?

```text
Yes
```

Kafka creates them as needed.

---

# Summary

Topics are the logical foundation of Kafka.

They provide:

```text
Data Organization
Scalability
Durability
Retention
Event Streaming
```

Key concepts include:

- Topic Creation
- Topic Configuration
- Topic Retention
- Topic Deletion
- Topic Compaction
- Topic Partitioning
- Internal Topics

Understanding Topics thoroughly is essential before learning:

```text
Partitions
Offsets
Producers
Consumers
Replication
Storage Internals
```

because every Kafka operation revolves around Topics and their Partitions.