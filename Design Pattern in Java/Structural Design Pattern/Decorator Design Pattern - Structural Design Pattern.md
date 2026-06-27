# Decorator Design Pattern

> **Goal:** Understand Decorator Design Pattern from scratch, why it exists, how it works internally, Dynamic Behavior Addition, Multiple Decorators, Java I/O examples, Spring usage, advantages, disadvantages, interview questions, and how it differs from Adapter, Proxy, and Composite.

---

# 1. What Problem Does Decorator Pattern Solve?

Suppose you own a coffee shop.

Base coffee:

```text
Simple Coffee
```

Customer wants:

```text
Coffee + Milk
```

Another customer wants:

```text
Coffee + Milk + Sugar
```

Another customer wants:

```text
Coffee + Milk + Sugar + Chocolate
```

---

Without Decorator Pattern:

You may create:

```text
SimpleCoffee

MilkCoffee

SugarCoffee

ChocolateCoffee

MilkSugarCoffee

MilkChocolateCoffee

SugarChocolateCoffee

MilkSugarChocolateCoffee
```

---

As toppings increase:

```text
Class Explosion
```

happens.

---

Very difficult to maintain.

---

Need a better solution.

---

# 2. Definition

> Decorator Pattern attaches additional responsibilities to an object dynamically without modifying its structure.

---

# 3. Simple Meaning

Instead of creating:

```text
MilkCoffee
```

or

```text
MilkSugarCoffee
```

---

Create:

```text
Coffee
    ↓
MilkDecorator
    ↓
SugarDecorator
    ↓
ChocolateDecorator
```

---

Add features at runtime.

---

# 4. Real-Life Example

## Gift Wrapping

Suppose you buy:

```text
Mobile Phone
```

---

Add:

```text
Gift Wrap
```

---

Add:

```text
Greeting Card
```

---

Add:

```text
Premium Box
```

---

Original item remains:

```text
Mobile Phone
```

---

But behavior/features increase.

---

This is Decorator Pattern.

---

# 5. Core Idea

Decorator wraps another object.

---

Structure:

```text
Object
   ↓
Decorator
   ↓
Decorator
   ↓
Decorator
```

---

Each decorator adds behavior.

---

# 6. Components

Decorator Pattern has four main components.

---

## 1. Component

Common interface.

Example:

```java
Coffee
```

---

## 2. Concrete Component

Actual object.

Example:

```java
SimpleCoffee
```

---

## 3. Decorator

Abstract wrapper.

Example:

```java
CoffeeDecorator
```

---

## 4. Concrete Decorator

Adds new functionality.

Example:

```java
MilkDecorator
SugarDecorator
```

---

# 7. UML Structure

```text
            Coffee
               ↑
               │
        SimpleCoffee

               ↑
               │
       CoffeeDecorator
               ↑
      ----------------
      ↓              ↓

MilkDecorator   SugarDecorator
```

---

# 8. Real-World Example

# Coffee Shop

---

## Step 1: Create Component

```java
public interface Coffee {

    String getDescription();

    double getCost();
}
```

---

# Step 2: Create Concrete Component

```java
public class SimpleCoffee
        implements Coffee {

    @Override
    public String getDescription() {

        return "Simple Coffee";
    }

    @Override
    public double getCost() {

        return 100;
    }
}
```

---

# Step 3: Create Decorator

```java
public abstract class CoffeeDecorator
        implements Coffee {

    protected Coffee coffee;

    public CoffeeDecorator(
            Coffee coffee) {

        this.coffee = coffee;
    }
}
```

---

Notice:

```java
CoffeeDecorator
```

contains:

```java
Coffee
```

---

This is the most important line.

---

```java
protected Coffee coffee;
```

---

Decorator wraps another Coffee.

---

# Step 4: Create Milk Decorator

```java
public class MilkDecorator
        extends CoffeeDecorator {

    public MilkDecorator(
            Coffee coffee) {

        super(coffee);
    }

    @Override
    public String getDescription() {

        return coffee.getDescription()
                + ", Milk";
    }

    @Override
    public double getCost() {

        return coffee.getCost() + 20;
    }
}
```

---

# Step 5: Create Sugar Decorator

```java
public class SugarDecorator
        extends CoffeeDecorator {

    public SugarDecorator(
            Coffee coffee) {

        super(coffee);
    }

    @Override
    public String getDescription() {

        return coffee.getDescription()
                + ", Sugar";
    }

    @Override
    public double getCost() {

        return coffee.getCost() + 10;
    }
}
```

---

# Step 6: Client Code

```java
Coffee coffee =
        new SimpleCoffee();

coffee =
        new MilkDecorator(coffee);

coffee =
        new SugarDecorator(coffee);

System.out.println(
        coffee.getDescription());

System.out.println(
        coffee.getCost());
```

---

Output:

```text
Simple Coffee, Milk, Sugar

130
```

---

# 9. Internal Working

Memory:

```text
SugarDecorator
       ↓
MilkDecorator
       ↓
SimpleCoffee
```

---

When:

```java
coffee.getCost()
```

executes.

---

Flow:

```text
SugarDecorator
       ↓
MilkDecorator
       ↓
SimpleCoffee
```

---

Calculation:

```text
100
 +
20
 +
10

=
130
```

---

This chaining behavior is the heart of Decorator Pattern.

---

# 10. Multiple Decorators

Unlimited decorators can be added.

---

Example:

```java
Coffee coffee =
    new ChocolateDecorator(
        new SugarDecorator(
            new MilkDecorator(
                new SimpleCoffee()
            )
        )
    );
```

---

Memory:

```text
Chocolate
    ↓
Sugar
    ↓
Milk
    ↓
Coffee
```

---

Very flexible.

---

# 11. Why Not Use Inheritance?

Without Decorator:

```text
MilkCoffee

SugarCoffee

MilkSugarCoffee

ChocolateCoffee

MilkChocolateCoffee

MilkSugarChocolateCoffee
```

---

Huge class explosion.

---

Decorator solves this.

---

Add features dynamically.

---

# 12. Open/Closed Principle

Decorator follows:

```text
Open For Extension

Closed For Modification
```

---

Need new topping?

Create:

```java
CaramelDecorator
```

---

No change required in existing classes.

---

# 13. Real-Life Example

# Notification System

Base:

```java
EmailNotification
```

---

Add:

```java
SMSDecorator
```

---

Add:

```java
SlackDecorator
```

---

Flow:

```text
Email
  ↓
SMS
  ↓
Slack
```

---

One notification sent through multiple channels.

---

# 14. Java I/O - Best Real Example

Java IO heavily uses Decorator Pattern.

---

Base Component:

```java
InputStream
```

---

Concrete Component:

```java
FileInputStream
```

---

Decorators:

```java
BufferedInputStream

DataInputStream

ObjectInputStream
```

---

Example:

```java
InputStream stream =
    new BufferedInputStream(
        new FileInputStream(
            "data.txt"
        )
    );
```

---

Memory:

```text
BufferedInputStream
          ↓
FileInputStream
```

---

Classic Decorator Pattern.

---

# 15. Spring Framework Examples

---

## HttpServletRequestWrapper

Servlet API uses decorator style.

---

Example:

```java
HttpServletRequestWrapper
```

wraps:

```java
HttpServletRequest
```

---

Adds functionality.

---

## Spring Security Filters

:contentReference[oaicite:0]{index=0}

Multiple filters wrap request processing.

Decorator-like chain.

---

## BeanPostProcessor

Adds behavior around beans.

Decorator concepts appear frequently.

---

# 16. Dynamic vs Static Behavior

Inheritance:

```text
Compile Time
```

---

Decorator:

```text
Runtime
```

---

Example:

```java
if(userPremium)
{
    coffee =
      new ChocolateDecorator(
            coffee);
}
```

---

Feature added dynamically.

---

# 17. Decorator vs Inheritance

---

## Inheritance

```text
Behavior Fixed
```

---

Example:

```java
MilkCoffee
```

always has milk.

---

## Decorator

```text
Behavior Dynamic
```

---

Example:

```java
new MilkDecorator(
        coffee)
```

can be added or removed anytime.

---

### Memory Trick

```text
Inheritance
    ↓
Static

Decorator
    ↓
Dynamic
```

---

# 18. Decorator vs Adapter

Very common interview question.

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

## Decorator

Goal:

```text
Add Behavior
```

---

Example:

```text
Coffee + Milk
```

---

### Memory Trick

```text
Adapter
    ↓
Convert

Decorator
    ↓
Enhance
```

---

# 19. Decorator vs Proxy

Most confusing comparison.

---

Structure looks almost identical.

---

## Decorator

Adds functionality.

---

Example:

```text
Coffee + Milk
```

---

## Proxy

Controls access.

---

Example:

```text
Permission Check
Lazy Loading
Caching
```

---

### Memory Trick

```text
Decorator
      ↓
Add Features

Proxy
      ↓
Control Access
```

---

# 20. Decorator vs Composite

---

## Composite

Represents:

```text
Tree Structure
```

---

Example:

```text
Folder → Files
```

---

## Decorator

Represents:

```text
Feature Layering
```

---

Example:

```text
Coffee → Milk → Sugar
```

---

### Memory Trick

```text
Composite
      ↓
Hierarchy

Decorator
      ↓
Enhancement
```

---

# 21. Advantages

---

## 1. Dynamic Behavior Addition

Add features at runtime.

---

## 2. Avoids Class Explosion

No need for:

```text
MilkSugarChocolateCoffee
```

---

## 3. Follows Open/Closed Principle

Easy extension.

---

## 4. More Flexible Than Inheritance

Features combined dynamically.

---

## 5. Reusable Decorators

Same decorator works for many objects.

---

# 22. Disadvantages

---

## 1. Many Small Objects

Large chains can be created.

---

## 2. Harder Debugging

Nested decorators may become confusing.

---

## 3. Initialization Complexity

Deep wrapping can look ugly.

---

Example:

```java
new A(
  new B(
    new C(
      new D()
    )
  )
);
```

---

# 23. When To Use Decorator

Use when:

- Features added dynamically
- Inheritance causing class explosion
- Runtime flexibility needed

Examples:

```text
Coffee Toppings
Notifications
Java IO Streams
Logging
Compression
Encryption
```

---

# 24. When NOT To Use

Avoid when:

- Behavior never changes
- Simple inheritance is enough

---

# 25. Interview Questions

### Q1. What problem does Decorator solve?

Adds functionality dynamically without modifying existing classes.

---

### Q2. What are the participants?

```text
Component

Concrete Component

Decorator

Concrete Decorator
```

---

### Q3. Why is Decorator better than inheritance?

Because behavior can be added at runtime.

---

### Q4. What principle does Decorator follow?

```text
Open/Closed Principle
```

---

### Q5. Give Java examples.

```text
BufferedInputStream

DataInputStream

ObjectInputStream
```

---

### Q6. Difference Between Decorator and Adapter?

Adapter:

```text
Convert Interface
```

Decorator:

```text
Add Behavior
```

---

### Q7. Difference Between Decorator and Proxy?

Decorator:

```text
Enhance Object
```

Proxy:

```text
Control Access
```

---

### Q8. Is Java IO a Decorator example?

```text
YES
```

One of the best real-world examples.

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

Decorator
    ↓
Add Features
Without Modifying Object
```

---

# Quick Revision

```text
Decorator Design Pattern
------------------------

Purpose:
Add behavior dynamically.

Structure:

Component
    ↑
Concrete Component

Component
    ↑
Decorator
    ↑
Concrete Decorator

Problem Solved:

✔ Dynamic Features
✔ Class Explosion
✔ Runtime Flexibility

Key Concepts:

✔ Wrapping
✔ Delegation
✔ Chaining
✔ Open/Closed Principle

Advantages:

✔ Flexible
✔ Reusable
✔ Runtime Enhancement

Disadvantages:

✘ Many Objects
✘ Deep Chains

Examples:

Coffee Toppings
Java IO Streams
Notifications
Logging
Compression
Encryption

Remember:

Adapter
     ↓
Convert

Facade
     ↓
Simplify

Decorator
     ↓
Enhance

Proxy
     ↓
Control
```