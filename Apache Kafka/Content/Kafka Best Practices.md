
Kafka is capable of handling millions of messages per second, but poor design decisions can lead to:

```text
Performance Issues

Consumer Lag

Data Loss

Operational Complexity

Scalability Problems

Security Risks
```

Following Kafka best practices ensures:

```text
High Availability

Scalability

Reliability

Maintainability

Performance
```

---

# Why Kafka Best Practices Matter?

A Kafka cluster may work correctly in development but fail in production due to:

```text
Bad Topic Design

Insufficient Partitions

Improper Consumer Configuration

Weak Security

Lack Of Monitoring
```

---

# Areas Covered

- Topic Design Best Practices
- Partition Design Best Practices
- Consumer Best Practices
- Producer Best Practices
- Security Best Practices
- Monitoring Best Practices

---

# Topic Design Best Practices

Topics are the foundation of Kafka architecture.

Poor topic design can create long-term scalability and maintenance issues.

---

# Use Domain-Driven Topic Names

Organize topics around business domains.

---

## Good Examples

```text
orders

payments

customers

inventory
```

---

## Bad Examples

```text
topic1

testTopic

myKafkaTopic
```

---

Reason:

```text
Business-Oriented Topics Are Easier To Understand
```

---

# Follow Consistent Naming Conventions

Choose one naming convention and use it consistently.

---

## Recommended

```text
orders

payment-events

customer-updates
```

---

Avoid:

```text
OrdersTopic

PAYMENTS

customerUpdates
```

---

# Use Environment Prefixes

Separate environments.

---

Examples:

```text
dev-orders

test-orders

prod-orders
```

---

Benefits:

```text
Avoid Cross-Environment Data Mixing
```

---

# Avoid Creating Too Many Topics

Every topic consumes:

```text
Memory

Metadata

File Handles

Network Resources
```

---

Bad:

```text
One Topic Per User
```

---

Good:

```text
One Topic Per Business Domain
```

---

# Define Retention Properly

Not all topics need the same retention.

---

Examples:

### Audit Logs

```text
1 Year
```

---

### Analytics

```text
30 Days
```

---

### Temporary Events

```text
1 Day
```

---

Example:

```properties
retention.ms=2592000000
```

(30 days)

---

# Use Log Compaction When Needed

Suitable for:

```text
Customer Profiles

Configurations

Reference Data
```

---

Not suitable for:

```text
Audit Logs

Financial Transactions
```

---

# Configure Replication Properly

Production recommendation:

```text
Replication Factor = 3
```

---

Benefits:

```text
Fault Tolerance

High Availability
```

---

# Topic Design Checklist

```text
Meaningful Names

Environment Prefixes

Proper Retention

Replication Factor >= 3

Use Compaction Carefully
```

---

# Partition Design Best Practices

Partitions determine scalability and parallelism.

Poor partition design is one of the most common Kafka mistakes.

---

# Understand Partition Purpose

Partitions provide:

```text
Parallel Processing

Scalability

Load Distribution
```

---

# Avoid Too Few Partitions

Example:

```text
1 Partition
```

Problems:

```text
Limited Throughput

Limited Consumer Scaling
```

---

# Avoid Too Many Partitions

Example:

```text
10000 Partitions
```

Problems:

```text
Metadata Overhead

Memory Consumption

Long Rebalancing
```

---

# Choose Partitions Based On Throughput

Example:

```text
1000 Messages/Second
```

might require:

```text
5–10 Partitions
```

---

Production sizing should be based on:

```text
Expected Throughput

Consumer Parallelism

Future Growth
```

---

# Design For Future Growth

Increasing partitions later is possible but may affect:

```text
Key Ordering

Consumer Assignment
```

---

Plan capacity early.

---

# Use Meaningful Partition Keys

Good keys:

```text
customerId

orderId

accountId
```

---

Benefits:

```text
Consistent Routing

Ordering Guarantee
```

---

# Bad Partition Keys

Avoid:

```text
Random UUID Per Event
```

if ordering is required.

---

# Partition Ordering Best Practice

Kafka guarantees ordering only within a partition.

---

To maintain order:

```text
Use Same Key
```

Example:

```text
orderId=100
```

All order events go to the same partition.

---

# Monitor Partition Distribution

Bad:

```text
Partition 1 = 90%

Partition 2 = 10%
```

---

Good:

```text
Even Distribution
```

---

# Partition Design Checklist

```text
Size For Growth

Use Proper Keys

Avoid Excessive Partitions

Monitor Distribution

Preserve Ordering Requirements
```

---

# Consumer Best Practices

Consumers are often the source of production issues.

---

# Use Consumer Groups Correctly

Each partition should be consumed by:

```text
One Consumer Per Group
```

---

# Scale Using Consumer Groups

Example:

```text
10 Partitions
```

Maximum parallelism:

```text
10 Consumers
```

---

# Disable Auto Commit For Critical Applications

Default:

```properties
enable.auto.commit=true
```

---

Risk:

```text
Data Loss
```

if processing fails after commit.

---

Recommended:

```properties
enable.auto.commit=false
```

---

Use manual commits.

---

# Process Messages Idempotently

Consumers may receive duplicates.

Design logic to safely reprocess events.

---

Example:

```text
Check Event ID

Skip Duplicate
```

---

# Handle Failures Gracefully

Implement:

```text
Retries

Retry Topics

DLT
```

---

# Avoid Long Processing Inside Poll Loop

Bad:

```java
poll()
   ↓
Heavy Database Processing
```

---

Problems:

```text
Heartbeat Failure

Rebalancing
```

---

Better:

```text
Poll

Queue Internally

Process Separately
```

---

# Monitor Consumer Lag

Critical metric:

```text
Lag = Latest Offset - Consumer Offset
```

---

Large lag indicates:

```text
Slow Consumer

Insufficient Capacity
```

---

# Configure Max Poll Records Properly

Example:

```properties
max.poll.records=500
```

---

Balance:

```text
Memory Usage

Processing Speed
```

---

# Consumer Checklist

```text
Manual Commit

Idempotent Processing

Monitor Lag

Use DLT

Avoid Long Poll Processing
```

---

# Producer Best Practices

Producers determine data reliability and throughput.

---

# Use ACK=all In Production

Most reliable configuration.

```properties
acks=all
```

---

Benefits:

```text
Highest Durability
```

---

# Enable Idempotence

Recommended:

```properties
enable.idempotence=true
```

---

Benefits:

```text
Duplicate Prevention
```

---

# Configure Retries

Example:

```properties
retries=10
```

---

Handles:

```text
Temporary Failures

Network Glitches
```

---

# Use Compression

Supported:

```text
gzip

snappy

lz4

zstd
```

---

Benefits:

```text
Reduced Network Traffic

Lower Storage Usage
```

---

Recommended:

```properties
compression.type=zstd
```

for modern deployments.

---

# Enable Batching

Example:

```properties
linger.ms=10
batch.size=65536
```

---

Benefits:

```text
Higher Throughput
```

---

# Use Meaningful Keys

Example:

```text
orderId

customerId
```

---

Benefits:

```text
Ordering

Partition Consistency
```

---

# Avoid Large Messages

Kafka is optimized for small-medium events.

---

Avoid:

```text
100 MB Payload
```

---

Recommended:

```text
Store Large Files Externally

Send Reference Through Kafka
```

---

# Producer Checklist

```text
ACK=all

Idempotence Enabled

Retries Enabled

Compression Enabled

Batching Enabled

Meaningful Keys
```

---

# Security Best Practices

Security is critical in production environments.

---

# Always Enable Authentication

Never allow anonymous access.

---

Supported:

```text
SSL

SASL/PLAIN

SASL/SCRAM

OAUTH

Kerberos
```

---

# Use TLS Encryption

Encrypt all communication.

---

Example:

```text
Producer ↔ Broker

Broker ↔ Broker

Consumer ↔ Broker
```

---

# Apply Principle Of Least Privilege

Grant only required permissions.

---

Example:

Producer:

```text
WRITE
```

only.

---

Consumer:

```text
READ
```

only.

---

# Use ACLs

Examples:

```text
Topic Access

Consumer Group Access

Cluster Access
```

---

# Rotate Credentials

Regularly rotate:

```text
Passwords

Certificates

Secrets
```

---

# Secure Secrets

Never store:

```text
Passwords In Source Code
```

---

Use:

```text
Vault

Kubernetes Secrets

Secret Manager
```

---

# Enable Audit Logging

Track:

```text
Authentication

Authorization

Administrative Changes
```

---

# Security Checklist

```text
Enable TLS

Enable Authentication

Use ACLs

Rotate Secrets

Audit Access
```

---

# Monitoring Best Practices

Monitoring is essential for stable Kafka operations.

---

# Monitor Consumer Lag

Most important metric.

---

Alert if:

```text
Lag Continues Growing
```

---

# Monitor Broker Health

Track:

```text
CPU

Memory

Disk

Network
```

---

# Monitor ISR Count

Healthy:

```text
All Replicas In ISR
```

---

Problem:

```text
ISR Shrinking
```

---

May indicate:

```text
Slow Broker

Network Issues
```

---

# Monitor Disk Usage

Kafka is storage intensive.

---

Alert Levels:

```text
70% Warning

85% Critical

95% Emergency
```

---

# Monitor Request Latency

Track:

```text
Produce Latency

Fetch Latency
```

---

High latency may indicate:

```text
Disk Bottleneck

Network Congestion
```

---

# Monitor Throughput

Measure:

```text
Messages Per Second

Bytes Per Second
```

---

Useful for:

```text
Capacity Planning
```

---

# Monitor Rebalancing Frequency

Frequent rebalances indicate:

```text
Consumer Instability

Configuration Issues
```

---

# Use Centralized Dashboards

Common stack:

```text
JMX

Prometheus

Grafana
```

---

# Configure Alerts

Alert on:

```text
Broker Down

Consumer Lag

Disk Full

ISR Shrink

Replication Issues
```

---

# Monitoring Checklist

```text
Track Lag

Track ISR

Monitor Disk

Monitor Throughput

Configure Alerts

Use Dashboards
```

---

# Production Kafka Checklist

Before going live:

---

## Reliability

```text
ACK=all

Replication Factor >= 3

Idempotence Enabled

Retries Enabled
```

---

## Scalability

```text
Proper Partition Count

Consumer Groups

Capacity Planning
```

---

## Security

```text
TLS Enabled

Authentication Enabled

ACLs Configured
```

---

## Monitoring

```text
Lag Monitoring

Broker Monitoring

Alerting
```

---

## Recovery

```text
Retry Topics

DLT

Backup Strategy
```

---

# Common Interview Questions

## What is the recommended replication factor in production?

```text
3
```

---

## Why should producers use ACK=all?

To ensure data is replicated to all ISR members before acknowledging success.

---

## Why disable auto commit?

To avoid committing offsets before successful processing.

---

## Why is consumer lag important?

It indicates whether consumers are keeping up with incoming data.

---

## Why should idempotence be enabled?

To prevent duplicate records during retries.

---

## Why use message keys?

To guarantee ordering and consistent partition assignment.

---

## Why monitor ISR?

ISR shrinkage can indicate replication or broker health problems.

---

## What is the biggest partition design mistake?

Creating too many or too few partitions without throughput planning.

---

# Summary

Kafka Best Practices are divided into six major areas:

- Topic Design Best Practices
- Partition Design Best Practices
- Consumer Best Practices
- Producer Best Practices
- Security Best Practices
- Monitoring Best Practices

Key Production Recommendations:

```text
Replication Factor >= 3

ACK=all

Enable Idempotence

Use Manual Offset Commit

Implement Retry + DLT

Enable TLS + Authentication

Monitor Consumer Lag

Monitor ISR

Monitor Disk Usage

Plan Partition Count Carefully
```

Following these practices results in:

```text
High Availability

High Throughput

Fault Tolerance

Strong Security

Operational Stability
```

These are the same principles used in large-scale Kafka deployments across financial systems, e-commerce platforms, streaming applications, and enterprise microservice architectures.