
# What is Event Driven Architecture?

## Definition

Event Driven Architecture (EDA) is a software architecture pattern in which systems communicate through events.

Instead of one application directly calling another application, applications produce events, and other applications react to those events.

---

## Simple Definition

An event is simply:

> "Something important that happened in the system."

Examples:

```text
Order Created
Payment Completed
Customer Registered
Product Added
Order Shipped
Money Debited
```

In EDA, systems don't ask for information continuously.

Instead, they react when events occur.

---

## Traditional Request-Response Model

```text
Order Service
      |
      | REST API Call
      |
      V
Payment Service
```

Order Service must know:

- Where Payment Service is running
- API Endpoint
- Authentication
- Availability

This creates tight coupling.

---

## Event-Driven Model

```text
Order Service
      |
      V
OrderCreated Event
      |
    Kafka
      |
+-----+-----+
|     |     |
Payment
Inventory
Notification
```

Services become independent.

---

## Core Principle

Instead of:

```java
paymentService.processPayment();
```

We publish an event:

```java
OrderCreatedEvent
```

Interested services automatically consume it.

---

## Characteristics of EDA

### Loose Coupling

Services don't know about each other.

---

### Scalability

Consumers can scale independently.

---

### Asynchronous Communication

No waiting for responses.

---

### Fault Tolerance

Consumers can recover and process events later.

---

### Real-Time Processing

Events are processed as soon as they occur.

---

# Components of EDA

A complete Event Driven Architecture consists of multiple components.

---

## High-Level Architecture

```text
Producer
   |
   V
 Event
   |
Broker (Kafka)
   |
   V
Consumers
```

---

## Main Components

### Event

Something that happened.

---

### Producer

Generates events.

---

### Event Broker

Stores and distributes events.

---

### Consumer

Processes events.

---

### Event Stream

Continuous flow of events.

---

### Event Store

Stores events permanently.

---

### Event Processor

Processes events and generates outcomes.

---

# Event

---

## Definition

An event is a record of something that happened.

Example:

```json
{
  "orderId": 101,
  "customerId": 2001,
  "status": "CREATED"
}
```

---

## Event Structure

Most events contain:

```text
Event ID
Timestamp
Event Type
Payload
Metadata
```

Example:

```json
{
  "eventId":"E1001",
  "eventType":"OrderCreated",
  "timestamp":"2026-01-01T10:00:00",
  "payload":{
      "orderId":101
  }
}
```

---

# Event Producers

---

## Definition

Producers are applications that generate events.

---

## Example

Customer places order.

```text
Customer
    |
Order Service
    |
OrderCreated Event
```

Order Service becomes the producer.

---

## Producer Responsibilities

### Create Events

```java
OrderCreatedEvent event = new OrderCreatedEvent();
```

---

### Publish Events

```java
producer.send(event);
```

---

### Ensure Reliability

Producer must guarantee event delivery.

---

## Examples of Producers

- Order Service
- Payment Service
- Banking System
- ATM Machine
- Mobile App
- IoT Sensors
- Website Click Tracking

---

# Event Consumers

---

## Definition

Consumers subscribe to events and react accordingly.

---

## Example

```text
OrderCreated Event
        |
      Kafka
        |
+-------+-------+
|       |       |
Payment Inventory Notification
```

Each service is a consumer.

---

## Consumer Responsibilities

### Read Events

```java
consumer.poll();
```

---

### Process Events

```java
validate();
save();
update();
```

---

### Trigger Business Actions

Examples:

- Send Email
- Update Inventory
- Create Invoice

---

## Multiple Consumers

One event can have many consumers.

```text
Order Created
      |
    Kafka
      |
+-----+-----+-----+
|     |     |     |
Payment Billing Analytics
```

This is one of EDA's biggest advantages.

---

# Event Streams

---

## Definition

An event stream is a continuous sequence of events flowing through the system.

---

## Example

```text
Event 1
Event 2
Event 3
Event 4
Event 5
...
```

Events never stop.

---

## Real Example

Amazon Orders:

```text
Order Created
Payment Completed
Order Packed
Order Shipped
Order Delivered
```

Continuous stream of business events.

---

## Kafka Event Stream

```text
Producer
    |
    V
Kafka Topic
    |
Consumer
```

Kafka stores events in order.

---

## Why Event Streams Matter

Allows:

- Real-Time Analytics
- Monitoring
- Fraud Detection
- Recommendation Systems

---

# Event Processing

---

## Definition

Processing events means performing business actions when events arrive.

---

## Example

Event:

```json
{
 "eventType":"OrderCreated"
}
```

Processing:

```text
Validate Order
Reserve Inventory
Generate Invoice
Send Email
```

---

## Types of Event Processing

### Simple Event Processing

One event triggers one action.

```text
Order Created
      |
Send Email
```

---

### Complex Event Processing (CEP)

Multiple events trigger actions.

```text
Login Event
+
Payment Event
+
Location Change
      |
Fraud Alert
```

---

### Stream Processing

Continuous event analysis.

```text
Events
  |
Analyze
  |
Dashboard
```

---

# Synchronous vs Asynchronous Communication

---

# Synchronous Communication

---

## Definition

Caller waits for response.

---

## Example

REST API

```text
Order Service
      |
      V
Payment Service
      |
 Response
      |
Order Service Continues
```

---

## Java Example

```java
paymentService.processPayment();
```

Order Service waits.

---

## Advantages

- Simple
- Easy to Understand

---

## Disadvantages

- Tight Coupling
- Slower
- Cascading Failures

---

# Asynchronous Communication

---

## Definition

Caller does not wait.

---

## Example

```text
Order Service
      |
Publish Event
      |
Continue Working
```

Consumer processes later.

---

## Kafka Example

```text
Order Service
      |
OrderCreated Event
      |
Kafka
      |
Payment Service
```

---

## Advantages

- Loose Coupling
- Scalability
- High Performance
- Fault Tolerance

---

## Disadvantages

- More Complex
- Eventual Consistency

---

# Comparison

| Feature | Synchronous | Asynchronous |
|----------|------------|------------|
| Wait for Response | Yes | No |
| Coupling | Tight | Loose |
| Performance | Lower | Higher |
| Scalability | Limited | Excellent |
| Reliability | Lower | Higher |
| Complexity | Lower | Higher |

---

# Event Sourcing

---

## Definition

Event Sourcing is a design pattern where every change in application state is stored as an event.

Instead of storing current state:

```text
Account Balance = 5000
```

Store all events:

```text
Account Created
Money Deposited 1000
Money Deposited 2000
Money Deposited 2000
```

Current balance can be reconstructed.

---

## Traditional Database

```text
Account
-------
Balance = 5000
```

Only latest state exists.

---

## Event Sourcing

```text
Event 1
Event 2
Event 3
Event 4
```

Entire history exists.

---

## Banking Example

```text
Account Opened
Deposit 5000
Withdraw 1000
Deposit 3000
Withdraw 500
```

Balance calculated from events.

---

## Benefits

### Complete Audit Trail

Everything is recorded.

---

### Replay Capability

System state can be rebuilt.

---

### Debugging

Past events can be analyzed.

---

### Historical Analytics

Analyze trends over time.

---

## Challenges

- Storage Growth
- Event Versioning
- Complexity

---

# CQRS with Kafka

---

# What is CQRS?

CQRS stands for:

```text
Command Query Responsibility Segregation
```

It separates:

```text
Write Operations (Commands)
```

from

```text
Read Operations (Queries)
```

---

## Traditional System

```text
Application
      |
Single Database
```

Same model handles:

- Reads
- Writes

---

## CQRS Architecture

```text
Commands
    |
Write Model
    |
Kafka
    |
Read Model
    |
Queries
```

---

## Example

### Command Side

```text
Create Order
Update Order
Delete Order
```

Writes data.

---

### Query Side

```text
Get Orders
Search Orders
Reports
```

Reads data.

---

## Kafka's Role

Kafka transports events from write side to read side.

```text
Write Database
      |
OrderCreated Event
      |
Kafka
      |
Read Database
```

---

## Benefits

### Independent Scaling

Read and write systems scale separately.

---

### Better Performance

Optimized read model.

---

### Event History

Events remain available.

---

### Microservice Friendly

Perfect for distributed systems.

---

# Real-World EDA Examples

---

# Example 1: E-Commerce Platform

---

## Event Flow

```text
Customer Places Order
          |
OrderCreated Event
          |
        Kafka
          |
+---------+---------+---------+
|         |         |         |
Payment Inventory Notification
```

---

# Example 2: Banking System

---

## Event Flow

```text
Money Transfer
      |
TransferCompleted Event
      |
Kafka
      |
+-----+-----+-----+
|     |     |     |
Audit Fraud Analytics
```

---

# Example 3: Uber

---

## Events

```text
Ride Requested
Driver Assigned
Ride Started
Ride Completed
Payment Completed
```

Each event triggers downstream services.

---

# Example 4: Netflix

---

## Events

```text
User Started Video
User Paused Video
User Stopped Video
```

Used for:

- Recommendations
- Analytics
- Monitoring

---

# Example 5: Amazon

---

## Events

```text
Product Viewed
Product Added To Cart
Order Created
Payment Completed
```

Consumers:

- Recommendation Engine
- Analytics Engine
- Inventory System

---

# Example 6: IoT Platform

---

## Event Flow

```text
Temperature Sensor
Humidity Sensor
Pressure Sensor
        |
      Kafka
        |
Analytics Dashboard
```

Millions of events per second.

---

# Interview Questions

## What is Event Driven Architecture?

An architecture where applications communicate by producing and consuming events rather than directly invoking each other.

---

## What is an Event?

A record of something that happened in a system.

Example:

```text
Order Created
Payment Completed
User Registered
```

---

## Why is Kafka ideal for EDA?

Because Kafka provides:

- Event Storage
- Event Streaming
- Scalability
- Fault Tolerance
- Replay Capability

---

## Difference Between Event Sourcing and CQRS?

### Event Sourcing

Stores all state changes as events.

### CQRS

Separates read and write operations.

Often used together.

---

## Is Event Driven Architecture Synchronous?

No.

EDA is primarily asynchronous.

---

# Summary

Event Driven Architecture (EDA) is a software architecture pattern where applications communicate through events.

Key Concepts:

- Events
- Producers
- Consumers
- Event Streams
- Event Processing

EDA provides:

- Loose Coupling
- Scalability
- Fault Tolerance
- Real-Time Processing

Advanced patterns commonly used with Kafka:

- Event Sourcing
- CQRS
- Stream Processing

This architecture forms the foundation of modern Microservices, Real-Time Analytics, Banking Systems, E-Commerce Platforms, IoT Systems, and Large-Scale Distributed Applications.