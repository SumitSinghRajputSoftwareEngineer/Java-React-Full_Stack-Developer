
Before learning Kafka development, producers, consumers, and cluster management, it is important to understand how Kafka is installed, configured, and executed in different environments.

Kafka can run in:

- Local Machine
- Docker
- Kubernetes
- Cloud Platforms
- Production Servers

---

# Installing Kafka

## What is Required?

Apache Kafka is written in Java.

Therefore, Java must be installed before Kafka.

---

## Installation Approaches

### Method 1: Local Installation

```text
Download Kafka
↓
Extract Zip/Tar
↓
Configure Kafka
↓
Start Kafka
```

---

### Method 2: Docker Installation

```text
Docker
↓
Kafka Container
```

---

### Method 3: Kubernetes Installation

```text
Kubernetes Cluster
↓
Kafka Pods
```

---

### Method 4: Managed Cloud Kafka

```text
AWS MSK
Azure Event Hubs
Confluent Cloud
Google Managed Kafka
```

---

# Download Kafka

Official Kafka Download Page:

```text
https://kafka.apache.org/downloads
```

---

## Download Example

```text
kafka_2.13-4.1.0.tgz
```

Where:

```text
2.13 = Scala Version
4.1.0 = Kafka Version
```

---

## Extract Kafka

Linux/Mac:

```bash
tar -xvf kafka_2.13-4.1.0.tgz
```

Windows:

```text
Extract ZIP using WinZip/7-Zip
```

---

## Kafka Directory Structure

```text
kafka/

├── bin/
├── config/
├── libs/
├── logs/
├── licenses/
├── site-docs/
```

---

## Important Directories

### bin

Contains startup scripts.

```text
kafka-server-start.sh
kafka-topics.sh
kafka-console-producer.sh
```

---

### config

Contains configuration files.

---

### libs

Kafka JAR files.

---

### logs

Broker log files.

---

# Kafka Prerequisites

---

# Java Installation

Kafka requires Java.

---

## Verify Java

```bash
java -version
```

Example:

```text
openjdk version "17.0.10"
```

---

## Recommended Java Versions

| Kafka Version | Java Version |
|--------------|-------------|
| Kafka 3.x | Java 11+ |
| Kafka 4.x | Java 17+ |
| Production | Java 17+ |

---

# Hardware Requirements

---

## Minimum Development Machine

```text
CPU: 2 Core
RAM: 4 GB
Disk: 10 GB
```

---

## Production Cluster

```text
CPU: 8+ Core
RAM: 16+ GB
SSD Storage
10Gb Network
```

---

# Operating System Support

Kafka supports:

```text
Linux
Windows
macOS
Docker
Kubernetes
```

---

## Recommended Production OS

```text
Linux
```

Most production Kafka clusters run on Linux.

---

# Kafka Configuration Files

All Kafka configuration files are located under:

```text
config/
```

---

# Important Configuration Files

---

## server.properties

Main broker configuration.

Location:

```text
config/server.properties
```

---

### Example

```properties
broker.id=1

listeners=PLAINTEXT://:9092

log.dirs=/tmp/kafka-logs

num.partitions=3
```

---

## broker.properties

Additional broker settings (version dependent).

---

## producer.properties

Producer settings.

```properties
acks=all
retries=3
```

---

## consumer.properties

Consumer settings.

```properties
group.id=my-group
auto.offset.reset=earliest
```

---

## connect-standalone.properties

Kafka Connect standalone mode.

---

## connect-distributed.properties

Kafka Connect distributed mode.

---

## tools-log4j.properties

Logging configuration.

---

# Important Broker Configurations

---

## broker.id

Unique broker identifier.

```properties
broker.id=1
```

---

## listeners

Port where Kafka listens.

```properties
listeners=PLAINTEXT://:9092
```

---

## log.dirs

Data storage location.

```properties
log.dirs=/tmp/kafka-logs
```

---

## num.partitions

Default partitions.

```properties
num.partitions=3
```

---

## default.replication.factor

Replication factor.

```properties
default.replication.factor=3
```

---

## log.retention.hours

Message retention.

```properties
log.retention.hours=168
```

---

# Running Kafka Locally

Kafka can run in two modes:

---

## Legacy Mode (ZooKeeper)

```text
Kafka
+
ZooKeeper
```

Used before Kafka 3.x.

---

## Modern Mode (KRaft)

```text
Kafka Only
```

No ZooKeeper required.

---

# Running Kafka Using KRaft

Recommended for Kafka 4.x.

---

## Step 1: Generate Cluster ID

Linux/Mac:

```bash
bin/kafka-storage.sh random-uuid
```

Output:

```text
abcd-1234-xyz
```

---

## Step 2: Format Storage

```bash
bin/kafka-storage.sh format \
-t <cluster-id> \
-c config/kraft/server.properties
```

Example:

```bash
bin/kafka-storage.sh format \
-t abcd-1234-xyz \
-c config/kraft/server.properties
```

---

## Step 3: Start Kafka

Linux/Mac:

```bash
bin/kafka-server-start.sh config/kraft/server.properties
```

Windows:

```cmd
bin\windows\kafka-server-start.bat config\kraft\server.properties
```

---

## Verify Startup

Look for:

```text
Kafka Server started
```

or

```text
Transitioning from STARTING to STARTED
```

---

# Create Topic

```bash
bin/kafka-topics.sh \
--create \
--topic orders \
--bootstrap-server localhost:9092 \
--partitions 3 \
--replication-factor 1
```

---

# List Topics

```bash
bin/kafka-topics.sh \
--list \
--bootstrap-server localhost:9092
```

---

# Running Producer

```bash
bin/kafka-console-producer.sh \
--topic orders \
--bootstrap-server localhost:9092
```

---

# Running Consumer

```bash
bin/kafka-console-consumer.sh \
--topic orders \
--from-beginning \
--bootstrap-server localhost:9092
```

---

# Kafka in Docker

Docker is the most common development setup.

---

# Why Docker?

Benefits:

```text
Easy Setup
Isolation
Portability
Fast Startup
```

---

# Simple Docker Compose

```yaml
version: '3'

services:
  kafka:
    image: apache/kafka:latest

    ports:
      - "9092:9092"

    environment:
      KAFKA_NODE_ID: 1
      KAFKA_PROCESS_ROLES: broker,controller
```

---

# Start Docker Kafka

```bash
docker-compose up -d
```

---

# Verify Containers

```bash
docker ps
```

---

# View Logs

```bash
docker logs kafka
```

---

# Stop Containers

```bash
docker-compose down
```

---

# Kafka in Kubernetes

Production Kafka often runs on Kubernetes.

---

# Benefits

```text
Auto Scaling
Self Healing
Rolling Updates
High Availability
```

---

# Deployment Options

---

## StatefulSet

Most common deployment.

```text
Kafka Pod 0
Kafka Pod 1
Kafka Pod 2
```

---

## Strimzi Operator

Most popular Kafka Kubernetes Operator.

---

### Architecture

```text
Kubernetes
     |
Strimzi Operator
     |
Kafka Cluster
```

---

# Deploy Kafka Using Strimzi

Install Operator:

```bash
kubectl apply -f strimzi-cluster-operator.yaml
```

---

Create Kafka Cluster:

```yaml
apiVersion: kafka.strimzi.io/v1beta2

kind: Kafka

metadata:
  name: my-cluster
```

---

Apply:

```bash
kubectl apply -f kafka-cluster.yaml
```

---

Verify:

```bash
kubectl get pods
```

---

# Kafka in Cloud Platforms

Managed Kafka services eliminate infrastructure management.

---

# AWS

---

## Amazon MSK

(Manged Streaming for Kafka)

Features:

```text
Automatic Scaling
Monitoring
Security
Backup
```

---

## Architecture

```text
Applications
      |
Amazon MSK
      |
Consumers
```

---

# Azure

---

## Azure Event Hubs for Kafka

Supports Kafka Protocol.

Applications can use Kafka clients.

---

# Google Cloud

---

## Google Managed Kafka

Managed Kafka service.

Provides:

```text
Scaling
Monitoring
Security
```

---

# Confluent Cloud

Most popular Kafka SaaS platform.

Provides:

```text
Kafka Cluster
Schema Registry
Connectors
Monitoring
ksqlDB
```

---

# Cloud Comparison

| Platform | Service |
|-----------|----------|
| AWS | MSK |
| Azure | Event Hubs |
| GCP | Managed Kafka |
| Confluent | Confluent Cloud |

---

# Kafka Startup Commands

---

# Start Kafka (KRaft Mode)

Linux/Mac:

```bash
bin/kafka-server-start.sh \
config/kraft/server.properties
```

---

Windows:

```cmd
bin\windows\kafka-server-start.bat ^
config\kraft\server.properties
```

---

# Start in Background

Linux:

```bash
nohup bin/kafka-server-start.sh \
config/kraft/server.properties &
```

---

# Start Producer

```bash
bin/kafka-console-producer.sh \
--topic orders \
--bootstrap-server localhost:9092
```

---

# Start Consumer

```bash
bin/kafka-console-consumer.sh \
--topic orders \
--bootstrap-server localhost:9092 \
--from-beginning
```

---

# Kafka Shutdown Commands

---

# Graceful Shutdown

Linux/Mac:

```bash
bin/kafka-server-stop.sh
```

---

Windows:

```cmd
Ctrl + C
```

or

```cmd
taskkill /F /PID <PID>
```

---

# Verify Shutdown

Linux:

```bash
ps -ef | grep kafka
```

No process should exist.

---

# Stop Docker Kafka

```bash
docker-compose down
```

---

# Stop Kubernetes Kafka

Delete cluster:

```bash
kubectl delete -f kafka-cluster.yaml
```

---

# Important Production Setup Checklist

Before running Kafka in production:

### Use Multiple Brokers

```text
Minimum 3 Brokers
```

---

### Use Replication

```text
Replication Factor = 3
```

---

### Enable Monitoring

```text
Prometheus
Grafana
JMX
```

---

### Configure Security

```text
SSL
SASL
ACL
```

---

### Use SSD Storage

Kafka is disk-intensive.

---

### Configure Backups

For disaster recovery.

---

### Tune Retention Policies

Avoid unnecessary storage growth.

---

# Interview Questions

## What are Kafka prerequisites?

- Java 17+
- Sufficient CPU/RAM
- Disk Storage
- Linux Preferred for Production

---

## What is the main Kafka configuration file?

```text
server.properties
```

---

## Which port does Kafka use by default?

```text
9092
```

---

## What replaced ZooKeeper in modern Kafka?

```text
KRaft (Kafka Raft Metadata Mode)
```

---

## Which managed Kafka service is available on AWS?

```text
Amazon MSK
```

---

## Which Kubernetes Operator is most commonly used for Kafka?

```text
Strimzi
```

---

# Summary

Kafka can be deployed in multiple environments:

- Local Machine
- Docker
- Kubernetes
- Cloud Platforms

The key setup steps are:

```text
Install Java
↓
Download Kafka
↓
Configure Broker
↓
Format Storage (KRaft)
↓
Start Kafka
↓
Create Topic
↓
Start Producer
↓
Start Consumer
```

Understanding installation and deployment is essential before moving to Kafka internals, brokers, topics, partitions, producers, consumers, and production cluster management.