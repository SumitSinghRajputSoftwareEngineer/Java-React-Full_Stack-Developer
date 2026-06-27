
Kafka Producers are responsible for publishing (writing) records to Kafka Topics.

Every Kafka data flow starts with a Producer.

```text
Producer
    ↓
Kafka Topic
    ↓
Consumer
```

Examples:

- Order Service publishes order events
- Payment Service publishes payment events
- Inventory Service publishes stock updates
- Mobile Applications publish user actions
- IoT Devices publish sensor data

Without Producers, no data enters Kafka.

---

# Kafka Producer Overview

## What is a Kafka Producer?

A Producer is a client application that sends records to Kafka Topics.

---

## Definition

A Kafka Producer:

```text
Creates Records
      ↓
Serializes Records
      ↓
Chooses Partition
      ↓
Sends To Kafka Broker
```

---

## Example

E-commerce System:

```text
Order Service
      |
      V
Kafka Producer
      |
      V
orders Topic
```

---

## Real-World Example

When a customer places an order:

```text
Customer Places Order
        ↓
Order Service
        ↓
Producer
        ↓
Kafka Topic
```

---

## Producer Responsibilities

- Create Messages
- Serialize Data
- Select Partition
- Send Records
- Handle Retries
- Handle Failures
- Manage Acknowledgements

---

## Producer Example Record

```json
{
  "orderId": 101,
  "customerId": 2001,
  "amount": 1500
}
```

---

# Producer Architecture

Producer architecture consists of several internal components.

---

## High-Level Architecture

```text
Application
     |
     V
Producer API
     |
Serializer
     |
Partitioner
     |
Buffer
     |
Sender Thread
     |
Kafka Broker
```

---

## Components

### Application

Creates events.

Example:

```java
OrderCreatedEvent
```

---

### Producer API

Provides methods to send records.

Example:

```java
producer.send()
```

---

### Serializer

Converts Java objects into bytes.

---

### Partitioner

Decides target partition.

---

### Buffer Memory

Temporarily stores records.

---

### Sender Thread

Sends batches to Kafka brokers.

---

### Kafka Broker

Receives and stores records.

---

## Detailed Architecture

```text
Application
     |
     V
ProducerRecord
     |
Serializer
     |
Partitioner
     |
Record Accumulator
     |
Sender Thread
     |
Network Client
     |
Kafka Broker
```

---

# Producer Workflow

Understanding Producer Workflow is a favorite interview topic.

---

## Step 1: Create Record

Application creates record.

```java
new ProducerRecord<>(
    "orders",
    key,
    value
);
```

---

## Step 2: Serialization

Convert data into bytes.

---

Example:

```text
Java Object
      ↓
Byte Array
```

---

## Step 3: Partition Selection

Producer determines partition.

Methods:

- Explicit Partition
- Key-Based Hashing
- Round Robin
- Sticky Partitioner

---

## Step 4: Buffering

Record stored in producer memory.

---

## Step 5: Batch Creation

Records grouped into batches.

---

## Step 6: Sender Thread

Background thread sends batches.

---

## Step 7: Broker Receives

Leader partition receives record.

---

## Step 8: Replication

Followers replicate record.

---

## Step 9: Acknowledgement

Broker returns ACK.

---

## Complete Flow

```text
Application
     ↓
Create Record
     ↓
Serialize
     ↓
Partition Selection
     ↓
Buffer
     ↓
Batch
     ↓
Send
     ↓
Broker
     ↓
ACK
```

---

# Producer Configuration

Producer behavior is controlled using configuration properties.

---

## Basic Configuration

```java
Properties props = new Properties();
```

---

# bootstrap.servers

Most important configuration.

Specifies Kafka brokers.

---

Example:

```java
props.put(
    ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,
    "localhost:9092"
);
```

---

## Purpose

Initial connection to Kafka cluster.

---

# key.serializer

Serializes record key.

---

Example:

```java
props.put(
    ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
    StringSerializer.class.getName()
);
```

---

# value.serializer

Serializes record value.

---

Example:

```java
props.put(
    ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
    StringSerializer.class.getName()
);
```

---

# acks

Controls acknowledgement behavior.

---

Example:

```java
props.put("acks","all");
```

---

# retries

Number of retry attempts.

---

Example:

```java
props.put("retries",3);
```

---

# batch.size

Maximum batch size.

---

Example:

```java
props.put("batch.size",16384);
```

---

# linger.ms

Wait time before sending batch.

---

Example:

```java
props.put("linger.ms",5);
```

---

# buffer.memory

Producer memory buffer.

---

Example:

```java
props.put("buffer.memory",33554432);
```

---

32 MB.

---

# compression.type

Compression algorithm.

---

Example:

```java
props.put("compression.type","snappy");
```

---

# Producer API

Kafka Producer API allows applications to publish records.

---

## Maven Dependency

```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
</dependency>
```

---

## Creating Producer

```java
KafkaProducer<String,String>
```

---

Example:

```java
KafkaProducer<String,String> producer =
    new KafkaProducer<>(props);
```

---

## ProducerRecord

Represents a Kafka record.

---

Example:

```java
ProducerRecord<String,String> record =
    new ProducerRecord<>(
        "orders",
        "101",
        "Order Created"
    );
```

---

## Sending Record

```java
producer.send(record);
```

---

## Send with Callback

```java
producer.send(record,
    (metadata, exception) -> {

    }
);
```

---

## Metadata Returned

```text
Topic
Partition
Offset
Timestamp
```

---

# Producer Lifecycle

Every producer follows a lifecycle.

---

## Step 1: Create Producer

```java
new KafkaProducer<>(props);
```

---

## Step 2: Create Records

```java
ProducerRecord
```

---

## Step 3: Send Records

```java
producer.send()
```

---

## Step 4: Flush Records

```java
producer.flush();
```

---

Ensures all records are sent.

---

## Step 5: Close Producer

```java
producer.close();
```

---

Releases resources.

---

## Lifecycle Diagram

```text
Create Producer
      ↓
Create Records
      ↓
Send Records
      ↓
Flush
      ↓
Close
```

---

# Producer Batching

One of Kafka's major performance optimizations.

---

## What is Batching?

Instead of sending one record at a time:

```text
Message1
Message2
Message3
```

Producer groups records into a batch.

---

## Without Batching

```text
Message1 → Network Call

Message2 → Network Call

Message3 → Network Call
```

---

## With Batching

```text
Message1
Message2
Message3
      ↓
Single Batch
      ↓
One Network Call
```

---

## Benefits

- Higher Throughput
- Fewer Network Calls
- Better Resource Usage

---

# batch.size

Controls maximum batch size.

---

Example:

```java
props.put("batch.size",16384);
```

---

Default:

```text
16 KB
```

---

# linger.ms

Controls batching delay.

---

Example:

```java
props.put("linger.ms",5);
```

Producer waits:

```text
5 ms
```

to collect more records.

---

# Producer Buffering

Producer uses memory buffers before sending data.

---

## What is Buffering?

Records are temporarily stored in memory.

---

Example:

```text
Message1
Message2
Message3
      ↓
Buffer Memory
      ↓
Batch
      ↓
Broker
```

---

## Benefits

- Better Throughput
- Better Batching
- Reduced Network Usage

---

# buffer.memory

Controls buffer size.

---

Example:

```java
props.put(
    "buffer.memory",
    33554432
);
```

---

Default:

```text
32 MB
```

---

# Buffer Full Scenario

If buffer becomes full:

```text
Producer Blocks
```

until space becomes available.

---

# Producer Compression

Compression reduces message size.

---

## Why Compression?

Benefits:

```text
Less Network Usage

Less Disk Usage

Higher Throughput
```

---

## Supported Compression Types

---

### gzip

Highest compression.

```text
Smaller Size
Slower
```

---

### snappy

Balanced choice.

```text
Fast
Good Compression
```

---

### lz4

Very fast.

```text
Low Latency
```

---

### zstd

Modern compression.

```text
Excellent Compression
Fast
```

---

# Configuration

```java
props.put(
    "compression.type",
    "snappy"
);
```

---

# Compression Comparison

| Type | Compression | Speed |
|--------|------------|--------|
| gzip | High | Slow |
| snappy | Medium | Fast |
| lz4 | Medium | Very Fast |
| zstd | High | Fast |

---

# Producer Retries

Network failures happen.

Retries help producers recover.

---

# What is Retry?

Producer resends failed requests.

---

## Example

```text
Producer
     ↓
Broker
     ↓
Network Failure
     ↓
Retry
```

---

# retries Configuration

```java
props.put(
    "retries",
    3
);
```

---

Meaning:

```text
Retry 3 Times
```

---

# Retry Flow

```text
Send Record
      ↓
Failure
      ↓
Retry
      ↓
Success
```

---

# Delivery Timeout

Maximum time allowed.

---

Configuration:

```java
props.put(
    "delivery.timeout.ms",
    120000
);
```

---

Default:

```text
120 Seconds
```

---

# Retry Risks

Without proper configuration:

```text
Duplicate Messages
```

may occur.

---

Modern Kafka solves this using:

```text
Idempotent Producer
```

(covered later).

---

# Producer Acknowledgements

Acknowledgements determine durability guarantees.

---

# What is ACK?

An ACK confirms that Kafka received the record.

---

Producer:

```text
Send Record
```

Broker:

```text
ACK
```

---

# ACK Modes

---

## acks=0

Producer does NOT wait.

---

Flow:

```text
Producer
     ↓
Send
     ↓
Continue
```

---

Advantages:

```text
Fastest
```

---

Disadvantages:

```text
Possible Data Loss
```

---

## acks=1

Leader broker acknowledges.

---

Flow:

```text
Producer
      ↓
Leader Stores Record
      ↓
ACK
```

---

Advantages:

```text
Balanced Performance
```

---

Disadvantages:

```text
Leader Failure Can Lose Data
```

---

## acks=all

Strongest durability.

---

Flow:

```text
Producer
      ↓
Leader
      ↓
Followers Replicate
      ↓
ACK
```

---

Advantages:

```text
Highest Reliability
```

---

Disadvantages:

```text
Slightly Higher Latency
```

---

# ACK Comparison

| ACK Mode | Reliability | Performance |
|-----------|-------------|-------------|
| 0 | Lowest | Highest |
| 1 | Medium | Medium |
| all | Highest | Lower |

---

# Typical Production Configuration

```java
Properties props = new Properties();

props.put(
    ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,
    "localhost:9092"
);

props.put(
    ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
    StringSerializer.class.getName()
);

props.put(
    ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
    StringSerializer.class.getName()
);

props.put("acks","all");
props.put("retries",Integer.MAX_VALUE);
props.put("compression.type","snappy");
props.put("linger.ms",5);
props.put("batch.size",16384);
```

---

# Interview Questions

## What is a Kafka Producer?

A client application that publishes records to Kafka topics.

---

## What are the major producer components?

- Serializer
- Partitioner
- Buffer
- Batch
- Sender Thread
- Network Client

---

## Why does Kafka use batching?

To:

- Reduce network calls
- Improve throughput
- Improve efficiency

---

## What is the purpose of `linger.ms`?

It determines how long the producer waits to accumulate records before sending a batch.

---

## What is the purpose of `batch.size`?

It defines the maximum batch size before sending records.

---

## Why is buffering needed?

To temporarily store records and support batching.

---

## Which compression type is commonly used in production?

```text
snappy
```

or

```text
zstd
```

---

## What is the difference between acks=0, acks=1, and acks=all?

| ACK | Description |
|-------|-------------|
| 0 | No acknowledgement |
| 1 | Leader acknowledgement |
| all | Leader + ISR acknowledgement |

---

## What happens if producer retries are enabled?

Failed requests are automatically resent until success or retry limit is reached.

---

## What is the Producer Lifecycle?

```text
Create Producer
      ↓
Create Record
      ↓
Send Record
      ↓
Flush
      ↓
Close
```

---

# Summary

Kafka Producers are responsible for publishing records into Kafka.

Core producer concepts include:

- Producer Architecture
- Producer Workflow
- Producer Configuration
- Producer API
- Producer Lifecycle
- Producer Batching
- Producer Buffering
- Producer Compression
- Producer Retries
- Producer Acknowledgements

Complete Producer Flow:

```text
Application
      ↓
ProducerRecord
      ↓
Serializer
      ↓
Partitioner
      ↓
Buffer
      ↓
Batch
      ↓
Sender Thread
      ↓
Kafka Broker
      ↓
Acknowledgement
```

Understanding Producers thoroughly is essential before moving to:

- Consumer Architecture
- Consumer Groups
- Delivery Guarantees
- Idempotent Producers
- Transactions
- Exactly Once Processing

because Producers are the entry point for all Kafka data.