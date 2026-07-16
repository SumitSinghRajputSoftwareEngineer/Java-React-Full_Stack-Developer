
## Definition

`@RabbitListener` is a Spring AMQP annotation used to create a RabbitMQ consumer method that listens to one or more queues and automatically processes incoming messages.

It tells Spring:

> Subscribe this method to a RabbitMQ queue and invoke it whenever a message arrives.

Package:

```java
import org.springframework.amqp.rabbit.annotation.RabbitListener;
```

---

# Why Do We Need @RabbitListener?

Suppose:

```text
Order Service
```

sends messages to:

```text
order.queue
```

---

Without a consumer:

```text
Messages Stay In Queue
```

---

With:

```java
@RabbitListener(
    queues = "order.queue"
)
public void consume(
        String message) {

}
```

---

Whenever a message arrives:

```text
Method Executes Automatically
```

---

# Real-Life Analogy

Imagine:

```text
Mailbox
```

---

Someone drops letters into it.

---

Without checking:

```text
Letters Keep Accumulating
```

---

A mail collector checks continuously.

---

`@RabbitListener` acts as:

```text
Mail Collector
```

for RabbitMQ queues.

---

# Required Dependency

```xml
<dependency>
    <groupId>
        org.springframework.boot
    </groupId>
    <artifactId>
        spring-boot-starter-amqp
    </artifactId>
</dependency>
```

---

Enable RabbitMQ:

```java
@Configuration
@EnableRabbit
public class RabbitConfig {
}
```

---

# Basic Example

Producer:

```java
rabbitTemplate.convertAndSend(
    "order.exchange",
    "order.created",
    "Order Created"
);
```

---

Consumer:

```java
@Component
public class OrderConsumer {

    @RabbitListener(
        queues = "order.queue"
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
Producer
   ↓

Exchange
   ↓

Queue
   ↓

@RabbitListener
   ↓

Method Executes
```

---

# What Happens Internally?

Application Startup:

```text
@EnableRabbit
      ↓
Find @RabbitListener
      ↓
Create Listener Container
      ↓
Connect To RabbitMQ
      ↓
Subscribe To Queue
```

---

When a message arrives:

```text
Read Message
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

 Exchange
    │
    ▼

  Queue
    │
    ▼

@RabbitListener
```

---

# Simple String Example

Producer:

```java
rabbitTemplate.convertAndSend(
    "order.exchange",
    "order.created",
    "Laptop Ordered"
);
```

---

Consumer:

```java
@RabbitListener(
    queues = "order.queue"
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

# Listening To Multiple Queues

```java
@RabbitListener(
    queues = {
        "order.queue",
        "payment.queue"
    }
)
public void consume(
        String msg) {

}
```

---

Consumes from:

```text
order.queue
payment.queue
```

---

# JSON Message Example

Producer:

```java
Order order =
       new Order(1,"Laptop");

rabbitTemplate.convertAndSend(
    exchange,
    routingKey,
    order
);
```

---

Consumer:

```java
@RabbitListener(
    queues = "order.queue"
)
public void consume(
        Order order) {

}
```

---

Spring converts:

```text
JSON
 ↓
Java Object
```

automatically.

---

# Accessing Message Metadata

```java
@RabbitListener(
    queues = "order.queue"
)
public void consume(
      Message message) {

    System.out.println(
      message.getMessageProperties()
             .getConsumerQueue()
    );
}
```

---

Useful for:

```text
Debugging
Monitoring
Auditing
```

---

# Listening To Different Payload Types

Combine with:

```java
@RabbitHandler
```

---

Example:

```java
@RabbitListener(
    queues="events.queue"
)
public class EventConsumer {

    @RabbitHandler
    public void process(
        OrderEvent event
    ) {}

    @RabbitHandler
    public void process(
        PaymentEvent event
    ) {}
}
```

---

Spring automatically routes:

```text
OrderEvent
   ↓
Order Handler

PaymentEvent
   ↓
Payment Handler
```

---

# Concurrency Example

```java
@RabbitListener(
    queues = "order.queue",
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

# Manual Acknowledgment

```java
@RabbitListener(
    queues = "order.queue"
)
public void consume(
    Message message,
    Channel channel
) throws Exception {

    channel.basicAck(
        message.getMessageProperties()
               .getDeliveryTag(),
        false
    );
}
```

---

Meaning:

```text
Acknowledge
Only After Success
```

---

Useful for:

```text
Reliable Processing
```

---

# Queue Binding Example

```java
@RabbitListener(
    bindings = @QueueBinding(
        value = @Queue("order.queue"),

        exchange = @Exchange(
            "order.exchange"
        ),

        key = "order.created"
    )
)
public void consume(
        String msg) {

}
```

---

Automatically creates:

```text
Exchange
Queue
Binding
```

---

# Real Production Scenario

E-Commerce:

```text
Order Service
      ↓

RabbitMQ

      ↓

Inventory Service

Notification Service

Shipping Service
```

---

Consumers:

```java
@RabbitListener(
   queues="inventory.queue"
)
```

---

```java
@RabbitListener(
   queues="notification.queue"
)
```

---

```java
@RabbitListener(
   queues="shipping.queue"
)
```

---

Each service consumes messages independently.

---

# Internal Workflow

```text
Producer Sends
      ↓

Exchange Routes
      ↓

Queue Stores
      ↓

Rabbit Listener Reads
      ↓

Deserialize
      ↓

Invoke Method
      ↓

Acknowledge
```

---

# Common Attributes

## queues

```java
queues = "order.queue"
```

Queue name.

---

## concurrency

```java
concurrency = "3"
```

Consumer threads.

---

## id

```java
id = "orderConsumer"
```

Listener identifier.

---

## containerFactory

```java
containerFactory =
    "rabbitFactory"
```

Custom container.

---

# @RabbitListener vs RabbitTemplate

Interview Favorite.

---

## RabbitTemplate

Purpose:

```text
Producer
```

---

Example:

```java
rabbitTemplate.convertAndSend(...)
```

---

## @RabbitListener

Purpose:

```text
Consumer
```

---

Example:

```java
@RabbitListener(
   queues="order.queue"
)
```

---

Comparison:

| Component | Purpose |
|------------|----------|
| RabbitTemplate | Produce |
| @RabbitListener | Consume |

---

# @RabbitListener vs @KafkaListener

| Feature | RabbitListener | KafkaListener |
|----------|---------------|--------------|
| Broker | RabbitMQ | Kafka |
| Reads From | Queue | Topic |
| Routing | Exchange + Queue | Topic + Partition |
| Ack Mechanism | RabbitMQ Ack | Kafka Offset |

---

# Common Use Cases

## Order Processing

```text
Order Queue
```

---

## Payment Processing

```text
Payment Queue
```

---

## Email Notifications

```text
Email Queue
```

---

## Background Jobs

```text
Task Queue
```

---

## Microservices Messaging

```text
Asynchronous Communication
```

---

# Common Mistakes

## Mistake 1

Wrong Queue Name

```java
queues="orders"
```

instead of:

```java
queues="order.queue"
```

---

No messages received.

---

# Mistake 2

Missing Queue

Queue not created.

---

Consumer startup fails.

---

# Mistake 3

Not Handling Failures

Messages may be requeued repeatedly.

---

# Common Interview Questions

## What is @RabbitListener?

Creates RabbitMQ consumers.

---

## Who Calls The Method?

Spring AMQP listener container.

---

## Can It Listen To Multiple Queues?

Yes.

---

## What Is Concurrency?

Number of consumer threads.

---

## Can It Consume JSON?

Yes.

Automatically converts JSON to Java objects.

---

## Difference Between RabbitTemplate and @RabbitListener?

Producer vs Consumer.

---

# Enterprise Best Practice

Use dedicated consumer classes.

---

Configure:

```text
Dead Letter Queues
Retries
Manual Acks
Monitoring
```

---

Separate queues for:

```text
Orders
Payments
Notifications
```

---

# Key Points To Remember

- `@RabbitListener` creates RabbitMQ consumers.
- Listens to RabbitMQ queues.
- Processes messages asynchronously.
- Supports multiple queues.
- Supports JSON deserialization.
- Supports concurrency.
- Supports manual acknowledgment.
- Works with exchanges and bindings.
- Integrates with `@RabbitHandler`.
- Frequently asked messaging interview topic.

---

# Quick Comparison

| Component | Purpose |
|------------|----------|
| `@RabbitListener` | Consume Messages |
| `RabbitTemplate` | Send Messages |
| `@EnableRabbit` | Enable RabbitMQ Infrastructure |
| `@RabbitHandler` | Route By Payload Type |

---

# Interview Shortcut

```java
@RabbitListener(
    queues = "order.queue"
)
public void consume(
        String message) {

}
```

Meaning:

```text
Subscribe To Queue

Receive Messages

Process Messages

Automatically
```

---

# Real Production Example

```java
@Component
public class OrderConsumer {

    @RabbitListener(
        queues = "inventory.queue"
    )
    public void updateStock(
            Order order) {

        System.out.println(
           "Stock Updated"
        );
    }
}
```

Execution:

```text
Order Created
      ↓
RabbitMQ Queue
      ↓
Inventory Consumer
      ↓
Stock Updated
```

Result:

```text
Asynchronous Processing
Reliable Messaging
Loose Coupling
Scalable Consumers
```

`@RabbitListener` is a Spring AMQP annotation that declares a method as a RabbitMQ consumer, automatically subscribing it to one or more queues and invoking the method whenever messages are received from those queues.