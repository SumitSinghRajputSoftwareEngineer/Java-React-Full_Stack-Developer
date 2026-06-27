
Kafka provides a rich set of Command Line Interface (CLI) tools for managing and troubleshooting Kafka clusters.

These commands are heavily used by:

```text
Kafka Administrators

Production Support Engineers

DevOps Engineers

Site Reliability Engineers (SRE)

Backend Developers
```

In real production environments, CLI commands are often the first tools used to investigate issues such as:

```text
Consumer Lag

Topic Problems

Broker Health

Offset Issues

Partition Distribution
```

---

# Why Kafka CLI Commands Are Important?

CLI commands help perform:

```text
Topic Management

Consumer Group Management

Message Production

Message Consumption

Configuration Changes

Broker Monitoring
```

---

# Kafka CLI Tool Directory

Kafka provides scripts under:

```text
bin/
```

Examples:

```text
kafka-topics.sh

kafka-console-producer.sh

kafka-console-consumer.sh

kafka-consumer-groups.sh

kafka-configs.sh

kafka-broker-api-versions.sh
```

---

# Kafka 3.x+ Bootstrap Server

Most commands use:

```bash
--bootstrap-server
```

Example:

```bash
--bootstrap-server localhost:9092
```

---

# Topic Commands

Topics are the fundamental storage unit in Kafka.

---

# List Topics

Displays all topics in the cluster.

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--list
```

---

Example Output:

```text
orders

payments

inventory

__consumer_offsets
```

---

# Create Topic

Create a topic with 3 partitions and replication factor 3.

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--create \
--topic orders \
--partitions 3 \
--replication-factor 3
```

---

# Verify Topic Creation

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--list
```

---

# Describe Topic

Displays detailed topic metadata.

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--describe \
--topic orders
```

---

Example Output:

```text
Topic: orders

PartitionCount: 3

ReplicationFactor: 3
```

---

Shows:

```text
Partitions

Leaders

Replicas

ISR
```

---

# Delete Topic

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--delete \
--topic orders
```

---

Important:

Broker configuration must allow topic deletion.

```properties
delete.topic.enable=true
```

---

# Increase Partitions

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--alter \
--topic orders \
--partitions 10
```

---

Important:

```text
Partitions Can Be Increased

Partitions Cannot Be Reduced
```

---

# Describe All Topics

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--describe
```

---

# Topic Command Summary

| Command | Purpose |
|----------|----------|
| --list | List Topics |
| --create | Create Topic |
| --describe | Topic Details |
| --delete | Delete Topic |
| --alter | Modify Topic |

---

# Producer Commands

Used to send messages to Kafka.

---

# Console Producer

Start producer:

```bash
kafka-console-producer.sh \
--bootstrap-server localhost:9092 \
--topic orders
```

---

After execution:

```text
Order Created

Order Updated

Order Cancelled
```

---

Messages are published immediately.

---

# Producer with Key

```bash
kafka-console-producer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--property parse.key=true \
--property key.separator=:
```

---

Input:

```text
1001:Order Created

1002:Order Created
```

---

Result:

```text
1001 → Key

Order Created → Value
```

---

# Producer with Acknowledgements

```bash
kafka-console-producer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--producer-property acks=all
```

---

Ensures stronger durability.

---

# Producer Command Summary

| Command | Purpose |
|----------|----------|
| kafka-console-producer.sh | Publish Messages |
| parse.key=true | Send Keys |
| acks=all | Strong Reliability |

---

# Consumer Commands

Used to read messages from Kafka.

---

# Console Consumer

```bash
kafka-console-consumer.sh \
--bootstrap-server localhost:9092 \
--topic orders
```

---

Consumes only new messages.

---

# Consume From Beginning

```bash
kafka-console-consumer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--from-beginning
```

---

Reads:

```text
All Historical Messages
```

---

# Show Keys

```bash
kafka-console-consumer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--property print.key=true
```

---

Output:

```text
1001 Order Created

1002 Order Updated
```

---

# Limit Messages

Consume only 5 messages.

```bash
kafka-console-consumer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--max-messages 5
```

---

# Read Specific Partition

```bash
kafka-console-consumer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--partition 0
```

---

# Consumer Command Summary

| Command | Purpose |
|----------|----------|
| Console Consumer | Read Messages |
| --from-beginning | Read All Messages |
| print.key=true | Display Keys |
| --max-messages | Limit Output |

---

# Consumer Group Commands

One of the most important CLI tools in production.

---

# List Consumer Groups

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--list
```

---

Example Output:

```text
payment-group

order-group

analytics-group
```

---

# Describe Consumer Group

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--describe \
--group order-group
```

---

Displays:

```text
Current Offset

Log End Offset

Lag

Partition Ownership
```

---

Example:

```text
Partition 0

Current Offset = 500

Log End Offset = 700

Lag = 200
```

---

# Check Consumer Lag

Same command:

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--describe \
--group order-group
```

---

Important Metric:

```text
LAG
```

---

# Reset Offsets

Move consumer to earliest messages.

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--group order-group \
--reset-offsets \
--to-earliest \
--execute \
--topic orders
```

---

Move consumer to latest:

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--group order-group \
--reset-offsets \
--to-latest \
--execute \
--topic orders
```

---

# Delete Consumer Group

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--delete \
--group order-group
```

---

# Consumer Group Command Summary

| Command | Purpose |
|----------|----------|
| --list | List Groups |
| --describe | Group Details |
| --reset-offsets | Offset Management |
| --delete | Remove Group |

---

# Configuration Commands

Used to manage Kafka configurations.

---

# Describe Topic Configurations

```bash
kafka-configs.sh \
--bootstrap-server localhost:9092 \
--entity-type topics \
--entity-name orders \
--describe
```

---

Example Output:

```text
retention.ms=604800000

cleanup.policy=delete
```

---

# Update Topic Configuration

Set retention to 1 day.

```bash
kafka-configs.sh \
--bootstrap-server localhost:9092 \
--entity-type topics \
--entity-name orders \
--alter \
--add-config retention.ms=86400000
```

---

# Configure Log Compaction

```bash
kafka-configs.sh \
--bootstrap-server localhost:9092 \
--entity-type topics \
--entity-name orders \
--alter \
--add-config cleanup.policy=compact
```

---

# Configure Multiple Settings

```bash
kafka-configs.sh \
--bootstrap-server localhost:9092 \
--entity-type topics \
--entity-name orders \
--alter \
--add-config retention.ms=86400000,max.message.bytes=1048576
```

---

# Remove Configuration

```bash
kafka-configs.sh \
--bootstrap-server localhost:9092 \
--entity-type topics \
--entity-name orders \
--alter \
--delete-config retention.ms
```

---

# Configuration Command Summary

| Command | Purpose |
|----------|----------|
| Describe Config | View Settings |
| Alter Config | Update Settings |
| Delete Config | Remove Settings |

---

# Broker Commands

Useful for troubleshooting and cluster management.

---

# Broker API Versions

Check supported broker APIs.

```bash
kafka-broker-api-versions.sh \
--bootstrap-server localhost:9092
```

---

Output:

```text
Broker Version

Supported APIs
```

---

# Cluster Information

Get cluster metadata.

```bash
kafka-cluster.sh \
--bootstrap-server localhost:9092 \
cluster-id
```

---

Example Output:

```text
clusterId=ABCD1234XYZ
```

---

# Check Metadata

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--describe
```

---

Displays:

```text
Leaders

Replicas

ISR
```

---

# Preferred Leader Election

```bash
kafka-leader-election.sh \
--bootstrap-server localhost:9092 \
--election-type preferred \
--all-topic-partitions
```

---

Purpose:

```text
Restore Preferred Leaders
```

---

# Reassign Partitions

Generate reassignment plan.

```bash
kafka-reassign-partitions.sh \
--bootstrap-server localhost:9092
```

---

Used for:

```text
Cluster Expansion

Broker Removal

Load Balancing
```

---

# Storage Information (KRaft)

```bash
kafka-storage.sh info \
-c config/server.properties
```

---

Displays:

```text
Cluster Metadata

Storage Information
```

---

# Broker Command Summary

| Command | Purpose |
|----------|----------|
| kafka-broker-api-versions.sh | Broker Capabilities |
| kafka-cluster.sh | Cluster Metadata |
| kafka-leader-election.sh | Leader Management |
| kafka-reassign-partitions.sh | Partition Movement |
| kafka-storage.sh | Storage Information |

---

# Most Common Production Commands

---

## Check Topics

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--list
```

---

## Describe Topic

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--describe \
--topic orders
```

---

## Check Consumer Lag

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--describe \
--group order-group
```

---

## Read Messages

```bash
kafka-console-consumer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--from-beginning
```

---

## Send Messages

```bash
kafka-console-producer.sh \
--bootstrap-server localhost:9092 \
--topic orders
```

---

## Change Retention

```bash
kafka-configs.sh \
--bootstrap-server localhost:9092 \
--entity-type topics \
--entity-name orders \
--alter \
--add-config retention.ms=86400000
```

---

# Production Troubleshooting Commands

---

## Consumer Lag Investigation

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--describe \
--group payment-group
```

---

## Verify ISR

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--describe \
--topic payments
```

Look for:

```text
ISR Count
```

---

## Check Partition Leaders

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--describe
```

---

## Verify Replication

Check:

```text
Replicas

ISR

Leader
```

---

# Common Interview Questions

## How do you create a topic?

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--create \
--topic orders \
--partitions 3 \
--replication-factor 3
```

---

## How do you check consumer lag?

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--describe \
--group order-group
```

---

## How do you read messages from the beginning?

```bash
kafka-console-consumer.sh \
--bootstrap-server localhost:9092 \
--topic orders \
--from-beginning
```

---

## How do you increase partitions?

```bash
kafka-topics.sh \
--bootstrap-server localhost:9092 \
--alter \
--topic orders \
--partitions 10
```

---

## Can partitions be reduced?

```text
No
```

Partitions can only be increased.

---

## How do you reset consumer offsets?

```bash
kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--group order-group \
--reset-offsets \
--to-earliest \
--execute \
--topic orders
```

---

## How do you configure retention?

```bash
kafka-configs.sh \
--bootstrap-server localhost:9092 \
--entity-type topics \
--entity-name orders \
--alter \
--add-config retention.ms=86400000
```

---

# Summary

Kafka CLI tools are essential for administration, monitoring, and troubleshooting.

Core Areas Covered:

- Topic Commands
- Producer Commands
- Consumer Commands
- Consumer Group Commands
- Configuration Commands
- Broker Commands

Most Frequently Used Production Commands:

```text
kafka-topics.sh

kafka-console-producer.sh

kafka-console-consumer.sh

kafka-consumer-groups.sh

kafka-configs.sh
```

Most Important Production Checks:

```text
Consumer Lag

Partition Distribution

Leader Status

ISR Status

Retention Configuration

Replication Health
```

Mastering these CLI commands is critical for Kafka production support, DevOps operations, cluster administration, and Kafka interview preparation.