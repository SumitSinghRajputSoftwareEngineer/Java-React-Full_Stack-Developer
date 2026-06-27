
Kafka Consumers are responsible for reading data from Kafka Topics.

If Producers are the **writers** of Kafka, then Consumers are the **readers**.

Every Kafka data flow eventually reaches a Consumer.

```text
Producer
    ↓
Kafka Topic
    ↓
Consumer
```

Examples:

- Order Service consumes payment events
- Inventory Service consumes order events
- Analytics Service consumes user activity
- Notification Service consumes customer events
- Fraud Detection System consumes transaction events

Without Consumers, data stored in Kafka would never be processed.

---

# Kafka Consumer Overview

## What is a Kafka Consumer?

A Kafka Consumer is a client application that subscribes to Kafka topics and reads records from partitions.

---

## Definition

A Consumer:

```text
Connects To Kafka
        ↓
Subscribes To Topic
        ↓
Reads Records
        ↓
Processes Records
        ↓
Commits Offsets
```

---

## Example

Topic:

```text
orders
```

Records:

```text
Order Created
Payment Done
Shipment Started
```

Consumer:

```text
Order Processing Service
```

reads these records and performs business logic.

---

## Real World Example

E-Commerce Application

```text
Customer Places Order
          ↓
Producer
          ↓
orders Topic
          ↓
Inventory Consumer
          ↓
Reduce Stock
```

---

## Consumer Responsibilities

- Subscribe to Topics
- Fetch Records
- Process Messages
- Track Offsets
- Commit Offsets
- Handle Failures
- Participate in Consumer Groups

---

## Consumer Characteristics

Kafka Consumers are:

```text
Pull-Based
Stateful
Scalable
Fault Tolerant
```

---

# Why Kafka Uses Pull Model?

Kafka uses:

```text
Consumer Pull Model
```

instead of:

```text
Broker Push Model
```

---

## Pull Model

Consumer asks:

```text
Give Me More Records
```

---

Example:

```text
Consumer
      ↓
Poll Request
      ↓
Broker
      ↓
Records Returned
```

---

## Advantages

### Better Flow Control

Consumer decides speed.

---

### No Consumer Overload

Broker never pushes unwanted data.

---

### Efficient Batching

Consumer fetches records in batches.

---

# Consumer Architecture

Understanding Consumer Architecture is a common interview topic.

---

## High-Level Architecture

```text
Application
      |
KafkaConsumer
      |
Subscription Manager
      |
Fetcher
      |
Network Client
      |
Kafka Broker
```

---

## Internal Components

### Application Layer

Contains business logic.

---

Example:

```java
Process Orders
Send Emails
Update Inventory
```

---

### KafkaConsumer API

Main consumer interface.

---

Example:

```java
KafkaConsumer<String,String>
```

---

### Subscription Manager

Tracks:

```text
Subscribed Topics
Assigned Partitions
Consumer Group
```

---

### Fetcher

Responsible for fetching records from brokers.

---

### Network Client

Communicates with Kafka brokers.

---

### Offset Manager

Tracks consumer progress.

---

## Internal Architecture Diagram

```text
Application
      ↓
KafkaConsumer
      ↓
Subscription State
      ↓
Fetcher
      ↓
Offset Manager
      ↓
Network Layer
      ↓
Kafka Broker
```

---

# Consumer Lifecycle

Every consumer follows a lifecycle.

---

## Step 1: Create Consumer

```java
new KafkaConsumer<>(props);
```

---

## Step 2: Subscribe To Topic

```java
consumer.subscribe(
    List.of("orders")
);
```

---

## Step 3: Poll Records

```java
consumer.poll(...)
```

---

## Step 4: Process Records

Business logic executes.

---

## Step 5: Commit Offsets

Store processing progress.

---

## Step 6: Repeat

Continue polling.

---

## Step 7: Close Consumer

```java
consumer.close();
```

---

# Lifecycle Diagram

```text
Create Consumer
       ↓
Subscribe
       ↓
Poll
       ↓
Process
       ↓
Commit Offset
       ↓
Repeat
       ↓
Close
```

---

# Consumer Lifecycle Example

```java
consumer.subscribe(
    List.of("orders")
);

while(true)
{
    ConsumerRecords<String,String> records =
        consumer.poll(Duration.ofSeconds(1));

    process(records);

    consumer.commitSync();
}
```

---

# Poll Mechanism

The Poll Mechanism is the heart of Kafka Consumer architecture.

---

## What is Poll?

Consumers actively fetch records using:

```java
consumer.poll(...)
```

---

## Why Poll Exists?

Kafka follows:

```text
Pull-Based Consumption
```

Consumer decides when to fetch records.

---

## Example

```text
Consumer
      ↓
Poll()
      ↓
Broker
      ↓
Records Returned
```

---

## Poll Flow

```text
Poll Request
      ↓
Broker Reads Data
      ↓
Records Returned
      ↓
Consumer Processes
```

---

# Poll Example

```java
ConsumerRecords<String,String> records =
    consumer.poll(
        Duration.ofMillis(1000)
    );
```

---

Returns:

```text
Batch Of Records
```

---

# Poll Loop

Most consumers use:

```java
while(true)
{
    consumer.poll(...);
}
```

---

Visualization:

```text
Poll
 ↓
Process
 ↓
Poll
 ↓
Process
 ↓
Poll
```

---

# Important Interview Point

Kafka Consumer becomes inactive if:

```text
Poll Not Called Frequently
```

---

This can trigger:

```text
Consumer Group Rebalance
```

---

# Consumer Configuration

Consumer behavior is controlled using configuration properties.

---

# bootstrap.servers

Kafka broker addresses.

---

Example:

```java
props.put(
    "bootstrap.servers",
    "localhost:9092"
);
```

---

# group.id

Consumer group identifier.

---

Example:

```java
props.put(
    "group.id",
    "order-group"
);
```

---

# key.deserializer

Deserializes record key.

---

Example:

```java
props.put(
    "key.deserializer",
    StringDeserializer.class.getName()
);
```

---

# value.deserializer

Deserializes record value.

---

Example:

```java
props.put(
    "value.deserializer",
    StringDeserializer.class.getName()
);
```

---

# enable.auto.commit

Offset commit mode.

---

Example:

```java
props.put(
    "enable.auto.commit",
    false
);
```

---

# auto.offset.reset

Starting offset behavior.

---

Values:

```text
earliest
latest
none
```

---

Example:

```java
props.put(
    "auto.offset.reset",
    "earliest"
);
```

---

# max.poll.records

Maximum records returned per poll.

---

Example:

```java
props.put(
    "max.poll.records",
    500
);
```

---

# session.timeout.ms

Consumer failure detection timeout.

---

Example:

```java
props.put(
    "session.timeout.ms",
    10000
);
```

---

# Consumer API

Kafka Consumer API provides methods for consuming records.

---

# Creating Consumer

```java
KafkaConsumer<String,String>
```

---

Example:

```java
KafkaConsumer<String,String> consumer =
    new KafkaConsumer<>(props);
```

---

# Subscribe

```java
consumer.subscribe(...)
```

---

Example:

```java
consumer.subscribe(
    List.of("orders")
);
```

---

# Poll

```java
consumer.poll(...)
```

---

Example:

```java
consumer.poll(
    Duration.ofSeconds(1)
);
```

---

# Commit Sync

```java
consumer.commitSync();
```

---

# Commit Async

```java
consumer.commitAsync();
```

---

# Seek

Move to a specific offset.

---

Example:

```java
consumer.seek(
    partition,
    100
);
```

---

# Close

```java
consumer.close();
```

---

# Fetch Process

The Fetch Process determines how records move from broker to consumer.

---

## What is Fetching?

Fetching means:

```text
Retrieving Records
From Kafka Broker
```

---

# Fetch Flow

```text
Consumer
      ↓
Fetch Request
      ↓
Broker
      ↓
Fetch Response
      ↓
Records
```

---

# Detailed Fetch Process

```text
Poll()
      ↓
Fetch Request
      ↓
Broker Reads Partition Data
      ↓
Records Packaged
      ↓
Fetch Response
      ↓
Consumer Receives Records
```

---

# Example

Partition:

```text
Offset 100
Offset 101
Offset 102
Offset 103
```

Consumer current position:

```text
Offset 100
```

---

Fetch request:

```text
Give Records After 100
```

---

Response:

```text
101
102
103
```

---

# Fetch Optimization

Kafka fetches records in batches.

---

Benefits:

```text
Fewer Network Calls

Higher Throughput

Lower Latency
```

---

# Important Fetch Configurations

---

## fetch.min.bytes

Minimum data returned.

---

Example:

```java
props.put(
    "fetch.min.bytes",
    1024
);
```

---

## fetch.max.bytes

Maximum fetch size.

---

Example:

```java
props.put(
    "fetch.max.bytes",
    52428800
);
```

---

## max.partition.fetch.bytes

Maximum data per partition.

---

Example:

```java
props.put(
    "max.partition.fetch.bytes",
    1048576
);
```

---

# Heartbeats

Heartbeats are critical for Consumer Groups.

---

## What is a Heartbeat?

A heartbeat is a periodic signal sent by the consumer to the Group Coordinator.

---

Purpose:

```text
I Am Alive
```

---

# Why Heartbeats Are Needed?

Without heartbeats:

```text
Kafka Cannot Know
Whether Consumer Is Alive
```

---

# Heartbeat Flow

```text
Consumer
      ↓
Heartbeat
      ↓
Group Coordinator
      ↓
Consumer Alive
```

---

# Visualization

```text
Heartbeat
Heartbeat
Heartbeat
Heartbeat
```

sent continuously.

---

# What Happens If Heartbeats Stop?

Example:

```text
Consumer Crash
```

---

No heartbeats received.

---

Coordinator marks consumer:

```text
Dead
```

---

Triggers:

```text
Rebalance
```

---

# Heartbeat Configuration

---

## heartbeat.interval.ms

Frequency of heartbeats.

---

Example:

```java
props.put(
    "heartbeat.interval.ms",
    3000
);
```

---

Default:

```text
3 Seconds
```

---

# session.timeout.ms

Maximum heartbeat gap allowed.

---

Example:

```java
props.put(
    "session.timeout.ms",
    10000
);
```

---

Meaning:

```text
10 Seconds
```

---

If no heartbeat within:

```text
10 Seconds
```

consumer is considered failed.

---

# Heartbeat Example

```text
Heartbeat Every 3 Seconds

Allowed Gap = 10 Seconds
```

---

Timeline:

```text
0 sec → Heartbeat

3 sec → Heartbeat

6 sec → Heartbeat

9 sec → Heartbeat
```

Healthy consumer.

---

Crash:

```text
0 sec → Heartbeat

3 sec → Heartbeat

(No More Heartbeats)
```

---

After:

```text
10 Seconds
```

Consumer removed.

---

# Consumer Best Practices

---

## Use Manual Offset Commit

```java
enable.auto.commit=false
```

---

## Monitor Consumer Lag

---

## Tune Poll Size

```java
max.poll.records
```

---

## Handle Rebalances Properly

---

## Use Appropriate Fetch Sizes

---

## Always Close Consumers Gracefully

```java
consumer.close();
```

---

## Monitor Heartbeat Failures

---

# Interview Questions

## What is a Kafka Consumer?

A client application that subscribes to topics and reads records from Kafka.

---

## Why does Kafka use Pull Model?

To allow consumers to control consumption rate and avoid overload.

---

## What is the Poll Mechanism?

Consumers fetch records using:

```java
consumer.poll(...)
```

---

## Why is poll() important?

It retrieves records and keeps the consumer active within the group.

---

## What happens if poll() is not called?

Consumer may be considered failed and trigger a rebalance.

---

## What are Heartbeats?

Periodic signals sent to the Group Coordinator indicating that the consumer is alive.

---

## What happens if heartbeats stop?

Consumer is removed from the group and a rebalance occurs.

---

## What is the purpose of group.id?

Identifies the Consumer Group.

---

## What is the Fetch Process?

The mechanism by which consumers retrieve records from brokers.

---

## What is the Consumer Lifecycle?

```text
Create Consumer
      ↓
Subscribe
      ↓
Poll
      ↓
Process
      ↓
Commit Offset
      ↓
Close
```

---

# Summary

Kafka Consumers are responsible for reading and processing records from Kafka topics.

Core consumer concepts include:

- Consumer Architecture
- Consumer Lifecycle
- Poll Mechanism
- Consumer Configuration
- Consumer API
- Fetch Process
- Heartbeats

Complete Consumer Flow:

```text
Create Consumer
      ↓
Subscribe
      ↓
Poll
      ↓
Fetch Records
      ↓
Process
      ↓
Commit Offset
      ↓
Heartbeat
      ↓
Repeat
```

Consumers are the foundation for:

```text
Consumer Groups
Offset Management
Rebalancing
Scalable Processing
Fault Tolerance
```

Understanding Consumers thoroughly is essential before learning Consumer Groups because Consumer Groups build directly on top of Consumer behavior.