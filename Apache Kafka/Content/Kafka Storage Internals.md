
Kafka is often described as:

```text
Distributed Commit Log
```

Understanding Kafka Storage Internals is crucial because Kafka's exceptional performance comes largely from its storage architecture.

Unlike traditional messaging systems that keep messages in memory and delete them after consumption, Kafka stores messages on disk in a highly optimized append-only log structure.

This design enables:

```text
High Throughput

Low Latency

Durability

Scalability

Fault Tolerance
```

---

# Why Kafka Storage Internals Matter?

Understanding storage internals helps explain:

```text
Why Kafka Is Fast

How Kafka Stores Billions Of Messages

How Retention Works

How Data Is Deleted

How Consumers Read Efficiently
```

---

# Traditional Messaging System

```text
Producer
     ↓
Queue
     ↓
Consumer
     ↓
Message Deleted
```

---

Problem:

```text
Consumers Cannot Re-read Data

Limited Scalability
```

---

# Kafka Approach

```text
Producer
     ↓
Kafka Log
     ↓
Consumer
```

---

Messages remain stored even after consumption.

---

Benefits:

```text
Replay Capability

Multiple Consumers

High Durability
```

---

# Kafka Log Structure

The Kafka Log is the fundamental storage unit.

---

# What is a Kafka Log?

A Kafka Log is an append-only sequence of records stored on disk.

---

Think of it like:

```text
A File
```

where new messages are always added at the end.

---

# Example

Partition:

```text
orders-0
```

Records:

```text
Offset 0 -> Order A

Offset 1 -> Order B

Offset 2 -> Order C

Offset 3 -> Order D
```

---

Visualization:

```text
Kafka Log

+------------------+
| Offset 0         |
+------------------+
| Offset 1         |
+------------------+
| Offset 2         |
+------------------+
| Offset 3         |
+------------------+
```

---

# Append-Only Design

Kafka never inserts data in the middle.

---

Kafka never updates existing records.

---

Kafka only:

```text
Append New Records
```

---

Example:

```text
Offset 0
Offset 1
Offset 2
Offset 3
Offset 4 ← New Record
```

---

Benefits:

```text
Sequential Disk Writes

Minimal Disk Seek

High Throughput
```

---

# Topic Storage Structure

Topic:

```text
orders
```

Partitions:

```text
P0
P1
P2
```

---

Stored As:

```text
orders-0

orders-1

orders-2
```

---

Each partition has its own log.

---

Visualization:

```text
orders Topic

P0 -> Log File

P1 -> Log File

P2 -> Log File
```

---

# Kafka Log Segments

Kafka does not store all data in one huge file.

---

Instead:

```text
Partition Log
       ↓
Multiple Segments
```

---

# What is a Log Segment?

A Log Segment is a chunk of a partition log stored as a separate file.

---

Example:

Partition:

```text
orders-0
```

---

Segment Files:

```text
00000000000000000000.log

00000000000000001000.log

00000000000000002000.log
```

---

Each file stores a range of offsets.

---

# Visualization

```text
Partition Log

Segment 1
Offsets 0-999

Segment 2
Offsets 1000-1999

Segment 3
Offsets 2000-2999
```

---

# Why Log Segments?

Without segments:

```text
Single Huge File
```

---

Problems:

```text
Difficult Deletion

Slow Retention Cleanup

Large Indexes
```

---

Segments solve these problems.

---

Benefits:

```text
Efficient Retention

Efficient Cleanup

Faster Recovery
```

---

# Active Segment

The latest segment is called:

```text
Active Segment
```

---

New records are appended here.

---

Example:

```text
Segment 1 -> Closed

Segment 2 -> Closed

Segment 3 -> Active
```

---

# Segment Rolling

Kafka creates a new segment when:

---

## Size Limit Reached

Configuration:

```properties
log.segment.bytes
```

---

Default:

```text
1 GB
```

---

## Time Limit Reached

Configuration:

```properties
log.roll.ms
```

or

```properties
log.roll.hours
```

---

Example:

```text
Every 24 Hours
```

---

# Segment Files

Each segment actually consists of multiple files.

---

Example:

```text
00000000000000000000.log

00000000000000000000.index

00000000000000000000.timeindex
```

---

These work together.

---

# Offset Index

One of Kafka's most important optimizations.

---

# Problem

Suppose a consumer wants:

```text
Offset 1,000,000
```

---

Without indexing:

```text
Scan Entire Log
```

---

Very slow.

---

# Solution: Offset Index

Kafka maintains:

```text
Offset → Physical Position
```

mapping.

---

# What is Offset Index?

An index file that maps offsets to locations inside the log segment.

---

Example:

```text
Offset 1000 → Byte Position 20000

Offset 2000 → Byte Position 45000

Offset 3000 → Byte Position 70000
```

---

Visualization:

```text
Offset Index

1000 → 20000

2000 → 45000

3000 → 70000
```

---

# Consumer Read Flow

Consumer requests:

```text
Offset 2500
```

---

Kafka:

```text
Check Index
      ↓
Jump To Position
      ↓
Read Data
```

---

Benefits:

```text
Fast Lookup

Efficient Reads

Low Latency
```

---

# Time Index

Kafka can also search using timestamps.

---

# What is Time Index?

Maps:

```text
Timestamp
       ↓
Offset
```

---

Used when consumers request records by time.

---

# Example

```text
10:00 AM → Offset 1000

10:30 AM → Offset 5000

11:00 AM → Offset 9000
```

---

Visualization:

```text
Time Index

Timestamp
     ↓
Offset
```

---

# Why Needed?

Consumer wants:

```text
Messages From Yesterday 10 AM
```

---

Kafka:

```text
Timestamp Lookup
      ↓
Find Offset
      ↓
Read Records
```

---

# Example API

```java
offsetsForTimes(...)
```

---

Uses:

```text
Time Index
```

internally.

---

# Segment File Structure

Each segment consists of:

```text
.log

.index

.timeindex
```

---

Visualization:

```text
Segment

+------------+
| .log       |
+------------+

+------------+
| .index     |
+------------+

+------------+
| .timeindex |
+------------+
```

---

# Kafka Storage Directory

Kafka stores data under:

```properties
log.dirs
```

---

Example:

```properties
log.dirs=/data/kafka-logs
```

---

Directory Structure:

```text
kafka-logs/

orders-0/

orders-1/

payments-0/
```

---

Inside Partition:

```text
orders-0/

00000000000000000000.log

00000000000000000000.index

00000000000000000000.timeindex
```

---

# Data Retention

One of Kafka's most important storage features.

---

# What is Data Retention?

Retention defines:

```text
How Long Kafka Keeps Data
```

---

Unlike traditional queues:

```text
Consumed Messages
Are Not Immediately Deleted
```

---

Kafka keeps them based on retention policies.

---

# Example

Retention:

```text
7 Days
```

---

Message Produced:

```text
Monday
```

---

Deleted:

```text
Next Monday
```

---

Even if consumed.

---

# Retention Types

Kafka supports:

```text
Time-Based Retention

Size-Based Retention
```

---

# Time-Based Retention

Configuration:

```properties
log.retention.hours
```

---

Example:

```properties
log.retention.hours=168
```

---

Meaning:

```text
7 Days
```

---

After:

```text
7 Days
```

old segments are deleted.

---

# Time-Based Example

```text
Monday Data

Tuesday Data

Wednesday Data
```

---

After 7 days:

```text
Monday Data Deleted
```

---

# Size-Based Retention

Configuration:

```properties
log.retention.bytes
```

---

Example:

```properties
log.retention.bytes=100GB
```

---

Meaning:

```text
Maximum Log Size
```

---

When exceeded:

```text
Old Segments Removed
```

---

# Retention Cleanup

Kafka removes:

```text
Entire Segments
```

---

Not individual messages.

---

Example:

```text
Segment 1 -> Expired

Segment 2 -> Active
```

---

Kafka deletes:

```text
Segment 1
```

---

Very efficient.

---

# Disk Management

Kafka is optimized for disk-based storage.

---

# Why Disk Instead of Memory?

Traditional assumption:

```text
Disk Is Slow
```

---

Kafka challenges this.

---

Using:

```text
Sequential Writes

OS Page Cache

Zero Copy
```

Kafka achieves very high performance.

---

# Sequential Writes

Kafka writes records sequentially.

---

Example:

```text
Offset 1

Offset 2

Offset 3

Offset 4
```

---

Benefits:

```text
Minimal Disk Seek

Maximum Throughput
```

---

# OS Page Cache

Kafka relies heavily on:

```text
Operating System Page Cache
```

---

Flow:

```text
Producer
      ↓
Kafka
      ↓
OS Cache
      ↓
Disk
```

---

Frequently read data remains in memory.

---

Benefits:

```text
Faster Reads

Reduced Disk I/O
```

---

# Zero-Copy Transfer

Advanced Kafka optimization.

---

Traditional Flow:

```text
Disk
 ↓
Kernel Buffer
 ↓
Application Buffer
 ↓
Socket Buffer
 ↓
Network
```

---

Kafka uses:

```text
sendfile()
```

---

Flow:

```text
Disk
 ↓
Kernel
 ↓
Network
```

---

Benefits:

```text
Lower CPU Usage

Higher Throughput

Lower Latency
```

---

# Disk Usage Monitoring

Important production metric.

---

Monitor:

```text
Disk Utilization

Log Growth

Segment Count

Retention Cleanup
```

---

# Important Storage Configurations

---

## log.dirs

Storage location.

```properties
log.dirs=/data/kafka
```

---

## log.segment.bytes

Segment size.

```properties
log.segment.bytes=1073741824
```

---

## log.retention.hours

Retention duration.

```properties
log.retention.hours=168
```

---

## log.retention.bytes

Maximum storage size.

```properties
log.retention.bytes=107374182400
```

---

## log.roll.hours

Segment rollover time.

```properties
log.roll.hours=24
```

---

# Real-World Example

Topic:

```text
orders
```

Traffic:

```text
50 Million Events Per Day
```

---

Configuration:

```text
RF = 3

Retention = 7 Days

Segment Size = 1 GB
```

---

Storage Flow:

```text
Producer
      ↓
Partition Log
      ↓
Active Segment
      ↓
Index Files
      ↓
Retention Cleanup
```

---

Benefits:

```text
Durability

Replay Capability

Efficient Reads

Scalable Storage
```

---

# Interview Questions

## What is a Kafka Log?

An append-only sequence of records stored on disk.

---

## Why is Kafka fast despite using disks?

Because of:

```text
Sequential Writes

OS Page Cache

Zero Copy Transfer
```

---

## What is a Log Segment?

A chunk of a partition log stored as a separate file.

---

## Why does Kafka use log segments?

To enable efficient retention, cleanup, indexing, and recovery.

---

## What is the Active Segment?

The segment currently receiving new records.

---

## What is an Offset Index?

A mapping of:

```text
Offset → Physical File Position
```

used for fast lookups.

---

## What is a Time Index?

A mapping of:

```text
Timestamp → Offset
```

used for time-based searches.

---

## What is Data Retention?

The policy that determines how long Kafka keeps data.

---

## Does Kafka delete consumed messages?

```text
No
```

Kafka deletes data based on retention policies.

---

## What is Zero Copy?

A technique that transfers data directly from disk to network with minimal CPU involvement.

---

# Summary

Kafka Storage Internals are built around an append-only log architecture optimized for performance and durability.

Core Components:

- Kafka Log Structure
- Log Segments
- Offset Index
- Time Index
- Data Retention
- Disk Management

Storage Architecture:

```text
Topic
   ↓
Partition
   ↓
Log
   ↓
Segments
   ↓
Index Files
```

Each Segment Contains:

```text
.log
.index
.timeindex
```

Kafka Performance Comes From:

```text
Sequential Writes
OS Page Cache
Zero Copy Transfer
Append-Only Logs
```

Retention Model:

```text
Produce Message
       ↓
Store In Segment
       ↓
Consume Multiple Times
       ↓
Retain For Configured Period
       ↓
Delete Expired Segments
```

This storage architecture is one of the primary reasons Kafka can handle:

```text
Millions Of Messages Per Second

Petabytes Of Data

Thousands Of Consumers
```

while maintaining durability, scalability, and high performance.