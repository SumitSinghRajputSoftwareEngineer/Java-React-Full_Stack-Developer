
Kafka is not just a messaging platform.

It is the foundation for building:

```text
Event-Driven Systems

Microservices

Distributed Systems

Real-Time Data Pipelines

Highly Scalable Applications
```

To solve common distributed system challenges, several architectural patterns are commonly implemented using Kafka.

---

# Why Design Patterns in Kafka?

Distributed systems face challenges such as:

```text
Data Consistency

Scalability

Service Coordination

Fault Tolerance

Reliable Messaging
```

Kafka provides the building blocks to implement proven architectural patterns.

---

# Most Important Kafka Design Patterns

```text
Event Sourcing

CQRS

Outbox Pattern

Saga Pattern

CDC Pattern

Request-Reply Pattern
```

---

# Design Pattern Overview

| Pattern | Purpose |
|----------|----------|
| Event Sourcing | Store Changes As Events |
| CQRS | Separate Reads And Writes |
| Outbox Pattern | Reliable Event Publishing |
| Saga Pattern | Distributed Transactions |
| CDC Pattern | Database Change Streaming |
| Request-Reply Pattern | Synchronous Communication Over Kafka |

---

# Event Sourcing

Event Sourcing is one of the most popular Kafka design patterns.

---

# What is Event Sourcing?

Instead of storing the current state of an object, store all changes (events) that happened to that object.

---

Traditional Approach:

```text
Current State Stored
```

---

Event Sourcing:

```text
All Events Stored
```

---

# Traditional Database Example

Order:

```text
Order ID = 101

Status = DELIVERED
```

---

Only current state exists.

History may be lost.

---

# Event Sourcing Example

Store every change:

```text
OrderCreated

OrderPaid

OrderPacked

OrderShipped

OrderDelivered
```

---

Current state is reconstructed from events.

---

# Event Sourcing Architecture

```text
Application
      ↓
Kafka Topic
      ↓
Event Store
      ↓
Consumers
```

---

# Event Flow

```text
Order Created
      ↓
Order Topic
      ↓
Kafka
      ↓
Store Event
```

---

# Rebuilding State

Replay all events:

```text
OrderCreated
      ↓
OrderPaid
      ↓
OrderShipped
      ↓
OrderDelivered
```

---

Final State:

```text
DELIVERED
```

---

# Benefits

```text
Complete Audit Trail

Easy Debugging

Replay Capability

Event History Preservation
```

---

# Challenges

```text
Storage Growth

Complex Event Evolution

Replay Cost
```

---

# Real-World Example

Banking System:

```text
Money Deposited

Money Withdrawn

Interest Added
```

---

Every transaction becomes an immutable event.

---

# CQRS

CQRS is commonly used with Event Sourcing.

---

# What is CQRS?

CQRS stands for:

```text
Command Query Responsibility Segregation
```

---

It separates:

```text
Write Operations

and

Read Operations
```

---

# Traditional Architecture

```text
Application
      ↓
Database
```

Same model handles:

```text
Reads

Writes
```

---

# CQRS Architecture

```text
Commands
    ↓
Write Model
    ↓
Kafka
    ↓
Read Model
    ↓
Queries
```

---

# Commands

Commands modify data.

Examples:

```text
Create Order

Update Order

Cancel Order
```

---

# Queries

Queries read data.

Examples:

```text
Get Order

List Orders

Search Orders
```

---

# CQRS Flow

```text
Command
    ↓
Write Database
    ↓
Kafka Event
    ↓
Read Database Updated
```

---

# Example

Create Order:

```text
Order Service
      ↓
OrderCreated Event
      ↓
Kafka
      ↓
Read Database Updated
```

---

Read Request:

```text
Customer Dashboard
      ↓
Read Database
```

---

# Benefits

```text
Scalable Reads

Scalable Writes

Independent Optimization

Better Performance
```

---

# Challenges

```text
Eventual Consistency

Complex Architecture
```

---

# Event Sourcing + CQRS

Very common combination:

```text
Commands
    ↓
Events
    ↓
Kafka
    ↓
Read Models
```

---

# Outbox Pattern

One of the most important microservice patterns.

---

# Problem

Suppose:

```text
Save Order
      ↓
Publish Kafka Event
```

---

What if:

```text
Database Success

Kafka Publish Failure
```

---

Result:

```text
Data Inconsistency
```

---

# What is Outbox Pattern?

Store events in an Outbox table within the same database transaction.

---

# Outbox Architecture

```text
Application
      ↓
Database Transaction
      ↓
Business Table
      +
Outbox Table
```

---

# Flow

```text
Save Order
      ↓
Save Event In Outbox
      ↓
Commit Transaction
```

---

After commit:

```text
CDC / Outbox Processor
      ↓
Kafka
```

---

# Example

Database:

```text
Orders Table

Outbox Table
```

---

Transaction:

```text
Insert Order

Insert Event
```

---

Both succeed together.

---

# Outbox Event Example

```json
{
  "eventType":"OrderCreated",
  "orderId":101
}
```

---

# Benefits

```text
No Lost Events

Reliable Messaging

Atomic Operations
```

---

# Challenges

```text
Additional Table

Cleanup Needed
```

---

# Real-World Example

E-Commerce:

```text
Order Saved
      ↓
Outbox Event Stored
      ↓
Kafka Event Published
```

---

# Saga Pattern

Used for distributed transactions.

---

# Problem

Distributed transactions are difficult.

Example:

```text
Order Service

Payment Service

Inventory Service

Shipping Service
```

---

How do we ensure consistency?

---

# What is Saga Pattern?

A Saga is a sequence of local transactions coordinated through events.

---

# Traditional Distributed Transaction

```text
Two-Phase Commit (2PC)
```

---

Problems:

```text
Slow

Complex

Not Scalable
```

---

# Saga Solution

Each service:

```text
Completes Local Transaction

Publishes Event
```

---

# Saga Architecture

```text
Order Service
      ↓
Payment Service
      ↓
Inventory Service
      ↓
Shipping Service
```

---

# Successful Saga

```text
Order Created
      ↓
Payment Success
      ↓
Inventory Reserved
      ↓
Shipment Created
```

---

# Failed Saga

```text
Order Created
      ↓
Payment Success
      ↓
Inventory Failure
```

---

Compensation Events:

```text
Refund Payment

Cancel Order
```

---

# Types of Saga

---

## Choreography Saga

Services communicate through events.

---

Architecture:

```text
Service A
     ↓
Kafka Event
     ↓
Service B
```

---

Benefits:

```text
Decentralized
```

---

# Orchestration Saga

Central coordinator controls workflow.

---

Architecture:

```text
Saga Orchestrator
        ↓
Services
```

---

Benefits:

```text
Better Control
```

---

# Benefits

```text
Scalable

Fault Tolerant

Microservice Friendly
```

---

# CDC Pattern

CDC is heavily used with Kafka.

---

# What is CDC?

CDC stands for:

```text
Change Data Capture
```

---

CDC captures database changes and publishes them as events.

---

# Why CDC?

Avoid polling databases.

---

Instead:

```text
Database Change
      ↓
Kafka Event
```

---

# CDC Architecture

```text
Database
      ↓
CDC Tool
      ↓
Kafka
```

---

# Common CDC Tools

```text
Debezium

GoldenGate

Maxwell
```

---

# Example

Database:

```text
INSERT INTO orders
```

---

CDC Event:

```json
{
  "op":"c",
  "orderId":101
}
```

---

Published to Kafka.

---

# CDC Flow

```text
Database Update
       ↓
Transaction Log
       ↓
CDC Tool
       ↓
Kafka Topic
```

---

# Benefits

```text
Real-Time Events

No Polling

Low Latency
```

---

# Challenges

```text
Schema Changes

Complex Setup
```

---

# Real-World Example

Customer Database:

```text
Customer Updated
       ↓
Kafka Event
       ↓
Analytics Service
```

---

# Request-Reply Pattern

Kafka is naturally asynchronous.

Sometimes applications require synchronous communication.

---

# What is Request-Reply Pattern?

A service sends a request and waits for a corresponding response.

---

# Traditional Kafka

```text
Producer
      ↓
Topic
      ↓
Consumer
```

---

No direct response.

---

# Request-Reply Architecture

```text
Client
   ↓
Request Topic
   ↓
Service
   ↓
Reply Topic
   ↓
Client
```

---

# Request Flow

```text
Send Request
      ↓
Process
      ↓
Send Reply
```

---

# Example

Request:

```json
{
  "customerId":101
}
```

---

Reply:

```json
{
  "customerName":"John"
}
```

---

# Correlation ID

Used to match requests and responses.

---

Example:

```text
Request ID = abc123
```

---

Response:

```text
Request ID = abc123
```

---

# Request-Reply Flow

```text
Request Topic
       ↓
Consumer
       ↓
Process
       ↓
Reply Topic
```

---

# Spring Kafka Support

Spring provides:

```java
ReplyingKafkaTemplate
```

---

Example:

```java
ReplyingKafkaTemplate
```

supports:

```text
Request

Response

Correlation
```

---

# Benefits

```text
Simple

Reliable

Kafka Native
```

---

# Challenges

```text
Higher Latency

Temporary Blocking

Additional Complexity
```

---

# Pattern Comparison

| Pattern | Main Purpose |
|----------|-------------|
| Event Sourcing | Store Complete History |
| CQRS | Separate Reads/Writes |
| Outbox Pattern | Reliable Event Publishing |
| Saga Pattern | Distributed Transactions |
| CDC Pattern | Database Change Streaming |
| Request-Reply | Synchronous Communication |

---

# How These Patterns Work Together

Modern Microservice Architecture:

```text
Order Service
      ↓
Outbox Pattern
      ↓
CDC
      ↓
Kafka
      ↓
Saga Events
      ↓
Other Services
```

---

Combined Architecture:

```text
Event Sourcing
       ↓
Kafka
       ↓
CQRS Read Models
       ↓
Consumers
```

---

# Real-World E-Commerce Example

Order Placement:

```text
Create Order
      ↓
Outbox Pattern
      ↓
Kafka Event
      ↓
Saga Workflow
      ↓
Payment Service
      ↓
Inventory Service
      ↓
Shipping Service
```

---

Read Side:

```text
CQRS Query Database
```

---

Audit Trail:

```text
Event Sourcing
```

---

# Best Practices

## Event Sourcing

```text
Use Immutable Events

Version Event Schemas
```

---

## CQRS

```text
Separate Read Models

Accept Eventual Consistency
```

---

## Outbox Pattern

```text
Use Single Transaction

Automate Outbox Cleanup
```

---

## Saga Pattern

```text
Implement Compensation Logic

Monitor Workflow States
```

---

## CDC

```text
Use Debezium

Monitor CDC Lag
```

---

## Request-Reply

```text
Use Correlation IDs

Configure Timeouts
```

---

# Common Interview Questions

## What is Event Sourcing?

A pattern where all state changes are stored as immutable events instead of storing only the current state.

---

## What is CQRS?

Command Query Responsibility Segregation, which separates write operations from read operations.

---

## Why is CQRS often used with Event Sourcing?

Events generated by the write model can be used to build optimized read models.

---

## What problem does the Outbox Pattern solve?

It prevents inconsistency between database transactions and Kafka event publishing.

---

## What is the Saga Pattern?

A distributed transaction pattern that coordinates multiple services using events and compensating actions.

---

## What is the difference between Choreography and Orchestration Saga?

| Choreography | Orchestration |
|-------------|--------------|
| Event Driven | Central Coordinator |
| Decentralized | Centralized |
| Less Control | More Control |

---

## What is CDC?

Change Data Capture, a pattern that streams database changes into Kafka topics.

---

## What is Debezium?

A popular CDC platform that captures database changes and publishes them to Kafka.

---

## Why use Request-Reply in Kafka?

To implement synchronous communication over Kafka when a response is required.

---

## What is a Correlation ID?

A unique identifier used to match a response with its original request.

---

# Summary

Kafka enables several powerful architectural patterns used in modern distributed systems.

Core Patterns:

- Event Sourcing
- CQRS
- Outbox Pattern
- Saga Pattern
- CDC Pattern
- Request-Reply Pattern

Event Sourcing:

```text
Store Events

Replay History

Audit Trail
```

CQRS:

```text
Separate Reads

Separate Writes

Scalable Architecture
```

Outbox Pattern:

```text
Reliable Event Publishing

Atomic Transactions
```

Saga Pattern:

```text
Distributed Transactions

Compensation Logic

Microservice Coordination
```

CDC Pattern:

```text
Capture Database Changes

Publish Real-Time Events
```

Request-Reply Pattern:

```text
Synchronous Communication

Correlation IDs

Response Handling
```

These patterns form the foundation of modern event-driven microservice architectures and are widely used in enterprise systems built on Apache Kafka.