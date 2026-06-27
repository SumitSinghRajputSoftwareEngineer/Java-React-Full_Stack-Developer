# 50. Spring Boot with Kafka

Apache Kafka is extremely powerful, but using the raw Kafka Java API can require a lot of boilerplate code.

Spring Boot simplifies Kafka integration through:

```text
Spring Kafka

Auto Configuration

Annotations

Dependency Injection

Error Handling

Retry Mechanisms
```

With Spring Kafka, developers can quickly build:

```text
Event-Driven Systems

Microservices

Real-Time Applications

Streaming Applications
```

---

# What is Spring Kafka?

## Definition

Spring Kafka is a Spring project that provides seamless integration between Spring applications and Apache Kafka.

---

Official Goals:

```text
Simplify Kafka Development

Reduce Boilerplate Code

Provide Spring-Friendly APIs

Enable Enterprise Features
```

---

# Why Spring Kafka?

Without Spring Kafka:

```text
Create KafkaProducer

Create KafkaConsumer

Manage Threads

Handle Serialization

Handle Errors Manually
```

---

With Spring Kafka:

```text
KafkaTemplate

@KafkaListener

Auto Configuration

Retry Support

Error Handling
```

---

# Architecture

```text
Spring Boot Application
          ↓
Spring Kafka
          ↓
Apache Kafka
          ↓
Kafka Cluster
```

---

# Maven Dependency

For Spring Boot 3.x:

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

---

# Spring Kafka Components

Core components:

```text
KafkaTemplate

@KafkaListener

ProducerFactory

ConsumerFactory

KafkaAdmin

ErrorHandler

RetryTopic
```

---

# Spring Kafka Flow

```text
Producer
   ↓
KafkaTemplate
   ↓
Kafka Topic
   ↓
@KafkaListener
   ↓
Consumer
```

---

# KafkaTemplate

KafkaTemplate is the primary API for sending messages.

---

# What is KafkaTemplate?

KafkaTemplate is Spring Kafka's abstraction over KafkaProducer.

---

Instead of:

```java
KafkaProducer
```

Spring provides:

```java
KafkaTemplate
```

---

# Benefits

```text
Simplified API

Spring Integration

Asynchronous Support

Callback Support
```

---

# Inject KafkaTemplate

```java
@Service
public class OrderProducer {

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;
}
```

---

# Send Message

```java
kafkaTemplate.send(
        "orders",
        "Order Created");
```

---

Flow:

```text
Application
      ↓
KafkaTemplate
      ↓
Kafka Topic
```

---

# Send Message With Key

```java
kafkaTemplate.send(
        "orders",
        "order-101",
        "CREATED");
```

---

# Send Message Object

```java
Order order =
        new Order(101, "Laptop");

kafkaTemplate.send(
        "orders",
        order);
```

---

# Async Send

```java
CompletableFuture<SendResult<String,String>>
future =
        kafkaTemplate.send(
                "orders",
                "message");
```

---

# Success Callback

```java
future.whenComplete(
    (result, ex) -> {

        if(ex == null){
            System.out.println(
                "Sent Successfully");
        }
    });
```

---

# KafkaTemplate Methods

| Method | Purpose |
|----------|----------|
| send() | Send Message |
| flush() | Force Send |
| execute() | Execute Operations |
| sendDefault() | Send To Default Topic |

---

# KafkaTemplate Best Practices

---

## Use Keys When Ordering Matters

---

## Use Async Processing

---

## Enable Idempotence

---

## Configure Retries

---

# @KafkaListener

@KafkaListener is the most important consumer-side annotation.

---

# What is @KafkaListener?

@KafkaListener automatically creates Kafka consumers and listens to Kafka topics.

---

Without Spring Kafka:

```text
Create KafkaConsumer

Subscribe

Poll

Process Messages
```

---

With Spring Kafka:

```java
@KafkaListener(...)
```

---

# Basic Example

```java
@Component
public class OrderConsumer {

    @KafkaListener(
            topics = "orders",
            groupId = "order-group")
    public void consume(String message) {

        System.out.println(message);
    }
}
```

---

# Workflow

```text
Kafka Topic
      ↓
@KafkaListener
      ↓
Method Invocation
```

---

# Listen To Multiple Topics

```java
@KafkaListener(
    topics = {
        "orders",
        "payments"
    })
```

---

# Consume Key and Value

```java
@KafkaListener(topics = "orders")
public void consume(
        ConsumerRecord<String,String>
        record){

    System.out.println(
            record.key());

    System.out.println(
            record.value());
}
```

---

# Read Headers

```java
@KafkaListener(topics="orders")
public void consume(
        @Header(KafkaHeaders.RECEIVED_KEY)
        String key){

    System.out.println(key);
}
```

---

# Concurrent Consumers

```java
@KafkaListener(
        topics = "orders",
        concurrency = "3")
```

---

Creates:

```text
3 Consumer Threads
```

---

# Batch Consumption

```java
@KafkaListener(
        topics = "orders")
public void consume(
        List<String> messages){

}
```

---

# Listener Container

Behind the scenes:

```text
KafkaMessageListenerContainer
```

manages:

```text
Consumer Threads

Polling

Offset Commit

Error Handling
```

---

# Producer Configuration

Spring Boot automatically configures producers through application.properties or application.yml.

---

# Producer Configuration

```properties
spring.kafka.producer.bootstrap-servers=localhost:9092

spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer

spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
```

---

# Important Producer Properties

| Property | Purpose |
|-----------|----------|
| bootstrap-servers | Kafka Brokers |
| key-serializer | Key Serialization |
| value-serializer | Value Serialization |
| retries | Retry Count |
| acks | Acknowledgement Mode |
| batch-size | Batch Configuration |
| linger-ms | Batch Delay |

---

# Reliability Configuration

```properties
spring.kafka.producer.acks=all

spring.kafka.producer.retries=5
```

---

# Idempotent Producer

```properties
spring.kafka.producer.properties.enable.idempotence=true
```

---

Benefits:

```text
Duplicate Prevention

Exactly Once Support
```

---

# Custom Producer Configuration

```java
@Bean
public ProducerFactory<String,String>
producerFactory() {

    Map<String,Object> config =
            new HashMap<>();

    config.put(
            ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,
            "localhost:9092");

    return new DefaultKafkaProducerFactory<>(
            config);
}
```

---

# Consumer Configuration

Consumer configuration controls how messages are consumed.

---

# Basic Consumer Configuration

```properties
spring.kafka.consumer.bootstrap-servers=localhost:9092

spring.kafka.consumer.group-id=order-group

spring.kafka.consumer.auto-offset-reset=earliest
```

---

# Deserializer Configuration

```properties
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer

spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
```

---

# Important Consumer Properties

| Property | Purpose |
|-----------|----------|
| group-id | Consumer Group |
| auto-offset-reset | Offset Strategy |
| enable-auto-commit | Auto Commit |
| max-poll-records | Batch Size |
| session-timeout-ms | Failure Detection |
| heartbeat-interval-ms | Heartbeat Frequency |

---

# Manual Offset Commit

```properties
spring.kafka.consumer.enable-auto-commit=false
```

---

Benefits:

```text
Better Reliability

Controlled Processing
```

---

# Custom Consumer Factory

```java
@Bean
public ConsumerFactory<String,String>
consumerFactory() {

    return new DefaultKafkaConsumerFactory<>(
            configs);
}
```

---

# Consumer Concurrency

```java
factory.setConcurrency(3);
```

---

Meaning:

```text
3 Concurrent Consumers
```

---

# Error Handling

Production systems must handle failures gracefully.

---

# Why Error Handling?

Failures happen because of:

```text
Invalid Data

Database Failures

Network Failures

Serialization Errors
```

---

Without error handling:

```text
Consumer Crashes
```

---

# Common Error Types

---

## Deserialization Errors

---

## Processing Errors

---

## Broker Errors

---

## Timeout Errors

---

# DefaultErrorHandler

Spring Kafka provides:

```java
DefaultErrorHandler
```

---

Example:

```java
@Bean
public DefaultErrorHandler errorHandler(){

    return new DefaultErrorHandler();
}
```

---

# Custom Error Handler

```java
@Bean
public DefaultErrorHandler errorHandler() {

    FixedBackOff backOff =
            new FixedBackOff(
                    1000L,
                    3);

    return new DefaultErrorHandler(
            backOff);
}
```

---

Meaning:

```text
Retry Every 1 Second

Maximum 3 Retries
```

---

# Dead Letter Topic (DLT)

Failed messages can be redirected.

---

Architecture:

```text
Orders Topic
      ↓
Consumer
      ↓
Failure
      ↓
DLT Topic
```

---

Example:

```text
orders

orders-dlt
```

---

Benefits:

```text
No Data Loss

Failure Investigation

Message Recovery
```

---

# Retry Mechanisms

Retry is essential for transient failures.

---

# Why Retry?

Many failures are temporary:

```text
Database Down

Network Timeout

Service Unavailable
```

---

Retry allows recovery without message loss.

---

# Retry Approaches

Spring Kafka supports:

```text
Blocking Retries

Non-Blocking Retries

Retry Topics
```

---

# Blocking Retry

Consumer retries immediately.

---

Example:

```java
DefaultErrorHandler
```

with:

```java
FixedBackOff
```

---

Flow:

```text
Process
   ↓
Failure
   ↓
Retry
   ↓
Success
```

---

# Fixed Backoff Retry

```java
new FixedBackOff(
        2000L,
        5);
```

---

Meaning:

```text
2 Seconds Delay

5 Retry Attempts
```

---

# Exponential Backoff

Retries increase gradually.

---

Example:

```text
1 Second

2 Seconds

4 Seconds

8 Seconds
```

---

Benefits:

```text
Avoid System Overload
```

---

# @RetryableTopic

Modern Spring Kafka retry mechanism.

---

Example:

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

# Internal Retry Flow

```text
orders
   ↓
orders-retry-0
   ↓
orders-retry-1
   ↓
orders-dlt
```

---

Benefits:

```text
Non-Blocking

Scalable

Production Ready
```

---

# Dead Letter Topic Example

```java
@DltHandler
public void handleDlt(
        String message){

    System.out.println(
            "Failed Message: "
            + message);
}
```

---

# Recommended Retry Strategy

Production:

```text
Retry Topic

Exponential Backoff

Dead Letter Topic
```

---

# Complete Spring Kafka Flow

```text
REST API
    ↓
KafkaTemplate
    ↓
Orders Topic
    ↓
@KafkaListener
    ↓
Business Logic
    ↓
Success
```

---

Failure Flow:

```text
Orders Topic
      ↓
Consumer
      ↓
Failure
      ↓
Retry Topic
      ↓
DLT
```

---

# Best Practices

## Producer

```text
Enable Idempotence

Use ACK=all

Configure Retries

Use Keys Properly
```

---

## Consumer

```text
Use Consumer Groups

Disable Auto Commit

Handle Errors Properly

Monitor Consumer Lag
```

---

## Error Handling

```text
Use DefaultErrorHandler

Log Failures

Use DLT
```

---

## Retry

```text
Prefer @RetryableTopic

Use Exponential Backoff

Limit Retry Attempts
```

---

# Common Interview Questions

## What is Spring Kafka?

A Spring project that simplifies Kafka integration in Spring applications.

---

## What is KafkaTemplate?

A high-level abstraction over KafkaProducer used to send messages.

---

## What is @KafkaListener?

An annotation that automatically creates Kafka consumers and listens to topics.

---

## How do you send messages in Spring Kafka?

```java
kafkaTemplate.send(...)
```

---

## How do you consume messages?

```java
@KafkaListener(...)
```

---

## What is DefaultErrorHandler?

A Spring Kafka component used to handle consumer processing failures.

---

## What is a Dead Letter Topic?

A topic where failed messages are redirected after retry exhaustion.

---

## What is @RetryableTopic?

A Spring Kafka feature that creates retry topics automatically for non-blocking retries.

---

## Why disable auto-commit?

To gain full control over offset commits and ensure reliable processing.

---

## What is the recommended retry strategy?

```text
Retry Topics

Exponential Backoff

Dead Letter Topic
```

---

# Summary

Spring Kafka provides seamless integration between Spring Boot and Apache Kafka.

Core Components:

- Spring Kafka Introduction
- KafkaTemplate
- @KafkaListener
- Producer Configuration
- Consumer Configuration
- Error Handling
- Retry Mechanisms

KafkaTemplate:

```text
Send Messages

Async Support

Producer Abstraction
```

@KafkaListener:

```text
Consume Messages

Automatic Consumer Creation

Concurrent Processing
```

Producer Configuration:

```text
ACK=all

Retries

Idempotence
```

Consumer Configuration:

```text
Consumer Groups

Offset Management

Concurrency
```

Error Handling:

```text
DefaultErrorHandler

DLT

Custom Recovery
```

Retry Mechanisms:

```text
Fixed Backoff

Exponential Backoff

@RetryableTopic
```

Production Recommendations:

```text
Enable Idempotence

Use ACK=all

Disable Auto Commit

Use Retry Topics

Use Dead Letter Topics

Monitor Consumer Lag
```

Spring Kafka significantly reduces boilerplate code and provides enterprise-grade features such as retries, dead-letter topics, error handling, and seamless Spring integration, making it the preferred way to build Kafka-based applications in Spring Boot.