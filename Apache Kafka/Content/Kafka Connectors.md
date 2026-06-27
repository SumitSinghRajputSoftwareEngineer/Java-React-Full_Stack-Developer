
Kafka Connect becomes powerful because of its connector ecosystem.

A connector is a reusable component that enables Kafka to exchange data with external systems without writing custom producer or consumer code.

Instead of building integrations manually:

```text
Database
   ↓
Custom Producer
   ↓
Kafka
```

Kafka Connect provides ready-made connectors:

```text
Database
   ↓
Connector
   ↓
Kafka
```

---

# What is a Kafka Connector?

## Definition

A Kafka Connector is a plugin that defines how data is transferred between Kafka and an external system.

---

Connectors can be:

```text
Source Connectors

Sink Connectors
```

---

# Source Connector

Moves data:

```text
External System
      ↓
Kafka
```

---

# Sink Connector

Moves data:

```text
Kafka
   ↓
External System
```

---

# Connector Architecture

```text
External System
       ↓
Connector
       ↓
Tasks
       ↓
Kafka Connect Worker
       ↓
Kafka
```

---

# Why Connectors?

Without connectors:

```text
Custom Development

Maintenance

Scaling Logic

Error Handling

Monitoring
```

---

With connectors:

```text
Configuration Driven

Reusable

Scalable

Fault Tolerant
```

---

# Popular Kafka Connectors

Kafka ecosystem provides connectors for:

```text
Relational Databases

NoSQL Databases

Search Engines

Cloud Storage

Data Warehouses

Messaging Systems
```

---

# JDBC Source Connector

One of the most commonly used source connectors.

---

# What is JDBC Source Connector?

A connector that imports data from relational databases into Kafka topics.

---

Flow:

```text
Database
     ↓
JDBC Source Connector
     ↓
Kafka
```

---

# Supported Databases

```text
MySQL

PostgreSQL

Oracle

SQL Server

MariaDB

DB2
```

---

# Architecture

```text
Customer Table
       ↓
JDBC Source Connector
       ↓
customer-topic
```

---

# Example

Database Table:

| id | name |
|----|------|
| 1 | Sumit |
| 2 | Amit |

---

Kafka Topic:

```json
{
  "id":1,
  "name":"Sumit"
}
```

```json
{
  "id":2,
  "name":"Amit"
}
```

---

# Data Extraction Modes

JDBC Source Connector supports multiple modes.

---

## Bulk Mode

Reads entire table.

---

Example:

```sql
SELECT * FROM customer;
```

---

# Use Cases

```text
Initial Data Load

Migration
```

---

## Incrementing Mode

Uses incrementing column.

---

Example:

```text
id
```

---

Only new rows fetched.

---

Example:

```text
id > previousId
```

---

# Use Cases

```text
Continuous Import
```

---

## Timestamp Mode

Uses timestamp column.

---

Example:

```text
updated_at
```

---

Fetches recently modified rows.

---

# Use Cases

```text
Change Tracking
```

---

## Timestamp + Incrementing

Most commonly used.

---

Provides:

```text
Better Reliability

Better Accuracy
```

---

# Example Configuration

```json
{
  "connector.class":
  "JdbcSourceConnector",

  "connection.url":
  "jdbc:mysql://localhost:3306/shop",

  "table.whitelist":
  "customer"
}
```

---

# Advantages

---

## Easy Database Integration

---

## Supports Multiple Databases

---

## Incremental Loading

---

## Scalable

---

# Limitations

---

## Poll-Based

---

## Not True CDC

---

# JDBC Sink Connector

The opposite of JDBC Source Connector.

---

# What is JDBC Sink Connector?

A connector that writes Kafka records into relational databases.

---

Flow:

```text
Kafka
   ↓
JDBC Sink Connector
   ↓
Database
```

---

# Architecture

```text
orders-topic
      ↓
JDBC Sink Connector
      ↓
orders_table
```

---

# Example

Kafka Record:

```json
{
  "id":101,
  "amount":5000
}
```

---

Database Row:

| id | amount |
|----|---------|
| 101 | 5000 |

---

# Supported Operations

---

## Insert

```sql
INSERT
```

---

## Update

```sql
UPDATE
```

---

## Upsert

```sql
INSERT OR UPDATE
```

---

Most commonly used.

---

# Example Configuration

```json
{
  "connector.class":
  "JdbcSinkConnector",

  "topics":
  "orders-topic",

  "auto.create":
  true
}
```

---

# Use Cases

```text
Data Warehousing

Reporting

Database Synchronization

Analytics
```

---

# Advantages

---

## Automatic Table Creation

---

## Supports Upserts

---

## Supports Multiple Databases

---

# MongoDB Connector

Used for integrating Kafka with MongoDB.

---

# What is MongoDB Connector?

A connector that moves data between MongoDB and Kafka.

---

Supports:

```text
MongoDB Source

MongoDB Sink
```

---

# MongoDB Source Connector

Flow:

```text
MongoDB
      ↓
Kafka
```

---

Reads:

```text
Insert

Update

Delete
```

events.

---

# Architecture

```text
MongoDB Collection
       ↓
MongoDB Source Connector
       ↓
Kafka Topic
```

---

# MongoDB Sink Connector

Flow:

```text
Kafka
   ↓
MongoDB
```

---

# Architecture

```text
Kafka Topic
      ↓
MongoDB Sink Connector
      ↓
Collection
```

---

# Example

Kafka Record:

```json
{
  "customerId":1,
  "name":"Sumit"
}
```

---

MongoDB Document:

```json
{
  "_id":1,
  "name":"Sumit"
}
```

---

# Use Cases

```text
Real-Time Data Sync

Event Sourcing

Microservices
```

---

# Advantages

---

## Supports Change Streams

---

## Near Real-Time Updates

---

## Scalable

---

# Elasticsearch Connector

One of the most widely used sink connectors.

---

# What is Elasticsearch Connector?

A connector that indexes Kafka records into Elasticsearch.

---

Flow:

```text
Kafka
   ↓
Elasticsearch Connector
   ↓
Elasticsearch
```

---

Architecture:

```text
Kafka Topic
      ↓
Elasticsearch Sink Connector
      ↓
Index
```

---

# Why Elasticsearch?

Provides:

```text
Full Text Search

Analytics

Dashboards

Monitoring
```

---

# Example

Kafka Record:

```json
{
  "orderId":1001,
  "status":"SUCCESS"
}
```

---

Elasticsearch Document:

```json
{
  "orderId":1001,
  "status":"SUCCESS"
}
```

---

# Use Cases

```text
Application Logs

Monitoring

Search Systems

Analytics Dashboards
```

---

# Popular Integration

```text
Kafka
   ↓
Elasticsearch
   ↓
Kibana
```

---

Visualization:

```text
Kafka
   ↓
Elasticsearch
   ↓
Dashboards
```

---

# Advantages

---

## Real-Time Search

---

## Scalable Analytics

---

## Near Real-Time Indexing

---

# S3 Connector

One of the most important cloud storage connectors.

---

# What is S3 Connector?

A connector that transfers Kafka data to:

:contentReference[oaicite:0]{index=0}

```text
Amazon S3
```

---

Flow:

```text
Kafka
   ↓
S3 Connector
   ↓
S3 Bucket
```

---

# Architecture

```text
Kafka Topic
      ↓
S3 Sink Connector
      ↓
Amazon S3
```

---

# Why S3?

Provides:

```text
Durable Storage

Data Lakes

Backups

Analytics
```

---

# Example

Kafka Events:

```text
Order1

Order2

Order3
```

---

Stored As:

```text
JSON Files

Avro Files

Parquet Files
```

inside S3.

---

# Supported Formats

---

## JSON

---

## Avro

---

## Parquet

---

## Byte Array

---

# Use Cases

```text
Data Lakes

Historical Storage

Archival

Compliance
```

---

# Example Configuration

```json
{
  "connector.class":
  "S3SinkConnector",

  "topics":
  "orders"
}
```

---

# Advantages

---

## Cheap Storage

---

## Highly Durable

---

## Scalable

---

# Custom Connectors

Sometimes built-in connectors are not enough.

---

# What is a Custom Connector?

A connector developed specifically for a custom application or proprietary system.

---

Example:

```text
Legacy Banking System

Internal ERP

Custom API

Mainframe System
```

---

# When to Build Custom Connectors?

If:

```text
No Existing Connector Available
```

---

or

```text
Business Logic Is Unique
```

---

# Custom Connector Architecture

```text
Custom System
      ↓
Custom Connector
      ↓
Kafka
```

---

# Components of a Custom Connector

---

## Connector Class

High-level configuration.

---

## Task Class

Actual processing logic.

---

## Configuration Class

Defines properties.

---

# Source Connector Example

```text
Legacy System
      ↓
Custom Source Connector
      ↓
Kafka
```

---

# Sink Connector Example

```text
Kafka
   ↓
Custom Sink Connector
   ↓
Custom Application
```

---

# Development Flow

```text
Implement Connector
       ↓
Implement Task
       ↓
Package JAR
       ↓
Deploy To Connect Cluster
```

---

# Advantages

---

## Full Flexibility

---

## Supports Proprietary Systems

---

## Custom Business Logic

---

# Challenges

---

## Development Effort

---

## Maintenance Cost

---

## Testing Complexity

---

# Connector Selection Guide

| Requirement | Recommended Connector |
|-------------|----------------------|
| Database → Kafka | JDBC Source |
| Kafka → Database | JDBC Sink |
| MongoDB Integration | MongoDB Connector |
| Search & Analytics | Elasticsearch Connector |
| Data Lake Storage | S3 Connector |
| Proprietary System | Custom Connector |

---

# Source vs Sink Connector Examples

| Connector | Type |
|------------|------|
| JDBC Source | Source |
| MongoDB Source | Source |
| JDBC Sink | Sink |
| Elasticsearch Sink | Sink |
| S3 Sink | Sink |

---

# Real-World Architectures

# Database CDC Pipeline

```text
MySQL
   ↓
Debezium/JDBC Source
   ↓
Kafka
```

---

# Search Platform

```text
Application
      ↓
Kafka
      ↓
Elasticsearch Connector
      ↓
Elasticsearch
```

---

# Data Lake Pipeline

```text
Kafka
   ↓
S3 Connector
   ↓
Amazon S3
```

---

# Analytics Pipeline

```text
PostgreSQL
      ↓
JDBC Source
      ↓
Kafka
      ↓
JDBC Sink
      ↓
Data Warehouse
```

---

# Best Practices

---

## Prefer Existing Connectors First

---

## Use JDBC Connectors For Relational Databases

---

## Use Elasticsearch For Search Workloads

---

## Use S3 For Long-Term Storage

---

## Monitor Connector Tasks

---

## Configure Error Handling

---

## Enable Schema Registry

---

## Scale Connector Tasks Properly

---

# Common Interview Questions

## What is a Kafka Connector?

A plugin used to move data between Kafka and external systems.

---

## What are the two connector types?

```text
Source Connector

Sink Connector
```

---

## What is JDBC Source Connector?

A connector that imports data from relational databases into Kafka.

---

## What is JDBC Sink Connector?

A connector that writes Kafka records into relational databases.

---

## What is MongoDB Connector?

A connector used to move data between MongoDB and Kafka.

---

## What is Elasticsearch Connector used for?

To index Kafka data into Elasticsearch for search and analytics.

---

## Why is S3 Connector commonly used?

For:

```text
Data Lakes

Backups

Archival

Analytics
```

---

## When should a Custom Connector be built?

When no existing connector supports the target system.

---

## Which connector is used for database replication?

```text
JDBC Source Connector

or

Debezium Connector
```

---

## Which connector is commonly used with ELK stack?

```text
Elasticsearch Connector
```

---

# Summary

Kafka Connectors provide reusable integrations between Kafka and external systems.

Core Topics:

- JDBC Source Connector
- JDBC Sink Connector
- MongoDB Connector
- Elasticsearch Connector
- S3 Connector
- Custom Connectors

Most Common Connectors:

```text
JDBC Source

JDBC Sink

MongoDB

Elasticsearch

S3
```

Architecture:

```text
External System
      ↓
Connector
      ↓
Kafka
```

or

```text
Kafka
   ↓
Connector
   ↓
External System
```

Typical Use Cases:

```text
Database Integration

Search Platforms

Data Lakes

Cloud Storage

CDC Pipelines

Analytics
```

Connector Selection:

```text
Database → JDBC

MongoDB → MongoDB Connector

Search → Elasticsearch

Storage → S3

Custom Systems → Custom Connector
```

Kafka Connectors are a foundational building block for creating scalable, fault-tolerant, and production-ready data pipelines in modern event-driven architectures.