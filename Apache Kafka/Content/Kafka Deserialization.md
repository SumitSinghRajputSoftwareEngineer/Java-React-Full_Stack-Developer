
Deserialization is the reverse process of serialization.

In Kafka, producers send data as:

```text
Byte Array (byte[])
```

Consumers, however, usually want to work with:

```text
Strings

JSON Objects

Java Objects

Avro Records

Protobuf Messages
```

Therefore, Kafka consumers must convert bytes back into meaningful objects.

This process is called:

```text
Deserialization
```

Without deserialization, consumers would only receive raw bytes.

---

# Why Deserialization is Needed?

Producer sends:

```java
User(
    101,
    "Sumit"
)
```

---

Serialization:

```text
User Object
      ↓
Serializer
      ↓
byte[]
```

---

Kafka stores:

```text
byte[]
```

---

Consumer receives:

```text
byte[]
```

---

But application needs:

```java
User(
    101,
    "Sumit"
)
```

---

Therefore:

```text
byte[]
      ↓
Deserializer
      ↓
User Object
```

---

# What is Deserialization?

## Definition

Deserialization is the process of converting bytes into an application-readable object.

---

# Serialization vs Deserialization

| Serialization | Deserialization |
|--------------|----------------|
| Object → Bytes | Bytes → Object |
| Producer Side | Consumer Side |
| Before Sending | After Receiving |
| Uses Serializer | Uses Deserializer |

---

# Kafka Message Flow

```text
Producer
     ↓
Object
     ↓
Serializer
     ↓
byte[]
     ↓
Kafka Broker
     ↓
byte[]
     ↓
Deserializer
     ↓
Object
     ↓
Consumer
```

---

# Kafka Deserializer Interface

Kafka provides:

```java
org.apache.kafka.common.serialization.Deserializer
```

---

Simplified Interface:

```java
public interface Deserializer<T> {

    T deserialize(
        String topic,
        byte[] data
    );
}
```

---

Purpose:

```text
Convert Bytes Into Objects
```

---

# Consumer Configuration

Deserializers are configured when creating a consumer.

---

Example:

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

# Built-In Kafka Deserializers

Kafka provides:

```text
StringDeserializer

IntegerDeserializer

LongDeserializer

DoubleDeserializer

ByteArrayDeserializer
```

---

# String Deserializer

Most commonly used deserializer.

---

# What is String Deserializer?

Converts bytes into a String using UTF-8 encoding.

---

# Example

Producer sends:

```java
"Hello Kafka"
```

---

Serialized:

```text
byte[]
```

---

Consumer:

```java
String value =
    record.value();
```

---

Result:

```text
Hello Kafka
```

---

# Configuration

```java
props.put(
    ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,
    StringDeserializer.class
);
```

---

# Flow

```text
Bytes
   ↓
StringDeserializer
   ↓
String
```

---

# Advantages

---

## Simple

---

## Fast

---

## Human Readable

---

# Limitations

---

## Only For Text Data

---

## Cannot Parse Complex Objects

---

# Use Cases

```text
Logs

Notifications

Simple Events

Monitoring Messages
```

---

# JSON Deserializer

One of the most commonly used deserializers in microservices.

---

# What is JSON Deserializer?

Converts JSON bytes into Java objects.

---

Producer sends:

```json
{
  "id":101,
  "name":"Sumit"
}
```

---

Consumer receives:

```java
User(
    101,
    "Sumit"
)
```

---

# Flow

```text
Bytes
    ↓
JSON
    ↓
Java Object
```

---

# Example JSON

```json
{
  "id": 101,
  "name": "Sumit"
}
```

---

# Java Class

```java
public class User {

    private int id;

    private String name;
}
```

---

# Deserialization Using Jackson

```java
ObjectMapper mapper =
        new ObjectMapper();

User user =
        mapper.readValue(
            json,
            User.class
        );
```

---

# Kafka Flow

```text
Kafka Bytes
       ↓
Jackson
       ↓
User Object
```

---

# Spring Kafka Example

```java
@JsonDeserialize
```

or

```java
JsonDeserializer<User>
```

---

# Advantages

---

## Human Readable

---

## Easy Debugging

---

## Language Independent

---

## Easy Integration

---

# Disadvantages

---

## Larger Payload

---

## Slower Than Binary Formats

---

## Weak Schema Validation

---

# Use Cases

```text
REST Services

Microservices

Event-Based Systems
```

---

# Avro Deserializer

One of the most important enterprise Kafka deserializers.

---

# What is Avro Deserializer?

Converts Avro binary data back into objects using schemas.

---

Producer:

```text
Object
   ↓
Avro Serializer
   ↓
Binary
```

---

Consumer:

```text
Binary
   ↓
Avro Deserializer
   ↓
Object
```

---

# Example Avro Schema

```json
{
  "type": "record",
  "name": "User",
  "fields": [
    {
      "name":"id",
      "type":"int"
    },
    {
      "name":"name",
      "type":"string"
    }
  ]
}
```

---

# Flow

```text
Kafka Bytes
       ↓
Schema Registry
       ↓
Avro Deserializer
       ↓
User Object
```

---

# Why Schema Needed?

Binary data alone does not describe:

```text
Field Names

Types

Structure
```

---

Schema provides:

```text
Metadata
```

required to reconstruct objects.

---

# Example

Stored:

```text
Compact Binary Data
```

---

Deserialized:

```java
User(
    101,
    "Sumit"
)
```

---

# Advantages

---

## Fast

---

## Compact

---

## Strong Validation

---

## Supports Schema Evolution

---

# Disadvantages

---

## Requires Schema Management

---

## More Complex Setup

---

# Use Cases

```text
Enterprise Kafka

Data Pipelines

Financial Systems

Streaming Platforms
```

---

# Avro and Schema Registry

Typically used together.

---

Architecture:

```text
Producer
     ↓
Schema Registry
     ↓
Kafka
     ↓
Consumer
     ↓
Schema Registry
     ↓
Avro Deserializer
```

---

# Schema Evolution Example

Version 1:

```json
{
  "id":101
}
```

---

Version 2:

```json
{
  "id":101,
  "name":"Sumit"
}
```

---

Avro can handle compatibility rules.

---

# Protobuf Deserializer

A high-performance enterprise deserializer.

---

# What is Protobuf Deserializer?

Converts Protocol Buffer binary data into objects.

---

Producer:

```text
Object
     ↓
Protobuf Serializer
     ↓
Binary
```

---

Consumer:

```text
Binary
     ↓
Protobuf Deserializer
     ↓
Object
```

---

# Example Schema

```proto
syntax = "proto3";

message User {

  int32 id = 1;

  string name = 2;
}
```

---

# Flow

```text
Kafka Bytes
       ↓
Protobuf Schema
       ↓
User Object
```

---

# Example

Received:

```text
Binary Data
```

---

Deserialized:

```java
User(
    101,
    "Sumit"
)
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

## Schema Evolution Support

---

## Efficient Network Usage

---

# Disadvantages

---

## Additional Complexity

---

## Requires Schema Definitions

---

# Use Cases

```text
gRPC Systems

High Throughput Applications

Real-Time Streaming

Microservices
```

---

# Avro vs Protobuf Deserialization

| Feature | Avro | Protobuf |
|----------|--------|----------|
| Schema Based | Yes | Yes |
| Binary Format | Yes | Yes |
| Performance | Fast | Very Fast |
| Ecosystem | Kafka-Centric | Broad Industry Usage |
| Schema Evolution | Excellent | Excellent |

---

# Custom Deserializer

Sometimes built-in deserializers are not enough.

---

# What is Custom Deserializer?

A user-defined deserializer that converts bytes into custom objects.

---

Used when:

```text
Custom Binary Format

Legacy Systems

Special Parsing Logic
```

---

# Create Custom Deserializer

Implement:

```java
Deserializer<T>
```

---

# Example

Producer stores:

```text
101,Sumit
```

---

Custom Deserializer:

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

# Consumer Configuration

```java
props.put(
    ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,
    UserDeserializer.class
);
```

---

# Flow

```text
Bytes
    ↓
Custom Logic
    ↓
Object
```

---

# Advantages

---

## Full Control

---

## Custom Formats

---

## Legacy Compatibility

---

# Disadvantages

---

## More Code

---

## Maintenance Overhead

---

## Compatibility Challenges

---

# Deserialization Error Handling

A common production concern.

---

# Example

Producer sends:

```json
{
  "id":101
}
```

---

Consumer expects:

```json
{
  "id":101,
  "name":"Sumit"
}
```

---

Result:

```text
Deserialization Exception
```

---

# Common Causes

---

## Schema Changes

---

## Corrupted Messages

---

## Wrong Serializer/Deserializer Pair

---

## Version Mismatch

---

# Example Problem

Producer:

```java
StringSerializer
```

---

Consumer:

```java
JsonDeserializer
```

---

Result:

```text
Failure
```

---

Because formats differ.

---

# Error Handling Strategies

---

## Dead Letter Topic (DLT)

Invalid messages moved to:

```text
error-topic
```

---

## Retry Processing

---

## Validation Before Consumption

---

## Schema Registry Compatibility Checks

---

# Serializer and Deserializer Matching

Very important interview topic.

---

Producer and consumer must agree on format.

---

Correct:

```text
StringSerializer
        ↓
StringDeserializer
```

---

Correct:

```text
JSON Serializer
        ↓
JSON Deserializer
```

---

Correct:

```text
Avro Serializer
        ↓
Avro Deserializer
```

---

Correct:

```text
Protobuf Serializer
        ↓
Protobuf Deserializer
```

---

Incorrect:

```text
JSON Serializer
        ↓
StringDeserializer
```

May work as plain text but loses object mapping.

---

Incorrect:

```text
Avro Serializer
        ↓
JSON Deserializer
```

Will fail.

---

# Real-World Examples

---

# Example 1: Log Processing

Producer:

```text
Application Started
```

Deserializer:

```text
StringDeserializer
```

---

# Example 2: Order Service

Producer:

```json
{
  "orderId":1001,
  "amount":5000
}
```

Deserializer:

```text
JSON Deserializer
```

---

# Example 3: Banking Platform

Producer:

```text
Transaction Events
```

Deserializer:

```text
Avro Deserializer
```

---

Reason:

```text
Strong Schema Validation
```

---

# Example 4: Trading Platform

Producer:

```text
Market Events
```

Deserializer:

```text
Protobuf Deserializer
```

---

Reason:

```text
Ultra-Low Latency
```

---

# Deserializer Selection Guide

| Scenario | Recommended Deserializer |
|-----------|--------------------------|
| Simple Text | StringDeserializer |
| REST/Microservices | JSON Deserializer |
| Enterprise Streaming | Avro Deserializer |
| High Performance Systems | Protobuf Deserializer |
| Special Formats | Custom Deserializer |

---

# Best Practices

---

## Match Serializer and Deserializer

---

## Use Schema Registry With Avro

---

## Use Schema Registry With Protobuf

---

## Handle Deserialization Errors Gracefully

---

## Use Dead Letter Topics

---

## Version Schemas Carefully

---

## Test Backward Compatibility

---

## Avoid Native Java Serialization

---

# Common Interview Questions

## What is Deserialization?

The process of converting bytes into application-readable objects.

---

## Why is Deserialization needed in Kafka?

Kafka stores data as:

```text
byte[]
```

Applications need objects.

---

## What is StringDeserializer?

A deserializer that converts bytes into Strings.

---

## What is JSON Deserializer?

A deserializer that converts JSON data into Java objects.

---

## Why is Avro Deserializer popular?

Because it provides:

```text
Compact Storage

Schema Validation

Schema Evolution
```

---

## What is Protobuf Deserializer?

A deserializer that converts Protocol Buffer binary data into objects.

---

## What is a Custom Deserializer?

A user-defined deserializer used for custom formats.

---

## What happens if Serializer and Deserializer don't match?

Deserialization failures or incorrect data interpretation.

---

## Why is Schema Registry important?

It manages schemas and ensures compatibility between producers and consumers.

---

## Which deserializer is best for enterprise Kafka?

Typically:

```text
Avro Deserializer
```

or

```text
Protobuf Deserializer
```

depending on organizational standards.

---

# Summary

Deserialization is the process of converting Kafka message bytes into application-readable objects.

Core Topics:

- String Deserializer
- JSON Deserializer
- Avro Deserializer
- Protobuf Deserializer
- Custom Deserializer

Deserialization Flow:

```text
Kafka Bytes
      ↓
Deserializer
      ↓
Object
```

Common Types:

```text
StringDeserializer

JSON Deserializer

Avro Deserializer

Protobuf Deserializer

Custom Deserializer
```

Key Rule:

```text
Serializer And Deserializer
Must Match
```

Production Recommendation:

```text
Simple Systems → String/JSON

Enterprise Kafka → Avro

High-Performance Systems → Protobuf

Special Cases → Custom
```

Proper deserialization is essential because every Kafka consumer depends on it to transform raw message bytes into usable business objects, events, and domain models.