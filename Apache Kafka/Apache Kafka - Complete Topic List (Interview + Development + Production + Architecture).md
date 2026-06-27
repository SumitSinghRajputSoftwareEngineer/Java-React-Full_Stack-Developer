
> These notes are structured from beginner to advanced level, covering everything required for Java/Spring Boot developers, Kafka developers, Kafka administrators, and system design interviews.

---

# 1. [[Introduction to Apache Kafka]]

## What is Apache Kafka?
## Why Kafka was created?
## Problems Kafka Solves
## Traditional Messaging Systems vs Kafka
## Kafka Use Cases
## Kafka Features
## Kafka Benefits
## Kafka Limitations
## Kafka Ecosystem Overview

---

# 2. [[Event Driven Architecture (EDA)]]

## What is Event Driven Architecture?
## Components of EDA
## Event Producers
## Event Consumers
## Event Streams
## Event Processing
## Synchronous vs Asynchronous Communication
## Event Sourcing
## CQRS with Kafka
## Real-world EDA Examples

---

# 3. [[Kafka Core Concepts]]

## Event
## Message
## Record
## Topic
## Partition
## Offset
## Producer
## Consumer
## Consumer Group
## Broker
## Cluster
## Leader
## Follower
## Replication
## ISR (In-Sync Replica)
## Metadata

---

# 4. [[Kafka Architecture]]

## High-Level Architecture
## Internal Architecture
## Kafka Cluster Architecture
## Broker Architecture
## Topic Architecture
## Partition Architecture
## Producer Architecture
## Consumer Architecture
## Replication Architecture
## Request Processing Flow
## Data Flow Inside Kafka

---

# 5. [[Kafka Installation & Setup]]

## Installing Kafka
## Kafka Prerequisites
## Kafka Configuration Files
## Running Kafka Locally
## Kafka in Docker
## Kafka in Kubernetes
## Kafka in Cloud Platforms
## Kafka Startup Commands
## Kafka Shutdown Commands

---

# 6. [[ZooKeeper (Legacy Kafka)]]

## What is ZooKeeper?
## Why Kafka Used ZooKeeper?
## ZooKeeper Responsibilities
## ZooKeeper Architecture
## Leader Election Using ZooKeeper
## Metadata Management
## ZooKeeper Limitations
## ZooKeeper Removal

---

# 7. [[KRaft Mode (Modern Kafka)]]

## What is KRaft?
## Why ZooKeeper Was Removed?
## KRaft Architecture
## Controller Quorum
## Metadata Management
## KRaft vs ZooKeeper
## Benefits of KRaft

---

# 8. [[Topics]]

## What is a Topic?
## Topic Naming Conventions
## Topic Creation
## Topic Configuration
## Topic Retention
## Topic Deletion
## Topic Compaction
## Topic Partitioning
## Internal Topics

---

# 9. [[Partitions]]

## What is a Partition?
## Why Partitions are Needed?
## Partition Distribution
## Partition Scalability
## Partition Ordering
## Partition Assignment
## Rebalancing
## Partition Strategy

---

# 10. [[Offsets]]

## What is Offset?
## Offset Management
## Offset Storage
## Auto Commit
## Manual Commit
## Offset Reset
## Offset Retention
## Offset Tracking

---

# 11. [[Producers]]

## Kafka Producer Overview
## Producer Architecture
## Producer Workflow
## Producer Configuration
## Producer API
## Producer Lifecycle
## Producer Batching
## Producer Buffering
## Producer Compression
## Producer Retries
## Producer Acknowledgements

---

# 12. [[Producer Acknowledgement Modes]]

## ACK = 0
## ACK = 1
## ACK = all (-1)
## Reliability Comparison
## Performance Comparison
## Best Practices

---

# 13. [[Producer Partitioning Strategies]]

## Round Robin Partitioning
## Key-Based Partitioning
## Sticky Partitioning
## Custom Partitioning
## Partition Selection Logic

---

# 14. [[Producer Reliability]]

## Message Delivery Guarantees
## Retries
## Idempotent Producer
## Duplicate Prevention
## Producer Failure Handling
## Message Ordering Guarantees

---

# 15. [[Consumers]]

## Kafka Consumer Overview
## Consumer Architecture
## Consumer Lifecycle
## Poll Mechanism
## Consumer Configuration
## Consumer API
## Fetch Process
## Heartbeats

---

# 16. [[Consumer Groups]]

## What is Consumer Group?
## Why Consumer Groups?
## Group Coordinator
## Consumer Group Architecture
## Partition Assignment
## Rebalancing
## Scaling Consumers
## Load Distribution

---

# 17. [[Consumer Rebalancing]]

## What is Rebalancing?
## Why Rebalancing Happens?
## Rebalancing Strategies
## Eager Rebalancing
## Cooperative Rebalancing
## Static Membership
## Rebalance Listener

---

# 18. [[Message Delivery Semantics]]

## At Most Once
## At Least Once
## Exactly Once
## Trade-offs
## Real World Examples

---

# 19. [[Kafka Replication]]

## Why Replication?
## Replication Factor
## Leader Replica
## Follower Replica
## Replica Synchronization
## ISR
## High Availability
## Failover Mechanism

---

# 20. [[Kafka Leader Election]]

## Leader Election Process
## Preferred Leader Election
## Unclean Leader Election
## Controller Role
## Broker Failure Scenarios

---

# 21. [[Kafka Storage Internals]]

## Kafka Log Structure
## Log Segments
## Index Files
## Time Index
## Offset Index
## Data Retention
## Disk Management

---

# 22. [[Kafka Retention Policies]]

## Time-Based Retention
## Size-Based Retention
## Log Cleanup
## Retention Configuration
## Retention Best Practices

---

# 23. [[Log Compaction]]

## What is Log Compaction?
## How Log Compaction Works?
## Tombstone Records
## Compacted Topics
## Compaction Use Cases

---

# 24. [[Kafka Serialization]]

## What is Serialization?
## String Serializer
## JSON Serializer
## Avro Serializer
## Protobuf Serializer
## Custom Serializer

---

# 25. [[Kafka Deserialization]]

## String Deserializer
## JSON Deserializer
## Avro Deserializer
## Protobuf Deserializer
## Custom Deserializer

---

# 26. [[Schema Management]]

## Why Schema Management?
## Schema Evolution
## Backward Compatibility
## Forward Compatibility
## Full Compatibility

---

# 27. [[Schema Registry]]

## What is Schema Registry?
## Why Use Schema Registry?
## Avro Integration
## Protobuf Integration
## Version Management

---

# 28. [[Kafka Streams]]

## What is Kafka Streams?
## Kafka Streams Architecture
## Stream Processing
## Stateless Operations
## Stateful Operations
## Windowing
## Aggregations
## Joins

---

# 29. [[Kafka Streams DSL]]

## KStream
## KTable
## GlobalKTable
## Filter
## Map
## FlatMap
## GroupBy
## Aggregate
## Join Operations

---

# 30. [[Kafka Streams State Management]]

## State Stores
## RocksDB
## Changelog Topics
## Fault Tolerance

---

# 31. [[Kafka Connect]]

## What is Kafka Connect?
## Kafka Connect Architecture
## Source Connectors
## Sink Connectors
## Standalone Mode
## Distributed Mode

---

# 32. [[Kafka Connectors]]

## JDBC Source Connector
## JDBC Sink Connector
## MongoDB Connector
## Elasticsearch Connector
## S3 Connector
## Custom Connectors

---

# 33. [[Kafka Transactions]]

## What are Transactions?
## Transactional Producer
## Transaction Coordinator
## Exactly Once Processing
## Transaction Lifecycle

---

# 34. [[Exactly Once Semantics (EOS)]]

## Why EOS?
## EOS Architecture
## Producer Side EOS
## Consumer Side EOS
## Kafka Streams EOS

---

# 35. [[Security in Kafka]]

## Authentication
## Authorization
## Encryption
## Secure Communication

---

# 36. [[Kafka Authentication]]

## SSL Authentication
## SASL Authentication
## SASL/PLAIN
## SASL/SCRAM
## SASL/OAUTHBEARER
## Kerberos Authentication

---

# 37. [[Kafka Authorization]]

## ACLs
## Role-Based Access
## Topic Permissions
## Consumer Permissions
## Producer Permissions

---

# 38. [[Kafka Encryption]]

## SSL/TLS
## Encryption In Transit
## Encryption At Rest

---

# 39. [[Kafka Monitoring]]

## Why Monitoring?
## Kafka Metrics
## Broker Metrics
## Producer Metrics
## Consumer Metrics
## Topic Metrics

---

# 40. [[Kafka Monitoring Tools]]

## JMX Metrics
## Prometheus
## Grafana
## Confluent Control Center
## Burrow
## Datadog

---

# 41. [[Kafka Performance Tuning]]

## Producer Tuning
## Consumer Tuning
## Broker Tuning
## Topic Tuning
## Network Optimization
## Disk Optimization

---

# 42. [Kafka Scaling]

## Horizontal Scaling
## Vertical Scaling
## Adding Brokers
## Partition Scaling
## Consumer Scaling

---

# 43. [[Kafka Failure Scenarios]]

## Broker Failure
## Consumer Failure
## Producer Failure
## Network Failure
## Data Loss Scenarios
## Recovery Mechanisms

---

# 44. [[Kafka High Availability]]

## Replication
## ISR
## Failover
## Multi Broker Setup
## Disaster Recovery

---

# 45. [[Kafka Cluster Management]]

## Cluster Expansion
## Broker Addition
## Broker Removal
## Partition Reassignment
## Maintenance Activities

---

# 46. [[Kafka Multi Datacenter Architecture]]

## Geo Replication
## Cross Region Replication
## Disaster Recovery
## Active-Active Setup
## Active-Passive Setup

---

# 47. [[MirrorMaker]]

## MirrorMaker Overview
## MirrorMaker 2
## Replication Between Clusters
## Disaster Recovery

---

# 48. [[Kafka APIs]]

## Producer API
## Consumer API
## Admin API
## Streams API
## Connect API

---

# 49. [[Kafka Admin Operations]]

## Topic Management
## Broker Management
## Consumer Group Management
## ACL Management

---

# 50. [[Spring Boot with Kafka]]

## Spring Kafka Introduction
## KafkaTemplate
## @KafkaListener
## Producer Configuration
## Consumer Configuration
## Error Handling
## Retry Mechanisms

---

# 51. [[Spring Kafka Advanced Topics]]

## Batch Consumers
## Manual Acknowledgement
## Dead Letter Topics
## Retry Topics
## Transactions
## Kafka Streams Integration

---

# 52. [[Error Handling & Recovery]]

## Retry Mechanisms
## Dead Letter Queue (DLQ)
## Dead Letter Topic (DLT)
## Poison Pill Messages
## Exception Handling

---

# 53. [[Design Patterns in Kafka]]

## Event Sourcing
## CQRS
## Outbox Pattern
## Saga Pattern
## CDC Pattern
## Request-Reply Pattern

---

# 54. [[Change Data Capture (CDC)]]

## What is CDC?
## CDC Architecture
## Debezium
## Database Synchronization
## Event Streaming

---

# 55. [[Kafka System Design]]

## Designing Notification System
## Designing Order Processing System
## Designing Payment System
## Designing Audit Logging System
## Designing Real-Time Analytics Platform

---

# 56. [[Kafka Interview Questions]]

## Beginner Questions
## Intermediate Questions
## Advanced Questions
## Scenario-Based Questions
## Production Support Questions

---

# 57. [[Kafka Best Practices]]

## Topic Design Best Practices
## Partition Design Best Practices
## Consumer Best Practices
## Producer Best Practices
## Security Best Practices
## Monitoring Best Practices

---

# 58. [[Real-World Kafka Production Scenarios]]

## Duplicate Messages
## Message Loss
## Consumer Lag
## Rebalancing Issues
## Broker Crash
## Slow Consumers
## High Throughput Systems
## Event Ordering Problems

---

# 59. [[Confluent Platform]]

## What is Confluent?
## Confluent Components
## Confluent Cloud
## Schema Registry
## ksqlDB

---

# 60. [[ksqlDB]]

## What is ksqlDB?
## Stream Queries
## Table Queries
## Aggregations
## Windowing
## Real-Time Analytics

---

# 61. [[Advanced Kafka Internals]]

## Page Cache
## Zero Copy
## SendFile API
## Pull Model
## Sequential Disk I/O
## Network Thread
## I/O Thread
## Request Handler Threads

---

# 62. [[Kafka Commands (CLI)]]

## Topic Commands
## Producer Commands
## Consumer Commands
## Consumer Group Commands
## Configuration Commands
## Broker Commands

---

# 63. [[Kafka in Cloud]]

## Kafka on AWS
## Kafka on Azure
## Kafka on GCP
## Managed Kafka Services

---

# 64. Kafka Certification Topics

## Confluent Kafka Certification
## Kafka Administrator Topics
## Kafka Developer Topics

---

# 65. [[End-to-End Production Project]]

## Project Architecture
## Producer Service
## Consumer Service
## Spring Boot Integration
## Monitoring Setup
## Security Setup
## Deployment Strategy
## Performance Tuning
## Failure Handling

---

# 66. [[Kafka Internal Interview Deep Dive]]

## Why Kafka is Fast?
## Why Kafka Uses Pull Model?
## Why Kafka Stores Data on Disk?
## Why Kafka Uses Offsets?
## Why Partitions Improve Throughput?
## Why Kafka Doesn't Use Traditional Queues?
## How Exactly Once Really Works?
## How Rebalancing Works Internally?
## How Leader Election Works Internally?
## How Kafka Handles Millions of Messages?