
## Definition

`@EnableKafka` is a Spring Kafka annotation that enables Kafka listener detection and processing in a Spring application.

It tells Spring:

> Scan the application for methods annotated with `@KafkaListener` and create Kafka consumer containers automatically.

Package:

```java
import org.springframework.kafka.annotation.EnableKafka;
```

---

# Why Do We Need @EnableKafka?

Suppose you have:

```java
@KafkaListener(
    topics = "orders"
)
public void consume(
        String message) {

    System.out.println(message);
}
```

---

Without:

```java
@EnableKafka
```

Spring will not activate Kafka listener infrastructure.

---

Result:

```text
Consumer Never Starts
Messages Never Consumed
```

---

# Real-Life Analogy

Imagine:

```text
Radio Station Broadcasting
```

---

Kafka Topic:

```text
Radio Channel
```

---

Consumer:

```text
Radio Receiver
```

---

Without turning on the radio:

```text
No Broadcast Received
```

---

`@EnableKafka` is like:

```text
Powering On The Radio
```

so listeners can receive messages.

---

# Required Dependency

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

# Basic Example

```java
@Configuration
@EnableKafka
public class KafkaConfig {

}
```

---

This activates:

```text
Kafka Listener Infrastructure
```

---

# Example Consumer

```java
@Component
public class OrderConsumer {

    @KafkaListener(
        topics = "orders",
        groupId = "group1"
    )
    public void consume(
            String message) {

        System.out.println(message);
    }
}
```

---

Application Startup:

```text
Spring Starts
      ↓
@EnableKafka Found
      ↓
Search @KafkaListener
      ↓
Create Consumers
      ↓
Subscribe To Topics
```

---

# What Happens Internally?

Without Kafka:

```text
Application Starts
```

---

With:

```java
@EnableKafka
```

Spring creates:

```text
KafkaListenerContainerFactory
Consumer Containers
Listener Endpoints
Background Threads
```

---

Then:

```text
Topic Subscription
Begins Automatically
```

---

# Internal Workflow

```text
@EnableKafka
      ↓
Scan @KafkaListener
      ↓
Create Listener Containers
      ↓
Connect To Kafka Broker
      ↓
Subscribe To Topic
      ↓
Consume Messages
```

---

# Architecture

```text
Producer
    │
    ▼

 Kafka Topic

    │
    ▼

@KafkaListener
```

---

# Producer Example

```java
@Autowired
private KafkaTemplate<
        String,
        String> kafkaTemplate;

public void send() {

    kafkaTemplate.send(
        "orders",
        "Order Created"
    );
}
```

---

# Consumer Example

```java
@KafkaListener(
    topics = "orders"
)
public void consume(
        String msg) {

    System.out.println(msg);
}
```

---

Execution:

```text
Producer Sends
      ↓
Topic Receives
      ↓
Consumer Reads
```

---

# Multiple Listeners

```java
@KafkaListener(
    topics = "orders"
)
public void orderConsumer(
    String msg
) {
}
```

---

```java
@KafkaListener(
    topics = "payments"
)
public void paymentConsumer(
    String msg
) {
}
```

---

`@EnableKafka` detects both.

---

# Multiple Topics

```java
@KafkaListener(
    topics = {
        "orders",
        "payments"
    }
)
public void consume(
        String msg) {

}
```

---

Consumes from:

```text
orders
payments
```

---

# Consumer Group Example

```java
@KafkaListener(
    topics = "orders",
    groupId = "group1"
)
```

---

Meaning:

```text
Consumer Belongs To
group1
```

---

Kafka ensures:

```text
Message Processed Once
Per Group
```

---

# Concurrency Example

```java
@KafkaListener(
    topics = "orders",
    concurrency = "3"
)
```

---

Meaning:

```text
3 Consumer Threads
```

---

Useful for:

```text
Parallel Processing
```

---

# Message Deserialization

```java
@KafkaListener(
    topics = "orders"
)
public void consume(
        Order order) {

}
```

---

Spring automatically:

```text
JSON → Java Object
```

---

When configured correctly.

---

# Configuration Example

application.yml

```yaml
spring:
  kafka:
    bootstrap-servers:
      localhost:9092

    consumer:
      group-id: group1

      auto-offset-reset:
        earliest
```

---

Meaning:

```text
Connect To Kafka

Join Group1

Read From Earliest Offset
```

---

# Real Production Scenario

E-Commerce:

```text
Order Service
      ↓
Kafka
      ↓
Inventory Service
      ↓
Notification Service
      ↓
Shipping Service
```

---

Inventory Consumer:

```java
@KafkaListener(
    topics="orders"
)
```

---

Notification Consumer:

```java
@KafkaListener(
    topics="orders"
)
```

---

Shipping Consumer:

```java
@KafkaListener(
    topics="orders"
)
```

---

All consume events independently.

---

# Without @EnableKafka

Consumer:

```java
@KafkaListener(
    topics="orders"
)
```

---

But:

```java
@EnableKafka
```

missing.

---

Result:

```text
Listener Not Registered
```

---

Messages:

```text
Never Consumed
```

---

# @EnableKafka vs @KafkaListener

Very Common Interview Question.

---

## @EnableKafka

Purpose:

```text
Enable Kafka Infrastructure
```

---

Used Once.

---

Example:

```java
@EnableKafka
```

---

## @KafkaListener

Purpose:

```text
Consume Messages
```

---

Used On Methods.

---

Example:

```java
@KafkaListener(
   topics="orders"
)
```

---

Comparison:

| Annotation | Purpose |
|------------|----------|
| `@EnableKafka` | Activate Kafka |
| `@KafkaListener` | Consume Messages |

---

# Common Use Cases

## Order Processing

```text
Orders Topic
```

---

## Payment Events

```text
Payments Topic
```

---

## Notifications

```text
Email/SMS Events
```

---

## Audit Logging

```text
Audit Topic
```

---

## Event-Driven Architecture

```text
Microservices Communication
```

---

# Common Mistakes

## Mistake 1

Missing Dependency

Without:

```xml
spring-kafka
```

---

Nothing works.

---

# Mistake 2

Missing @EnableKafka

Wrong:

```java
@KafkaListener
```

only.

---

Result:

```text
Listener Never Starts
```

---

# Mistake 3

Wrong Topic Name

```java
topics="order"
```

instead of:

```java
topics="orders"
```

---

No messages consumed.

---

# Common Interview Questions

## What is @EnableKafka?

Enables Kafka listener processing.

---

## Why Is It Used?

To activate Kafka consumer infrastructure.

---

## What Does It Scan?

```java
@KafkaListener
```

methods.

---

## Is It Mandatory?

Generally yes, unless auto-configuration handles it.

---

## Difference Between @EnableKafka and @KafkaListener?

Infrastructure vs actual consumer.

---

# Enterprise Best Practice

Place:

```java
@EnableKafka
```

inside:

```java
@Configuration
```

class.

---

Keep listeners:

```text
consumer package
```

---

Example:

```text
com.company.consumer
```

---

Use with:

```text
KafkaTemplate
KafkaListener
Consumer Groups
Partitions
```

---

# Key Points To Remember

- `@EnableKafka` enables Kafka listener processing.
- Scans methods annotated with `@KafkaListener`.
- Creates Kafka listener containers automatically.
- Required for Kafka consumers.
- Works with consumer groups.
- Supports concurrency.
- Used in event-driven microservices.
- Frequently asked Kafka interview topic.
- Activates Kafka infrastructure.
- Usually placed in configuration classes.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableKafka` | Enable Kafka Infrastructure |
| `@KafkaListener` | Consume Messages |
| `@KafkaHandler` | Handle Different Message Types |
| `@SendTo` | Send Reply Message |

---

# Interview Shortcut

```java
@Configuration
@EnableKafka
public class KafkaConfig {
}
```

Meaning:

```text
Activate Kafka

Scan @KafkaListener

Create Consumers

Start Listening
```

---

# Real Production Example

```java
@Configuration
@EnableKafka
public class KafkaConfig {
}
```

```java
@Component
public class OrderConsumer {

    @KafkaListener(
        topics = "orders",
        groupId = "group1"
    )
    public void consume(
            String message) {

        System.out.println(message);
    }
}
```

Execution:

```text
Application Starts
      ↓
Kafka Listener Registered
      ↓
Consumer Subscribed
      ↓
Messages Consumed
```

Result:

```text
Asynchronous Processing
Event Driven Architecture
Scalable Consumers
Loose Coupling
```

`@EnableKafka` is a Spring Kafka annotation that activates Kafka listener infrastructure, enabling Spring to detect `@KafkaListener` methods, create consumer containers, subscribe to Kafka topics, and process messages automatically.