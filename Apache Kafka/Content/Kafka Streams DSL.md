
Kafka Streams provides two APIs:

```text
1. Streams DSL (Domain Specific Language)

2. Processor API
```

---

The **Streams DSL** is the high-level API and is the most commonly used approach for building Kafka Streams applications.

It provides simple and expressive methods for:

```text
Filtering

Transformation

Aggregation

Windowing

Joining

State Management
```

without requiring developers to handle low-level processing details.

---

# What is Kafka Streams DSL?

## Definition

Kafka Streams DSL (Domain Specific Language) is a high-level abstraction built on top of Kafka Streams that simplifies stream processing operations.

---

Instead of writing:

```java
Consume Message
→ Process
→ Store State
→ Produce Result
```

manually,

you can write:

```java
stream.filter(...)
      .map(...)
      .groupByKey(...)
      .count();
```

---

# Why Kafka Streams DSL?

Without DSL:

```text
More Code

Manual State Management

Complex Logic

Higher Maintenance
```

---

With DSL:

```text
Readable

Declarative

Less Code

Built-In Optimizations
```

---

# Kafka Streams DSL Architecture

```text
Kafka Topic
      ↓
KStream
      ↓
Transformation
      ↓
Aggregation
      ↓
Join
      ↓
Output Topic
```

---

# Core DSL Components

```text
KStream

KTable

GlobalKTable
```

These form the foundation of Kafka Streams.

---

# KStream

The most fundamental abstraction in Kafka Streams.

---

# What is KStream?

A KStream represents:

```text
An Immutable Stream Of Events
```

---

Each record is treated independently.

---

# Characteristics

---

## Event-Oriented

Every message is an event.

---

## Append-Only

Events are continuously added.

---

## Supports Duplicate Keys

Multiple records may have the same key.

---

# Example

Orders Topic:

```text
Order1

Order2

Order3
```

---

KStream Representation:

```text
Order1
Order2
Order3
```

Every event remains significant.

---

# Example Code

```java
StreamsBuilder builder =
        new StreamsBuilder();

KStream<String, String> orders =
        builder.stream("orders");
```

---

# Visualization

```text
Topic
  ↓
KStream

Order1

Order2

Order3
```

---

# Use Cases

```text
Transactions

Orders

Logs

Clicks

IoT Events
```

---

# KTable

Represents the latest value for a key.

---

# What is KTable?

A KTable is a changelog stream that represents current state.

---

Instead of preserving every event:

```text
Latest Value Wins
```

---

# Example

Messages:

```text
User1 → A

User1 → B

User1 → C
```

---

KStream View:

```text
A
B
C
```

---

KTable View:

```text
User1 → C
```

---

Only latest value retained.

---

# Visualization

```text
Topic

User1 → A

User1 → B

User1 → C
```

---

KTable:

```text
User1 → C
```

---

# Example Code

```java
KTable<String, String> customers =
        builder.table("customers");
```

---

# Characteristics

---

## Stateful

---

## Key-Based

---

## Latest State Maintained

---

## Backed By State Store

---

# Use Cases

```text
Customer Profiles

Configuration Data

Inventory State

Reference Data
```

---

# KStream vs KTable

| Feature | KStream | KTable |
|----------|----------|---------|
| Data Model | Events | State |
| Keeps All Records | Yes | No |
| Latest Value Only | No | Yes |
| Duplicate Keys | Allowed | Overwritten |
| Stateful | No | Yes |

---

# GlobalKTable

A specialized version of KTable.

---

# What is GlobalKTable?

A KTable that is fully replicated to every Kafka Streams instance.

---

Unlike KTable:

```text
Partitioned
```

---

GlobalKTable:

```text
Fully Replicated
```

---

# Example

Customer Data:

```text
Customer1

Customer2

Customer3
```

---

Copied to:

```text
Instance A

Instance B

Instance C
```

---

# Architecture

```text
Global Topic
      ↓
Instance A

Instance B

Instance C
```

---

# Example Code

```java
GlobalKTable<String, Customer>
        customerTable =
        builder.globalTable(
                "customers"
        );
```

---

# Advantages

---

## Fast Lookups

---

## Simplifies Joins

---

## No Partition Alignment Needed

---

# Disadvantages

---

## Higher Memory Usage

---

## Replicated To All Instances

---

# Use Cases

```text
Reference Data

Country Codes

Product Catalog

Configuration Tables
```

---

# Filter

One of the most commonly used DSL operations.

---

# What is Filter?

Selects records that satisfy a condition.

---

Records failing the condition are discarded.

---

# Example

Input:

```text
100

500

2000

3000
```

---

Condition:

```text
Amount > 1000
```

---

Output:

```text
2000

3000
```

---

# Code Example

```java
stream.filter(
    (key, value) ->
        value.getAmount() > 1000
);
```

---

# Visualization

```text
Events
  ↓
Filter
  ↓
Valid Events
```

---

# Use Cases

```text
Fraud Detection

High-Value Orders

Error Filtering
```

---

# Map

Used for transforming records.

---

# What is Map?

Converts one record into another record.

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

# Example Code

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

# Visualization

```text
Record
  ↓
Transformation
  ↓
New Record
```

---

# Use Cases

```text
Data Transformation

Field Renaming

Format Conversion
```

---

# FlatMap

A more powerful version of map.

---

# What is FlatMap?

Transforms one input record into multiple output records.

---

# Example

Input:

```text
Java Kafka Streams
```

---

Output:

```text
Java

Kafka

Streams
```

---

# Code Example

```java
stream.flatMap(
    (key, value) ->
        Arrays.stream(
            value.split(" ")
        )
        .map(word ->
            KeyValue.pair(
                word,
                word
            )
        )
        .toList()
);
```

---

# Visualization

```text
One Record
      ↓
FlatMap
      ↓
Many Records
```

---

# Use Cases

```text
Word Count

Tokenization

Event Expansion
```

---

# Map vs FlatMap

| Feature | Map | FlatMap |
|----------|------|---------|
| Input Records | 1 | 1 |
| Output Records | 1 | Many |
| Transformation | Simple | Complex |

---

# GroupBy

Required before aggregation.

---

# What is GroupBy?

Groups records based on a key.

---

Example:

Input:

```text
Order1 → Electronics

Order2 → Books

Order3 → Electronics
```

---

Grouped:

```text
Electronics

Books
```

---

# Code Example

```java
stream.groupBy(
    (key, value) ->
        value.getCategory()
);
```

---

# Visualization

```text
Events
  ↓
GroupBy
  ↓
Categories
```

---

# Why GroupBy?

Aggregation requires grouping.

---

Example:

```text
Count Orders Per Category
```

---

Need:

```text
GroupBy Category
```

first.

---

# Aggregate

One of the most important stateful operations.

---

# What is Aggregate?

Combines multiple records into a summarized result.

---

Examples:

```text
Count

Sum

Average

Max

Min
```

---

# Count Aggregation

Input:

```text
Electronics

Electronics

Books
```

---

Output:

```text
Electronics → 2

Books → 1
```

---

# Example Code

```java
stream.groupByKey()
      .count();
```

---

# Sum Aggregation

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

# Example Code

```java
stream.groupByKey()
      .aggregate(
          () -> 0,
          (key, value, total) ->
              total + value
      );
```

---

# Aggregate Flow

```text
Records
    ↓
GroupBy
    ↓
Aggregate
    ↓
State Store
```

---

# State Store

Aggregations require storage.

---

Examples:

```text
Running Count

Running Total

Statistics
```

---

Stored in:

```text
Local State Store
```

---

Backed by:

```text
Kafka Changelog Topics
```

---

# Join Operations

One of the most powerful DSL capabilities.

---

# What is a Join?

Combining data from multiple streams or tables.

---

Similar to SQL JOIN.

---

# Example

Orders:

```text
Order1 → Customer1
```

---

Customers:

```text
Customer1 → Sumit
```

---

Output:

```text
Order1 → Sumit
```

---

# Join Types

---

## Stream-Stream Join

---

Combines two KStreams.

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

# Example Code

```java
orders.join(
    payments,
    (order, payment) ->
        order + payment
);
```

---

# Use Cases

```text
Fraud Detection

Event Correlation

Transaction Tracking
```

---

# Stream-Table Join

---

Combines KStream with KTable.

---

Example:

```text
Orders Stream

Customer Table
```

---

Output:

```text
Enriched Orders
```

---

# Example Code

```java
orders.join(
    customers,
    (order, customer) ->
        order + customer
);
```

---

# Use Cases

```text
Customer Enrichment

Product Enrichment

Metadata Lookup
```

---

# Table-Table Join

---

Combines two KTables.

---

Example:

```text
Customer Table

Address Table
```

---

Output:

```text
Customer + Address
```

---

# Example Code

```java
customers.join(
    addresses,
    (customer, address) ->
        customer + address
);
```

---

# Join Types by Matching Strategy

---

## Inner Join

Only matching records.

---

Example:

```text
A ∩ B
```

---

# Left Join

All left-side records included.

---

# Outer Join

All records included.

---

# Visualization

```text
Stream A
    +
Stream B
    ↓
Join
    ↓
Combined Result
```

---

# Complete DSL Pipeline Example

```java
builder.stream("orders")

       .filter(
           (k, v) ->
               v.getAmount() > 1000
       )

       .groupBy(
           (k, v) ->
               v.getCategory()
       )

       .count()

       .toStream()

       .to("order-count");
```

---

Flow:

```text
Orders Topic
      ↓
Filter
      ↓
GroupBy
      ↓
Count
      ↓
Output Topic
```

---

# Kafka Streams DSL Execution Model

```text
Source Node
      ↓
Filter
      ↓
Map
      ↓
GroupBy
      ↓
Aggregate
      ↓
Join
      ↓
Sink Node
```

---

# Real-World Examples

---

# Word Count Application

Operations:

```text
FlatMap

GroupBy

Count
```

---

# Fraud Detection

Operations:

```text
Filter

Join

Aggregate
```

---

# Order Analytics

Operations:

```text
GroupBy

Count

Sum
```

---

# Recommendation Engine

Operations:

```text
Join

Aggregate

Window
```

---

# Customer Enrichment

Operations:

```text
Stream-Table Join
```

---

# Best Practices

---

## Prefer KStream For Event Data

---

## Use KTable For Current State

---

## Use GlobalKTable For Small Reference Data

---

## Filter Early

Reduces processing overhead.

---

## Group Before Aggregation

Mandatory for most aggregations.

---

## Monitor State Store Growth

---

## Design Joins Carefully

Can be resource-intensive.

---

## Use Schema Registry With Avro/Protobuf

---

# Common Interview Questions

## What is Kafka Streams DSL?

A high-level API for building stream processing applications using Kafka Streams.

---

## What is KStream?

An immutable stream of events where every record is significant.

---

## What is KTable?

A state representation that stores the latest value for each key.

---

## What is GlobalKTable?

A fully replicated KTable available on every Streams instance.

---

## What is the difference between Map and FlatMap?

```text
Map → One Input → One Output

FlatMap → One Input → Multiple Outputs
```

---

## Why is GroupBy needed?

To organize records before aggregation.

---

## What is Aggregation?

Combining multiple records into summary results such as count, sum, average, max, or min.

---

## What types of joins are supported?

```text
Stream-Stream Join

Stream-Table Join

Table-Table Join
```

---

## Which operations are stateless?

```text
Filter

Map

FlatMap
```

---

## Which operations are stateful?

```text
GroupBy

Aggregate

Count

Join
```

---

# Summary

Kafka Streams DSL is the high-level API used to build stream processing applications with minimal code and built-in support for state management, aggregations, and joins.

Core Components:

- KStream
- KTable
- GlobalKTable
- Filter
- Map
- FlatMap
- GroupBy
- Aggregate
- Join Operations

Processing Flow:

```text
KStream
    ↓
Filter
    ↓
Map / FlatMap
    ↓
GroupBy
    ↓
Aggregate
    ↓
Join
    ↓
Output Topic
```

Key Abstractions:

```text
KStream
```

Event Stream

---

```text
KTable
```

Latest State Per Key

---

```text
GlobalKTable
```

Replicated State Across All Instances

---

Common Operations:

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

- GroupBy
- Aggregate
- Join

Kafka Streams DSL is widely used for:

```text
Real-Time Analytics

Fraud Detection

Order Processing

Recommendation Systems

Monitoring

Data Enrichment
```

and provides a simple yet powerful abstraction for building scalable event-driven applications on top of Kafka.