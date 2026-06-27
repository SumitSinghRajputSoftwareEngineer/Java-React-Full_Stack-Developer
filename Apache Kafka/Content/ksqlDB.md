
ksqlDB is one of the most powerful technologies in the Kafka ecosystem.

It allows developers, analysts, and data engineers to process Kafka streams using SQL instead of Java code.

Without ksqlDB:

```java
stream.filter()
      .map()
      .groupBy()
      .aggregate();
```

With ksqlDB:

```sql
SELECT *
FROM orders
WHERE amount > 1000;
```

This makes real-time stream processing significantly easier.

---

# What is ksqlDB?

## Definition

ksqlDB is a SQL-based stream processing engine built on top of Kafka.

It allows users to:

```text
Read Kafka Topics

Transform Events

Filter Data

Join Streams

Aggregate Data

Create Real-Time Dashboards
```

using SQL queries.

---

# Why Was ksqlDB Created?

Before ksqlDB, stream processing required:

```text
Java

Kafka Streams API

Custom Applications
```

---

This created challenges:

```text
Steeper Learning Curve

More Code

Longer Development Time
```

---

ksqlDB solves this by providing:

```text
SQL Interface

Real-Time Queries

Stream Processing Without Java Code
```

---

# ksqlDB Architecture

```text
Producer
    ↓
Kafka Topic
    ↓
ksqlDB Engine
    ↓
Continuous Queries
    ↓
New Kafka Topics
    ↓
Consumers
```

---

# High-Level Flow

```text
Events Enter Kafka
       ↓
ksqlDB Processes Events
       ↓
Results Written Back To Kafka
       ↓
Applications Consume Results
```

---

# Core Concepts

ksqlDB works with two primary abstractions:

```text
Streams

Tables
```

---

# Stream

Represents an immutable sequence of events.

Examples:

```text
Orders

Payments

User Clicks

Transactions
```

---

Example:

```json
{
  "orderId":101,
  "amount":500
}
```

---

Each event is independent.

---

# Table

Represents the latest state of data.

Examples:

```text
Customer Profiles

Inventory

Account Balances
```

---

Example:

```json
{
  "customerId":100,
  "name":"John"
}
```

---

Updates overwrite previous state.

---

# Stream Queries

Stream queries operate on continuously arriving events.

---

# Creating a Stream

Suppose Kafka Topic:

```text
orders
```

---

Create Stream:

```sql
CREATE STREAM orders_stream (
    orderId INT,
    customerId INT,
    amount DOUBLE
)
WITH (
    KAFKA_TOPIC='orders',
    VALUE_FORMAT='JSON'
);
```

---

Explanation:

```text
orders_stream → Stream Name

orders → Kafka Topic

JSON → Serialization Format
```

---

# Viewing Stream Data

```sql
SELECT *
FROM orders_stream
EMIT CHANGES;
```

---

Result:

```text
101, 1001, 500

102, 1002, 1200

103, 1003, 800
```

---

Important:

```text
EMIT CHANGES
```

creates a continuous query.

---

# Filtering Stream Data

Retrieve orders above ₹1000.

```sql
SELECT *
FROM orders_stream
WHERE amount > 1000
EMIT CHANGES;
```

---

Input:

```text
500
1200
800
1500
```

---

Output:

```text
1200
1500
```

---

# Projection

Select specific columns.

```sql
SELECT orderId,
       amount
FROM orders_stream
EMIT CHANGES;
```

---

# Transformation

Calculate tax.

```sql
SELECT orderId,
       amount,
       amount * 0.18 AS tax
FROM orders_stream
EMIT CHANGES;
```

---

Output:

```text
Order 101

Amount = 1000

Tax = 180
```

---

# Creating Derived Streams

Create a new stream from another stream.

```sql
CREATE STREAM high_value_orders AS
SELECT *
FROM orders_stream
WHERE amount > 1000;
```

---

Flow:

```text
Orders Topic
      ↓
orders_stream
      ↓
Filter
      ↓
high_value_orders
```

---

# Stream Query Characteristics

```text
Continuous

Event-Oriented

Append-Only

Real-Time
```

---

# Table Queries

Tables represent the latest state of data.

---

# Creating a Table

Suppose customer topic:

```text
customers
```

---

Create Table:

```sql
CREATE TABLE customers_table (
    customerId INT PRIMARY KEY,
    name VARCHAR,
    city VARCHAR
)
WITH (
    KAFKA_TOPIC='customers',
    VALUE_FORMAT='JSON'
);
```

---

# Example Data

Event 1:

```json
{
  "customerId":100,
  "name":"John",
  "city":"Delhi"
}
```

---

Event 2:

```json
{
  "customerId":100,
  "name":"John",
  "city":"Mumbai"
}
```

---

Current Table State:

```json
{
  "customerId":100,
  "city":"Mumbai"
}
```

---

Latest value wins.

---

# Querying Tables

```sql
SELECT *
FROM customers_table
EMIT CHANGES;
```

---

Output:

```text
Current State Of Customers
```

---

# Table Characteristics

```text
Mutable

Latest State

Key Based

Supports Updates
```

---

# Streams vs Tables

| Stream | Table |
|----------|---------|
| Event History | Current State |
| Immutable | Mutable |
| Append Only | Updated |
| Orders | Customer Profiles |

---

# Aggregations

Aggregations summarize streaming data.

---

# Why Aggregations?

Example:

```text
Count Orders

Calculate Revenue

Average Payment
```

---

# Count Aggregation

```sql
SELECT COUNT(*)
FROM orders_stream
EMIT CHANGES;
```

---

Example:

```text
Order 1
Order 2
Order 3
```

---

Output:

```text
Count = 3
```

---

# Group By Aggregation

Count orders per customer.

```sql
SELECT customerId,
       COUNT(*)
FROM orders_stream
GROUP BY customerId
EMIT CHANGES;
```

---

Input:

```text
Customer 1

Customer 1

Customer 2
```

---

Output:

```text
Customer 1 → 2

Customer 2 → 1
```

---

# SUM Aggregation

Calculate revenue.

```sql
SELECT SUM(amount)
FROM orders_stream
EMIT CHANGES;
```

---

Input:

```text
500

700

300
```

---

Output:

```text
1500
```

---

# AVG Aggregation

```sql
SELECT AVG(amount)
FROM orders_stream
EMIT CHANGES;
```

---

# MIN Aggregation

```sql
SELECT MIN(amount)
FROM orders_stream
EMIT CHANGES;
```

---

# MAX Aggregation

```sql
SELECT MAX(amount)
FROM orders_stream
EMIT CHANGES;
```

---

# Aggregation Flow

```text
Events
    ↓
Group By
    ↓
Aggregation
    ↓
Results Topic
```

---

# Windowing

Windowing is one of the most important concepts in stream processing.

---

# Why Windowing?

Streams are infinite.

Example:

```text
Orders Never Stop
```

---

Question:

```text
Count Orders Every 5 Minutes
```

---

Windowing solves this problem.

---

# Types of Windows

```text
Tumbling Window

Hopping Window

Session Window
```

---

# Tumbling Window

Fixed-size non-overlapping windows.

---

Example:

```text
5 Minute Window
```

---

Timeline:

```text
10:00 - 10:05

10:05 - 10:10

10:10 - 10:15
```

---

Query:

```sql
SELECT COUNT(*)
FROM orders_stream
WINDOW TUMBLING (
    SIZE 5 MINUTES
)
EMIT CHANGES;
```

---

# Use Cases

```text
Orders Per Minute

Revenue Per Hour

Requests Per Day
```

---

# Hopping Window

Overlapping windows.

---

Example:

```text
Window Size = 10 Minutes

Advance = 5 Minutes
```

---

Timeline:

```text
10:00 - 10:10

10:05 - 10:15

10:10 - 10:20
```

---

Query:

```sql
SELECT COUNT(*)
FROM orders_stream
WINDOW HOPPING (
    SIZE 10 MINUTES,
    ADVANCE BY 5 MINUTES
)
EMIT CHANGES;
```

---

# Use Cases

```text
Moving Average

Rolling Metrics

Trend Analysis
```

---

# Session Window

Groups events based on activity.

---

Example:

User Activity:

```text
10:00 Click

10:01 Click

10:02 Click

30 Minute Gap

10:35 Click
```

---

Results:

```text
Session 1

Session 2
```

---

Query:

```sql
SELECT COUNT(*)
FROM clicks_stream
WINDOW SESSION (
    30 MINUTES
)
EMIT CHANGES;
```

---

# Window Comparison

| Window Type | Overlap | Use Case |
|-------------|----------|----------|
| Tumbling | No | Fixed Reports |
| Hopping | Yes | Moving Analytics |
| Session | Dynamic | User Sessions |

---

# Real-Time Analytics

One of the most popular uses of ksqlDB.

---

# What is Real-Time Analytics?

Analyzing events immediately after they occur.

---

Traditional Analytics:

```text
Database

Batch Processing

Reports After Hours
```

---

Real-Time Analytics:

```text
Event Occurs

Analysis Runs Immediately

Dashboard Updates Instantly
```

---

# Architecture

```text
Applications
      ↓
Kafka
      ↓
ksqlDB
      ↓
Analytics Dashboard
```

---

# Real-Time Sales Dashboard

Query:

```sql
SELECT COUNT(*) AS total_orders
FROM orders_stream
WINDOW TUMBLING (
    SIZE 1 MINUTE
)
EMIT CHANGES;
```

---

Displays:

```text
Orders Per Minute
```

---

# Revenue Monitoring

```sql
SELECT SUM(amount)
FROM orders_stream
WINDOW TUMBLING (
    SIZE 5 MINUTES
)
EMIT CHANGES;
```

---

Displays:

```text
Revenue Every 5 Minutes
```

---

# Fraud Detection

Detect large transactions.

```sql
SELECT *
FROM payments_stream
WHERE amount > 50000
EMIT CHANGES;
```

---

Output:

```text
Suspicious Payment Alerts
```

---

# Website Click Analytics

```sql
SELECT page,
       COUNT(*)
FROM clicks_stream
GROUP BY page
EMIT CHANGES;
```

---

Output:

```text
Most Viewed Pages
```

---

# IoT Monitoring

Sensor Stream:

```text
Temperature

Pressure

Humidity
```

---

Query:

```sql
SELECT *
FROM sensors_stream
WHERE temperature > 80
EMIT CHANGES;
```

---

Generate:

```text
Real-Time Alerts
```

---

# Benefits of ksqlDB

## SQL-Based

Easy to learn.

---

## Real-Time Processing

No batch delay.

---

## No Java Required

Useful for analysts.

---

## Native Kafka Integration

Works directly with Kafka topics.

---

## Scalable

Scales with Kafka clusters.

---

## Fault Tolerant

Uses Kafka's durability and replication.

---

# Limitations of ksqlDB

## Not Ideal for Complex Logic

Kafka Streams provides more flexibility.

---

## SQL Learning Required

Need understanding of stream SQL.

---

## Large Stateful Queries

May require careful tuning.

---

## Limited Compared to Full Programming Languages

Complex workflows can become difficult.

---

# ksqlDB vs Kafka Streams

| Feature | ksqlDB | Kafka Streams |
|----------|---------|--------------|
| Language | SQL | Java |
| Learning Curve | Easy | Moderate |
| Flexibility | Medium | Very High |
| Developer Effort | Low | Higher |
| Analytics | Excellent | Excellent |
| Complex Logic | Limited | Powerful |

---

# Real-World Use Cases

## Fraud Detection

```text
Detect Suspicious Transactions
```

---

## Monitoring

```text
System Health Monitoring
```

---

## Clickstream Analytics

```text
User Behavior Analysis
```

---

## IoT Analytics

```text
Sensor Data Processing
```

---

## Financial Analytics

```text
Live Trading Metrics
```

---

## E-Commerce Analytics

```text
Real-Time Sales Dashboard
```

---

# Common Interview Questions

## What is ksqlDB?

A SQL-based stream processing engine built on Kafka.

---

## Why use ksqlDB?

To perform real-time stream processing using SQL instead of Java code.

---

## What are the two primary abstractions in ksqlDB?

```text
Streams

Tables
```

---

## Difference Between Stream and Table?

| Stream | Table |
|----------|---------|
| Event History | Current State |
| Immutable | Mutable |
| Append Only | Updated |

---

## What is EMIT CHANGES?

It creates a continuous query that keeps processing new events as they arrive.

---

## What is Windowing?

A technique used to process infinite streams in finite chunks.

---

## Types of Windows?

```text
Tumbling

Hopping

Session
```

---

## What is a Tumbling Window?

Fixed-size non-overlapping window.

---

## What is a Hopping Window?

Overlapping window used for rolling analytics.

---

## What is a Session Window?

Dynamic window based on user activity and inactivity.

---

## ksqlDB vs Kafka Streams?

```text
ksqlDB → SQL

Kafka Streams → Java
```

---

# Summary

ksqlDB is a powerful SQL engine built on Kafka that enables real-time stream processing without writing Java code.

Core Topics Covered:

- What is ksqlDB?
- Stream Queries
- Table Queries
- Aggregations
- Windowing
- Real-Time Analytics

Most Important Concepts:

```text
Streams

Tables

Continuous Queries

EMIT CHANGES

Aggregations

Windowing

Real-Time Dashboards
```

Most Important Window Types:

```text
Tumbling Window

Hopping Window

Session Window
```

Most Common Production Use Cases:

```text
Fraud Detection

Monitoring

Clickstream Analytics

IoT Analytics

Real-Time Dashboards

Financial Analytics
```

ksqlDB is widely used by organizations that need real-time analytics and event processing but prefer SQL over Java-based stream processing.