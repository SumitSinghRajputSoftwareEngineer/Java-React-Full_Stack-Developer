# Bridge Design Pattern

> **Goal:** Understand Bridge Design Pattern from scratch, why it exists, how it works internally, Abstraction vs Implementation, Refined Abstraction, multiple implementations, real-world examples, Spring usage, advantages, disadvantages, interview questions, and most importantly how it differs from Adapter.

---

# 1. What Problem Does Bridge Pattern Solve?

Suppose you are building a Notification System.

You have:

```text
Email Notification
SMS Notification
Push Notification
```

And notifications can be sent through:

```text
AWS
Azure
GCP
```

---

Without Bridge Pattern:

You may create:

```text
EmailViaAWS
EmailViaAzure
EmailViaGCP

SMSViaAWS
SMSViaAzure
SMSViaGCP

PushViaAWS
PushViaAzure
PushViaGCP
```

---

Total Classes:

```text
3 Notification Types
×
3 Providers

=
9 Classes
```

---

Now suppose a new provider arrives:

```text
DigitalOcean
```

Need:

```text
EmailViaDigitalOcean
SMSViaDigitalOcean
PushViaDigitalOcean
```

More classes.

---

As combinations increase:

```text
Class Explosion
```

occurs.

---

# 2. Definition

> Bridge Pattern separates Abstraction from Implementation so that both can vary independently.

---

# 3. Simple Meaning

Instead of:

```text
Notification + Provider
```

being tightly coupled,

Separate them.

---

### Before Bridge

```text
EmailViaAWS
EmailViaAzure
EmailViaGCP
```

---

### After Bridge

```text
Notification
      ↓
Provider
```

Each evolves independently.

---

# 4. Real-Life Example

## Remote Control and TV

Remote:

```text
Basic Remote
Advanced Remote
```

TV:

```text
Sony TV
Samsung TV
LG TV
```

---

Without Bridge:

```text
BasicSonyRemote
BasicSamsungRemote
BasicLGRemote

AdvancedSonyRemote
AdvancedSamsungRemote
AdvancedLGRemote
```

---

Many classes.

---

With Bridge:

```text
Remote
    ↓
TV
```

Remote works with any TV.

---

### Structure

```text
Remote
   ↓
TV Interface
   ↓
Sony TV

or

Samsung TV

or

LG TV
```

---

# 5. Core Idea

Bridge Pattern divides system into two hierarchies.

---

## Abstraction Hierarchy

What client sees.

Example:

```text
Remote
```

---

## Implementation Hierarchy

Actual implementation.

Example:

```text
TV
```

---

### Diagram

```text
Abstraction
     ↓
Implementation
```

---

# 6. Important Terminology

---

## 1. Abstraction

High-level interface.

Example:

```java
Notification
```

---

## 2. Refined Abstraction

Extended abstraction.

Example:

```java
UrgentNotification
```

---

## 3. Implementor

Implementation interface.

Example:

```java
MessageSender
```

---

## 4. Concrete Implementor

Actual implementation.

Example:

```java
AWSSender
AzureSender
```

---

# 7. Real-World Example

## Notification System

---

### Step 1: Create Implementor

```java
public interface MessageSender {

    void sendMessage(String message);
}
```

---

# Step 2: Create Concrete Implementors

---

## AWS Sender

```java
public class AWSSender
        implements MessageSender {

    @Override
    public void sendMessage(
            String message) {

        System.out.println(
            "AWS : " + message);
    }
}
```

---

## Azure Sender

```java
public class AzureSender
        implements MessageSender {

    @Override
    public void sendMessage(
            String message) {

        System.out.println(
            "Azure : " + message);
    }
}
```

---

# Step 3: Create Abstraction

```java
public abstract class Notification {

    protected MessageSender sender;

    public Notification(
            MessageSender sender) {

        this.sender = sender;
    }

    public abstract void send(
            String message);
}
```

---

Notice:

```java
Notification
```

contains:

```java
MessageSender
```

This is the bridge.

---

# Step 4: Create Refined Abstractions

---

## Normal Notification

```java
public class NormalNotification
        extends Notification {

    public NormalNotification(
            MessageSender sender) {

        super(sender);
    }

    @Override
    public void send(String message) {

        sender.sendMessage(message);
    }
}
```

---

## Urgent Notification

```java
public class UrgentNotification
        extends Notification {

    public UrgentNotification(
            MessageSender sender) {

        super(sender);
    }

    @Override
    public void send(String message) {

        sender.sendMessage(
            "[URGENT] " + message);
    }
}
```

---

# Step 5: Client Code

---

## AWS + Normal Notification

```java
Notification notification =
        new NormalNotification(
                new AWSSender());

notification.send("Order Placed");
```

Output:

```text
AWS : Order Placed
```

---

## Azure + Urgent Notification

```java
Notification notification =
        new UrgentNotification(
                new AzureSender());

notification.send("Server Down");
```

Output:

```text
Azure : [URGENT] Server Down
```

---

# 8. Internal Working

Client creates:

```java
new UrgentNotification(
    new AWSSender()
);
```

---

Memory:

```text
UrgentNotification
        ↓
     AWSSender
```

---

Execution:

```text
Notification.send()
         ↓
AWSSender.sendMessage()
```

---

# 9. Why Is It Called "Bridge"?

Because:

```java
Notification
```

and

```java
MessageSender
```

are separate hierarchies.

---

Bridge connects them.

```text
Notification
      ↕
 MessageSender
```

---

Hence:

```text
Bridge Pattern
```

---

# 10. Class Explosion Problem

Without Bridge:

```text
EmailAWS
EmailAzure
EmailGCP

SMSAWS
SMSAzure
SMSGCP

PushAWS
PushAzure
PushGCP
```

9 classes.

---

With Bridge:

```text
Email
SMS
Push

AWS
Azure
GCP
```

Only:

```text
6 Classes
```

---

As system grows:

Bridge saves hundreds of classes.

---

# 11. UML Structure

```text
             Notification
                    |
         ---------------------
         |                   |
      Normal           Urgent

                    |
                    |
              MessageSender
                    |
        -----------------------
        |                     |
      AWS                 Azure
```

---

# 12. Composition Over Inheritance

Bridge heavily uses:

```text
Composition
```

---

Instead of:

```java
class AWSNotification
```

use:

```java
Notification
    HAS-A
MessageSender
```

---

Important Interview Point.

---

# 13. Real-Life Example: Remote and TV

---

## Implementor

```java
interface TV
```

---

Implementations:

```java
SonyTV
SamsungTV
LGTV
```

---

## Abstraction

```java
abstract class Remote
```

---

Refined Abstractions:

```java
BasicRemote
AdvancedRemote
```

---

Usage:

```java
Remote remote =
        new AdvancedRemote(
                new SonyTV());
```

---

Any remote works with any TV.

---

# 14. Bridge vs Adapter

Most commonly confused patterns.

---

## Adapter

Problem:

```text
Interfaces don't match.
```

Goal:

```text
Convert Interface.
```

---

Example:

```text
USB Adapter
```

---

### Structure

```text
Client
   ↓
Adapter
   ↓
Old System
```

---

## Bridge

Problem:

```text
Class Explosion
```

Goal:

```text
Separate Abstraction and Implementation.
```

---

### Structure

```text
Abstraction
    ↓
Implementation
```

---

### Easy Memory Trick

```text
Adapter
     ↓
Convert

Bridge
     ↓
Separate
```

---

# 15. Bridge vs Strategy

Another interview favorite.

---

## Strategy

Changes algorithm.

---

Example:

```text
Credit Card Payment
UPI Payment
PayPal Payment
```

Different algorithms.

---

## Bridge

Changes implementation hierarchy.

---

Example:

```text
Notification
    +
Provider
```

---

### Memory Trick

```text
Strategy
    ↓
Algorithm

Bridge
    ↓
Implementation
```

---

# 16. Java Examples

Java doesn't have a direct textbook Bridge example.

But concept appears in:

---

## JDBC

Abstraction:

```java
Connection
Statement
```

---

Implementations:

```text
MySQL Driver
Oracle Driver
PostgreSQL Driver
```

---

Application code:

```java
Connection
```

doesn't care about database vendor.

---

Bridge-like architecture.

---

# 17. Spring Framework Examples

---

## JDBC Template

:contentReference[oaicite:0]{index=0}

Abstraction:

```java
JdbcTemplate
```

---

Implementation:

```text
MySQL
Oracle
PostgreSQL
```

---

## Logging Frameworks

Abstraction:

```java
Logger
```

Implementation:

```text
Logback
Log4j
JUL
```

---

Bridge-style separation.

---

# 18. Advantages

---

## 1. Avoids Class Explosion

Biggest advantage.

---

## 2. Independent Evolution

Can add:

```java
New Notification
```

without changing providers.

---

Can add:

```java
New Provider
```

without changing notifications.

---

## 3. Loose Coupling

Abstraction independent of implementation.

---

## 4. Better Maintainability

Cleaner architecture.

---

## 5. Follows Open/Closed Principle

Add new abstractions or implementations easily.

---

# 19. Disadvantages

---

## More Classes

Bridge introduces:

```text
Abstraction
Implementation
Concrete Classes
```

---

## Higher Complexity

For small systems it may be overkill.

---

## Harder To Understand Initially

Especially for beginners.

---

# 20. When To Use Bridge Pattern

Use when:

- Two independent dimensions exist
- Class explosion is happening
- Abstraction and implementation change separately

Examples:

```text
Notification + Provider
Remote + TV
Shape + Color
Vehicle + Engine
Report + Export Format
```

---

# 21. When NOT To Use

Avoid when:

- Only one implementation exists
- No class explosion risk
- Simple inheritance works fine

---

# 22. Interview Questions

### Q1. What problem does Bridge solve?

```text
Class Explosion
```

caused by multiple dimensions of variation.

---

### Q2. Why is it called Bridge?

Because it bridges:

```text
Abstraction
      ↕
Implementation
```

---

### Q3. What principle does Bridge follow?

```text
Composition Over Inheritance
```

---

### Q4. Difference Between Adapter and Bridge?

Adapter:

```text
Convert Interface
```

Bridge:

```text
Separate Abstraction and Implementation
```

---

### Q5. Difference Between Bridge and Strategy?

Strategy:

```text
Change Algorithm
```

Bridge:

```text
Change Implementation Hierarchy
```

---

### Q6. Give a real-life example.

```text
Remote + TV
Notification + Provider
Shape + Color
```

---

# Real-Life Memory Trick

```text
Singleton
    ↓
One Object

Factory
    ↓
Create Object

Abstract Factory
    ↓
Create Family

Builder
    ↓
Build Step By Step

Prototype
    ↓
Clone Object

Adapter
    ↓
Convert Interface

Bridge
    ↓
Separate Abstraction
from
Implementation
```

---

# Quick Revision

```text
Bridge Design Pattern
---------------------

Purpose:
Separate abstraction from implementation.

Problem Solved:
Class Explosion

Structure:

Abstraction
      ↓
Implementation

Key Components:

1. Abstraction
2. Refined Abstraction
3. Implementor
4. Concrete Implementor

Advantages:

✔ Avoids Class Explosion
✔ Loose Coupling
✔ Easy Extension
✔ Better Maintainability

Disadvantages:

✘ More Classes
✘ More Complexity

Examples:

Remote + TV
Notification + Provider
Shape + Color
Logger + Logging Framework

Remember:

Adapter
    ↓
Convert Interface

Bridge
    ↓
Separate Abstraction
and Implementation
```