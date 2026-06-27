# 48. Kafka APIs

Kafka provides a rich set of APIs that allow applications to:

```text
Produce Messages

Consume Messages

Manage Kafka Clusters

Process Streams

Integrate External Systems
```

These APIs are the primary way developers interact with Kafka.

---

# What are Kafka APIs?

## Definition

Kafka APIs are Java libraries (and language client equivalents) that expose Kafka functionality to applications.

---

Using Kafka APIs, developers can:

```text
Publish Events

Read Events

Manage Topics

Process Data Streams

Build Data Pipelines
```

---

# Why Kafka APIs?

Without APIs:

```text
Applications Cannot Communicate With Kafka
```

---

Kafka APIs provide:

```text
Abstraction

Scalability

Reliability

Ease Of Development
```

---

# Kafka API Ecosystem

Kafka provides five major APIs:

```text
Producer API

Consumer API

Admin API

Streams API

Connect API
```

---

# API Architecture

```text
Application
      ↓
Kafka API
      ↓
Kafka Cluster
```

---

# Overview of Kafka APIs

| API | Purpose |
|-------|---------|
| Producer API | Publish Messages |
| Consumer API | Read Messages |
| Admin API | Manage Kafka Resources |
| Streams API | Stream Processing |
| Connect API | Data Integration |

---

# Producer API

The Producer API is used to publish messages to Kafka topics.

---

# What is Producer API?

Producer API enables applications to send records to Kafka.

---

Flow:

```text
Application
      ↓
Producer API
      ↓
Kafka Topic
```

---

# Producer API Responsibilities

```text
Send Messages

Choose Partitions

Handle Retries

Handle Acknowledgements

Manage Batching
```

---

# Producer Architecture

```text
Application
      ↓
KafkaProducer
      ↓
Broker
```

---

# Creating a Producer

```java
Properties props = new Properties();

props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer",
          StringSerializer.class.getName());
props.put("value.serializer",
          StringSerializer.class.getName());

KafkaProducer<String, String> producer =
        new KafkaProducer<>(props);
```

---

# Sending a Message

```java
ProducerRecord<String, String> record =
        new ProducerRecord<>(
                "orders",
                "order-1",
                "created");

producer.send(record);
```

---

# Synchronous Send

```java
producer.send(record).get();
```

---

Behavior:

```text
Waits For Acknowledgement
```

---

# Asynchronous Send

```java
producer.send(record,
    (metadata, exception) -> {

        if(exception == null){
            System.out.println(
                metadata.partition());
        }
    });
```

---

Behavior:

```text
Non-Blocking
```

---

# Important Producer Classes

| Class | Purpose |
|---------|----------|
| KafkaProducer | Main Producer Client |
| ProducerRecord | Message Container |
| RecordMetadata | Metadata Returned After Send |
| Callback | Async Response Handler |

---

# Producer API Workflow

```text
Create Producer
       ↓
Create Record
       ↓
Send Record
       ↓
Broker Stores Message
       ↓
Acknowledgement Returned
```

---

# Producer Use Cases

```text
Order Events

Payment Events

Audit Logs

User Activity Events
```

---

# Consumer API

Consumer API is used to read records from Kafka topics.

---

# What is Consumer API?

Consumer API allows applications to subscribe to topics and process records.

---

Flow:

```text
Kafka Topic
      ↓
Consumer API
      ↓
Application
```

---

# Consumer Responsibilities

```text
Read Messages

Track Offsets

Handle Rebalancing

Process Events

Commit Offsets
```

---

# Consumer Architecture

```text
Broker
   ↓
KafkaConsumer
   ↓
Application
```

---

# Creating a Consumer

```java
Properties props = new Properties();

props.put("bootstrap.servers", "localhost:9092");
props.put("group.id", "order-group");

props.put(
    "key.deserializer",
    StringDeserializer.class.getName());

props.put(
    "value.deserializer",
    StringDeserializer.class.getName());

KafkaConsumer<String,String> consumer =
        new KafkaConsumer<>(props);
```

---

# Subscribe to Topic

```java
consumer.subscribe(
    List.of("orders"));
```

---

# Poll Messages

```java
ConsumerRecords<String,String> records =
        consumer.poll(
            Duration.ofMillis(100));
```

---

# Process Messages

```java
for (ConsumerRecord<String,String> record : records) {

    System.out.println(record.value());
}
```

---

# Commit Offset

```java
consumer.commitSync();
```

---

# Important Consumer Classes

| Class | Purpose |
|---------|---------|
| KafkaConsumer | Main Consumer Client |
| ConsumerRecord | Single Message |
| ConsumerRecords | Collection Of Messages |
| OffsetAndMetadata | Offset Information |

---

# Consumer API Workflow

```text
Subscribe
     ↓
Poll
     ↓
Process Records
     ↓
Commit Offset
```

---

# Consumer Use Cases

```text
Order Processing

Notification Service

Fraud Detection

Analytics Pipelines
```

---

# Admin API

Admin API is used to manage Kafka infrastructure programmatically.

---

# What is Admin API?

Admin API allows applications to create, modify, and manage Kafka resources.

---

Without Admin API:

```text
CLI Commands Required
```

---

With Admin API:

```text
Programmatic Management
```

---

# Admin API Responsibilities

```text
Create Topics

Delete Topics

Manage Partitions

View Cluster Metadata

Manage Configurations
```

---

# Admin API Architecture

```text
Application
      ↓
AdminClient
      ↓
Kafka Cluster
```

---

# Creating Admin Client

```java
Properties props = new Properties();

props.put(
    "bootstrap.servers",
    "localhost:9092");

AdminClient admin =
        AdminClient.create(props);
```

---

# Create Topic

```java
NewTopic topic =
    new NewTopic(
        "orders",
        3,
        (short)3);

admin.createTopics(
        List.of(topic));
```

---

# Delete Topic

```java
admin.deleteTopics(
    List.of("orders"));
```

---

# List Topics

```java
admin.listTopics()
     .names()
     .get();
```

---

# Describe Cluster

```java
admin.describeCluster();
```

---

# Important Admin Classes

| Class | Purpose |
|---------|---------|
| AdminClient | Main Administrative Client |
| NewTopic | Topic Definition |
| TopicDescription | Topic Metadata |
| ConfigResource | Configuration Resource |

---

# Admin API Use Cases

```text
Topic Provisioning

Infrastructure Automation

DevOps Automation

Cluster Monitoring
```

---

# Streams API

Streams API is Kafka's native stream-processing library.

---

# What is Streams API?

Kafka Streams allows applications to process data continuously as it arrives.

---

Flow:

```text
Input Topic
      ↓
Streams Processing
      ↓
Output Topic
```

---

# Streams API Responsibilities

```text
Filtering

Transformation

Aggregation

Windowing

Joining Streams
```

---

# Streams Architecture

```text
Topic
   ↓
Kafka Streams
   ↓
Topic
```

---

# Creating Streams Application

```java
StreamsBuilder builder =
        new StreamsBuilder();
```

---

# Create Stream

```java
KStream<String,String> orders =
        builder.stream("orders");
```

---

# Filter Records

```java
orders.filter(
    (key, value) ->
        value.contains("SUCCESS"));
```

---

# Write Output

```java
orders.to("processed-orders");
```

---

# Start Streams Application

```java
KafkaStreams streams =
    new KafkaStreams(
        builder.build(),
        props);

streams.start();
```

---

# Important Streams Classes

| Class | Purpose |
|---------|---------|
| KafkaStreams | Main Runtime |
| StreamsBuilder | Topology Builder |
| KStream | Event Stream |
| KTable | Stateful Table |
| GlobalKTable | Globally Replicated Table |

---

# Streams API Use Cases

```text
Fraud Detection

Real-Time Analytics

Event Enrichment

Monitoring Systems
```

---

# Connect API

Connect API powers Kafka Connect.

---

# What is Connect API?

Connect API enables integration between Kafka and external systems.

---

Examples:

```text
Databases

Cloud Storage

Search Engines

Message Queues
```

---

# Connect Architecture

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

---

# Connect API Responsibilities

```text
Data Import

Data Export

Connector Management

Pipeline Automation
```

---

# Connector Types

---

## Source Connector

Imports data into Kafka.

---

Example:

```text
MySQL
   ↓
Kafka
```

---

# Sink Connector

Exports data from Kafka.

---

Example:

```text
Kafka
   ↓
Elasticsearch
```

---

# Connect API Components

| Component | Purpose |
|------------|----------|
| Connector | Integration Definition |
| Task | Processing Unit |
| Worker | Runtime Process |
| Converter | Serialization Handling |

---

# Example Connect Workflow

```text
MySQL
   ↓
JDBC Source Connector
   ↓
Kafka Topic
   ↓
Elasticsearch Sink Connector
   ↓
Elasticsearch
```

---

# Connect API Use Cases

```text
Database CDC

Data Lake Pipelines

Search Indexing

ETL Workflows
```

---

# Comparison of Kafka APIs

| API | Read | Write | Admin | Processing | Integration |
|------|------|--------|--------|------------|-------------|
| Producer API | No | Yes | No | No | No |
| Consumer API | Yes | No | No | No | No |
| Admin API | No | No | Yes | No | No |
| Streams API | Yes | Yes | No | Yes | No |
| Connect API | Yes | Yes | Limited | No | Yes |

---

# When to Use Which API?

| Requirement | API |
|------------|-----|
| Publish Events | Producer API |
| Consume Events | Consumer API |
| Create Topics | Admin API |
| Real-Time Processing | Streams API |
| Database Integration | Connect API |

---

# End-to-End Example

Order Processing System:

```text
Order Service
      ↓
Producer API
      ↓
Orders Topic
      ↓
Streams API
      ↓
Fraud Detection Topic
      ↓
Consumer API
      ↓
Notification Service
```

---

Database Integration:

```text
MySQL
      ↓
Connect API
      ↓
Kafka
      ↓
Consumer API
```

---

# Best Practices

---

## Producer API

```text
Enable Idempotence

Use ACK=all

Configure Retries
```

---

## Consumer API

```text
Use Manual Offset Commit

Handle Rebalancing

Monitor Consumer Lag
```

---

## Admin API

```text
Automate Topic Creation

Validate Configurations
```

---

## Streams API

```text
Use Stateful Processing Carefully

Monitor State Stores
```

---

## Connect API

```text
Use Distributed Mode

Monitor Connector Health
```

---

# Common Interview Questions

## What are Kafka APIs?

Kafka APIs are client libraries that allow applications to interact with Kafka for producing, consuming, managing, processing, and integrating data.

---

## What are the five major Kafka APIs?

```text
Producer API

Consumer API

Admin API

Streams API

Connect API
```

---

## Which API is used to publish messages?

```text
Producer API
```

---

## Which API is used to consume messages?

```text
Consumer API
```

---

## Which API is used to create topics programmatically?

```text
Admin API
```

---

## Which API is used for stream processing?

```text
Streams API
```

---

## Which API is used for database integration?

```text
Connect API
```

---

## What is the difference between Streams API and Consumer API?

Consumer API only reads records.

Streams API reads, processes, transforms, and writes records.

---

## Can Streams API write back to Kafka?

```text
Yes
```

It can consume from input topics and produce to output topics.

---

## What powers Kafka Connect?

```text
Connect API
```

---

# Summary

Kafka APIs provide the primary interface for interacting with Kafka clusters.

Core APIs:

- Producer API
- Consumer API
- Admin API
- Streams API
- Connect API

Producer API:

```text
Publish Messages

Handle Retries

Manage Acknowledgements
```

Consumer API:

```text
Read Messages

Commit Offsets

Handle Rebalancing
```

Admin API:

```text
Create Topics

Manage Configurations

Inspect Cluster Metadata
```

Streams API:

```text
Real-Time Processing

Aggregations

Joins

Windowing
```

Connect API:

```text
Database Integration

ETL Pipelines

External System Connectivity
```

These APIs collectively enable developers to build complete event-driven systems, from message production and consumption to stream processing, cluster administration, and enterprise-scale data integration.