# 49. Kafka Admin Operations

Kafka administrators are responsible for managing and maintaining Kafka clusters in production environments.

Administrative operations ensure that Kafka remains:

```text
Available

Scalable

Secure

Reliable

Efficient
```

Kafka provides several ways to perform administration:

```text
Kafka CLI Tools

Admin API

Confluent Control Center

Third-Party Monitoring Tools
```

---

# What are Kafka Admin Operations?

## Definition

Kafka Admin Operations are activities performed to manage Kafka infrastructure, topics, brokers, security, and consumers.

---

# Main Administrative Areas

Kafka administration typically involves:

```text
Topic Management

Broker Management

Consumer Group Management

ACL Management
```

---

# Kafka Administration Architecture

```text
Administrator
       ↓
Admin API / CLI
       ↓
Kafka Cluster
```

---

# Common Admin Responsibilities

```text
Create Topics

Delete Topics

Monitor Brokers

Manage Partitions

Manage Security

Monitor Consumer Groups

Perform Maintenance
```

---

# Topic Management

Topics are one of the most frequently managed Kafka resources.

---

# What is Topic Management?

Topic Management refers to creating, configuring, modifying, monitoring, and deleting Kafka topics.

---

# Why Topic Management Matters?

Poor topic management can lead to:

```text
Storage Problems

Performance Issues

Consumer Delays

Operational Complexity
```

---

# Topic Lifecycle

```text
Create Topic
      ↓
Configure Topic
      ↓
Monitor Topic
      ↓
Modify Topic
      ↓
Delete Topic
```

---

# Topic Creation

Topics can be created using:

```text
CLI

Admin API

Control Center
```

---

CLI Example

```bash
kafka-topics.sh \
--create \
--topic orders \
--partitions 3 \
--replication-factor 3 \
--bootstrap-server localhost:9092
```

---

# Topic Configuration

Common topic settings:

```properties
retention.ms

retention.bytes

cleanup.policy

min.insync.replicas
```

---

Example:

```properties
cleanup.policy=delete

retention.ms=604800000
```

---

Meaning:

```text
Keep Data For 7 Days
```

---

# View Topic Details

```bash
kafka-topics.sh \
--describe \
--topic orders \
--bootstrap-server localhost:9092
```

---

Returns:

```text
Partitions

Replication Factor

Leader

ISR
```

---

# Increasing Partitions

```bash
kafka-topics.sh \
--alter \
--topic orders \
--partitions 10 \
--bootstrap-server localhost:9092
```

---

Important:

```text
Partitions Can Be Increased

Partitions Cannot Be Decreased
```

---

# Topic Deletion

```bash
kafka-topics.sh \
--delete \
--topic orders \
--bootstrap-server localhost:9092
```

---

Warning:

```text
Data Will Be Permanently Lost
```

---

# Topic Management Best Practices

---

## Use Meaningful Names

Example:

```text
orders

payments

notifications
```

---

## Avoid Excessive Partitions

Too many partitions increase:

```text
Memory Usage

Metadata Size

Operational Complexity
```

---

## Configure Retention Carefully

---

## Monitor Topic Growth

---

## Use Replication Factor ≥ 3

---

# Broker Management

Broker management focuses on Kafka servers themselves.

---

# What is Broker Management?

Managing Kafka broker nodes that store data and process requests.

---

# Why Broker Management?

Brokers are responsible for:

```text
Storage

Networking

Replication

Request Processing
```

---

Poor broker health affects the entire cluster.

---

# Broker Management Responsibilities

```text
Broker Addition

Broker Removal

Broker Monitoring

Broker Configuration

Broker Upgrades
```

---

# View Cluster Brokers

Using Admin API:

```java
admin.describeCluster();
```

---

CLI:

```bash
kafka-broker-api-versions.sh \
--bootstrap-server localhost:9092
```

---

# Broker Health Checks

Monitor:

```text
CPU Usage

Memory Usage

Disk Usage

Network Usage

Request Latency
```

---

# Broker Addition

Steps:

```text
Provision Server
      ↓
Install Kafka
      ↓
Configure Broker
      ↓
Start Broker
      ↓
Rebalance Partitions
```

---

# Broker Removal

Steps:

```text
Move Partitions
      ↓
Verify Replication
      ↓
Shutdown Broker
      ↓
Remove Broker
```

---

Never remove a broker before reassigning partitions.

---

# Broker Configuration Management

Important settings:

```properties
broker.id

log.dirs

num.partitions

default.replication.factor

min.insync.replicas
```

---

Example:

```properties
broker.id=1
```

---

# Rolling Restart

Used for:

```text
Configuration Changes

Upgrades

Maintenance
```

---

Procedure:

```text
Restart Broker-1

Wait Healthy

Restart Broker-2

Wait Healthy

Restart Broker-3
```

---

Benefits:

```text
No Cluster Downtime
```

---

# Broker Monitoring Metrics

Monitor:

```text
Request Rate

Request Latency

CPU Usage

Disk Utilization

Replication Lag
```

---

# Broker Management Best Practices

---

## Maintain Multiple Brokers

Minimum:

```text
3 Brokers
```

---

## Use Dedicated Storage

---

## Monitor Disk Usage

---

## Monitor ISR Health

---

## Plan Capacity Growth

---

# Consumer Group Management

Consumer groups are critical for scalability and fault tolerance.

---

# What is Consumer Group Management?

Managing consumer groups that consume Kafka topics.

---

Responsibilities:

```text
Monitor Groups

Reset Offsets

Troubleshoot Lag

Manage Membership
```

---

# Why Consumer Group Management?

Problems include:

```text
Consumer Lag

Failed Consumers

Rebalancing Issues

Duplicate Processing
```

---

# View Consumer Groups

```bash
kafka-consumer-groups.sh \
--list \
--bootstrap-server localhost:9092
```

---

Example Output:

```text
order-group

payment-group

analytics-group
```

---

# Describe Consumer Group

```bash
kafka-consumer-groups.sh \
--describe \
--group order-group \
--bootstrap-server localhost:9092
```

---

Shows:

```text
Current Offset

Log End Offset

Lag

Assigned Partitions
```

---

# Consumer Lag

Formula:

```text
Lag =
Log End Offset
-
Current Offset
```

---

Example:

```text
Log End Offset = 1000

Current Offset = 700
```

---

Lag:

```text
300
```

---

# Why Lag Matters?

Large lag indicates:

```text
Slow Consumers

Consumer Failures

Traffic Spikes
```

---

# Reset Consumer Offsets

Reset to earliest:

```bash
kafka-consumer-groups.sh \
--group order-group \
--reset-offsets \
--to-earliest \
--execute \
--topic orders \
--bootstrap-server localhost:9092
```

---

Reset to latest:

```bash
kafka-consumer-groups.sh \
--group order-group \
--reset-offsets \
--to-latest \
--execute \
--topic orders \
--bootstrap-server localhost:9092
```

---

# Consumer Group States

Common states:

```text
Stable

PreparingRebalance

CompletingRebalance

Empty

Dead
```

---

# Rebalancing Monitoring

Monitor:

```text
Frequent Rebalances

Consumer Join Rate

Heartbeat Failures
```

---

# Consumer Group Best Practices

---

## Monitor Lag Continuously

---

## Avoid Frequent Rebalances

---

## Use Static Membership

When appropriate.

---

## Tune Poll Settings

---

## Investigate Dead Groups

---

# ACL Management

ACL management controls security and access permissions.

---

# What is ACL?

ACL stands for:

```text
Access Control List
```

---

ACL defines:

```text
Who Can Access What
```

---

# Why ACL Management?

Without ACLs:

```text
Any Client May Access Topics
```

---

This creates security risks.

---

# ACL Architecture

```text
User
   ↓
ACL Check
   ↓
Kafka Resource
```

---

# Resources Protected by ACLs

```text
Topics

Consumer Groups

Clusters

Transactional IDs
```

---

# Operations Controlled by ACLs

```text
Read

Write

Create

Delete

Describe

Alter
```

---

# Example ACL

Allow producer to write:

```text
User: payment-service

Resource: payments-topic

Permission: Write
```

---

# Add ACL

```bash
kafka-acls.sh \
--add \
--allow-principal User:payment-service \
--operation Write \
--topic payments \
--bootstrap-server localhost:9092
```

---

# Read Permission Example

```bash
kafka-acls.sh \
--add \
--allow-principal User:analytics-service \
--operation Read \
--topic payments \
--group analytics-group \
--bootstrap-server localhost:9092
```

---

# View ACLs

```bash
kafka-acls.sh \
--list \
--bootstrap-server localhost:9092
```

---

# Remove ACL

```bash
kafka-acls.sh \
--remove
```

---

# ACL Types

---

## Topic ACL

Control topic access.

---

## Group ACL

Control consumer group access.

---

## Cluster ACL

Control cluster-level operations.

---

## Transactional ACL

Control transactions.

---

# ACL Permission Types

| Permission | Description |
|------------|-------------|
| Read | Consume Messages |
| Write | Produce Messages |
| Create | Create Resources |
| Delete | Delete Resources |
| Describe | View Metadata |
| Alter | Modify Resources |

---

# Principle of Least Privilege

Grant only required permissions.

---

Bad:

```text
Allow Everything
```

---

Good:

```text
Allow Only Required Operations
```

---

# Security Best Practices

---

## Enable Authentication

---

## Enable Authorization

---

## Use ACLs

---

## Audit Access Logs

---

## Avoid Wildcard Permissions

---

## Rotate Credentials

---

# Admin API for Administrative Operations

Many administrative tasks can be automated.

---

# Create Topic

```java
admin.createTopics(...);
```

---

# Delete Topic

```java
admin.deleteTopics(...);
```

---

# Describe Cluster

```java
admin.describeCluster();
```

---

# List Topics

```java
admin.listTopics();
```

---

# Benefits of Admin API

```text
Automation

Infrastructure As Code

DevOps Integration
```

---

# Common Operational Tasks

Daily:

```text
Check Broker Health

Monitor Consumer Lag

Review Disk Usage
```

---

Weekly:

```text
Review Topic Growth

Review ACLs

Check Replication Health
```

---

Monthly:

```text
Capacity Planning

Upgrade Planning

Disaster Recovery Testing
```

---

# Common Interview Questions

## What are Kafka Admin Operations?

Administrative activities used to manage Kafka clusters, topics, brokers, security, and consumer groups.

---

## What are the main administrative areas?

```text
Topic Management

Broker Management

Consumer Group Management

ACL Management
```

---

## Can Kafka partitions be decreased?

```text
No
```

Partitions can only be increased.

---

## How do you monitor consumer lag?

Using:

```bash
kafka-consumer-groups.sh --describe
```

---

## What is an ACL?

Access Control List used to control permissions on Kafka resources.

---

## Why is ACL management important?

To prevent unauthorized access and enforce security policies.

---

## What should be done before removing a broker?

Move all partitions and replicas to other brokers.

---

## What is the purpose of rolling restarts?

To perform maintenance without causing cluster downtime.

---

## Which API is used for administrative automation?

```text
Admin API
```

---

## What is the principle of least privilege?

Grant users only the permissions they require.

---

# Summary

Kafka Admin Operations are essential for maintaining healthy, secure, and scalable Kafka clusters.

Core Areas:

- Topic Management
- Broker Management
- Consumer Group Management
- ACL Management

Topic Management:

```text
Create Topics

Modify Partitions

Configure Retention

Delete Topics
```

Broker Management:

```text
Add Brokers

Remove Brokers

Monitor Health

Perform Upgrades
```

Consumer Group Management:

```text
Monitor Lag

Reset Offsets

Manage Rebalancing

Troubleshoot Consumers
```

ACL Management:

```text
Control Access

Protect Resources

Enforce Security Policies
```

Key Tools:

```text
Kafka CLI

Admin API

Control Center

Monitoring Platforms
```

Best Practices:

```text
Monitor Continuously

Automate Administration

Use ACLs

Plan Capacity

Perform Regular Maintenance
```

Effective Kafka administration ensures reliable operations, strong security, optimal performance, and long-term scalability for production-grade event streaming platforms.