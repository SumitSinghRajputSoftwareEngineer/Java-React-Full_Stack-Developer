
This section combines everything learned so far and demonstrates how Kafka is used in a real-world production-grade system.

The goal is to design and implement a scalable, fault-tolerant, secure, and highly available event-driven architecture using:

```text
Apache Kafka

Spring Boot

Docker

Kubernetes

Prometheus

Grafana

Schema Registry

Kafka Connect
```

This is the type of architecture used by:

```text
Amazon

Netflix

Uber

Paytm

PhonePe

Swiggy

Zomato
```

---

# Project Overview

## Business Requirement

Build an E-Commerce Order Processing System.

Users can:

```text
Place Orders

Make Payments

Track Orders

Receive Notifications
```

The system must support:

```text
Millions of Orders

High Availability

Real-Time Processing

Fault Tolerance

Scalability
```

---

# Functional Requirements

## Order Creation

Customer places an order.

---

## Payment Processing

Payment service processes payment.

---

## Inventory Update

Inventory service updates stock.

---

## Notification Service

Customer receives confirmation.

---

## Analytics Service

Business dashboards update in real time.

---

# Non-Functional Requirements

```text
High Throughput

Low Latency

Zero Data Loss

Horizontal Scalability

Disaster Recovery

Monitoring
```

---

# Project Architecture

## High-Level Architecture

```text
                Customer
                    │
                    ▼
            API Gateway
                    │
                    ▼
            Order Service
                    │
                    ▼
              Kafka Topic
              order-events
                    │
 ┌──────────────────┼──────────────────┐
 │                  │                  │
 ▼                  ▼                  ▼
Payment         Inventory        Notification
Service          Service            Service
 │                  │                  │
 ▼                  ▼                  ▼
payment-events  inventory-events  notification-events
                    │
                    ▼
            Analytics Service
```

---

# Event Flow

```text
Order Created
      ↓
Kafka Topic
      ↓
Payment Service
      ↓
Payment Success
      ↓
Inventory Service
      ↓
Stock Updated
      ↓
Notification Service
      ↓
Email Sent
```

---

# Kafka Topics

---

## order-events

Stores order events.

Example:

```json
{
  "orderId":1001,
  "customerId":500,
  "amount":1500
}
```

---

## payment-events

Stores payment status.

```json
{
  "orderId":1001,
  "status":"SUCCESS"
}
```

---

## inventory-events

Stores inventory updates.

```json
{
  "productId":200,
  "quantity":2
}
```

---

## notification-events

Stores notification requests.

```json
{
  "customerId":500,
  "type":"EMAIL"
}
```

---

## audit-events

Stores audit logs.

```json
{
  "action":"ORDER_CREATED"
}
```

---

# Topic Configuration

| Topic | Partitions | Replication |
|---------|-----------|-------------|
| order-events | 12 | 3 |
| payment-events | 12 | 3 |
| inventory-events | 12 | 3 |
| notification-events | 6 | 3 |
| audit-events | 6 | 3 |

---

# Producer Service

The producer publishes business events.

---

# Order Service

Acts as Kafka Producer.

---

Flow:

```text
REST Request
      ↓
Create Order
      ↓
Publish Event
      ↓
Kafka
```

---

# Spring Boot Producer

```java
@Service
public class OrderProducer {

    @Autowired
    private KafkaTemplate<String, OrderEvent> kafkaTemplate;

    public void publish(OrderEvent event) {

        kafkaTemplate.send(
            "order-events",
            event.getOrderId(),
            event
        );
    }
}
```

---

# Why Use OrderId as Key?

```text
Ordering Guarantee

Partition Affinity
```

---

All events for the same order go to the same partition.

---

# Producer Configuration

```yaml
spring:
  kafka:
    producer:
      acks: all
      retries: 10
      enable-idempotence: true
```

---

# Reliability Features

```text
ACK = all

Idempotent Producer

Retries Enabled
```

---

# Consumer Service

Consumers process events.

---

# Payment Service

Consumes:

```text
order-events
```

Produces:

```text
payment-events
```

---

# Consumer Example

```java
@KafkaListener(
    topics = "order-events",
    groupId = "payment-group"
)
public void process(OrderEvent event) {

    processPayment(event);

}
```

---

# Consumer Groups

| Service | Group ID |
|-----------|----------|
| Payment | payment-group |
| Inventory | inventory-group |
| Notification | notification-group |
| Analytics | analytics-group |

---

# Why Separate Consumer Groups?

Each service receives every event.

```text
Publish Once

Consume Many Times
```

---

# Manual Offset Commit

Used for reliability.

```java
@KafkaListener(...)
public void consume(
      OrderEvent event,
      Acknowledgment ack) {

      process(event);

      ack.acknowledge();
}
```

---

Benefits:

```text
Avoid Message Loss

Control Processing
```

---

# Spring Boot Integration

Spring Kafka simplifies development.

---

# Dependencies

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

# Producer Bean

```java
@Bean
public ProducerFactory<String, OrderEvent>
producerFactory() {

    return new DefaultKafkaProducerFactory<>(configs);
}
```

---

# Consumer Bean

```java
@Bean
public ConsumerFactory<String, OrderEvent>
consumerFactory() {

    return new DefaultKafkaConsumerFactory<>(configs);
}
```

---

# KafkaTemplate

Used for publishing.

```java
kafkaTemplate.send(...)
```

---

# Kafka Listener

Used for consumption.

```java
@KafkaListener
```

---

# Error Handler

```java
@Bean
public DefaultErrorHandler handler() {

    return new DefaultErrorHandler();
}
```

---

# Monitoring Setup

Production systems must be monitored continuously.

---

# Monitoring Architecture

```text
Kafka
  │
  ▼
JMX Exporter
  │
  ▼
Prometheus
  │
  ▼
Grafana
```

---

# Key Metrics

## Broker Metrics

```text
CPU Usage

Memory Usage

Disk Usage
```

---

## Producer Metrics

```text
Request Latency

Error Rate

Throughput
```

---

## Consumer Metrics

```text
Consumer Lag

Processing Time
```

---

## Replication Metrics

```text
ISR Count

Replication Lag
```

---

# Grafana Dashboards

Create dashboards for:

```text
Broker Health

Topic Throughput

Consumer Lag

Partition Distribution
```

---

# Alerts

Examples:

```text
Lag > 10,000

ISR Shrinks

Disk Usage > 80%
```

---

# Security Setup

Production Kafka must be secured.

---

# Authentication

Use:

```text
SASL/SCRAM

OAuth

SSL Certificates
```

---

# Encryption

Enable:

```text
TLS
```

---

Example:

```properties
security.protocol=SASL_SSL
```

---

# Authorization

Use ACLs.

---

Producer ACL:

```text
Write Access
```

---

Consumer ACL:

```text
Read Access
```

---

# Schema Validation

Use Schema Registry.

---

Benefits:

```text
Schema Evolution

Compatibility

Governance
```

---

# Deployment Strategy

Modern Kafka deployments use containers.

---

# Docker Architecture

```text
Kafka

Spring Boot Services

Prometheus

Grafana
```

All containerized.

---

# Docker Compose Example

```yaml
services:
  kafka:
  zookeeper:
  order-service:
  payment-service:
```

---

# Kubernetes Deployment

Production setup.

---

Architecture:

```text
Kubernetes Cluster
      │
      ├── Kafka Brokers
      ├── Order Service
      ├── Payment Service
      ├── Inventory Service
      └── Notification Service
```

---

# Kubernetes Features

```text
Auto Scaling

Self Healing

Rolling Deployments
```

---

# CI/CD Pipeline

```text
Git
 ↓
Jenkins/GitHub Actions
 ↓
Docker Build
 ↓
Kubernetes Deploy
```

---

# Blue-Green Deployment

Deploy:

```text
Version A

Version B
```

Switch traffic after validation.

---

# Performance Tuning

Production systems require tuning.

---

# Producer Tuning

```properties
batch.size=65536

linger.ms=10

compression.type=zstd
```

---

Benefits:

```text
Higher Throughput

Reduced Network Usage
```

---

# Consumer Tuning

```properties
max.poll.records=500

fetch.min.bytes=50000
```

---

Benefits:

```text
Batch Processing

Lower Overhead
```

---

# Broker Tuning

```properties
num.network.threads=8

num.io.threads=16
```

---

Benefits:

```text
Parallel Request Handling
```

---

# Topic Tuning

Partitions:

```text
Enough For Future Scaling
```

Example:

```text
12 Partitions
```

---

# Compression

Recommended:

```text
ZSTD

Snappy
```

---

# Capacity Planning

Estimate:

```text
Messages Per Second

Storage Growth

Retention Requirements
```

---

# Failure Handling

The most important production concern.

---

# Scenario 1

## Producer Failure

Problem:

```text
Network Timeout
```

---

Solution:

```properties
retries=10

enable.idempotence=true
```

---

# Scenario 2

## Consumer Failure

Problem:

```text
Application Crash
```

---

Solution:

```text
Manual Offset Commit

Restart Consumer
```

---

# Scenario 3

## Broker Failure

Problem:

```text
Broker Down
```

---

Solution:

```text
Replication Factor = 3

Leader Election
```

---

# Scenario 4

## Poison Message

Problem:

```text
Invalid Event
```

---

Solution:

```text
Dead Letter Topic (DLT)
```

---

Example:

```text
order-events-dlt
```

---

# Scenario 5

## Consumer Lag

Problem:

```text
Slow Processing
```

---

Solution:

```text
Scale Consumers

Increase Partitions
```

---

# Dead Letter Topic Flow

```text
Consumer
    ↓
Processing Error
    ↓
Retry
    ↓
Retry Failed
    ↓
DLT
```

---

# Retry Strategy

```text
Retry 1

Retry 2

Retry 3

Dead Letter Topic
```

---

# Disaster Recovery

---

## Multi-Region Architecture

```text
Primary Cluster
      ↓
MirrorMaker 2
      ↓
Secondary Cluster
```

---

Benefits:

```text
Regional Failure Protection
```

---

# Backup Strategy

Store:

```text
Topic Configurations

Schemas

Connector Configurations
```

---

# Production Readiness Checklist

## Reliability

```text
ACK = all

Replication Factor = 3

ISR Protection
```

---

## Security

```text
TLS

ACLs

Authentication
```

---

## Monitoring

```text
Prometheus

Grafana

Alerts
```

---

## Scalability

```text
Partitions

Consumer Groups

Auto Scaling
```

---

## Recovery

```text
DLT

Retries

Disaster Recovery
```

---

# End-to-End Event Flow

```text
Customer Places Order
          ↓
Order Service
          ↓
Kafka Topic
          ↓
Payment Service
          ↓
Payment Topic
          ↓
Inventory Service
          ↓
Inventory Topic
          ↓
Notification Service
          ↓
Customer Notification
```

---

# Common Interview Questions

## Why use Kafka in this architecture?

```text
Decoupling

Scalability

Reliability

Asynchronous Processing
```

---

## Why use separate consumer groups?

Each service can independently consume the same event.

---

## How do you avoid duplicate messages?

```text
Idempotent Producer

Transactions

Idempotent Consumers
```

---

## How do you handle failures?

```text
Retries

DLT

Replication

Monitoring
```

---

## How do you monitor Kafka?

```text
Prometheus

Grafana

JMX Metrics
```

---

## How do you secure Kafka?

```text
TLS

SASL

ACLs
```

---

## How do you scale consumers?

```text
Increase Consumers

Increase Partitions
```

---

# Summary

This project demonstrates a complete production-grade Kafka ecosystem.

Core Topics Covered:

- Project Architecture
- Producer Service
- Consumer Service
- Spring Boot Integration
- Monitoring Setup
- Security Setup
- Deployment Strategy
- Performance Tuning
- Failure Handling

Production Technologies:

```text
Apache Kafka

Spring Boot

Docker

Kubernetes

Prometheus

Grafana

Schema Registry
```

Production Best Practices:

```text
ACK = all

Replication Factor = 3

Idempotent Producers

Manual Offset Commit

DLT

Monitoring

TLS Security
```

This architecture represents a realistic enterprise event-driven system and combines the most important Kafka concepts used in production environments, system design interviews, and large-scale distributed applications.