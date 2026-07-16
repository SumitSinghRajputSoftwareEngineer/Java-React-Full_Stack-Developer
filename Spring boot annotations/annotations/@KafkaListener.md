
## Definition

`@KafkaListener` is a Spring Kafka annotation used to create a Kafka consumer method that listens to one or more Kafka topics and automatically processes incoming messages.

It tells Spring:

> Subscribe this method to a Kafka topic and invoke it whenever a new message arrives.

Package:

```java
import org.springframework.kafka.annotation.KafkaListener;
```

---

# Why Do We Need @KafkaListener?

Suppose:

```text
Order Service
```

publishes:

```text
Order Created
```

events into Kafka.

---

Without `@KafkaListener`:

```text
No Consumer
No Message Processing
```

---

With `@KafkaListener`:

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

Whenever a message arrives:

```text
Method Executes Automatically
```

---

# Real-Life Analogy

Think of:

```text
YouTube Subscription
```

---

You subscribe to:

```text
Spring Boot Channel
```

---

Whenever a new video is uploaded:

```text
You Receive Notification
```

---

Similarly:

```java
@KafkaListener(topics="orders")
```

means:

```text
Subscribe To Orders Topic
```

and get notified when messages arrive.

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

Enable Kafka:

```java
@Configuration
@EnableKafka
public class KafkaConfig {
}
```

---

# Basic Example

Producer:

```java
kafkaTemplate.send(
    "orders",
    "Order Created"
);
```

---

Consumer:

```java
@Component
public class OrderConsumer {

    @KafkaListener(
        topics = "orders"
    )
    public void consume(
            String message) {

        System.out.println(message);
    }
}
```

---

Execution:

```text
Producer Sends Message
         ↓
Kafka Topic
         ↓
@KafkaListener
         ↓
Method Executes
```

---

# What Happens Internally?

Application Startup:

```text
@EnableKafka
        ↓
Find @KafkaListener
        ↓
Create Consumer Container
        ↓
Subscribe To Topic
        ↓
Start Polling
```

---

When message arrives:

```text
Poll Message
       ↓
Deserialize
       ↓
Invoke Method
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

# Simple String Example

Producer:

```java
kafkaTemplate.send(
    "orders",
    "Laptop Ordered"
);
```

---

Consumer:

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

Output:

```text
Laptop Ordered
```

---

# Listening To Multiple Topics

```java
@KafkaListener(
    topics = {
        "orders",
        "payments",
        "shipments"
    }
)
public void consume(
        String msg) {

}
```

---

Consumer subscribes to:

```text
orders
payments
shipments
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
Consumer Belongs To Group1
```

---

Kafka guarantees:

```text
One Message
Processed Once
Per Group
```

---

# Multiple Consumers Same Group

Consumer 1:

```java
@KafkaListener(
    topics="orders",
    groupId="group1"
)
```

---

Consumer 2:

```java
@KafkaListener(
    topics="orders",
    groupId="group1"
)
```

---

Messages distributed:

```text
Msg1 → Consumer1

Msg2 → Consumer2

Msg3 → Consumer1
```

---

Load balancing occurs.

---

# Different Consumer Groups

Consumer A:

```java
group1
```

---

Consumer B:

```java
group2
```

---

Message:

```text
Order Created
```

---

Result:

```text
group1 gets copy

group2 gets copy
```

---

Useful for:

```text
Inventory Service
Notification Service
Shipping Service
```

---

# Reading JSON Objects

Producer:

```java
Order order =
      new Order(1,"Laptop");

kafkaTemplate.send(
    "orders",
    order
);
```

---

Consumer:

```java
@KafkaListener(
    topics = "orders"
)
public void consume(
        Order order) {

    System.out.println(
        order.getName()
    );
}
```

---

Spring converts:

```text
JSON → Java Object
```

automatically.

---

# Accessing Metadata

```java
@KafkaListener(
    topics = "orders"
)
public void consume(
    ConsumerRecord<
        String,
        String
    > record) {

    System.out.println(
        record.topic()
    );

    System.out.println(
        record.partition()
    );

    System.out.println(
        record.offset()
    );
}
```

---

Useful for:

```text
Debugging
Auditing
Monitoring
```

---

# Listening To Specific Partition

```java
@KafkaListener(
    topicPartitions = {

        @TopicPartition(
            topic = "orders",
            partitions = {"0"}
        )
    }
)
```

---

Only reads:

```text
Partition 0
```

---

# Concurrent Consumers

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

# Batch Consumption

```java
@KafkaListener(
    topics = "orders"
)
public void consume(
        List<String> messages) {

}
```

---

Receives:

```text
Multiple Messages
At Once
```

---

Improves throughput.

---

# Manual Acknowledgment

```java
@KafkaListener(
    topics="orders"
)
public void consume(
    String msg,
    Acknowledgment ack
) {

    process(msg);

    ack.acknowledge();
}
```

---

Meaning:

```text
Commit Offset
Only After Success
```

---

Useful for:

```text
Reliable Processing
```

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

Read From Beginning
```

---

# Real Production Scenario

Order Created Event:

```text
Order Service
      ↓
Kafka Topic
      ↓
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

Each service processes independently.

---

# Internal Polling Mechanism

Kafka Consumer:

```text
Poll Broker
      ↓
Fetch Messages
      ↓
Deserialize
      ↓
Call Listener Method
      ↓
Commit Offset
```

---

# Common Attributes

## topics

```java
topics = "orders"
```

Topic name.

---

## groupId

```java
groupId = "group1"
```

Consumer group.

---

## concurrency

```java
concurrency = "3"
```

Consumer threads.

---

## containerFactory

```java
containerFactory =
   "kafkaFactory"
```

Custom listener factory.

---

# @KafkaListener vs KafkaTemplate

Interview Favorite.

---

## KafkaTemplate

Purpose:

```text
Producer
```

---

Sends messages.

---

Example:

```java
kafkaTemplate.send(...)
```

---

## @KafkaListener

Purpose:

```text
Consumer
```

---

Receives messages.

---

Comparison:

| Component | Purpose |
|------------|----------|
| KafkaTemplate | Produce |
| @KafkaListener | Consume |

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

## Notification Systems

```text
Email/SMS Events
```

---

## Audit Logs

```text
Audit Topic
```

---

## Event-Driven Microservices

```text
Loose Coupling
```

---

# Common Mistakes

## Mistake 1

Wrong Topic Name

```java
topics="order"
```

instead of:

```java
topics="orders"
```

---

No messages received.

---

# Mistake 2

Missing Group ID

Can cause unexpected behavior.

---

# Mistake 3

Not Handling Failures

```java
throw RuntimeException
```

may repeatedly reprocess messages.

---

# Common Interview Questions

## What is @KafkaListener?

Creates Kafka consumer methods.

---

## Who Calls The Method?

Spring Kafka consumer container.

---

## Can It Listen To Multiple Topics?

Yes.

---

## What Is groupId?

Consumer group identifier.

---

## What Happens If Two Consumers Have Same Group?

Messages are distributed.

---

## What Happens If Consumers Have Different Groups?

Each group gets a copy.

---

## What Is concurrency?

Number of consumer threads.

---

# Enterprise Best Practice

Use:

```java
@KafkaListener
```

inside dedicated consumer classes.

---

Handle:

```text
Retries
Dead Letter Topics
Manual Acks
Error Handling
```

---

Monitor:

```text
Lag
Offsets
Partitions
```

---

# Key Points To Remember

- `@KafkaListener` creates Kafka consumers.
- Automatically subscribes to Kafka topics.
- Processes messages asynchronously.
- Supports consumer groups.
- Supports multiple topics.
- Supports partition-based consumption.
- Supports batch processing.
- Supports manual acknowledgments.
- Works with JSON deserialization.
- Frequently asked Kafka interview topic.

---

# Quick Comparison

| Component | Purpose |
|------------|----------|
| `@KafkaListener` | Consume Messages |
| `KafkaTemplate` | Produce Messages |
| `@EnableKafka` | Enable Kafka Infrastructure |
| `@KafkaHandler` | Handle Multiple Payload Types |
| `@SendTo` | Send Reply Message |

---

# Interview Shortcut

```java
@KafkaListener(
    topics = "orders",
    groupId = "group1"
)
public void consume(
        String message) {

}
```

Meaning:

```text
Subscribe To Orders Topic

Join Consumer Group1

Receive Messages

Execute Method Automatically
```

---

# Real Production Example

```java
@Component
public class OrderConsumer {

    @KafkaListener(
        topics = "orders",
        groupId = "inventory-group"
    )
    public void processOrder(
            Order order) {

        System.out.println(
            "Inventory Updated"
        );
    }
}
```

Execution:

```text
Order Created
      ↓
Kafka Topic
      ↓
Inventory Consumer
      ↓
Stock Updated
```

Result:

```text
Asynchronous Processing
Scalable Consumers
Loose Coupling
High Throughput
```

`@KafkaListener` is a Spring Kafka annotation that declares a method as a Kafka consumer, automatically subscribing it to one or more topics and invoking the method whenever messages are received from Kafka.