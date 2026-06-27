# Strategy Design Pattern

> **Goal:** Understand Strategy Design Pattern from scratch, why it exists, how it works internally, Context, Strategy Interface, Concrete Strategies, Runtime Strategy Selection, Java examples, Spring examples, Lambda-based strategies, advantages, disadvantages, interview questions, and how it differs from State, Factory, and Template Method.

---

# 1. What Problem Does Strategy Pattern Solve?

Suppose we are building a Payment Application.

Users can pay using:

```text
Credit Card

UPI

PayPal

Net Banking
```

---

Without Strategy Pattern:

```java
public class PaymentService {

    public void pay(
            String paymentType,
            double amount) {

        if(paymentType.equals(
                "UPI")) {

            System.out.println(
                    "UPI Payment");
        }

        else if(paymentType.equals(
                "CARD")) {

            System.out.println(
                    "Card Payment");
        }

        else if(paymentType.equals(
                "PAYPAL")) {

            System.out.println(
                    "PayPal Payment");
        }
    }
}
```

---

Problems:

```text
Huge if-else chain

Hard to maintain

Violates Open/Closed Principle

New payment method requires modifying existing code
```

---

Need a way to:

```text
Encapsulate each algorithm separately
```

---

This is exactly what Strategy Pattern solves.

---

# 2. Definition

> Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable at runtime.

---

# 3. Simple Meaning

Instead of:

```text
One class
containing all algorithms
```

---

Create:

```text
One class per algorithm
```

---

Choose algorithm at runtime.

---

# 4. Real-Life Examples

---

## Example 1: Payment Methods

```text
Credit Card

UPI

PayPal
```

---

User chooses payment mode.

---

# Example 2: Google Maps

Routes:

```text
Car Route

Walking Route

Bike Route
```

---

Algorithm changes.

---

# Example 3: Sorting

Algorithms:

```text
Bubble Sort

Merge Sort

Quick Sort
```

---

Choose strategy.

---

# Example 4: Compression

Algorithms:

```text
ZIP

RAR

7Z
```

---

Interchangeable strategies.

---

# 5. Core Idea

Move algorithms from:

```text
One Large Class
```

to

```text
Separate Strategy Classes
```

---

# 6. Components

---

## 1. Strategy Interface

Common contract.

---

## 2. Concrete Strategy

Actual algorithm implementation.

---

## 3. Context

Uses strategy.

---

# 7. UML Structure

```text
               Strategy
                    ↑

      ----------------------------

      ↑            ↑            ↑

 CreditCard     UPI        PayPal

                    ↑

             PaymentService
                 (Context)
```

---

# 8. Real-World Example

# Payment System

---

## Step 1: Strategy Interface

```java
public interface PaymentStrategy {

    void pay(double amount);
}
```

---

# Step 2: Credit Card Strategy

```java
public class CreditCardPayment
        implements PaymentStrategy {

    @Override
    public void pay(
            double amount) {

        System.out.println(
                "Paid via Card: "
                + amount);
    }
}
```

---

# Step 3: UPI Strategy

```java
public class UpiPayment
        implements PaymentStrategy {

    @Override
    public void pay(
            double amount) {

        System.out.println(
                "Paid via UPI: "
                + amount);
    }
}
```

---

# Step 4: PayPal Strategy

```java
public class PaypalPayment
        implements PaymentStrategy {

    @Override
    public void pay(
            double amount) {

        System.out.println(
                "Paid via PayPal: "
                + amount);
    }
}
```

---

# Step 5: Context

```java
public class PaymentService {

    private PaymentStrategy
            strategy;

    public void setStrategy(
            PaymentStrategy strategy) {

        this.strategy = strategy;
    }

    public void pay(
            double amount) {

        strategy.pay(amount);
    }
}
```

---

Context delegates work.

---

# Step 6: Client

```java
PaymentService service =
        new PaymentService();

service.setStrategy(
        new UpiPayment());

service.pay(500);
```

---

Output:

```text
Paid via UPI: 500
```

---

Change strategy:

```java
service.setStrategy(
        new CreditCardPayment());

service.pay(1000);
```

---

Output:

```text
Paid via Card: 1000
```

---

No code modification.

---

# 9. Internal Working

Client chooses:

```text
UPI
```

↓

```text
PaymentService
```

↓

```text
UpiPayment.pay()
```

---

Switch strategy:

```text
Credit Card
```

↓

```text
CreditCardPayment.pay()
```

---

Behavior changes dynamically.

---

# 10. Why Strategy Pattern?

Without Strategy:

```java
if(type.equals(...))
```

---

With Strategy:

```java
strategy.pay();
```

---

No conditional logic.

---

# 11. Runtime Strategy Selection

Most important feature.

---

User chooses:

```text
UPI
```

at runtime.

---

System loads:

```java
new UpiPayment()
```

---

Dynamic behavior.

---

# 12. Lambda Strategy (Java 8+)

Important interview topic.

---

Strategy Interface:

```java
@FunctionalInterface
public interface PaymentStrategy {

    void pay(double amount);
}
```

---

Usage:

```java
PaymentStrategy strategy =
        amount ->
                System.out.println(
                        "UPI Payment: "
                                + amount);
```

---

Execute:

```java
strategy.pay(500);
```

---

No separate class needed.

---

Modern Strategy Pattern.

---

# 13. Functional Strategy Pattern

Java 8 made Strategy much simpler.

---

Example:

```java
Comparator<String>
```

---

Different comparison strategies.

---

```java
Collections.sort(
        list,
        comparator);
```

---

Classic Strategy Pattern.

---

# 14. Java Examples

---

## Comparator

Most famous Strategy Pattern example.

---

Strategy:

```java
Comparator
```

---

Algorithms:

```java
Name Comparison

Age Comparison

Salary Comparison
```

---

# Example

```java
Collections.sort(
        employees,
        new AgeComparator());
```

---

Change strategy:

```java
Collections.sort(
        employees,
        new SalaryComparator());
```

---

# 15. Spring Framework Example

:contentReference[oaicite:0]{index=0}

Spring often injects different implementations.

---

Example:

```java
NotificationService
```

Implementations:

```java
EmailNotification

SmsNotification

PushNotification
```

---

Dependency Injection selects strategy.

---

Conceptually Strategy Pattern.

---

# 16. Discount Engine Example

Strategies:

```text
Regular Customer

Premium Customer

VIP Customer
```

---

Each has different discount algorithm.

---

Strategy Pattern.

---

# 17. Shipping Cost Example

Strategies:

```text
Air Shipping

Road Shipping

Sea Shipping
```

---

Different calculation algorithms.

---

# 18. Advantages

---

## 1. Eliminates if-else

Biggest benefit.

---

## 2. Open/Closed Principle

Add new strategies without changing code.

---

## 3. Runtime Flexibility

Choose algorithm dynamically.

---

## 4. Better Maintainability

Algorithms isolated.

---

## 5. Reusable Algorithms

Strategies reused elsewhere.

---

# 19. Disadvantages

---

## 1. More Classes

Every strategy becomes a class.

---

## 2. Client Must Know Strategy

Client selects strategy.

---

## 3. Object Creation Overhead

Many strategy objects.

---

# 20. Strategy vs State

Most asked interview question.

---

UML looks almost identical.

---

## Strategy

Client chooses algorithm.

---

Example:

```text
UPI

Card

PayPal
```

---

User selects.

---

## State

Object changes behavior automatically.

---

Example:

```text
ATM States

No Card

Card Inserted

Transaction
```

---

State transitions itself.

---

### Memory Trick

```text
Strategy
      ↓
Client Chooses

State
      ↓
State Chooses
```

---

# 21. Strategy vs Factory

---

## Factory

Creates objects.

---

Example:

```java
PaymentFactory
```

---

Returns:

```java
PaymentStrategy
```

---

## Strategy

Uses object behavior.

---

Example:

```java
strategy.pay()
```

---

### Memory Trick

```text
Factory
      ↓
Create Object

Strategy
      ↓
Use Algorithm
```

---

# 22. Strategy vs Template Method

---

## Strategy

Composition.

---

Behavior injected.

---

```java
context.setStrategy(...)
```

---

## Template Method

Inheritance.

---

Behavior overridden.

---

```java
extends AbstractClass
```

---

### Memory Trick

```text
Strategy
      ↓
HAS-A

Template Method
      ↓
IS-A
```

---

# 23. Strategy + Factory

Very common combination.

---

Example:

```java
PaymentStrategy strategy =
        factory.getStrategy(
                "UPI");
```

---

Then:

```java
strategy.pay();
```

---

Factory creates.

Strategy executes.

---

Used heavily in enterprise projects.

---

# 24. When To Use Strategy Pattern

Use when:

- Multiple algorithms exist
- Runtime selection required
- Large if-else chains exist

Examples:

```text
Payments

Sorting

Discounts

Shipping

Compression
```

---

# 25. When NOT To Use

Avoid when:

- Only one algorithm exists
- Algorithms rarely change
- Complexity outweighs benefit

---

# 26. Interview Questions

### Q1. What problem does Strategy solve?

Encapsulates interchangeable algorithms.

---

### Q2. What are participants?

```text
Strategy

Concrete Strategy

Context
```

---

### Q3. Biggest benefit?

Runtime algorithm selection.

---

### Q4. Give Java example.

```java
Comparator
```

---

### Q5. Difference Between Strategy and State?

Strategy:

```text
Client Chooses
```

State:

```text
Automatic Transition
```

---

### Q6. Difference Between Strategy and Factory?

Factory creates.

Strategy executes.

---

### Q7. Why is Comparator a Strategy Pattern?

Different comparison algorithms.

---

### Q8. Can Lambdas implement Strategy?

```text
Yes
```

Using Functional Interfaces.

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

Composite
     ↓
Tree Structure

Facade
     ↓
Hide Complexity

Decorator
     ↓
Add Behavior

Flyweight
     ↓
Share State

Chain Of Responsibility
     ↓
Pass Request

Proxy
     ↓
Control Access

Command
     ↓
Encapsulate Request

Interpreter
     ↓
Interpret Grammar

Iterator
     ↓
Traverse Collection

Mediator
     ↓
Coordinate Communication

Memento
     ↓
Save State

Observer
     ↓
Notify Dependents

State
     ↓
Behavior By State

Strategy
     ↓
Interchangeable Algorithms
```

---

# Quick Revision

```text
Strategy Design Pattern
-----------------------

Purpose:
Encapsulate interchangeable algorithms.

Participants:

1. Strategy
2. Concrete Strategy
3. Context

Examples:

✔ Payment Methods
✔ Sorting Algorithms
✔ Shipping Calculation
✔ Discount Calculation
✔ Compression

Java Examples:

✔ Comparator
✔ Lambda Strategies

Advantages:

✔ Eliminates if-else
✔ Runtime Flexibility
✔ Open/Closed Principle

Disadvantages:

✘ More Classes
✘ Client Chooses Strategy

Remember:

Strategy
      ↓
Client Chooses Algorithm

State
      ↓
Behavior Changes Automatically

Factory
      ↓
Creates Strategy
```