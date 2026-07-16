
## Definition

`@KafkaHandler` is a Spring Kafka annotation used inside a class annotated with `@KafkaListener` to handle different message payload types using separate methods.

It tells Spring:

> Route incoming Kafka messages to the appropriate method based on the message type.

Package:

```java
import org.springframework.kafka.annotation.KafkaHandler;
```

---

# Why Do We Need @KafkaHandler?

Suppose a Kafka topic contains:

```text
OrderCreatedEvent
PaymentEvent
ShipmentEvent
```

---

Without `@KafkaHandler`:

```java
@KafkaListener(topics = "events")
public void consume(Object event) {

}
```

---

You would need:

```java
if(event instanceof OrderCreatedEvent)

if(event instanceof PaymentEvent)

if(event instanceof ShipmentEvent)
```

---

Messy code.

---

# Solution

```java
@KafkaHandler
```

---

Spring automatically chooses the correct method.

---

# Real-Life Analogy

Imagine a customer service desk.

---

Customers arrive for:

```text
Billing
Technical Support
Returns
```

---

Instead of:

```text
One Employee Handling Everything
```

---

You have:

```text
Billing Desk
Support Desk
Returns Desk
```

---

Each request goes to the correct desk.

---

Exactly what `@KafkaHandler` does.

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

# Basic Structure

```java
@KafkaListener(
    topics = "events"
)
public class EventConsumer {

    @KafkaHandler
    public void handle(
            OrderCreatedEvent event) {

    }

    @KafkaHandler
    public void handle(
            PaymentEvent event) {

    }
}
```

---

Spring decides:

```text
OrderCreatedEvent
        ↓
Order Handler

PaymentEvent
        ↓
Payment Handler
```

---

# Why Can't We Use @KafkaListener On Every Method?

You can.

Example:

```java
@KafkaListener(
    topics="orders"
)
public void consumeOrder(
    Order order
)
```

---

```java
@KafkaListener(
    topics="payments"
)
public void consumePayment(
    Payment payment
)
```

---

But if:

```text
Same Topic
Contains Multiple Event Types
```

---

`@KafkaHandler` becomes useful.

---

# Basic Example

Events:

```java
public class OrderCreatedEvent {

}
```

---

```java
public class PaymentEvent {

}
```

---

Consumer:

```java
@KafkaListener(
    topics = "events"
)
public class EventConsumer {

    @KafkaHandler
    public void handle(
        OrderCreatedEvent event) {

        System.out.println(
           "Order Event"
        );
    }

    @KafkaHandler
    public void handle(
        PaymentEvent event) {

        System.out.println(
           "Payment Event"
        );
    }
}
```

---

Execution:

Message:

```json
{
  "type":"OrderCreatedEvent"
}
```

---

Spring calls:

```java
handle(OrderCreatedEvent)
```

---

Message:

```json
{
  "type":"PaymentEvent"
}
```

---

Spring calls:

```java
handle(PaymentEvent)
```

---

# Internal Workflow

```text
Message Received
       ↓
Deserialize Payload
       ↓
Determine Java Type
       ↓
Find Matching @KafkaHandler
       ↓
Invoke Method
```

---

# Architecture

```text
Kafka Topic
      │
      ▼

@KafkaListener Class

      │
      ├────────► @KafkaHandler(Order)

      │
      ├────────► @KafkaHandler(Payment)

      │
      └────────► @KafkaHandler(Shipment)
```

---

# Example With Multiple Event Types

```java
@KafkaListener(
    topics = "events"
)
public class EventConsumer {

    @KafkaHandler
    public void processOrder(
        OrderCreatedEvent event
    ) {

    }

    @KafkaHandler
    public void processPayment(
        PaymentEvent event
    ) {

    }

    @KafkaHandler
    public void processShipment(
        ShipmentEvent event
    ) {

    }
}
```

---

Spring automatically routes:

```text
Order → processOrder()

Payment → processPayment()

Shipment → processShipment()
```

---

# Default Handler

Very useful.

---

Suppose:

```text
Unknown Event Arrives
```

---

Add:

```java
@KafkaHandler(
    isDefault = true
)
public void defaultHandler(
        Object event) {

}
```

---

Example:

```java
@KafkaListener(
    topics="events"
)
public class EventConsumer {

    @KafkaHandler
    public void order(
        OrderCreatedEvent event
    ) {

    }

    @KafkaHandler(
        isDefault = true
    )
    public void unknown(
            Object event) {

        System.out.println(
          "Unknown Event"
        );
    }
}
```

---

Execution:

```text
Known Event
      ↓
Specific Handler

Unknown Event
      ↓
Default Handler
```

---

# Real Production Example

Topic:

```text
ecommerce-events
```

Contains:

```text
OrderCreated

PaymentCompleted

ShipmentCreated

RefundProcessed
```

---

Consumer:

```java
@KafkaListener(
   topics="ecommerce-events"
)
public class EventConsumer {

    @KafkaHandler
    public void order(
      OrderCreatedEvent e
    ) {}

    @KafkaHandler
    public void payment(
      PaymentCompletedEvent e
    ) {}

    @KafkaHandler
    public void shipment(
      ShipmentCreatedEvent e
    ) {}

    @KafkaHandler
    public void refund(
      RefundProcessedEvent e
    ) {}
}
```

---

Cleaner than:

```java
if(event instanceof ...)
```

---

# How Spring Chooses Handler

Example:

```java
@KafkaHandler
public void process(
     PaymentEvent event
)
```

---

Incoming Payload:

```java
PaymentEvent
```

---

Spring:

```text
Find Matching Parameter Type
```

---

Then:

```java
process(PaymentEvent)
```

is invoked.

---

# JSON Deserialization

Producer sends:

```json
{
   "id":1,
   "amount":500
}
```

---

Spring converts:

```text
JSON
   ↓
PaymentEvent
```

---

Then routes to:

```java
@KafkaHandler
```

---

# @KafkaHandler vs @KafkaListener

Interview Favorite.

---

## @KafkaListener

Purpose:

```text
Subscribe To Topic
```

---

Example:

```java
@KafkaListener(
    topics="events"
)
```

---

## @KafkaHandler

Purpose:

```text
Handle Specific Payload Type
```

---

Example:

```java
@KafkaHandler
public void handle(
      OrderEvent e
)
```

---

Comparison:

| Annotation | Purpose |
|------------|----------|
| `@KafkaListener` | Topic Subscription |
| `@KafkaHandler` | Payload Routing |

---

# When To Use @KafkaHandler?

Use when:

```text
One Topic
Contains Multiple Event Types
```

---

Examples:

```text
Event Sourcing
CQRS
Event Driven Architecture
```

---

# Common Use Cases

## E-Commerce Events

```text
Orders
Payments
Refunds
```

---

## Banking Events

```text
Transfer
Deposit
Withdrawal
```

---

## Logistics Events

```text
Shipment
Tracking
Delivery
```

---

## Audit Events

```text
Login
Logout
Password Change
```

---

# Common Mistakes

## Mistake 1

Using @KafkaHandler Without @KafkaListener

Wrong:

```java
@KafkaHandler
public void process(...)
```

---

Must be inside:

```java
@KafkaListener
class
```

---

# Mistake 2

Duplicate Handlers

Wrong:

```java
@KafkaHandler
process(OrderEvent)
```

---

```java
@KafkaHandler
another(OrderEvent)
```

---

Spring won't know which one to use.

---

# Mistake 3

No Default Handler

Unknown events may fail.

---

# Common Interview Questions

## What is @KafkaHandler?

Routes messages to methods based on payload type.

---

## Can It Work Alone?

No.

Requires:

```java
@KafkaListener
```

---

## Why Use It?

To handle multiple event types cleanly.

---

## How Does Spring Choose Method?

By payload type.

---

## What Is isDefault=true?

Fallback handler.

---

# Enterprise Best Practice

Use:

```java
@KafkaHandler
```

when:

```text
Single Topic
Contains Multiple Event Types
```

---

Add:

```java
isDefault = true
```

for unknown messages.

---

Keep event classes:

```text
Serializable
Versioned
Backward Compatible
```

---

# Key Points To Remember

- `@KafkaHandler` handles different message types.
- Must be used inside a class annotated with `@KafkaListener`.
- Spring selects the handler based on payload type.
- Eliminates `instanceof` checks.
- Supports multiple event classes.
- Supports default handlers.
- Useful for event-driven systems.
- Frequently asked Kafka interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@KafkaListener` | Subscribe To Topic |
| `@KafkaHandler` | Route By Payload Type |
| `KafkaTemplate` | Send Messages |
| `@EnableKafka` | Enable Kafka Infrastructure |

---

# Interview Shortcut

```java
@KafkaListener(
    topics="events"
)
public class EventConsumer {

    @KafkaHandler
    public void handle(
        OrderEvent e
    ) {}

    @KafkaHandler
    public void handle(
        PaymentEvent e
    ) {}
}
```

Meaning:

```text
Listen To Events Topic

Determine Message Type

Call Matching Method

Automatically
```

---

# Real Production Scenario

Topic:

```text
ecommerce-events
```

Messages:

```text
OrderCreated
PaymentCompleted
ShipmentCreated
RefundProcessed
```

Consumer:

```java
@KafkaListener(
   topics="ecommerce-events"
)
public class EventConsumer {

    @KafkaHandler
    public void order(
        OrderCreatedEvent e
    ) {}

    @KafkaHandler
    public void payment(
        PaymentCompletedEvent e
    ) {}

    @KafkaHandler
    public void shipment(
        ShipmentCreatedEvent e
    ) {}
}
```

Result:

```text
Clean Code
Automatic Routing
No instanceof Checks
Better Maintainability
```

`@KafkaHandler` is a Spring Kafka annotation used within a `@KafkaListener` class to dispatch incoming Kafka messages to different methods based on the deserialized payload type, enabling clean handling of multiple event types from the same topic.