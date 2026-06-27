# Facade Design Pattern

> **Goal:** Understand Facade Design Pattern from scratch, why it exists, how it works internally, Simple Facade vs Layered Facade, Real-world examples, Spring usage, Microservices usage, advantages, disadvantages, interview questions, and how it differs from Adapter, Proxy, and Mediator.

---

# 1. What Problem Does Facade Pattern Solve?

Suppose you want to watch a movie at home.

To start the movie, you need to:

```text
Turn On TV
Turn On Sound System
Turn On Streaming Device
Set HDMI Source
Connect WiFi
Open Netflix
Play Movie
```

---

Without Facade:

```java
tv.on();

soundSystem.on();

streamingDevice.on();

streamingDevice.connectWifi();

netflix.open();

movie.play();
```

---

Client must know:

```text
Every Object
Every Method
Execution Order
```

---

Very complex.

---

Instead:

Create:

```java
homeTheater.watchMovie();
```

---

One method does everything.

---

This is Facade Pattern.

---

# 2. Definition

> Facade Pattern provides a simplified interface to a complex subsystem.

---

# 3. Simple Meaning

Instead of:

```text
Client
 ↓
10 Different Classes
```

Use:

```text
Client
 ↓
Facade
 ↓
10 Different Classes
```

---

Client sees:

```text
One Simple Interface
```

---

Complexity stays hidden.

---

# 4. Real-Life Examples

---

## Example 1: ATM Machine

When you withdraw cash:

```text
Insert Card
Validate Card
Verify PIN
Check Balance
Deduct Balance
Dispense Cash
Print Receipt
```

---

You simply:

```text
Withdraw Money
```

---

ATM acts as:

```text
Facade
```

---

# Example 2: Restaurant Waiter

Customer:

```text
Orders Food
```

---

Waiter internally contacts:

```text
Chef
Billing
Inventory
Kitchen
```

---

Customer doesn't know.

---

Waiter acts as:

```text
Facade
```

---

# Example 3: Mobile App

You click:

```text
Book Cab
```

Internally:

```text
GPS Service
Payment Service
Driver Service
Notification Service
```

work together.

---

App screen acts as:

```text
Facade
```

---

# 5. Core Idea

Facade hides:

```text
Complexity
```

and exposes:

```text
Simple API
```

---

# 6. Structure

```text
Client
   ↓
Facade
   ↓
Subsystem Classes
```

---

# 7. Components

---

## 1. Client

Uses the system.

---

## 2. Facade

Provides simplified interface.

---

## 3. Subsystem

Actual business classes.

---

Example:

```text
HomeTheaterFacade
```

calls:

```text
TV
SoundSystem
Netflix
Projector
```

---

# 8. Real-World Example

# Home Theater System

---

## Step 1: Create Subsystems

---

### TV

```java
public class TV {

    public void on() {
        System.out.println("TV ON");
    }
}
```

---

### Sound System

```java
public class SoundSystem {

    public void on() {
        System.out.println(
                "Sound System ON");
    }
}
```

---

### Netflix

```java
public class Netflix {

    public void playMovie() {

        System.out.println(
                "Movie Started");
    }
}
```

---

# Step 2: Create Facade

```java
public class HomeTheaterFacade {

    private TV tv;

    private SoundSystem soundSystem;

    private Netflix netflix;

    public HomeTheaterFacade(
            TV tv,
            SoundSystem soundSystem,
            Netflix netflix) {

        this.tv = tv;
        this.soundSystem = soundSystem;
        this.netflix = netflix;
    }

    public void watchMovie() {

        tv.on();

        soundSystem.on();

        netflix.playMovie();
    }
}
```

---

# Step 3: Client

```java
HomeTheaterFacade homeTheater =
        new HomeTheaterFacade(
                new TV(),
                new SoundSystem(),
                new Netflix());

homeTheater.watchMovie();
```

---

Output:

```text
TV ON

Sound System ON

Movie Started
```

---

# 9. Internal Working

Client calls:

```java
watchMovie()
```

---

Flow:

```text
Client
   ↓
Facade
   ↓
TV
   ↓
Sound System
   ↓
Netflix
```

---

Client knows only:

```java
watchMovie()
```

---

# 10. Why Is It Called Facade?

In architecture:

```text
Facade
```

means:

```text
Front Face Of Building
```

---

You see only:

```text
Outside Appearance
```

---

Not internal complexity.

---

Same concept here.

---

# 11. UML Structure

```text
            Client
               ↓
       HomeTheaterFacade
               ↓
     --------------------
     ↓         ↓        ↓

    TV     Sound      Netflix
```

---

# 12. Real-Life Example

# E-Commerce Checkout

Without Facade:

```java
inventoryService.checkStock();

paymentService.process();

notificationService.send();

shippingService.createOrder();
```

---

Client manages everything.

---

With Facade:

```java
checkoutFacade.placeOrder();
```

---

Facade internally calls:

```text
Inventory Service
Payment Service
Shipping Service
Notification Service
```

---

# 13. Facade in Microservices

This is one of the most common real-world usages.

---

Suppose:

```text
Order Service
Payment Service
Inventory Service
Shipping Service
```

exist.

---

Frontend should not call:

```text
4 Services
```

individually.

---

Create:

```text
OrderFacade
```

---

Flow:

```text
Frontend
     ↓
OrderFacade
     ↓
Microservices
```

---

Advantages:

```text
Single Entry Point
```

---

# 14. Layered Facade

Large systems often have:

```text
Controller
    ↓
Facade
    ↓
Service
    ↓
Repository
```

---

Example:

```java
OrderController
```

calls

```java
OrderFacade
```

which calls

```java
OrderService
```

---

Common in enterprise applications.

---

# 15. Spring Boot Example

Very common.

---

### Facade Layer

```java
@RestController
public class OrderController {

}
```

---

Calls:

```java
OrderFacade
```

---

Facade internally calls:

```java
PaymentService

InventoryService

ShippingService
```

---

Example:

```java
@Component
public class OrderFacade {

    public void placeOrder() {

        inventoryService.check();

        paymentService.pay();

        shippingService.ship();
    }
}
```

---

# 16. Java Examples

Java doesn't have an explicit Facade class.

But many APIs use the idea.

---

## JDBC

You use:

```java
DriverManager
```

instead of interacting with low-level driver internals.

---

Facade-like behavior.

---

## Spring JdbcTemplate

:contentReference[oaicite:0]{index=0}

Simplifies:

```text
Connection
Statement
ResultSet
Exception Handling
```

into:

```java
jdbcTemplate.query(...)
```

---

Classic Facade idea.

---

# 17. Advantages

---

## 1. Simplifies Client Code

Client sees:

```java
placeOrder()
```

instead of:

```java
inventory()

payment()

shipping()

notification()
```

---

## 2. Reduces Coupling

Client doesn't depend on subsystem classes.

---

## 3. Hides Complexity

Internal implementation hidden.

---

## 4. Improves Maintainability

Subsystem changes don't affect client.

---

## 5. Better Readability

Cleaner APIs.

---

# 18. Disadvantages

---

## 1. Facade Can Become God Class

Bad design:

```java
HugeFacade
```

with:

```text
100 Methods
```

---

Difficult to maintain.

---

## 2. Extra Layer

Adds another abstraction layer.

---

## 3. Can Hide Useful Features

Client only sees exposed methods.

---

# 19. Facade vs Adapter

Most asked interview question.

---

## Adapter

Goal:

```text
Convert Interface
```

---

Example:

```text
USB Adapter
```

---

Structure:

```text
Client
 ↓
Adapter
 ↓
Old System
```

---

## Facade

Goal:

```text
Simplify Interface
```

---

Structure:

```text
Client
 ↓
Facade
 ↓
Complex System
```

---

### Memory Trick

```text
Adapter
    ↓
Convert

Facade
    ↓
Simplify
```

---

# 20. Facade vs Proxy

Another interview favorite.

---

## Facade

Provides:

```text
Simplified Access
```

---

## Proxy

Provides:

```text
Controlled Access
```

---

Example:

Facade:

```java
placeOrder()
```

---

Proxy:

```java
checkPermission()

then

placeOrder()
```

---

### Memory Trick

```text
Facade
   ↓
Simplify

Proxy
   ↓
Control
```

---

# 21. Facade vs Mediator

Very commonly confused.

---

## Facade

Client talks to:

```text
One Object
```

which hides subsystem complexity.

---

## Mediator

Objects communicate through:

```text
Central Coordinator
```

---

Facade:

```text
Simplify Access
```

---

Mediator:

```text
Manage Communication
```

---

# 22. Facade vs Service Layer

Important in Spring interviews.

---

## Service Layer

Contains:

```text
Business Logic
```

---

## Facade

Contains:

```text
Workflow Coordination
```

---

Example:

```java
OrderFacade
```

calls:

```java
InventoryService

PaymentService

ShippingService
```

---

Facade orchestrates.

Services execute.

---

# 23. When To Use Facade

Use when:

- System is complex
- Multiple subsystems exist
- Client needs simple API
- Microservices must be orchestrated

Examples:

```text
Checkout System
Home Theater
ATM
Travel Booking
Microservices Gateway
```

---

# 24. When NOT To Use

Avoid when:

- System is already simple
- Only one subsystem exists

Facade adds unnecessary complexity.

---

# 25. Interview Questions

### Q1. What problem does Facade solve?

It hides subsystem complexity behind a simple interface.

---

### Q2. What are the participants?

```text
Client
Facade
Subsystem Classes
```

---

### Q3. Does Facade add functionality?

No.

It only:

```text
Simplifies Access
```

---

### Q4. Difference Between Facade and Adapter?

Adapter:

```text
Converts Interface
```

Facade:

```text
Simplifies Interface
```

---

### Q5. Difference Between Facade and Proxy?

Facade:

```text
Simplifies
```

Proxy:

```text
Controls
```

---

### Q6. Is Facade a God Object?

Not necessarily.

Poor design can make it one.

---

### Q7. Give Real-Life Examples.

```text
ATM
Restaurant Waiter
Checkout System
Home Theater
API Gateway
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

Builder
    ↓
Build Object

Prototype
    ↓
Clone Object

Adapter
    ↓
Convert Interface

Bridge
    ↓
Separate Abstraction
and Implementation

Composite
    ↓
Tree Structure

Facade
    ↓
Hide Complexity
Provide Simple Interface
```

---

# Quick Revision

```text
Facade Design Pattern
---------------------

Purpose:
Provide a simple interface
to a complex subsystem.

Structure:

Client
   ↓
Facade
   ↓
Subsystem Classes

Problem Solved:

✔ Complexity
✔ Tight Coupling
✔ Difficult APIs

Types:

1. Simple Facade
2. Layered Facade
3. Microservice Facade

Advantages:

✔ Simplifies Code
✔ Reduces Coupling
✔ Hides Complexity
✔ Better Maintainability

Disadvantages:

✘ Extra Layer
✘ Can Become God Class

Examples:

ATM
Restaurant Waiter
Home Theater
Checkout System
API Gateway

Remember:

Adapter
     ↓
Convert

Facade
     ↓
Simplify

Proxy
     ↓
Control
```