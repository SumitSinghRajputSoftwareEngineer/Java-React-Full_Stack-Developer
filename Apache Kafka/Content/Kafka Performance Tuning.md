# 41. Kafka Performance Tuning

Kafka is designed for:

```text
High Throughput

Low Latency

Scalability

Fault Tolerance
```

However, default configurations are not always optimal for production workloads.

A poorly tuned Kafka cluster may suffer from:

```text
High Latency

Low Throughput

Consumer Lag

Broker Overload

Network Bottlenecks

Disk Bottlenecks
```

Performance tuning is the process of optimizing Kafka components to maximize:

```text
Throughput

Latency

Resource Utilization

Scalability

Reliability
```

---

# What is Kafka Performance Tuning?

## Definition

Kafka Performance Tuning is the process of adjusting producer, consumer, broker, topic, network, and storage configurations to achieve the desired balance between performance, reliability, and resource consumption.

---

# Performance Goals

Different systems require different optimizations.

---

## Low Latency Systems

Examples:

```text
Fraud Detection

Real-Time Trading

Payment Processing
```

Goal:

```text
Minimum Delay
```

---

## High Throughput Systems

Examples:

```text
Log Collection

Analytics Pipelines

IoT Systems
```

Goal:

```text
Maximum Messages Per Second
```

---

## Balanced Systems

Examples:

```text
Microservices

Business Event Processing
```

Goal:

```text
Balance Between Speed And Reliability
```

---

# Performance Factors

Kafka performance depends on:

```text
Producer Configuration

Consumer Configuration

Broker Configuration

Topic Design

Network

Disk Performance
```

---

# Performance Tuning Architecture

```text
Producer
    ↓
Network
    ↓
Broker
    ↓
Disk
    ↓
Consumer
```

---

Every layer impacts performance.

---

# Key Performance Metrics

Before tuning, monitor:

```text
Throughput

Latency

Consumer Lag

CPU Usage

Memory Usage

Disk Usage

Network Utilization
```

---

# Producer Tuning

Producer tuning significantly impacts Kafka throughput.

---

# Why Producer Tuning?

Producers generate Kafka traffic.

Improper producer configuration causes:

```text
High Latency

Low Throughput

Excessive Network Calls
```

---

# Producer Performance Factors

```text
Batching

Compression

Acknowledgements

Buffering

Retries
```

---

# Batch Size

Configuration:

```properties
batch.size
```

---

Controls:

```text
Maximum Batch Size
```

---

Default:

```text
16384 Bytes (16 KB)
```

---

Larger batches:

```text
Higher Throughput

Lower Network Overhead
```

---

Trade-off:

```text
Higher Latency
```

---

Example:

```properties
batch.size=65536
```

---

# Linger Time

Configuration:

```properties
linger.ms
```

---

Controls:

```text
How Long Producer Waits Before Sending Batch
```

---

Example:

```properties
linger.ms=20
```

---

Benefits:

```text
Larger Batches

Higher Throughput
```

---

Trade-off:

```text
Slightly Increased Latency
```

---

# Compression

Configuration:

```properties
compression.type
```

---

Supported:

```text
gzip

snappy

lz4

zstd
```

---

Recommended:

```text
zstd
```

or

```text
lz4
```

---

Benefits:

```text
Reduced Network Usage

Reduced Storage Usage
```

---

# Buffer Memory

Configuration:

```properties
buffer.memory
```

---

Controls:

```text
Producer Memory Buffer
```

---

Example:

```properties
buffer.memory=67108864
```

(64 MB)

---

Benefits:

```text
Higher Throughput
```

---

# ACK Configuration

Configuration:

```properties
acks
```

---

Options:

```text
0

1

all
```

---

Performance Comparison:

| ACK Mode | Throughput | Reliability |
|-----------|------------|-------------|
| 0 | Highest | Lowest |
| 1 | Medium | Medium |
| all | Lowest | Highest |

---

Production Recommendation:

```properties
acks=all
```

---

# Idempotent Producer

Configuration:

```properties
enable.idempotence=true
```

---

Benefits:

```text
No Duplicate Messages

Better Reliability
```

---

# Producer Tuning Summary

| Configuration | Purpose |
|---------------|----------|
| batch.size | Larger batches |
| linger.ms | Better batching |
| compression.type | Reduce bandwidth |
| buffer.memory | Increase buffering |
| acks | Reliability control |
| enable.idempotence | Prevent duplicates |

---

# Consumer Tuning

Consumers determine how quickly data is processed.

---

# Why Consumer Tuning?

Poorly configured consumers may cause:

```text
Consumer Lag

Slow Processing

Frequent Rebalances
```

---

# Consumer Performance Factors

```text
Fetch Size

Polling

Commit Strategy

Consumer Parallelism
```

---

# Fetch Minimum Bytes

Configuration:

```properties
fetch.min.bytes
```

---

Controls:

```text
Minimum Data Returned Per Fetch
```

---

Higher values:

```text
Higher Throughput
```

---

Trade-off:

```text
Increased Latency
```

---

# Fetch Maximum Bytes

Configuration:

```properties
fetch.max.bytes
```

---

Controls:

```text
Maximum Fetch Size
```

---

Example:

```properties
fetch.max.bytes=52428800
```

(50 MB)

---

# Maximum Poll Records

Configuration:

```properties
max.poll.records
```

---

Controls:

```text
Records Returned Per Poll
```

---

Example:

```properties
max.poll.records=1000
```

---

Benefits:

```text
Higher Processing Efficiency
```

---

# Session Timeout

Configuration:

```properties
session.timeout.ms
```

---

Controls:

```text
Consumer Failure Detection
```

---

Too Low:

```text
Frequent Rebalancing
```

---

Too High:

```text
Slow Failure Detection
```

---

# Manual Offset Commit

Recommended for:

```text
Business Critical Applications
```

---

Benefits:

```text
Better Processing Control
```

---

# Scaling Consumers

Increase:

```text
Consumer Instances
```

---

To improve:

```text
Parallel Processing
```

---

Constraint:

```text
Consumers <= Partitions
```

---

# Consumer Tuning Summary

| Configuration | Purpose |
|---------------|----------|
| fetch.min.bytes | Larger fetches |
| fetch.max.bytes | Increase fetch capacity |
| max.poll.records | More records per poll |
| session.timeout.ms | Rebalance control |
| Manual Commit | Better reliability |

---

# Broker Tuning

Broker tuning affects the entire Kafka cluster.

---

# Why Broker Tuning?

Brokers handle:

```text
Storage

Replication

Networking

Request Processing
```

---

# Broker Performance Factors

```text
Threads

Memory

Replication

I/O
```

---

# Network Threads

Configuration:

```properties
num.network.threads
```

---

Handles:

```text
Network Requests
```

---

Example:

```properties
num.network.threads=8
```

---

# I/O Threads

Configuration:

```properties
num.io.threads
```

---

Handles:

```text
Disk Operations
```

---

Example:

```properties
num.io.threads=16
```

---

# Socket Buffer Sizes

Configurations:

```properties
socket.send.buffer.bytes

socket.receive.buffer.bytes
```

---

Benefits:

```text
Better Network Throughput
```

---

# Replica Fetchers

Configuration:

```properties
num.replica.fetchers
```

---

Controls:

```text
Replication Speed
```

---

Example:

```properties
num.replica.fetchers=4
```

---

# Log Flush Configuration

Configuration:

```properties
log.flush.interval.messages
```

---

Usually:

```text
Leave Default
```

---

Kafka already relies heavily on:

```text
Operating System Page Cache
```

---

# JVM Tuning

Monitor:

```text
Heap Usage

GC Time

Memory Pressure
```

---

Recommended Heap:

```text
4 GB - 8 GB
```

for most brokers.

---

# Broker Tuning Summary

| Configuration | Purpose |
|---------------|----------|
| num.network.threads | Network processing |
| num.io.threads | Disk processing |
| num.replica.fetchers | Faster replication |
| socket buffers | Better networking |
| JVM tuning | Stable performance |

---

# Topic Tuning

Topic design directly affects Kafka performance.

---

# Why Topic Tuning?

Incorrect topic design causes:

```text
Hot Partitions

Poor Parallelism

Storage Issues
```

---

# Partition Count

Most important topic optimization.

---

Benefits:

```text
Parallel Processing

Scalability
```

---

Example:

```text
1 Partition
```

↓

```text
Single Consumer Thread
```

---

Example:

```text
20 Partitions
```

↓

```text
20 Consumer Threads
```

---

# Replication Factor

Recommended:

```text
3
```

---

Higher values:

```text
More Reliability
```

---

Trade-off:

```text
More Network Traffic
```

---

# Retention Configuration

Reduce retention if:

```text
Storage Is Limited
```

---

Example:

```properties
retention.ms=604800000
```

(7 Days)

---

# Compression

Topic-level compression improves:

```text
Storage

Network Efficiency
```

---

# Topic Tuning Summary

| Area | Optimization |
|--------|-------------|
| Partitions | Increase parallelism |
| Replication | Reliability |
| Retention | Storage management |
| Compression | Reduce disk usage |

---

# Network Optimization

Network is often a major bottleneck.

---

# Why Network Optimization?

Kafka traffic includes:

```text
Producer Traffic

Consumer Traffic

Replication Traffic
```

---

# Network Performance Factors

```text
Bandwidth

Latency

Packet Loss
```

---

# Use Compression

Reduces:

```text
Network Usage
```

---

# Increase Socket Buffers

Configurations:

```properties
socket.send.buffer.bytes

socket.receive.buffer.bytes
```

---

Benefits:

```text
Higher Throughput
```

---

# Use High-Speed Networks

Recommended:

```text
10 Gbps

25 Gbps

40 Gbps
```

---

Production systems often use:

```text
Dedicated Kafka Networks
```

---

# Minimize Cross-Region Traffic

Avoid:

```text
Producer → Remote Region Broker
```

---

Prefer:

```text
Local Broker Communication
```

---

# Network Optimization Summary

| Optimization | Benefit |
|--------------|----------|
| Compression | Reduced bandwidth |
| Socket Buffers | Higher throughput |
| High-Speed Networks | Better performance |
| Local Traffic | Lower latency |

---

# Disk Optimization

Kafka is heavily disk-dependent.

---

# Why Disk Optimization?

Kafka stores:

```text
Messages

Indexes

Snapshots

Metadata
```

---

Disk performance directly impacts:

```text
Throughput

Latency

Replication
```

---

# Use SSDs

Recommended:

```text
NVMe SSDs
```

---

Benefits:

```text
Lower Latency

Higher Throughput
```

---

Avoid:

```text
Slow HDDs
```

for high-volume workloads.

---

# Separate Kafka Logs

Store Kafka logs on dedicated disks.

---

Avoid sharing with:

```text
Operating System

Databases

Other Applications
```

---

# Multiple Log Directories

Configuration:

```properties
log.dirs
```

---

Example:

```properties
log.dirs=/disk1,/disk2,/disk3
```

---

Benefits:

```text
Load Distribution
```

---

# Monitor Disk Utilization

Monitor:

```text
Disk Usage

Disk IOPS

Disk Latency
```

---

# Use OS Page Cache

Kafka leverages:

```text
Linux Page Cache
```

---

Benefits:

```text
Fast Reads

Reduced Disk Access
```

---

# Disk Optimization Summary

| Optimization | Benefit |
|--------------|----------|
| SSD/NVMe | Lower latency |
| Dedicated Disks | Better throughput |
| Multiple Log Dirs | Load balancing |
| Monitor IOPS | Detect bottlenecks |
| Page Cache | Faster reads |

---

# Performance Tuning Checklist

Before Production:

```text
Enable Compression

Tune Batch Size

Tune Linger Time

Use SSDs

Increase Partitions

Monitor Consumer Lag

Optimize JVM

Monitor Disk Usage

Monitor Network Usage
```

---

# Common Performance Bottlenecks

---

## Producer Bottleneck

Symptoms:

```text
High Producer Latency
```

---

Solutions:

```text
Increase Batch Size

Increase Buffer Memory

Enable Compression
```

---

## Consumer Bottleneck

Symptoms:

```text
High Consumer Lag
```

---

Solutions:

```text
Increase Consumer Count

Increase Partitions

Tune Fetch Size
```

---

## Broker Bottleneck

Symptoms:

```text
High CPU Usage

Request Queue Growth
```

---

Solutions:

```text
Increase Threads

Scale Brokers
```

---

## Disk Bottleneck

Symptoms:

```text
High Disk Latency
```

---

Solutions:

```text
Use SSDs

Add More Disks
```

---

# Common Interview Questions

## What is Kafka Performance Tuning?

The process of optimizing Kafka configurations to improve throughput, latency, scalability, and reliability.

---

## Which producer settings most affect performance?

```text
batch.size

linger.ms

compression.type

buffer.memory
```

---

## Which compression algorithm is commonly recommended?

```text
zstd

lz4
```

---

## What causes high consumer lag?

```text
Slow Processing

Insufficient Consumers

Insufficient Partitions
```

---

## Why are partitions important for performance?

Partitions enable parallel processing and scalability.

---

## Why are SSDs recommended for Kafka?

Because Kafka is heavily disk-intensive and SSDs provide lower latency and higher throughput.

---

## What broker configurations affect performance?

```text
num.network.threads

num.io.threads

num.replica.fetchers
```

---

## Why is compression beneficial?

It reduces:

```text
Network Traffic

Disk Usage
```

---

## What is the most important topic-level tuning parameter?

```text
Partition Count
```

---

# Summary

Kafka Performance Tuning optimizes producers, consumers, brokers, topics, networks, and storage to maximize throughput and minimize latency.

Core Topics:

- Producer Tuning
- Consumer Tuning
- Broker Tuning
- Topic Tuning
- Network Optimization
- Disk Optimization

Key Producer Optimizations:

```text
Batching

Compression

Buffering

ACK Tuning
```

Key Consumer Optimizations:

```text
Fetch Tuning

Polling

Scaling Consumers
```

Key Broker Optimizations:

```text
Network Threads

I/O Threads

Replication Tuning
```

Key Topic Optimizations:

```text
Partition Count

Replication Factor

Retention
```

Key Infrastructure Optimizations:

```text
High-Speed Networks

SSD/NVMe Storage

Dedicated Disks
```

Most Important Performance Factors:

```text
Partitions

Compression

Batching

Consumer Lag

Disk Speed
```

Benefits:

```text
Higher Throughput

Lower Latency

Better Scalability

Improved Reliability

Efficient Resource Usage
```

Effective Kafka performance tuning requires understanding the entire data path—from producers and networks to brokers, disks, and consumers—and optimizing each layer to meet the application's throughput and latency requirements.