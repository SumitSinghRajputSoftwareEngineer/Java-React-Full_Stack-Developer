
As Kafka applications grow in scale and complexity, basic producers and consumers are no longer enough.

Enterprise-grade Kafka applications often require:

```text
High Throughput

Reliable Processing

Error Recovery

Exactly Once Processing

Stream Processing

Transactional Messaging
```

Spring Kafka provides advanced features that help solve these requirements.

---

# Advanced Topics Overview

The most important Spring Kafka advanced features are:

```text
Batch Consumers

Manual Acknowledgement

Dead Letter Topics (DLT)

Retry Topics

Transactions

Kafka Streams Integration
```

---

# Why Advanced Features?

Basic consumers work well for:

```text
Low Traffic Applications

Simple Event Processing
```

---

Large-scale systems need:

```text
Millions of Messages

Fault Tolerance

Failure Recovery

Guaranteed Processing
```

---

Example:

```text
E-Commerce Platform

100,000 Orders/Minute
```

Processing messages one-by-one may become inefficient.

---

# Batch Consumers

Batch consumers improve throughput by processing multiple messages together.

---

# What is Batch Consumption?

Instead of receiving:

```text
1 Message
```

Consumer receives:

```text
Multiple Messages
```

in a single poll.

---

# Single Message Processing

```text
Poll
 ↓
Message-1
 ↓
Process
```

---

# Batch Processing

```text
Poll
 ↓
Message-1
Message-2
Message-3
Message-4
 ↓
Process Batch
```

---

# Why Batch Consumers?

Benefits:

```text
Higher Throughput

Reduced Network Calls

Reduced CPU Usage

Better Resource Utilization
```

---

# Enable Batch Consumption

```properties
spring.kafka.listener.type=batch
```

---

# Batch Consumer Example

```java
@KafkaListener(
        topics = "orders")
public void consume(
        List<String> messages){

    messages.forEach(
            System.out::println);
}
```

---

# Batch Consumer Workflow

```text
Kafka Topic
      ↓
Poll Records
      ↓
Create Batch
      ↓
Process Batch
```

---

# Using ConsumerRecords

```java
@KafkaListener(
        topics = "orders")
public void consume(
    ConsumerRecords<String,String>
    records){

    for(ConsumerRecord<String,String>
            record : records){

        System.out.println(
                record.value());
    }
}
```

---

# Batch Size Control

```properties
spring.kafka.consumer.max-poll-records=500
```

---

Meaning:

```text
Maximum 500 Records
Per Poll
```

---

# Batch Consumer Advantages

```text
Higher Throughput

Lower Poll Overhead

Better Performance
```

---

# Batch Consumer Limitations

```text
Higher Memory Usage

More Complex Error Handling

Longer Processing Time
```

---

# Real-World Example

Analytics Service:

```text
1000 Events
      ↓
Batch Consumer
      ↓
Single Database Insert
```

---

Instead of:

```text
1000 Individual Inserts
```

---

# Manual Acknowledgement

Manual acknowledgement gives complete control over offset commits.

---

# What is Manual Acknowledgement?

Consumer explicitly decides:

```text
When Offset Should Be Committed
```

---

# Why Manual Acknowledgement?

Auto commit may cause:

```text
Message Loss

Duplicate Processing
```

---

Manual acknowledgment improves reliability.

---

# Auto Commit Flow

```text
Receive Message
       ↓
Offset Committed
       ↓
Processing
```

---

Problem:

```text
Processing Fails

Offset Already Committed
```

---

Message is lost.

---

# Manual Commit Flow

```text
Receive Message
       ↓
Process Message
       ↓
Commit Offset
```

---

If processing fails:

```text
Offset Not Committed
```

Message can be retried.

---

# Enable Manual Acknowledgement

```properties
spring.kafka.consumer.enable-auto-commit=false
```

---

# Listener Configuration

```java
@Bean
public ConcurrentKafkaListenerContainerFactory
factory() {

    factory.getContainerProperties()
           .setAckMode(
              AckMode.MANUAL);

    return factory;
}
```

---

# Manual Ack Example

```java
@KafkaListener(
        topics = "orders")
public void consume(
        String message,
        Acknowledgment ack){

    process(message);

    ack.acknowledge();
}
```

---

# Workflow

```text
Receive Message
      ↓
Process Successfully
      ↓
ack.acknowledge()
      ↓
Offset Commit
```

---

# Ack Modes

| Mode | Description |
|--------|------------|
| RECORD | Commit After Each Record |
| BATCH | Commit After Batch |
| TIME | Commit At Time Interval |
| COUNT | Commit After N Records |
| MANUAL | Manual Commit |
| MANUAL_IMMEDIATE | Immediate Manual Commit |

---

# Best Use Cases

```text
Payments

Financial Systems

Order Processing

Critical Workflows
```

---

# Dead Letter Topics (DLT)

DLT is one of the most important reliability mechanisms.

---

# What is Dead Letter Topic?

A special Kafka topic where failed messages are sent.

---

# Why DLT?

Some messages:

```text
Cannot Be Processed

Invalid Data

Corrupted Payload

Permanent Failures
```

---

Without DLT:

```text
Consumer Gets Stuck
```

---

# DLT Architecture

```text
Orders Topic
      ↓
Consumer
      ↓
Failure
      ↓
Orders-DLT
```

---

# DLT Flow

```text
Message
   ↓
Processing
   ↓
Failure
   ↓
Retry
   ↓
Failure
   ↓
DLT
```

---

# DLT Example

```java
@DltHandler
public void processDlt(
        String message){

    System.out.println(
        "Failed Message: "
         + message);
}
```

---

# Benefits

```text
No Message Loss

Failure Analysis

Easy Recovery

Operational Visibility
```

---

# DLT Naming Convention

Typically:

```text
orders
```

DLT:

```text
orders-dlt
```

---

# Retry Topics

Retry topics allow failed messages to be retried asynchronously.

---

# What are Retry Topics?

Special Kafka topics used for delayed retries.

---

# Why Retry Topics?

Failures are often temporary.

Examples:

```text
Database Down

Network Timeout

API Failure
```

---

Retrying later often succeeds.

---

# Retry Topic Architecture

```text
Orders
   ↓
Failure
   ↓
Orders-Retry
   ↓
Retry Consumer
   ↓
Success
```

---

# Spring Kafka Retry Topics

Spring provides:

```java
@RetryableTopic
```

---

# Example

```java
@RetryableTopic(
        attempts = "5")
@KafkaListener(
        topics = "orders")
public void consume(
        String message){

}
```

---

# Internal Topic Creation

Spring automatically creates:

```text
orders-retry-0

orders-retry-1

orders-retry-2

orders-dlt
```

---

# Retry Flow

```text
Orders
   ↓
Failure
   ↓
Retry-0
   ↓
Failure
   ↓
Retry-1
   ↓
Failure
   ↓
DLT
```

---

# Backoff Configuration

```java
@RetryableTopic(
    attempts = "5",
    backoff = @Backoff(
        delay = 2000))
```

---

Meaning:

```text
2 Second Delay
Between Retries
```

---

# Exponential Backoff

```java
@Backoff(
    delay = 1000,
    multiplier = 2)
```

---

Retry intervals:

```text
1 sec

2 sec

4 sec

8 sec
```

---

# Retry Topics Benefits

```text
Non-Blocking

Scalable

Production Ready

Easy Recovery
```

---

# Transactions

Transactions provide atomic operations across Kafka messages.

---

# What are Kafka Transactions?

A transaction ensures:

```text
Either Everything Happens

Or

Nothing Happens
```

---

# Why Transactions?

Without transactions:

```text
Partial Processing Possible
```

---

Example:

```text
Message Written
Database Failed
```

System becomes inconsistent.

---

# Transaction Flow

```text
Begin Transaction
      ↓
Send Messages
      ↓
Commit Transaction
```

---

If failure:

```text
Abort Transaction
```

---

# Enable Transactions

```properties
spring.kafka.producer.transaction-id-prefix=tx-
```

---

# Transactional Producer Example

```java
@Transactional
public void processOrder(){

    kafkaTemplate.send(
        "orders",
        "created");

    kafkaTemplate.send(
        "audit",
        "logged");
}
```

---

# Workflow

```text
Transaction Starts
      ↓
Send Message-1
      ↓
Send Message-2
      ↓
Commit
```

---

If failure:

```text
Rollback
```

---

# Benefits

```text
Atomic Writes

Consistency

Exactly Once Support
```

---

# Chained Transactions

Spring supports:

```text
Database Transaction
+
Kafka Transaction
```

---

Example:

```text
Save Order
      ↓
Publish Event
      ↓
Commit Together
```

---

# Kafka Streams Integration

Spring Boot integrates seamlessly with Kafka Streams.

---

# Why Kafka Streams?

Consumers only read data.

Streams applications:

```text
Read

Process

Transform

Write
```

---

# Kafka Streams Architecture

```text
Input Topic
      ↓
Kafka Streams
      ↓
Output Topic
```

---

# Add Dependency

```xml
<dependency>
    <groupId>
        org.springframework.kafka
    </groupId>
    <artifactId>
        spring-kafka
    </artifactId>
</dependency>
```

---

# Streams Configuration

```properties
spring.kafka.streams.application-id=order-stream-app

spring.kafka.bootstrap-servers=localhost:9092
```

---

# Create Stream

```java
@Bean
public KStream<String,String>
processStream(
        StreamsBuilder builder){

    KStream<String,String>
        stream =
        builder.stream("orders");

    return stream;
}
```

---

# Filter Stream

```java
stream.filter(
    (key,value) ->
        value.contains("SUCCESS"));
```

---

# Write To Topic

```java
stream.to(
    "processed-orders");
```

---

# Streams Workflow

```text
Orders Topic
      ↓
KStream
      ↓
Filter
      ↓
Transform
      ↓
Processed Topic
```

---

# Stateful Processing

Streams supports:

```text
Aggregations

Joins

Windowing
```

---

Example:

```java
stream.groupByKey()
      .count();
```

---

# Streams Use Cases

```text
Fraud Detection

Analytics

Monitoring

Data Enrichment
```

---

# Advanced Architecture Example

```text
Producer
    ↓
Orders Topic
    ↓
Batch Consumer
    ↓
Retry Topic
    ↓
Dead Letter Topic
```

---

Transactional Example:

```text
REST API
    ↓
Database
    ↓
Kafka Transaction
    ↓
Orders Topic
```

---

Streaming Example:

```text
Orders Topic
      ↓
Kafka Streams
      ↓
Aggregated Topic
```

---

# Best Practices

## Batch Consumers

```text
Use For High Throughput

Tune max.poll.records

Monitor Memory Usage
```

---

## Manual Acknowledgement

```text
Use For Critical Systems

Commit Only After Success
```

---

## Dead Letter Topics

```text
Always Use In Production

Monitor DLT Growth
```

---

## Retry Topics

```text
Prefer Non-Blocking Retries

Use Exponential Backoff
```

---

## Transactions

```text
Use For Financial Systems

Enable Idempotence
```

---

## Kafka Streams

```text
Use For Real-Time Processing

Monitor State Stores
```

---

# Common Interview Questions

## What is a Batch Consumer?

A consumer that processes multiple Kafka records in a single poll operation.

---

## Why use Manual Acknowledgement?

To control exactly when offsets are committed and avoid message loss.

---

## What is a Dead Letter Topic?

A special topic where failed messages are stored after retries are exhausted.

---

## What is @RetryableTopic?

A Spring Kafka feature that automatically creates retry topics and manages retries asynchronously.

---

## What is the benefit of Retry Topics over blocking retries?

Retry topics do not block consumer threads and scale much better.

---

## Why use Kafka Transactions?

To ensure atomic writes and support Exactly Once Semantics.

---

## How are Kafka Transactions enabled in Spring Boot?

```properties
spring.kafka.producer.transaction-id-prefix=tx-
```

---

## What is Kafka Streams Integration?

Using Kafka Streams inside Spring Boot applications for real-time stream processing.

---

## When should Batch Consumers be used?

When high throughput is required and processing messages one-by-one becomes inefficient.

---

## What happens if a message repeatedly fails?

After configured retries, it is sent to the Dead Letter Topic (DLT).

---

# Summary

Spring Kafka provides advanced capabilities for building enterprise-grade Kafka applications.

Core Advanced Topics:

- Batch Consumers
- Manual Acknowledgement
- Dead Letter Topics
- Retry Topics
- Transactions
- Kafka Streams Integration

Batch Consumers:

```text
Process Multiple Records

Higher Throughput

Reduced Overhead
```

Manual Acknowledgement:

```text
Explicit Offset Control

Improved Reliability
```

Dead Letter Topics:

```text
Store Failed Messages

Prevent Data Loss
```

Retry Topics:

```text
Asynchronous Retries

Exponential Backoff

Scalable Recovery
```

Transactions:

```text
Atomic Operations

Exactly Once Support

Consistency Guarantees
```

Kafka Streams Integration:

```text
Real-Time Processing

Transformations

Aggregations

Stream Analytics
```

Production Recommendations:

```text
Use Manual Ack For Critical Systems

Use DLT In Production

Prefer Retry Topics

Enable Transactions When Needed

Use Batch Consumers For High Throughput

Use Kafka Streams For Real-Time Analytics
```

These advanced Spring Kafka features enable developers to build highly reliable, fault-tolerant, scalable, and production-ready event-driven systems that can handle real-world enterprise workloads.