
Change Data Capture (CDC) is one of the most important patterns in modern event-driven architectures.

CDC enables applications to capture database changes in real time and stream them to Kafka without modifying application code.

It is widely used for:

```text
Microservices

Data Pipelines

Real-Time Analytics

Database Synchronization

Event-Driven Systems

Data Warehousing
```

---

# What is CDC?

## Definition

Change Data Capture (CDC) is a technique that identifies and captures changes made to a database and publishes those changes as events.

---

Instead of periodically polling the database:

```text
Application
     ↓
Query Database Every Minute
```

CDC continuously captures changes:

```text
Database Change
       ↓
CDC
       ↓
Kafka Event
```

---

# Why CDC?

Traditional systems often use polling.

Example:

```text
Every 30 Seconds

SELECT * FROM orders
WHERE updated_at > last_time
```

Problems:

```text
High Database Load

Increased Latency

Duplicate Reads

Inefficient Queries
```

---

CDC solves these issues.

---

# CDC Example

Suppose an order is created.

Database:

```sql
INSERT INTO orders
VALUES (101, 'Laptop')
```

---

CDC captures:

```json
{
  "operation":"INSERT",
  "orderId":101,
  "product":"Laptop"
}
```

---

Publishes:

```text
orders-topic
```

---

# CDC Workflow

```text
Database Change
      ↓
Transaction Log
      ↓
CDC Engine
      ↓
Kafka Topic
      ↓
Consumers
```

---

# Traditional Polling vs CDC

| Feature | Polling | CDC |
|----------|----------|------|
| Database Load | High | Low |
| Latency | Higher | Near Real-Time |
| Scalability | Limited | High |
| Efficiency | Lower | Higher |
| Event Driven | No | Yes |

---

# Real-World Example

E-Commerce System:

```text
Order Created
      ↓
Database Updated
      ↓
CDC Captures Change
      ↓
Kafka Event Published
      ↓
Inventory Service
      ↓
Analytics Service
      ↓
Notification Service
```

---

# CDC Architecture

CDC architecture revolves around capturing database changes and converting them into events.

---

# High-Level CDC Architecture

```text
Application
      ↓
Database
      ↓
Transaction Log
      ↓
CDC Engine
      ↓
Kafka
      ↓
Consumers
```

---

# Components of CDC Architecture

```text
Database

Transaction Log

CDC Connector

Kafka

Consumers
```

---

# Step-by-Step Flow

## Step 1

Application updates database.

```sql
UPDATE orders
SET status='SHIPPED'
WHERE id=101
```

---

## Step 2

Database writes change into transaction log.

Examples:

```text
MySQL Binlog

PostgreSQL WAL

Oracle Redo Logs

SQL Server Transaction Log
```

---

## Step 3

CDC engine reads the log.

```text
Debezium
```

captures the change.

---

## Step 4

Change converted into event.

Example:

```json
{
  "before":"CREATED",
  "after":"SHIPPED"
}
```

---

## Step 5

Event published to Kafka.

```text
orders-topic
```

---

## Step 6

Consumers process the event.

```text
Analytics

Notifications

Search Index

Data Warehouse
```

---

# CDC Architecture Diagram

```text
Application
      ↓
Database
      ↓
Transaction Log
      ↓
Debezium
      ↓
Kafka Topic
      ↓
Consumers
```

---

# Benefits of CDC Architecture

```text
Near Real-Time Updates

Low Database Impact

Loose Coupling

Scalable Event Streaming
```

---

# Debezium

Debezium is the most popular CDC platform used with Kafka.

---

# What is Debezium?

Debezium is an open-source CDC platform that captures database changes and streams them into Kafka.

---

Official Purpose:

```text
Database Changes
      ↓
Kafka Events
```

---

# Why Debezium?

Without Debezium:

```text
Custom CDC Logic

Manual Polling

Complex Integration
```

---

With Debezium:

```text
Automatic Change Capture

Kafka Integration

Scalable Streaming
```

---

# Debezium Architecture

```text
Database
      ↓
Transaction Log
      ↓
Debezium Connector
      ↓
Kafka Connect
      ↓
Kafka Topic
```

---

# Debezium Components

## Connector

Captures database changes.

---

## Kafka Connect

Runs Debezium connectors.

---

## Kafka Topics

Stores change events.

---

## Consumers

Consume CDC events.

---

# Supported Databases

Debezium supports:

```text
MySQL

PostgreSQL

Oracle

SQL Server

MongoDB

Db2
```

---

# MySQL Example

Database:

```sql
INSERT INTO customers
VALUES(1,'John')
```

---

MySQL writes:

```text
Binlog Entry
```

---

Debezium reads:

```text
Binlog
```

---

Publishes event:

```json
{
  "op":"c",
  "id":1,
  "name":"John"
}
```

---

# Debezium Event Structure

Typical event:

```json
{
  "before": null,
  "after": {
      "id":101,
      "status":"CREATED"
  },
  "op":"c"
}
```

---

# Operation Types

| Operation | Meaning |
|------------|----------|
| c | Create |
| u | Update |
| d | Delete |
| r | Snapshot Read |

---

# Debezium Snapshot

When connector starts:

```text
Existing Data
```

must also be captured.

---

Debezium performs:

```text
Initial Snapshot
```

---

Example:

```text
10,000 Existing Orders
```

loaded into Kafka.

---

Then:

```text
Real-Time Streaming
```

begins.

---

# Benefits of Debezium

```text
Open Source

Reliable

Scalable

Kafka Native

Production Ready
```

---

# Database Synchronization

One major CDC use case is keeping databases synchronized.

---

# What is Database Synchronization?

Keeping multiple databases consistent by propagating changes automatically.

---

# Problem

Suppose:

```text
Customer Database
```

changes.

Need updates in:

```text
Search Database

Analytics Database

Reporting Database
```

---

Traditional Solution

```text
Batch Jobs
```

---

Problems:

```text
Delay

Complexity

Data Inconsistency
```

---

# CDC Synchronization Flow

```text
Source Database
       ↓
CDC
       ↓
Kafka
       ↓
Target Databases
```

---

# Example

Customer Updated:

```sql
UPDATE customer
SET city='Delhi'
```

---

CDC Event:

```json
{
  "customerId":1,
  "city":"Delhi"
}
```

---

Consumers update:

```text
Elasticsearch

Data Warehouse

Reporting DB
```

---

# Multi-System Synchronization

```text
Source DB
      ↓
Kafka
      ↓
ElasticSearch

Data Lake

Analytics DB

Reporting DB
```

---

# Advantages

```text
Near Real-Time

Scalable

Reliable

Decoupled
```

---

# Event Streaming

CDC naturally enables Event Streaming.

---

# What is Event Streaming?

Continuous flow of events from one system to another in real time.

---

Instead of:

```text
Store Data
```

CDC creates:

```text
Stream of Changes
```

---

# Event Streaming Example

Database Events:

```text
Order Created

Order Paid

Order Shipped

Order Delivered
```

---

CDC converts into:

```text
Kafka Events
```

---

Consumers process in real time.

---

# Event Streaming Architecture

```text
Database
      ↓
CDC
      ↓
Kafka
      ↓
Consumers
```

---

# Real-Time Analytics Example

Order Database:

```text
New Order
```

---

CDC Event:

```text
Kafka
```

---

Analytics Consumer:

```text
Update Dashboard Instantly
```

---

# Event Streaming Benefits

```text
Real-Time Processing

Scalable Architecture

Loose Coupling

Event-Driven Design
```

---

# CDC vs Event Sourcing

These concepts are often confused.

---

# CDC

```text
Captures Existing Database Changes
```

---

Example:

```text
Database First
      ↓
Events Generated Later
```

---

# Event Sourcing

```text
Events Are Primary Source Of Truth
```

---

Example:

```text
Events First
      ↓
Database Built From Events
```

---

# Comparison

| Feature | CDC | Event Sourcing |
|----------|------|----------------|
| Source of Truth | Database | Events |
| Change Capture | Yes | Native |
| Replay Capability | Limited | Excellent |
| Audit Trail | Partial | Complete |
| Complexity | Lower | Higher |

---

# Common CDC Use Cases

## Microservices Integration

```text
Database Change
      ↓
Kafka Event
      ↓
Other Services
```

---

## Search Index Synchronization

```text
MySQL
     ↓
CDC
     ↓
Elasticsearch
```

---

## Data Warehouse Updates

```text
Operational DB
      ↓
CDC
      ↓
Data Warehouse
```

---

## Cache Updates

```text
Database
      ↓
CDC
      ↓
Redis Cache
```

---

## Real-Time Analytics

```text
Database Changes
      ↓
Kafka
      ↓
Analytics Dashboard
```

---

# Challenges of CDC

## Schema Changes

Table structure may change.

---

## Initial Snapshot Cost

Large databases may require lengthy snapshots.

---

## Event Ordering

Must preserve transaction ordering.

---

## Log Retention

Transaction logs must be retained long enough for CDC processing.

---

## Operational Complexity

Requires:

```text
Kafka Connect

Debezium

Monitoring
```

---

# CDC Best Practices

## Use Transaction Logs

Avoid query-based CDC.

---

## Use Debezium

Industry-standard solution.

---

## Monitor CDC Lag

Track delay between database change and Kafka event.

---

## Use Schema Registry

Manage schema evolution.

---

## Configure Snapshots Carefully

Large datasets require planning.

---

# Common Interview Questions

## What is CDC?

Change Data Capture is a technique used to capture database changes and publish them as events.

---

## Why is CDC used?

To stream database changes in real time without polling.

---

## What is Debezium?

An open-source CDC platform that captures database changes and publishes them to Kafka.

---

## How does CDC work?

```text
Database Change
      ↓
Transaction Log
      ↓
CDC Engine
      ↓
Kafka
```

---

## Which database logs are used by CDC?

Examples:

```text
MySQL Binlog

PostgreSQL WAL

Oracle Redo Logs

SQL Server Transaction Logs
```

---

## What is a Debezium Snapshot?

The initial capture of existing database records before real-time streaming begins.

---

## What are Debezium operation types?

```text
c = Create

u = Update

d = Delete

r = Snapshot Read
```

---

## What is the difference between CDC and Polling?

CDC reads transaction logs and streams changes in real time, while polling repeatedly queries the database.

---

## What is the difference between CDC and Event Sourcing?

CDC treats the database as the source of truth, while Event Sourcing treats events as the source of truth.

---

## What are common CDC use cases?

```text
Microservices

Database Synchronization

Search Index Updates

Analytics Pipelines

Data Warehousing
```

---

# Summary

Change Data Capture (CDC) is a powerful technique for streaming database changes into Kafka.

Core Topics:

- What is CDC?
- CDC Architecture
- Debezium
- Database Synchronization
- Event Streaming

CDC Flow:

```text
Database
      ↓
Transaction Log
      ↓
Debezium
      ↓
Kafka
      ↓
Consumers
```

Debezium Features:

```text
Real-Time Change Capture

Kafka Integration

Multiple Database Support

Reliable Streaming
```

Database Synchronization:

```text
Source Database
      ↓
Kafka
      ↓
Multiple Targets
```

Event Streaming:

```text
Continuous Data Flow

Real-Time Processing

Event-Driven Architecture
```

Production Recommendations:

```text
Use Debezium

Use Kafka Connect

Monitor CDC Lag

Use Schema Registry

Retain Transaction Logs Properly
```

CDC is one of the most widely adopted Kafka patterns because it enables real-time data movement, database synchronization, microservice integration, and event-driven architectures without requiring major application changes.