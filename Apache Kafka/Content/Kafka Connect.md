
Kafka Connect is one of the most important components of the Apache Kafka ecosystem.

It enables seamless integration between Kafka and external systems without writing custom producer or consumer applications.

Instead of building custom code for every database, file system, cloud service, or messaging platform, Kafka Connect provides a standardized framework for moving data in and out of Kafka.

---

# What is Kafka Connect?

## Definition

Kafka Connect is a framework for reliably and scalably streaming data between Apache Kafka and external systems.

---

Kafka Connect acts as a bridge between:

```text
Kafka
    ↔
External Systems
```

Examples:

```text
Databases

Data Warehouses

Cloud Storage

File Systems

Search Engines

Messaging Systems
```

---

# Why Kafka Connect?

Without Kafka Connect:

```text
Database
    ↓
Custom Producer
    ↓
Kafka
    ↓
Custom Consumer
    ↓
Target System
```

Requires:

```text
Custom Development

Maintenance

Error Handling

Scaling Logic
```

---

With Kafka Connect:

```text
Database
    ↓
Source Connector
    ↓
Kafka
    ↓
Sink Connector
    ↓
Target System
```

No custom integration code required.

---

# Real-World Example

Suppose customer data is stored in:

```text
MySQL
```

and must be sent to:

```text
Kafka
```

Instead of writing a producer:

```text
MySQL Source Connector
```

can automatically stream data into Kafka.

---

Similarly:

```text
Kafka
    ↓
Elasticsearch
```

can be implemented using:

```text
Elasticsearch Sink Connector
```

---

# Kafka Connect Responsibilities

Kafka Connect provides:

```text
Data Integration

Scalability

Fault Tolerance

Offset Tracking

Data Transformation

Connector Management
```

---

# Benefits of Kafka Connect

---

## No Custom Integration Code

---

## Scalable

---

## Fault Tolerant

---

## Easy Deployment

---

## Reusable Connectors

---

## Supports Hundreds Of Systems

---

# Kafka Connect Architecture

Kafka Connect follows a distributed architecture.

---

# High-Level Architecture

```text
Source System
       ↓
Source Connector
       ↓
Kafka Connect
       ↓
Kafka Topics
       ↓
Kafka Connect
       ↓
Sink Connector
       ↓
Target System
```

---

# Example

```text
MySQL
   ↓
Source Connector
   ↓
Kafka
   ↓
Sink Connector
   ↓
Elasticsearch
```

---

# Main Components

Kafka Connect architecture consists of:

```text
Connect Cluster

Connector

Task

Worker

Converter

Transformation
```

---

# Architecture Diagram

```text
Connector
      ↓
Tasks
      ↓
Workers
      ↓
Kafka Topics
```

---

# Connect Cluster

Group of Kafka Connect workers.

---

Responsible for:

```text
Connector Management

Task Distribution

Fault Tolerance
```

---

# Worker

A Kafka Connect process.

---

Runs:

```text
Connectors

Tasks
```

---

# Connector

High-level configuration describing data movement.

---

Examples:

```text
MySQL Connector

JDBC Connector

S3 Connector

MongoDB Connector
```

---

# Task

Actual unit of work executed by a connector.

---

Connector:

```text
MySQL Connector
```

may create:

```text
Task1

Task2

Task3
```

for parallel processing.

---

# Visualization

```text
Connector
     ↓
Task1

Task2

Task3
```

---

# Converter

Responsible for data serialization and deserialization.

---

Examples:

```text
JSON

Avro

Protobuf
```

---

# Single Message Transform (SMT)

Allows lightweight message transformation.

---

Examples:

```text
Rename Fields

Add Metadata

Filter Fields
```

---

# Kafka Connect Data Flow

```text
External System
       ↓
Connector
       ↓
Tasks
       ↓
Kafka Connect
       ↓
Kafka Topic
```

---

# Source Connectors

One of the two major connector types.

---

# What is a Source Connector?

A connector that imports data into Kafka.

---

Flow:

```text
External System
       ↓
Kafka
```

---

# Architecture

```text
Database
      ↓
Source Connector
      ↓
Kafka Topic
```

---

# Example

```text
MySQL
      ↓
JDBC Source Connector
      ↓
Kafka
```

---

# Common Source Connectors

---

## JDBC Source Connector

Imports relational database data.

---

Supported Databases:

```text
MySQL

PostgreSQL

Oracle

SQL Server
```

---

# Example

```text
Customer Table
      ↓
Kafka Topic
```

---

## MongoDB Source Connector

Streams MongoDB changes.

---

## Debezium Connector

Captures database changes using CDC.

---

CDC:

```text
Change Data Capture
```

---

Example:

```text
INSERT

UPDATE

DELETE
```

captured automatically.

---

## File Source Connector

Reads data from files.

---

Example:

```text
Log Files
```

---

## Cloud Storage Source Connectors

Examples:

```text
Amazon S3

Google Cloud Storage

Azure Blob Storage
```

---

# Source Connector Flow

```text
Source System
       ↓
Read Data
       ↓
Convert Data
       ↓
Publish To Kafka
```

---

# Sink Connectors

The second major connector type.

---

# What is a Sink Connector?

A connector that exports Kafka data to external systems.

---

Flow:

```text
Kafka
   ↓
External System
```

---

# Architecture

```text
Kafka Topic
      ↓
Sink Connector
      ↓
Target System
```

---

# Example

```text
Kafka
   ↓
Elasticsearch Sink Connector
   ↓
Elasticsearch
```

---

# Common Sink Connectors

---

## JDBC Sink Connector

Writes Kafka records into databases.

---

Example:

```text
Kafka
   ↓
MySQL
```

---

## Elasticsearch Sink Connector

Indexes Kafka events into Elasticsearch.

---

Use Cases:

```text
Search

Analytics

Monitoring
```

---

## S3 Sink Connector

Writes Kafka data to:

```text
Amazon S3
```

---

Useful for:

```text
Data Lakes

Backups

Analytics
```

---

## HDFS Sink Connector

Stores Kafka data in:

```text
Hadoop HDFS
```

---

## Cloud Storage Sink Connectors

Examples:

```text
Azure Blob Storage

Google Cloud Storage

Amazon S3
```

---

# Sink Connector Flow

```text
Kafka Topic
      ↓
Read Records
      ↓
Transform
      ↓
Write To Target
```

---

# Source vs Sink Connectors

| Feature | Source Connector | Sink Connector |
|----------|-----------------|---------------|
| Direction | External → Kafka | Kafka → External |
| Purpose | Import Data | Export Data |
| Reads From | External System | Kafka Topic |
| Writes To | Kafka Topic | External System |

---

# Standalone Mode

Kafka Connect supports two deployment modes.

First:

```text
Standalone Mode
```

---

# What is Standalone Mode?

Runs Kafka Connect as a single process.

---

Architecture:

```text
Worker
   ↓
Connector
```

---

# Characteristics

---

## Single Worker

---

## Single Machine

---

## No Fault Tolerance

---

## Simple Setup

---

# Architecture

```text
Connector
      ↓
Worker
      ↓
Kafka
```

---

# Advantages

---

## Easy To Configure

---

## Suitable For Development

---

## Lightweight

---

# Limitations

---

## No High Availability

---

## No Horizontal Scaling

---

## Single Point Of Failure

---

# Use Cases

```text
Development

Testing

Proof Of Concept
```

---

# Standalone Startup Example

```bash
connect-standalone.sh
```

---

# Distributed Mode

The recommended production deployment mode.

---

# What is Distributed Mode?

Multiple Kafka Connect workers form a cluster.

---

Architecture:

```text
Worker1

Worker2

Worker3
```

---

All workers collaborate.

---

# Architecture

```text
Connector
      ↓
Tasks
      ↓
Worker Cluster
      ↓
Kafka
```

---

# Distributed Mode Features

---

## Fault Tolerance

---

## High Availability

---

## Horizontal Scaling

---

## Dynamic Rebalancing

---

# Example

Connector:

```text
MySQL Source Connector
```

---

Tasks:

```text
Task1

Task2

Task3
```

---

Workers:

```text
Worker1

Worker2

Worker3
```

---

Tasks distributed automatically.

---

# Failure Scenario

Suppose:

```text
Worker2 Crashes
```

---

Kafka Connect automatically:

```text
Reassigns Tasks
```

to remaining workers.

---

Processing continues.

---

# Distributed Mode Startup

```bash
connect-distributed.sh
```

---

# Internal Topics Used By Distributed Mode

Kafka Connect stores metadata in Kafka topics.

---

Examples:

```text
connect-configs

connect-offsets

connect-status
```

---

# connect-configs

Stores:

```text
Connector Configurations
```

---

# connect-offsets

Stores:

```text
Source Connector Offsets
```

---

# connect-status

Stores:

```text
Connector Status

Task Status
```

---

# Standalone vs Distributed

| Feature | Standalone | Distributed |
|-----------|-----------|-------------|
| Workers | One | Multiple |
| Fault Tolerance | No | Yes |
| Scalability | Low | High |
| Production Ready | No | Yes |
| Rebalancing | No | Yes |
| High Availability | No | Yes |

---

# Kafka Connect Fault Tolerance

Kafka Connect achieves reliability through:

```text
Offset Tracking

Distributed Workers

Task Reassignment

Kafka Internal Topics
```

---

# Offset Management

Source connectors track:

```text
Last Processed Record
```

---

Stored in:

```text
connect-offsets
```

---

If connector restarts:

```text
Resume From Last Offset
```

---

# Data Transformation

Kafka Connect supports:

```text
Single Message Transforms (SMT)
```

---

Examples:

```text
Rename Field

Mask Sensitive Data

Add Headers

Filter Records
```

---

# Example SMT

Input:

```json
{
  "name":"Sumit",
  "salary":100000
}
```

---

Transformation:

```text
Remove salary
```

---

Output:

```json
{
  "name":"Sumit"
}
```

---

# Real-World Use Cases

---

## Database Replication

```text
MySQL
   ↓
Kafka
```

---

## Data Warehousing

```text
Kafka
   ↓
Snowflake
```

---

## Search Indexing

```text
Kafka
   ↓
Elasticsearch
```

---

## Data Lake Integration

```text
Kafka
   ↓
Amazon S3
```

---

## CDC Pipelines

```text
Database Changes
   ↓
Kafka
```

---

## Cloud Migration

```text
On-Premise
   ↓
Cloud Storage
```

---

# Best Practices

---

## Use Distributed Mode In Production

---

## Monitor Connector Health

---

## Configure Proper Task Parallelism

---

## Use Schema Registry

---

## Enable Error Handling

---

## Use SMT For Lightweight Transformations

---

## Secure Connector Credentials

---

## Monitor Internal Topics

---

# Common Interview Questions

## What is Kafka Connect?

A framework for integrating Kafka with external systems without writing custom producer or consumer code.

---

## What are the two connector types?

```text
Source Connector

Sink Connector
```

---

## What is a Source Connector?

Imports data from external systems into Kafka.

---

## What is a Sink Connector?

Exports data from Kafka to external systems.

---

## What is a Worker?

A Kafka Connect process that executes connectors and tasks.

---

## What is a Task?

The actual unit of work performed by a connector.

---

## What is the difference between Standalone and Distributed mode?

```text
Standalone → Single Worker

Distributed → Multiple Workers
```

---

## Which mode is used in production?

```text
Distributed Mode
```

---

## What are Kafka Connect internal topics?

```text
connect-configs

connect-offsets

connect-status
```

---

## What is SMT?

Single Message Transform used for lightweight record transformations.

---

## How does Kafka Connect achieve fault tolerance?

Using:

```text
Kafka Internal Topics

Offset Tracking

Task Rebalancing

Distributed Workers
```

---

# Summary

Kafka Connect is a framework for scalable and reliable data integration between Kafka and external systems.

Core Topics:

- What is Kafka Connect?
- Kafka Connect Architecture
- Source Connectors
- Sink Connectors
- Standalone Mode
- Distributed Mode

Architecture:

```text
External System
       ↓
Source Connector
       ↓
Kafka
       ↓
Sink Connector
       ↓
External System
```

Key Components:

```text
Connector

Task

Worker

Converter

SMT
```

Connector Types:

```text
Source Connector
```

External System → Kafka

---

```text
Sink Connector
```

Kafka → External System

Deployment Modes:

```text
Standalone Mode
```

Single Worker

---

```text
Distributed Mode
```

Multiple Workers (Production)

Internal Topics:

```text
connect-configs

connect-offsets

connect-status
```

Kafka Connect is the standard solution for building scalable, fault-tolerant, and production-ready data pipelines between Kafka and external systems.