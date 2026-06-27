
Kafka Streams is one of the most powerful components in the Apache Kafka ecosystem.

It allows developers to build:

```text
Real-Time Data Processing Applications
```

directly on top of Kafka.

Instead of simply producing and consuming messages, Kafka Streams enables:

```text
Filtering

Transforming

Aggregating

Joining

Analyzing
```

data as it flows through Kafka.

---

# What is Kafka Streams?

## Definition

Kafka Streams is a Java library for building real-time stream processing applications and microservices using Kafka topics as input and output.

---

Unlike traditional consumers:

```text
Consumer
     ↓
Read Messages
     ↓
Process
     ↓
Store Somewhere
```

Kafka Streams provides:

```text
Built-in Stream Processing APIs
```

for handling continuous data streams.

---

# Key Characteristics

---

## Client Library

No separate cluster required.

---

Runs inside your application.

---

## Distributed

Can run on multiple instances.

---

## Fault Tolerant

Uses Kafka's replication and changelog topics.

---

## Scalable

Horizontal scaling supported.

---

## Real-Time

Processes events as they arrive.

---

# Kafka Streams vs Traditional Consumer

| Traditional Consumer | Kafka Streams |
|------------|------------|
| Manual Processing | Built-In Processing API |
| More Boilerplate Code | Less Code |
| Custom State Management | Built-In State Stores |
| Manual Aggregation | Built-In Aggregation |
| Manual Windowing | Built-In Windowing |

---

# Why Kafka Streams?

Imagine an e-commerce platform.

Orders are continuously arriving:

```text
Order 1

Order 2

Order 3

Order 4
```

---

Business requirements:

```text
Calculate Revenue

Count Orders

Detect Fraud

Generate Reports

Monitor Trends
```

---

Kafka Streams can process these events in real time.

---

# Kafka Streams Architecture

Kafka Streams follows:

```text
Input Topic
      ↓
Streams Application
      ↓
Output Topic
```

---

# High-Level Architecture

```text
Producer
    ↓
Input Topic
    ↓
Kafka Streams
    ↓
Output Topic
    ↓
Consumer
```

---

# Example

Orders Topic:

```text
Order Created
```

---

Kafka Streams:

```text
Calculate Revenue
```

---

Output Topic:

```text
Revenue Updates
```

---

# Internal Components

Kafka Streams application consists of:

```text
StreamsBuilder

Topology

Processor Nodes

State Stores

Kafka Topics
```

---

# Architecture Diagram

```text
Kafka Topic
      ↓
Source Node
      ↓
Processor Node
      ↓
State Store
      ↓
Sink Node
      ↓
Kafka Topic
```

---

# Source Node

Reads records from Kafka topics.

---

Example:

```text
orders-topic
```

---

# Processor Node

Processes records.

---

Examples:

```text
Filter

Map

Aggregate

Join
```

---

# State Store

Stores local state.

---

Examples:

```text
Counts

Totals

Windows

Session Data
```

---

# Sink Node

Writes processed data back to Kafka.

---

# Kafka Streams Processing Flow

```text
Input Topic
      ↓
Read Event
      ↓
Process Event
      ↓
Update State
      ↓
Write Result
      ↓
Output Topic
```

---

# Stream Processing

Core concept of Kafka Streams.

---

# What is Stream Processing?

Continuous processing of data as it arrives.

---

Unlike batch processing:

```text
Process Every Hour
```

Stream processing:

```text
Process Immediately
```

---

# Example

Orders:

```text
Order1

Order2

Order3
```

---

Kafka Streams:

```text
Process Instantly
```

---

Result:

```text
Real-Time Analytics
```

---

# Stream vs Batch Processing

| Stream Processing | Batch Processing |
|------------------|------------------|
| Continuous | Periodic |
| Low Latency | Higher Latency |
| Real-Time | Scheduled |
| Event-Based | File-Based |

---

# Kafka Streams DSL

Kafka Streams provides:

```java
StreamsBuilder
```

---

Example:

```java
StreamsBuilder builder =
        new StreamsBuilder();
```

---

Create Stream:

```java
KStream<String, String> stream =
        builder.stream("orders");
```

---

# Core Stream Types

---

## KStream

Represents an event stream.

---

Example:

```text
Order Events
```

---

Each event is independent.

---

# Example

```java
KStream<String, Order>
```

---

# KTable

Represents latest state per key.

---

Example:

```text
Customer Profile
```

---

Only latest value retained.

---

# Example

```java
KTable<String, Customer>
```

---

# GlobalKTable

Replicated to every Streams instance.

---

Useful for:

```text
Reference Data

Lookup Tables
```

---

# Stateless Operations

Operations that do not require previously processed records.

---

# What are Stateless Operations?

Processing where each record is handled independently.

---

No state is stored.

---

Example:

```text
Current Record Only
```

---

# Common Stateless Operations

---

## Filter

---

Removes unwanted records.

---

Example:

```java
stream.filter(
    (key, value) -> value.getAmount() > 1000
);
```

---

Input:

```text
500

1500

3000
```

---

Output:

```text
1500

3000
```

---

## Map

---

Transforms records.

---

Example:

```java
stream.map(
    (key, value) ->
        KeyValue.pair(
            key,
            value.toUpperCase()
        )
);
```

---

Input:

```text
hello
```

---

Output:

```text
HELLO
```

---

## FlatMap

---

One record becomes multiple records.

---

Example:

```text
"Java Kafka"
```

---

Output:

```text
Java

Kafka
```

---

## Peek

---

Used for logging/debugging.

---

Example:

```java
stream.peek(
    (key, value) ->
        System.out.println(value)
);
```

---

# Stateful Operations

Stateful operations require maintaining state across records.

---

# What are Stateful Operations?

Operations that depend on previously processed events.

---

Examples:

```text
Count

Sum

Average

Window

Join
```

---

Need:

```text
State Store
```

---

# Example

Orders:

```text
100

200

300
```

---

Running Total:

```text
100

300

600
```

---

Requires remembering previous values.

---

# State Store

Kafka Streams stores state locally.

---

Examples:

```text
Count

Revenue

Windows
```

---

# Fault Tolerance

State store changes are written to:

```text
Changelog Topics
```

---

Architecture:

```text
State Store
      ↓
Changelog Topic
      ↓
Recovery
```

---

# Stateful Operations Examples

---

## Count

---

```java
groupedStream.count();
```

---

Input:

```text
A

A

B
```

---

Output:

```text
A=2

B=1
```

---

## Reduce

---

Combines values.

---

Example:

```java
reduce((v1, v2) -> v1 + v2);
```

---

# Windowing

One of the most important Kafka Streams concepts.

---

# Why Windowing?

Infinite streams cannot be aggregated forever.

---

Example:

```text
Website Visits
```

---

Need:

```text
Visits Per Minute
```

---

Windowing divides streams into time-based chunks.

---

# What is a Window?

A time interval over which events are grouped.

---

Example:

```text
10:00 - 10:05

10:05 - 10:10

10:10 - 10:15
```

---

# Window Types

---

## Tumbling Window

---

Fixed-size, non-overlapping windows.

---

Example:

```text
00-05

05-10

10-15
```

---

Visualization:

```text
|-----|
      |-----|
            |-----|
```

---

# Use Cases

```text
Revenue Every 5 Minutes

Orders Per Hour
```

---

## Hopping Window

---

Fixed-size, overlapping windows.

---

Example:

```text
Window Size = 10 min

Advance = 5 min
```

---

Visualization:

```text
|----------|
     |----------|
          |----------|
```

---

# Use Cases

```text
Moving Averages

Trend Analysis
```

---

## Sliding Window

---

Created dynamically based on event timing.

---

Useful for:

```text
Fraud Detection

Real-Time Monitoring
```

---

## Session Window

---

Groups activity separated by inactivity.

---

Example:

```text
User Login Activity
```

---

Inactive for:

```text
30 Minutes
```

---

New Session Starts.

---

# Aggregations

Aggregation combines multiple records into a summary result.

---

# Common Aggregations

---

## Count

---

Count records.

---

Example:

```java
groupedStream.count();
```

---

Input:

```text
Order

Order

Order
```

---

Output:

```text
Count = 3
```

---

## Sum

---

Input:

```text
100

200

300
```

---

Output:

```text
600
```

---

## Average

---

Input:

```text
100

200

300
```

---

Output:

```text
200
```

---

## Max

---

Input:

```text
100

500

300
```

---

Output:

```text
500
```

---

## Min

---

Input:

```text
100

500

300
```

---

Output:

```text
100
```

---

# Aggregation Flow

```text
Events
   ↓
Group By Key
   ↓
Aggregate
   ↓
State Store
   ↓
Output
```

---

# Joins

Another powerful Kafka Streams feature.

---

# What is a Join?

Combining data from multiple streams or tables.

---

Similar to SQL JOIN.

---

# Example

Orders Stream:

```text
Order1 -> Customer1
```

---

Customer Table:

```text
Customer1 -> Sumit
```

---

Join Result:

```text
Order1 -> Sumit
```

---

# Types of Joins

---

## Stream-Stream Join

---

Join two streams.

---

Example:

```text
Orders Stream

Payments Stream
```

---

Result:

```text
Order + Payment
```

---

# Example

```java
orders.join(payments);
```

---

# Use Cases

```text
Fraud Detection

Event Correlation
```

---

## Stream-Table Join

---

Join stream with latest table state.

---

Example:

```text
Orders Stream

Customer Table
```

---

Result:

```text
Enriched Orders
```

---

# Use Cases

```text
Customer Enrichment

Product Enrichment
```

---

## Table-Table Join

---

Join two KTables.

---

Example:

```text
Customer Table

Address Table
```

---

Result:

```text
Customer + Address
```

---

# Join Types

---

## Inner Join

---

Only matching records.

---

## Left Join

---

All left-side records retained.

---

## Outer Join

---

All records retained.

---

# Example

Orders:

```text
Order1 -> Customer1
```

---

Customers:

```text
Customer1 -> Sumit
```

---

Inner Join Result:

```text
Order1 -> Sumit
```

---

# Kafka Streams Fault Tolerance

One of the strongest features.

---

Uses:

```text
Kafka Topics

Changelog Topics

Replication
```

---

State recovery possible after failures.

---

# Scaling Kafka Streams

Multiple instances can run.

---

Example:

```text
Instance 1

Instance 2

Instance 3
```

---

Partitions distributed automatically.

---

Architecture:

```text
Topic Partitions
      ↓
Streams Instances
      ↓
Parallel Processing
```

---

# Kafka Streams vs Consumer API

| Feature | Consumer API | Kafka Streams |
|----------|-------------|---------------|
| Read Messages | Yes | Yes |
| Stream Processing | Manual | Built-In |
| Aggregations | Manual | Built-In |
| Joins | Manual | Built-In |
| Windowing | Manual | Built-In |
| State Management | Manual | Built-In |
| Scalability | Moderate | High |

---

# Real-World Use Cases

---

## Fraud Detection

Analyze transactions in real time.

---

## Real-Time Dashboards

Update metrics continuously.

---

## Order Processing

Process orders immediately.

---

## Recommendation Engines

Track user activity.

---

## IoT Monitoring

Analyze sensor data.

---

## Financial Analytics

Compute real-time metrics.

---

## Log Processing

Analyze application logs.

---

# Best Practices

---

## Use Stateless Operations When Possible

Lower memory usage.

---

## Use Windowing For Time-Based Analytics

---

## Use Changelog Topics For Recovery

---

## Design Topics With Proper Partitioning

---

## Monitor State Store Size

---

## Use Avro/Protobuf With Schema Registry

---

## Test Join Performance Carefully

---

# Common Interview Questions

## What is Kafka Streams?

A Java library for building real-time stream processing applications using Kafka.

---

## Does Kafka Streams require a separate cluster?

```text
No
```

It runs as a client library inside applications.

---

## What is the difference between KStream and KTable?

```text
KStream → Event Stream

KTable → Latest State Per Key
```

---

## What are Stateless Operations?

Operations that do not require stored state.

Examples:

```text
Filter

Map

FlatMap
```

---

## What are Stateful Operations?

Operations that require previous records.

Examples:

```text
Count

Aggregate

Join

Window
```

---

## Why is Windowing needed?

To perform time-based calculations on infinite streams.

---

## What are Changelog Topics?

Kafka topics used to back up state stores for recovery.

---

## What types of joins are supported?

```text
Stream-Stream

Stream-Table

Table-Table
```

---

## What is a State Store?

Local storage used for stateful operations.

---

## How does Kafka Streams achieve fault tolerance?

Using:

```text
State Stores

Changelog Topics

Kafka Replication
```

---

# Summary

Kafka Streams is a powerful Java library for building real-time event processing applications on top of Kafka.

Core Topics:

- What is Kafka Streams?
- Kafka Streams Architecture
- Stream Processing
- Stateless Operations
- Stateful Operations
- Windowing
- Aggregations
- Joins

Architecture:

```text
Input Topic
      ↓
Kafka Streams
      ↓
Output Topic
```

Key Components:

```text
KStream

KTable

State Store

Processor Node

Changelog Topic
```

Processing Types:

```text
Stateless
```

- Filter
- Map
- FlatMap

---

```text
Stateful
```

- Count
- Aggregate
- Join
- Window

Window Types:

```text
Tumbling

Hopping

Sliding

Session
```

Join Types:

```text
Stream-Stream

Stream-Table

Table-Table
```

Kafka Streams is widely used for:

```text
Real-Time Analytics

Fraud Detection

Monitoring

IoT Processing

Financial Systems

Recommendation Engines
```

and is one of the most important technologies for building scalable event-driven systems on top of Kafka.