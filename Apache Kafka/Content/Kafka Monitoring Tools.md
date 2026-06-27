
Monitoring Kafka is not just about collecting metrics.

You also need tools to:

```text
Collect Metrics

Store Metrics

Visualize Metrics

Generate Alerts

Analyze Trends

Troubleshoot Problems
```

Kafka monitoring tools help operators understand:

```text
Cluster Health

Broker Performance

Consumer Health

Producer Performance

Replication Status

Storage Usage
```

---

# Why Monitoring Tools Are Needed?

Imagine a Kafka cluster with:

```text
50 Brokers

500 Topics

5000 Partitions

Hundreds Of Producers

Thousands Of Consumers
```

Without monitoring tools:

```text
Manual Analysis Becomes Impossible
```

---

Monitoring tools provide:

```text
Dashboards

Graphs

Historical Data

Alerting

Health Checks
```

---

# Kafka Monitoring Architecture

```text
Kafka Cluster
        ↓
Metrics Collection
        ↓
Monitoring Tool
        ↓
Dashboard / Alerts
```

---

# Common Kafka Monitoring Tools

The most widely used monitoring tools are:

```text
JMX Metrics

Prometheus

Grafana

Confluent Control Center

Burrow

Datadog
```

---

# Monitoring Tool Ecosystem

```text
Kafka
  ↓
JMX Metrics
  ↓
Prometheus
  ↓
Grafana
```

---

Additional Tools:

```text
Confluent Control Center

Burrow

Datadog
```

---

# JMX Metrics

JMX is the foundation of Kafka monitoring.

---

# What is JMX?

JMX stands for:

```text
Java Management Extensions
```

---

Kafka is written in Java.

JMX exposes internal Kafka metrics.

---

# Why JMX?

Kafka brokers continuously generate metrics.

Examples:

```text
Consumer Lag

Request Latency

Message Rate

Disk Usage
```

---

JMX provides access to these metrics.

---

# JMX Architecture

```text
Kafka Broker
       ↓
JMX Metrics
       ↓
Monitoring Tool
```

---

# What Can JMX Monitor?

---

## Broker Metrics

```text
Bytes In

Bytes Out

Request Rate
```

---

## JVM Metrics

```text
Heap Usage

GC Time

Threads
```

---

## Replication Metrics

```text
ISR

URP
```

---

## Network Metrics

```text
Connections

Traffic
```

---

# JMX Metric Example

```text
kafka.server:type=BrokerTopicMetrics,name=MessagesInPerSec
```

---

Measures:

```text
Messages Per Second
```

---

# Advantages

---

## Native Kafka Monitoring

---

## Rich Metric Collection

---

## Industry Standard

---

# Limitations

---

## Raw Metrics Only

---

## No Dashboard

---

## Requires External Tools

---

# Prometheus

One of the most popular monitoring systems for Kafka.

---

# What is Prometheus?

Prometheus is an open-source monitoring and metrics collection platform.

---

Main Responsibilities:

```text
Collect Metrics

Store Metrics

Query Metrics

Generate Alerts
```

---

# Prometheus Architecture

```text
Kafka
   ↓
JMX Exporter
   ↓
Prometheus
```

---

# How Prometheus Collects Kafka Metrics?

Kafka exposes:

```text
JMX Metrics
```

---

Prometheus cannot directly read JMX.

---

Solution:

```text
JMX Exporter
```

---

Flow:

```text
Kafka
   ↓
JMX Exporter
   ↓
Prometheus
```

---

# Prometheus Components

---

## Metric Scraper

Collects metrics.

---

## Time Series Database

Stores metrics.

---

## Query Engine

Executes PromQL queries.

---

## Alert Manager

Generates alerts.

---

# Example Metrics

```text
Consumer Lag

Request Latency

CPU Usage

Disk Usage
```

---

# Example Query

```text
MessagesInPerSec
```

---

Returns:

```text
Current Message Throughput
```

---

# Prometheus Benefits

---

## Open Source

---

## Time-Series Database

---

## Powerful Queries

---

## Alerting Support

---

## Cloud Native

---

# Limitations

---

## Requires Dashboard Tool

---

Usually paired with:

```text
Grafana
```

---

# Grafana

Grafana is the most popular Kafka visualization tool.

---

# What is Grafana?

Grafana is an open-source dashboard and visualization platform.

---

It converts metrics into:

```text
Charts

Graphs

Dashboards

Heatmaps
```

---

# Grafana Architecture

```text
Kafka
   ↓
Prometheus
   ↓
Grafana
```

---

# What Grafana Does?

Prometheus stores metrics.

Grafana visualizes them.

---

# Example Dashboard

```text
Consumer Lag

Broker Status

Request Latency

Disk Usage

Message Rate
```

---

# Visualization Example

```text
Messages/sec
    ↑
    |
    |      /\
    |     /  \
    |____/____\____
```

---

# Popular Kafka Dashboards

---

## Broker Dashboard

Monitors:

```text
Broker Health

Network

CPU

Disk
```

---

## Consumer Dashboard

Monitors:

```text
Lag

Rebalancing

Consumption Rate
```

---

## Topic Dashboard

Monitors:

```text
Traffic

Partitions

Retention
```

---

# Grafana Benefits

---

## Beautiful Dashboards

---

## Real-Time Visualization

---

## Multiple Data Sources

---

## Alerting

---

# Limitations

---

## Requires Backend Storage

---

Typically:

```text
Prometheus
```

---

# Confluent Control Center

An enterprise-grade Kafka monitoring platform.

---

# What is Confluent Control Center?

A web-based monitoring and management solution provided by Confluent.

---

Provides:

```text
Monitoring

Management

Alerting

Observability
```

---

# Architecture

```text
Kafka Cluster
      ↓
Confluent Control Center
```

---

# Features

---

## Broker Monitoring

---

## Topic Monitoring

---

## Consumer Monitoring

---

## Producer Monitoring

---

## Cluster Health

---

## Alerting

---

# Unique Feature

Consumer lag visualization.

---

Shows:

```text
Consumer Group

Lag

Throughput
```

---

in a single dashboard.

---

# Additional Features

---

## Topic Management

---

## Connector Monitoring

---

## Schema Registry Monitoring

---

## Streams Monitoring

---

# Benefits

---

## Enterprise Ready

---

## Kafka Specific

---

## Rich UI

---

## Easy Troubleshooting

---

# Limitations

---

## Primarily Available In Confluent Ecosystem

---

## Advanced Features May Require Licensing

---

# Burrow

A specialized Kafka consumer lag monitoring tool.

---

# What is Burrow?

Burrow is an open-source Kafka consumer monitoring system created by LinkedIn.

---

Primary purpose:

```text
Consumer Lag Monitoring
```

---

# Why Burrow?

Consumer lag is one of the most important Kafka metrics.

---

Traditional monitoring:

```text
Lag Value Only
```

---

Burrow analyzes:

```text
Consumer Health

Consumption Progress

Lag Trends
```

---

# Burrow Architecture

```text
Kafka
   ↓
Burrow
   ↓
Consumer Health Analysis
```

---

# How Burrow Works?

Burrow continuously:

```text
Reads Consumer Offsets

Reads Topic Offsets

Calculates Lag

Evaluates Progress
```

---

# Health States

Burrow classifies consumers as:

```text
OK

WARNING

ERROR

STOPPED
```

---

# Example

Consumer:

```text
Lag = 5000
```

---

If lag is decreasing:

```text
Healthy
```

---

If lag is increasing:

```text
Unhealthy
```

---

# Benefits

---

## Kafka-Specific

---

## Smart Lag Detection

---

## Consumer Health Analysis

---

## Open Source

---

# Limitations

---

## Focused Primarily On Consumers

---

# Datadog

A cloud-based monitoring platform.

---

# What is Datadog?

Datadog is a commercial observability platform that provides:

```text
Metrics

Logs

Tracing

Dashboards

Alerting
```

---

# Kafka Monitoring Architecture

```text
Kafka Cluster
       ↓
Datadog Agent
       ↓
Datadog Platform
```

---

# What Can Datadog Monitor?

---

## Brokers

---

## Producers

---

## Consumers

---

## Topics

---

## JVM Metrics

---

## Infrastructure Metrics

---

# Kafka Metrics in Datadog

Examples:

```text
Consumer Lag

Request Rate

Request Latency

Bytes In

Bytes Out

Disk Usage
```

---

# Datadog Dashboards

Provides:

```text
Pre-Built Dashboards

Custom Dashboards

Real-Time Analytics
```

---

# Alerting

Examples:

```text
Consumer Lag > 10000

Disk Usage > 80%

Broker Down
```

---

Automatically generates alerts.

---

# Datadog Benefits

---

## Fully Managed

---

## Cloud Native

---

## Easy Setup

---

## Integrated Observability

---

## Powerful Alerting

---

# Limitations

---

## Commercial Product

---

## Licensing Cost

---

# Monitoring Tool Comparison

| Tool | Purpose | Open Source | Dashboard | Alerting |
|--------|----------|-------------|------------|----------|
| JMX | Metric Source | Yes | No | No |
| Prometheus | Metric Collection | Yes | Limited | Yes |
| Grafana | Visualization | Yes | Yes | Yes |
| Confluent Control Center | Kafka Monitoring | Partially | Yes | Yes |
| Burrow | Consumer Lag Monitoring | Yes | Limited | Yes |
| Datadog | Full Observability | No | Yes | Yes |

---

# Typical Production Monitoring Stack

The most common Kafka monitoring architecture:

```text
Kafka
   ↓
JMX Metrics
   ↓
JMX Exporter
   ↓
Prometheus
   ↓
Grafana
```

---

# Enterprise Monitoring Stack

```text
Kafka
   ↓
Prometheus
   ↓
Grafana
   ↓
Alert Manager
```

---

# Confluent Monitoring Stack

```text
Kafka
   ↓
Confluent Control Center
```

---

# Consumer Lag Monitoring Stack

```text
Kafka
   ↓
Burrow
```

---

# Cloud Monitoring Stack

```text
Kafka
   ↓
Datadog Agent
   ↓
Datadog
```

---

# Which Tool Should You Choose?

---

## Small Teams

```text
Prometheus + Grafana
```

---

## Kafka-Specific Operations

```text
Burrow
```

---

## Enterprise Kafka

```text
Confluent Control Center
```

---

## Cloud-Native Organizations

```text
Datadog
```

---

## Production Open-Source Stack

```text
JMX
+
Prometheus
+
Grafana
```

---

# Monitoring Best Practices

---

## Monitor Consumer Lag

---

## Track Broker Health

---

## Monitor Disk Usage

---

## Monitor Replication Metrics

---

## Configure Alerts

---

## Maintain Historical Metrics

---

## Visualize Trends

---

## Monitor JVM Performance

---

## Monitor Topic Growth

---

## Monitor Throughput

---

# Common Interview Questions

## What is JMX?

Java Management Extensions, used by Kafka to expose internal metrics.

---

## Why is JMX important in Kafka?

It is the primary source of Kafka metrics.

---

## What is Prometheus?

An open-source monitoring and metrics collection platform.

---

## Why is Prometheus commonly used with Kafka?

It collects and stores Kafka metrics efficiently and supports alerting.

---

## What is Grafana?

A dashboard and visualization platform used to display Kafka metrics.

---

## Why is Grafana used with Prometheus?

Prometheus stores metrics, while Grafana visualizes them.

---

## What is Confluent Control Center?

A Kafka-specific monitoring and management platform provided by Confluent.

---

## What is Burrow?

A LinkedIn-developed tool for monitoring Kafka consumer lag and consumer health.

---

## What makes Burrow unique?

It evaluates consumer progress rather than only displaying lag values.

---

## What is Datadog?

A cloud-based observability platform providing monitoring, logging, tracing, dashboards, and alerting.

---

## What is the most common open-source Kafka monitoring stack?

```text
JMX
↓
Prometheus
↓
Grafana
```

---

# Summary

Kafka Monitoring Tools help collect, store, visualize, and analyze Kafka metrics.

Core Tools:

- JMX Metrics
- Prometheus
- Grafana
- Confluent Control Center
- Burrow
- Datadog

Monitoring Flow:

```text
Kafka
   ↓
Metrics
   ↓
Monitoring Tool
   ↓
Dashboard / Alert
```

Most Common Open-Source Stack:

```text
JMX
↓
Prometheus
↓
Grafana
```

Tool Responsibilities:

```text
JMX → Metric Source

Prometheus → Metric Collection & Storage

Grafana → Visualization

Burrow → Consumer Lag Monitoring

Confluent Control Center → Kafka Management

Datadog → Full Observability
```

Benefits:

```text
Real-Time Monitoring

Alerting

Capacity Planning

Performance Analysis

Failure Detection

Operational Visibility
```

Kafka monitoring tools are essential for operating production Kafka clusters, enabling teams to proactively detect issues, optimize performance, ensure reliability, and maintain high availability across distributed event-driven systems.