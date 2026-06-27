
Kafka is often used as the backbone of mission-critical systems such as:

```text
Banking Systems

Payment Platforms

E-Commerce Applications

Fraud Detection Systems

Real-Time Analytics

Microservices Architectures
```

A Kafka cluster may process:

```text
Millions Of Messages Per Second

Thousands Of Producers

Thousands Of Consumers

Hundreds Of Topics
```

Without monitoring:

```text
Performance Issues Go Undetected

Consumers Fall Behind

Brokers Become Overloaded

Storage Runs Out

Failures Remain Hidden
```

Monitoring helps operators identify problems before they impact applications.

---

# What is Kafka Monitoring?

## Definition

Kafka Monitoring is the process of continuously observing, collecting, analyzing, and visualizing Kafka metrics to ensure cluster health, performance, reliability, and availability.

---

Monitoring provides visibility into:

```text
Cluster Health

Broker Performance

Producer Performance

Consumer Performance

Topic Activity

Storage Usage

Replication Health
```

---

# Why Monitoring?

Monitoring is essential for running Kafka in production.

---

# Why Monitoring is Important?

Kafka is a distributed system.

Problems can occur in:

```text
Brokers

Producers

Consumers

Topics

Partitions

Replication
```

---

Without monitoring:

```text
Problems Are Discovered Too Late
```

---

# Example

Consumer processing speed:

```text
500 Messages/Second
```

---

Producer speed:

```text
10,000 Messages/Second
```

---

Result:

```text
Consumer Lag Increases
```

---

Without monitoring:

```text
System Eventually Fails
```

---

With monitoring:

```text
Lag Detected Early

Additional Consumers Added
```

---

# Benefits of Monitoring

---

## Detect Failures Early

---

## Improve Performance

---

## Capacity Planning

---

## Reduce Downtime

---

## Troubleshooting

---

## SLA Compliance

---

## Cost Optimization

---

# Monitoring Architecture

```text
Kafka Cluster
       ↓
Metrics Collection
       ↓
Monitoring System
       ↓
Dashboards & Alerts
```

---

# What Should Be Monitored?

```text
Broker Metrics

Producer Metrics

Consumer Metrics

Topic Metrics

Partition Metrics

Replication Metrics

Storage Metrics

Network Metrics
```

---

# Kafka Monitoring Ecosystem

Common tools:

```text
JMX

Prometheus

Grafana

Datadog

New Relic

Confluent Control Center
```

---

# Kafka Metrics

Kafka exposes metrics through:

```text
JMX (Java Management Extensions)
```

---

# What are Kafka Metrics?

Metrics are numerical measurements representing Kafka's behavior and performance.

---

Examples:

```text
Messages Per Second

Bytes Per Second

Request Latency

Consumer Lag
```

---

# Categories of Kafka Metrics

```text
Broker Metrics

Producer Metrics

Consumer Metrics

Topic Metrics

Replication Metrics

JVM Metrics
```

---

# Important Metric Characteristics

A good metric should:

```text
Be Accurate

Be Actionable

Be Continuously Collected

Support Alerting
```

---

# Key Monitoring Areas

```text
Throughput

Latency

Availability

Resource Utilization

Data Reliability
```

---

# Broker Metrics

Broker metrics measure Kafka broker health and performance.

---

# Why Broker Metrics Matter?

Brokers are the heart of Kafka.

If brokers fail:

```text
Topics Become Unavailable

Consumers Stop Reading

Producers Fail To Write
```

---

# Broker Monitoring Architecture

```text
Producer
      ↓
Kafka Broker
      ↓
Consumer
```

---

Broker metrics determine whether the broker is healthy.

---

# Critical Broker Metrics

---

## Broker Availability

Measures:

```text
Broker Up Or Down
```

---

Most important metric.

---

Healthy:

```text
Broker Running
```

---

Problem:

```text
Broker Offline
```

---

# Request Rate

Measures:

```text
Requests Per Second
```

---

Includes:

```text
Produce Requests

Fetch Requests

Metadata Requests
```

---

High values indicate:

```text
Heavy Traffic
```

---

# Request Latency

Measures:

```text
Time To Process Requests
```

---

Formula:

```text
Request End Time
-
Request Start Time
```

---

High latency indicates:

```text
CPU Issues

Disk Issues

Network Issues
```

---

# Bytes In Per Second

Measures:

```text
Incoming Data Rate
```

---

Example:

```text
500 MB/sec
```

---

Useful for:

```text
Capacity Planning
```

---

# Bytes Out Per Second

Measures:

```text
Outgoing Data Rate
```

---

Represents:

```text
Consumer Traffic
```

---

# Message Rate

Measures:

```text
Messages Produced Per Second
```

---

Helps identify:

```text
Traffic Trends
```

---

# Under Replicated Partitions (URP)

One of the most important Kafka metrics.

---

Measures:

```text
Partitions Missing Replicas
```

---

Healthy:

```text
0
```

---

Example:

```text
URP = 15
```

---

Meaning:

```text
Replication Problems Exist
```

---

# Offline Partitions Count

Measures:

```text
Unavailable Partitions
```

---

Healthy:

```text
0
```

---

Any value greater than zero:

```text
Critical Alert
```

---

# Active Controller Count

Measures:

```text
Number Of Active Controllers
```

---

Healthy:

```text
1
```

---

If:

```text
0
```

or

```text
>1
```

there is a problem.

---

# Network Metrics

Measures:

```text
Network Traffic

Connection Count

Socket Activity
```

---

# Disk Usage

Measures:

```text
Log Storage Consumption
```

---

Monitor:

```text
Used Space

Available Space
```

---

# JVM Metrics

Kafka runs on JVM.

Monitor:

```text
Heap Usage

GC Time

Thread Count

Memory Usage
```

---

# Producer Metrics

Producer metrics measure producer performance and reliability.

---

# Why Producer Metrics Matter?

Producers generate Kafka data.

Poor producer performance impacts:

```text
Message Delivery

Latency

Throughput
```

---

# Producer Monitoring Architecture

```text
Application
      ↓
Producer
      ↓
Kafka Broker
```

---

# Critical Producer Metrics

---

## Record Send Rate

Measures:

```text
Messages Sent Per Second
```

---

# Record Error Rate

Measures:

```text
Failed Sends
```

---

Healthy:

```text
Near Zero
```

---

# Retry Rate

Measures:

```text
Producer Retries
```

---

High values may indicate:

```text
Broker Issues

Network Problems
```

---

# Request Latency

Measures:

```text
Producer → Broker Response Time
```

---

# Batch Size

Measures:

```text
Average Records Per Batch
```

---

Large batches:

```text
Higher Throughput
```

---

# Compression Rate

Measures:

```text
Compression Effectiveness
```

---

Useful when using:

```text
GZIP

Snappy

LZ4

ZSTD
```

---

# Buffer Available Bytes

Measures:

```text
Remaining Producer Buffer Space
```

---

Low values indicate:

```text
Producer Bottlenecks
```

---

# Produce Throughput

Measures:

```text
Data Sent Per Second
```

---

Examples:

```text
MB/sec

GB/sec
```

---

# Producer Metric Summary

| Metric | Purpose |
|----------|----------|
| Record Send Rate | Messages/sec |
| Error Rate | Failed sends |
| Retry Rate | Retries |
| Batch Size | Batch efficiency |
| Latency | Producer performance |
| Throughput | Data transfer |

---

# Consumer Metrics

Consumer metrics measure message consumption efficiency.

---

# Why Consumer Metrics Matter?

Consumers drive business processing.

Problems may cause:

```text
Delayed Processing

Data Backlogs

Business Impact
```

---

# Consumer Monitoring Architecture

```text
Kafka Broker
      ↓
Consumer
      ↓
Business Logic
```

---

# Critical Consumer Metrics

---

## Consumer Lag

Most important consumer metric.

---

# What is Consumer Lag?

Difference between:

```text
Latest Offset
-
Consumed Offset
```

---

Formula:

```text
Lag = End Offset - Current Offset
```

---

Example:

```text
Latest Offset = 10000

Consumer Offset = 7000
```

---

Lag:

```text
3000
```

---

# Why Lag Matters?

High lag means:

```text
Consumers Are Falling Behind
```

---

# Records Consumed Rate

Measures:

```text
Messages Consumed Per Second
```

---

# Fetch Rate

Measures:

```text
Fetch Requests Per Second
```

---

# Fetch Latency

Measures:

```text
Time Required To Fetch Messages
```

---

# Commit Rate

Measures:

```text
Offset Commits Per Second
```

---

# Rebalance Count

Measures:

```text
Consumer Group Rebalances
```

---

High rebalance frequency indicates:

```text
Group Instability
```

---

# Heartbeat Rate

Measures:

```text
Heartbeats Sent To Coordinator
```

---

Low heartbeat rate may cause:

```text
Consumer Removal
```

---

# Consumer Metric Summary

| Metric | Purpose |
|----------|----------|
| Consumer Lag | Processing delay |
| Fetch Rate | Read activity |
| Fetch Latency | Read performance |
| Commit Rate | Offset tracking |
| Rebalance Count | Group stability |
| Heartbeat Rate | Consumer health |

---

# Topic Metrics

Topic metrics measure activity and health of Kafka topics.

---

# Why Topic Metrics Matter?

Topics are the primary data containers in Kafka.

Monitoring topics helps identify:

```text
Traffic Patterns

Storage Growth

Partition Issues
```

---

# Topic Monitoring Architecture

```text
Producer
      ↓
Topic
      ↓
Consumer
```

---

# Critical Topic Metrics

---

## Message Rate

Measures:

```text
Messages Produced To Topic
```

---

# Bytes In

Measures:

```text
Incoming Data Volume
```

---

# Bytes Out

Measures:

```text
Outgoing Data Volume
```

---

# Topic Size

Measures:

```text
Disk Usage Of Topic
```

---

Example:

```text
5 TB
```

---

Useful for:

```text
Retention Planning
```

---

# Partition Count

Measures:

```text
Number Of Partitions
```

---

Helps evaluate:

```text
Scalability
```

---

# Replication Health

Measures:

```text
Replica Synchronization
```

---

Monitors:

```text
ISR Count

URP Count
```

---

# Topic Throughput

Measures:

```text
Messages Per Second

Bytes Per Second
```

---

# Topic Metric Summary

| Metric | Purpose |
|----------|----------|
| Message Rate | Topic traffic |
| Bytes In | Incoming data |
| Bytes Out | Outgoing data |
| Topic Size | Storage growth |
| Partition Count | Scalability |
| Replication Health | Reliability |

---

# Golden Signals for Kafka Monitoring

A common production monitoring approach.

---

# Latency

Measures:

```text
Request Processing Time
```

---

# Traffic

Measures:

```text
Messages

Requests

Bandwidth
```

---

# Errors

Measures:

```text
Failed Requests

Broker Errors

Producer Errors
```

---

# Saturation

Measures:

```text
CPU

Memory

Disk

Network Utilization
```

---

# Monitoring Dashboard Example

A production dashboard typically shows:

```text
Broker Health

Consumer Lag

Throughput

Request Latency

Disk Usage

URP Count

Active Controllers
```

---

# Alerting Strategy

Monitoring without alerting is incomplete.

---

# Critical Alerts

---

## Broker Down

---

## Consumer Lag Threshold Exceeded

---

## Under Replicated Partitions > 0

---

## Offline Partitions > 0

---

## Disk Usage > 80%

---

## High Request Latency

---

## Frequent Rebalancing

---

# Monitoring Best Practices

---

## Monitor Consumer Lag Continuously

---

## Keep URP At Zero

---

## Monitor Disk Usage

---

## Monitor Broker Availability

---

## Monitor Replication Health

---

## Create Automated Alerts

---

## Track Throughput Trends

---

## Monitor JVM Metrics

---

## Analyze Long-Term Capacity Growth

---

## Use Dashboards For Visualization

---

# Common Interview Questions

## Why is Kafka Monitoring Important?

To ensure cluster health, reliability, performance, and availability.

---

## What is Consumer Lag?

Difference between the latest offset and the consumer's current offset.

---

## Why is Consumer Lag Important?

It indicates whether consumers are keeping up with incoming data.

---

## What is URP?

Under Replicated Partitions, which indicates partitions whose replicas are not fully synchronized.

---

## What should URP ideally be?

```text
0
```

---

## What should Active Controller Count be?

```text
1
```

---

## What is Request Latency?

Time taken by Kafka to process a request.

---

## What broker metrics are most important?

```text
Broker Availability

URP

Offline Partitions

Request Latency

Disk Usage
```

---

## What producer metrics are most important?

```text
Send Rate

Error Rate

Retry Rate

Latency
```

---

## What consumer metric is most important?

```text
Consumer Lag
```

---

# Summary

Kafka Monitoring ensures cluster stability, reliability, and performance.

Core Topics:

- Why Monitoring?
- Kafka Metrics
- Broker Metrics
- Producer Metrics
- Consumer Metrics
- Topic Metrics

Monitoring Areas:

```text
Cluster Health

Performance

Availability

Reliability

Capacity
```

Key Broker Metrics:

```text
URP

Offline Partitions

Request Latency

Disk Usage

Active Controller Count
```

Key Producer Metrics:

```text
Send Rate

Error Rate

Retry Rate

Latency
```

Key Consumer Metrics:

```text
Consumer Lag

Fetch Rate

Commit Rate

Rebalance Count
```

Key Topic Metrics:

```text
Message Rate

Bytes In

Bytes Out

Topic Size

Partition Count
```

Most Critical Production Metrics:

```text
Consumer Lag

URP

Offline Partitions

Disk Usage

Broker Availability
```

Benefits:

```text
Early Failure Detection

Performance Optimization

Capacity Planning

Reduced Downtime

Better Reliability
```

Kafka monitoring is one of the most important operational practices in production environments, helping teams maintain high availability, detect issues early, and ensure that Kafka continues to reliably process massive volumes of data.