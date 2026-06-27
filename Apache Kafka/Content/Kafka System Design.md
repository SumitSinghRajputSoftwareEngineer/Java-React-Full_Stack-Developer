
Kafka is widely used as the backbone of large-scale distributed systems because it provides:

```text
High Throughput

Scalability

Fault Tolerance

Durability

Event Streaming

Loose Coupling
```

In system design interviews, Kafka is commonly used to build:

```text
Notification Systems

Order Processing Systems

Payment Systems

Audit Logging Systems

Real-Time Analytics Platforms
```

---

# Why Use Kafka in System Design?

Traditional architectures often use:

```text
Synchronous Communication
```

Problems:

```text
Tight Coupling

Slow Response Times

Poor Scalability

Single Point of Failure
```

---

Kafka enables:

```text
Asynchronous Communication

Event-Driven Architecture

Independent Scaling

Reliable Messaging
```

---

# Kafka-Based Architecture

```text
Producer
    ↓
Kafka
    ↓
Consumers
```

---

Benefits:

```text
Loose Coupling

High Availability

Scalability
```

---

# Designing Notification System

One of Kafka's most common use cases.

---

# Problem Statement

Build a notification system that sends:

```text
Email Notifications

SMS Notifications

Push Notifications

WhatsApp Notifications
```

for:

```text
Orders

Payments

Promotions

Security Alerts
```

---

# Requirements

## Functional Requirements

```text
Send Notifications

Support Multiple Channels

Retry Failed Notifications
```

---

## Non-Functional Requirements

```text
High Throughput

Fault Tolerance

Scalability

Reliability
```

---

# Traditional Architecture

```text
Application
      ↓
Email Service
      ↓
SMS Service
      ↓
Push Service
```

---

Problems:

```text
Tightly Coupled

Slow Response

Failure Propagation
```

---

# Kafka-Based Notification Architecture

```text
Order Service
       ↓
Notification Topic
       ↓
Kafka
       ↓
Email Consumer

SMS Consumer

Push Consumer
```

---

# Workflow

## Step 1

Order placed.

---

## Step 2

Producer publishes:

```json
{
  "event":"ORDER_CREATED",
  "userId":101
}
```

---

## Step 3

Kafka stores event.

---

## Step 4

Consumers receive event.

```text
Email Service

SMS Service

Push Service
```

---

# Topic Design

```text
notification-topic
```

or

```text
email-topic

sms-topic

push-topic
```

---

# Failure Handling

```text
Notification Failure
        ↓
Retry Topic
        ↓
DLT
```

---

# Scaling Strategy

Increase consumers:

```text
Email Consumer 1

Email Consumer 2

Email Consumer 3
```

---

# Benefits

```text
Scalable

Reliable

Asynchronous

Independent Services
```

---

# Designing Order Processing System

A classic event-driven system.

---

# Problem Statement

Build an order processing platform.

---

Workflow:

```text
Create Order

Reserve Inventory

Process Payment

Ship Order
```

---

# Challenges

```text
Service Coordination

Scalability

Failure Recovery
```

---

# Kafka Architecture

```text
Order Service
      ↓
Order Topic
      ↓
Kafka
      ↓
Inventory Service

Payment Service

Shipping Service
```

---

# Event Flow

## Step 1

Customer places order.

---

## Step 2

Order Service publishes:

```json
{
  "orderId":101,
  "status":"CREATED"
}
```

---

## Step 3

Inventory Service consumes.

---

Inventory Reserved:

```json
{
  "orderId":101,
  "status":"INVENTORY_RESERVED"
}
```

---

## Step 4

Payment Service consumes.

---

Payment Successful:

```json
{
  "orderId":101,
  "status":"PAYMENT_SUCCESS"
}
```

---

## Step 5

Shipping Service consumes.

---

# Architecture Diagram

```text
Order Service
      ↓
order-created-topic
      ↓
Kafka
      ↓
Inventory Service
      ↓
inventory-topic
      ↓
Payment Service
      ↓
payment-topic
      ↓
Shipping Service
```

---

# Saga Pattern Integration

Failures:

```text
Payment Failed
```

---

Compensation:

```text
Release Inventory

Cancel Order
```

---

# Topic Design

```text
order-created

inventory-events

payment-events

shipping-events
```

---

# Benefits

```text
Loose Coupling

Scalable

Fault Tolerant
```

---

# Designing Payment System

One of the most critical Kafka use cases.

---

# Problem Statement

Build a payment processing platform.

---

Requirements:

```text
High Reliability

No Double Charging

Auditability

Exactly Once Processing
```

---

# Challenges

```text
Duplicate Payments

Network Failures

Transaction Consistency
```

---

# Architecture

```text
Payment API
      ↓
Payment Topic
      ↓
Kafka
      ↓
Payment Processor
```

---

# Event Flow

## Step 1

User initiates payment.

---

## Step 2

Producer publishes:

```json
{
  "paymentId":1001,
  "amount":500
}
```

---

## Step 3

Payment Consumer processes payment.

---

## Step 4

Publish result.

```json
{
  "paymentId":1001,
  "status":"SUCCESS"
}
```

---

# Reliability Requirements

Use:

```text
ACK = all

Replication Factor = 3

Idempotent Producer

Transactions

Exactly Once Semantics
```

---

# Payment Topic Design

```text
payment-requests

payment-results
```

---

# Failure Recovery

```text
Retry Topic

DLT

Manual Investigation
```

---

# Payment Architecture

```text
Client
   ↓
Payment API
   ↓
Kafka
   ↓
Payment Processor
   ↓
Bank Gateway
```

---

# Benefits

```text
No Data Loss

High Reliability

Scalable Processing
```

---

# Designing Audit Logging System

Audit logs are mandatory in many industries.

---

# Problem Statement

Track every action performed in the system.

---

Examples:

```text
User Login

Profile Update

Order Creation

Payment Processing
```

---

# Requirements

```text
Immutable Storage

Searchability

Long-Term Retention
```

---

# Kafka Architecture

```text
Applications
      ↓
Audit Topic
      ↓
Kafka
      ↓
Storage Systems
```

---

# Event Example

```json
{
  "userId":101,
  "action":"LOGIN",
  "timestamp":"2026-01-01"
}
```

---

# Audit Flow

```text
Application Event
      ↓
Audit Topic
      ↓
Kafka
      ↓
Audit Storage
```

---

# Storage Targets

```text
Elasticsearch

S3

Data Lake

Database
```

---

# Why Kafka?

Kafka provides:

```text
Immutable Log

Replay Capability

High Throughput
```

---

# Topic Design

```text
audit-topic
```

---

Partition Key:

```text
userId
```

---

Benefits:

```text
User Events Ordered
```

---

# Retention Strategy

```text
Years Of Retention
```

---

Example:

```properties
retention.ms=31536000000
```

(1 Year)

---

# Benefits

```text
Compliance

Audit Trail

Forensics

Replay Support
```

---

# Designing Real-Time Analytics Platform

One of Kafka's strongest use cases.

---

# Problem Statement

Build a platform to analyze events in real time.

---

Examples:

```text
Website Clicks

Orders

Payments

User Activity
```

---

# Requirements

```text
Low Latency

Scalable

Real-Time Dashboard
```

---

# Architecture

```text
Applications
      ↓
Kafka
      ↓
Kafka Streams
      ↓
Analytics Database
      ↓
Dashboard
```

---

# Event Example

```json
{
  "userId":101,
  "event":"CLICK"
}
```

---

# Streaming Pipeline

```text
Producer
      ↓
Kafka Topic
      ↓
Kafka Streams
      ↓
Aggregation
      ↓
Dashboard
```

---

# Real-Time Metrics

```text
Orders Per Minute

Revenue Per Hour

Active Users

Top Products
```

---

# Topic Design

```text
click-events

order-events

payment-events
```

---

# Kafka Streams Processing

Example:

```java
stream.groupByKey()
      .count();
```

---

Output:

```text
Event Counts
```

---

# Analytics Architecture

```text
Applications
      ↓
Kafka
      ↓
Kafka Streams
      ↓
Aggregations
      ↓
ElasticSearch
      ↓
Grafana Dashboard
```

---

# Scaling Strategy

Increase:

```text
Partitions

Consumers

Stream Instances
```

---

# Benefits

```text
Near Real-Time Insights

Scalable Processing

Event Replay
```

---

# Topic Design Recommendations

| System | Topics |
|----------|----------|
| Notification | notification-topic |
| Order Processing | order-created, inventory-events |
| Payment | payment-requests, payment-results |
| Audit Logging | audit-topic |
| Analytics | click-events, order-events |

---

# Reliability Considerations

For all systems:

```text
Replication Factor = 3

ACK = all

ISR Enabled

Retries Enabled
```

---

# Error Handling Strategy

```text
Consumer Failure
       ↓
Retry
       ↓
Retry Topic
       ↓
DLT
```

---

# Scaling Strategy

## Producer Scaling

```text
Multiple Producer Instances
```

---

## Consumer Scaling

```text
Consumer Groups
```

---

## Broker Scaling

```text
Add Brokers
```

---

## Partition Scaling

```text
Increase Partitions
```

---

# System Design Interview Approach

When Kafka appears in interviews:

---

## Step 1

Identify Events

```text
OrderCreated

PaymentProcessed

UserRegistered
```

---

## Step 2

Identify Producers

```text
Order Service

Payment Service
```

---

## Step 3

Identify Consumers

```text
Inventory

Analytics

Notifications
```

---

## Step 4

Design Topics

```text
Domain-Oriented Topics
```

---

## Step 5

Plan Scaling

```text
Partitions

Consumer Groups
```

---

## Step 6

Plan Failure Recovery

```text
Retries

DLT

Replication
```

---

# Common Interview Questions

## Why is Kafka useful for system design?

Because it provides:

```text
Scalability

Reliability

Asynchronous Communication

Event Streaming
```

---

## How would you design a Notification System using Kafka?

Publish notification events to Kafka and allow independent consumers to send Email, SMS, and Push notifications.

---

## How would you design an Order Processing System?

Use event-driven workflows where each service consumes and publishes events through Kafka topics.

---

## Why use Kafka for Payment Systems?

Kafka provides durability, reliability, exactly-once processing, and replay capabilities.

---

## Why is Kafka suitable for Audit Logging?

Kafka acts as an immutable append-only log and supports long-term retention.

---

## How would Kafka help in Real-Time Analytics?

Kafka Streams can process events in real time and continuously update dashboards and analytics systems.

---

## What Kafka features are critical for financial systems?

```text
Transactions

Idempotent Producers

ACK=all

Replication

Exactly Once Semantics
```

---

# Summary

Kafka is a foundational technology for building scalable distributed systems.

System Designs Covered:

- Designing Notification System
- Designing Order Processing System
- Designing Payment System
- Designing Audit Logging System
- Designing Real-Time Analytics Platform

Notification System:

```text
Kafka
      ↓
Email

SMS

Push
```

Order Processing:

```text
Order
 ↓
Inventory
 ↓
Payment
 ↓
Shipping
```

Payment System:

```text
Exactly Once

Transactions

Idempotence
```

Audit Logging:

```text
Immutable Events

Long-Term Retention

Replay Capability
```

Real-Time Analytics:

```text
Kafka Streams

Aggregations

Dashboards
```

Production Recommendations:

```text
Use Replication Factor >= 3

Use ACK = all

Use Retry Topics

Use DLT

Use Consumer Groups

Use Domain-Based Topics

Enable Monitoring
```

These Kafka-based system designs are among the most frequently discussed architectures in real-world enterprise systems and technical interviews, especially for backend, distributed systems, and senior software engineering roles.