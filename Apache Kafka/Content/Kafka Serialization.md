
Serialization is one of the most important concepts in Kafka because Kafka can only store and transmit data as:

```text
Bytes
```

Regardless of whether your application sends:

```text
String

JSON

Java Object

Avro Record

Protobuf Message
```

Kafka ultimately stores everything as:

```text
Byte Array (byte[])
```

Therefore, before sending data to Kafka:

```text
Object
   ↓
Serialization
   ↓
Bytes
```

And when consuming:

```text
Bytes
   ↓
Deserialization
   ↓
Object
```

Without serialization, Kafka would not know how to store application data.

---

# Why Serialization is Needed?

Suppose we have a Java object:

```java
User user =
    new User(
        101,
        "Sumit"
    );
```

---

Can Kafka store this object directly?

```text
No
```

Kafka understands only:

```text
byte[]
```

---

Therefore:

```text
User Object
       ↓
Serializer
       ↓
Byte Array
       ↓
Kafka Topic
```

---

Consumer:

```text
Byte Array
       ↓
Deserializer
       ↓
User Object
```

---

# What is Serialization?

## Definition

Serialization is the process of converting an object into a sequence of bytes that can be transmitted or stored.

---

# Deserialization

The reverse process.

---

Definition:

Converting bytes back into the original object.

---

# Visualization

```text
Producer

Java Object
      ↓
Serializer
      ↓
Bytes
      ↓
Kafka
      ↓
Bytes
      ↓
Deserializer
      ↓
Java Object

Consumer
```

---

# Kafka Serialization Flow

```text
Producer
     ↓
Serializer
     ↓
Byte Array
     ↓
Kafka Broker
     ↓
Byte Array
     ↓
Deserializer
     ↓
Consumer
```

---

# Kafka Serializer Interface

Kafka serializers implement:

```java
org.apache.kafka.common.serialization.Serializer
```

---

Simplified Interface:

```java
public interface Serializer<T> {

    byte[] serialize(
        String topic,
        T data
    );
}
```

---

Deserializer:

```java
public interface Deserializer<T> {

    T deserialize(
        String topic,
        byte[] data
    );
}
```

---

# Built-In Kafka Serializers

Kafka provides several serializers.

---

Common ones:

```text
StringSerializer

IntegerSerializer

LongSerializer

DoubleSerializer

ByteArraySerializer
```

---

# String Serializer

The most commonly used serializer.

---

# What is String Serializer?

Converts a String into bytes.

---

Example:

```java
"Hello Kafka"
```

---

Serialization:

```text
String
      ↓
UTF-8 Bytes
```

---

# Producer Configuration

```java
props.put(
    ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
    StringSerializer.class
);

props.put(
    ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
    StringSerializer.class
);
```

---

# Example

Producer:

```java
producer.send(
    new ProducerRecord<>(
        "orders",
        "Order Created"
    )
);
```

---

Internally:

```text
Order Created
       ↓
UTF-8 Encoding
       ↓
Byte Array
```

---

# Consumer Configuration

```java
props.put(
    ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG,
    StringDeserializer.class
);

props.put(
    ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,
    StringDeserializer.class
);
```

---

# Advantages

---

## Simple

---

## Lightweight

---

## Fast

---

## Human Readable

---

# Disadvantages

---

## Limited To Text Data

---

## Not Suitable For Complex Objects

---

# Use Cases

```text
Logs

Notifications

Events

Simple Messages
```

---

# JSON Serializer

One of the most widely used serializers.

---

# What is JSON Serializer?

Converts Java objects into JSON format.

---

Example Object:

```java
User(
    101,
    "Sumit"
)
```

---

Serialized JSON:

```json
{
  "id": 101,
  "name": "Sumit"
}
```

---

# Flow

```text
Java Object
       ↓
JSON
       ↓
Bytes
```

---

# Example Using Jackson

Object:

```java
User user =
    new User(
        101,
        "Sumit"
    );
```

---

Serialization:

```java
ObjectMapper mapper =
    new ObjectMapper();

String json =
    mapper.writeValueAsString(user);
```

---

Result:

```json
{
  "id":101,
  "name":"Sumit"
}
```

---

# Advantages

---

## Human Readable

---

## Language Independent

---

## Easy Debugging

---

## Widely Supported

---

# Disadvantages

---

## Larger Payload Size

---

## Slower Than Binary Formats

---

## Schema Enforcement Weak

---

# Example Kafka Message

```json
{
  "orderId": 1001,
  "amount": 5000
}
```

---

# Use Cases

```text
Microservices

REST Systems

Event-Driven Architectures

Application Integration
```

---

# JSON Serializer Architecture

```text
Java Object
      ↓
Jackson
      ↓
JSON
      ↓
Bytes
      ↓
Kafka
```

---

# Avro Serializer

One of the most important enterprise Kafka serializers.

---

# What is Avro?

Avro is a compact binary serialization framework developed by:

:contentReference[oaicite:0]{index=0}

---

Features:

```text
Schema Based

Compact

Fast

Language Independent
```

---

# Why Avro?

JSON has problems:

```text
Large Messages

No Strong Schema Enforcement

Schema Evolution Difficult
```

---

Avro solves these.

---

# Example Schema

```json
{
  "type": "record",
  "name": "User",
  "fields": [
    {
      "name": "id",
      "type": "int"
    },
    {
      "name": "name",
      "type": "string"
    }
  ]
}
```

---

# Serialization Flow

```text
Object
    ↓
Avro Schema
    ↓
Binary Format
    ↓
Kafka
```

---

# Example

Object:

```java
{
    "id":101,
    "name":"Sumit"
}
```

---

Stored as:

```text
Compact Binary Bytes
```

---

Not human-readable.

---

# Schema Registry

Avro commonly works with:

```text
Schema Registry
```

---

Purpose:

```text
Store Schemas

Manage Versions

Enable Compatibility
```

---

Common Architecture

```text
Producer
      ↓
Schema Registry
      ↓
Kafka
      ↓
Consumer
```

---

# Advantages

---

## Smaller Payload

---

## Faster Processing

---

## Strong Schema Enforcement

---

## Supports Schema Evolution

---

## Enterprise Standard

---

# Disadvantages

---

## More Complex Setup

---

## Schema Management Required

---

# Use Cases

```text
Large Kafka Deployments

Financial Systems

Data Pipelines

Enterprise Event Streaming
```

---

# Avro Example Size Comparison

JSON:

```json
{
  "id":101,
  "name":"Sumit"
}
```

---

May require:

```text
~30+ Bytes
```

---

Avro:

```text
Binary Representation
```

---

May require significantly fewer bytes.

---

# Protobuf Serializer

Another popular enterprise serialization format.

---

# What is Protobuf?

Protobuf (Protocol Buffers) is a serialization framework developed by:

:contentReference[oaicite:1]{index=1}

---

Features:

```text
Binary Format

Compact

Fast

Schema Based
```

---

# Protobuf Schema

Example:

```proto
syntax = "proto3";

message User {

  int32 id = 1;

  string name = 2;
}
```

---

# Serialization Flow

```text
Object
     ↓
Protobuf
     ↓
Binary Data
     ↓
Kafka
```

---

# Advantages

---

## Very Fast

---

## Compact Payload

---

## Strong Typing

---

## Backward Compatibility

---

## Cross Language Support

---

# Disadvantages

---

## Additional Complexity

---

## Schema Files Required

---

# Use Cases

```text
Microservices

High Throughput Systems

gRPC Applications

Real-Time Platforms
```

---

# Avro vs Protobuf

| Feature | Avro | Protobuf |
|----------|--------|----------|
| Format | Binary | Binary |
| Schema Required | Yes | Yes |
| Human Readable | No | No |
| Performance | High | Very High |
| Schema Evolution | Excellent | Excellent |
| Enterprise Usage | Very Common | Very Common |

---

# JSON vs Avro vs Protobuf

| Feature | JSON | Avro | Protobuf |
|----------|--------|--------|-----------|
| Readable | Yes | No | No |
| Payload Size | Large | Small | Very Small |
| Speed | Moderate | Fast | Very Fast |
| Schema Enforcement | Weak | Strong | Strong |
| Enterprise Ready | Yes | Yes | Yes |
| Storage Efficiency | Low | High | Very High |

---

# Custom Serializer

Sometimes built-in serializers are not enough.

---

# What is Custom Serializer?

A user-defined serializer that converts custom objects into bytes.

---

Used when:

```text
Custom Format Needed

Special Compression Needed

Legacy Systems Integration Needed
```

---

# Creating Custom Serializer

Implement:

```java
Serializer<T>
```

---

Example

```java
public class UserSerializer
        implements Serializer<User> {

    @Override
    public byte[] serialize(
            String topic,
            User user) {

        return (
            user.getId() + "," +
            user.getName()
        ).getBytes();
    }
}
```

---

# Custom Deserializer

```java
public class UserDeserializer
        implements Deserializer<User> {

    @Override
    public User deserialize(
            String topic,
            byte[] data) {

        String value =
            new String(data);

        String[] parts =
            value.split(",");

        return new User(
            Integer.parseInt(parts[0]),
            parts[1]
        );
    }
}
```

---

# Producer Configuration

```java
props.put(
    ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
    UserSerializer.class
);
```

---

# Consumer Configuration

```java
props.put(
    ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,
    UserDeserializer.class
);
```

---

# Advantages

---

## Full Control

---

## Custom Formats

---

## Specialized Optimization

---

# Disadvantages

---

## More Development Effort

---

## Harder Maintenance

---

## Potential Compatibility Issues

---

# Serialization Best Practices

---

## Use StringSerializer For Simple Text

---

## Use JSON For Simplicity

---

## Use Avro For Enterprise Systems

---

## Use Protobuf For High Performance

---

## Use Schema Registry For Schema-Based Formats

---

## Version Your Schemas

---

## Avoid Java Native Serialization

Example:

```java
Serializable
```

---

Reason:

```text
Large Payloads

Performance Issues

Language Dependency
```

---

# Real-World Examples

---

# Example 1: Logging System

Message:

```text
Application Started
```

Serializer:

```text
StringSerializer
```

---

# Example 2: E-Commerce Orders

Message:

```json
{
  "orderId":1001,
  "amount":5000
}
```

Serializer:

```text
JSON Serializer
```

---

# Example 3: Banking System

Message:

```text
Transaction Event
```

Serializer:

```text
Avro
```

---

Reason:

```text
Schema Validation

Compact Storage

Version Control
```

---

# Example 4: High-Frequency Trading

Message:

```text
Market Events
```

Serializer:

```text
Protobuf
```

---

Reason:

```text
Very Low Latency

Small Payload Size
```

---

# Serialization Performance Comparison

```text
Fastest
   ↓

Protobuf

Avro

JSON

String
```

---

# Payload Size Comparison

```text
Smallest
   ↓

Protobuf

Avro

JSON

String
```

---

# Common Interview Questions

## What is Serialization?

The process of converting an object into bytes for storage or transmission.

---

## Why is Serialization needed in Kafka?

Kafka stores and transmits data as:

```text
byte[]
```

---

## What is Deserialization?

Converting bytes back into the original object.

---

## Which serializer is most commonly used?

```text
StringSerializer

JSON Serializer
```

---

## What are the limitations of JSON?

```text
Large Payloads

Slower Processing

Weak Schema Enforcement
```

---

## Why is Avro popular in Kafka?

Because it provides:

```text
Compact Storage

Schema Evolution

Strong Validation
```

---

## What is Schema Registry?

A centralized repository that stores and manages schemas.

---

## What is Protobuf?

A binary serialization framework developed by Google.

---

## Which is faster: JSON or Protobuf?

```text
Protobuf
```

---

## When should you create a Custom Serializer?

When application-specific serialization logic is required.

---

# Summary

Kafka stores all data as:

```text
byte[]
```

Serialization converts application objects into bytes, while deserialization converts bytes back into objects.

Core Topics:

- What is Serialization?
- String Serializer
- JSON Serializer
- Avro Serializer
- Protobuf Serializer
- Custom Serializer

Serialization Flow:

```text
Object
   ↓
Serializer
   ↓
Bytes
   ↓
Kafka
   ↓
Bytes
   ↓
Deserializer
   ↓
Object
```

Comparison:

| Serializer | Readable | Performance | Payload Size |
|------------|-----------|------------|--------------|
| String | Yes | Good | Medium |
| JSON | Yes | Moderate | Large |
| Avro | No | Fast | Small |
| Protobuf | No | Very Fast | Very Small |
| Custom | Depends | Depends | Depends |

Production Recommendations:

```text
Simple Applications → JSON

Enterprise Kafka → Avro

High-Performance Systems → Protobuf

Special Cases → Custom Serializer
```

Serialization is a foundational Kafka concept because every message flowing through Kafka must eventually be transformed into bytes before being stored, replicated, transmitted, and consumed.