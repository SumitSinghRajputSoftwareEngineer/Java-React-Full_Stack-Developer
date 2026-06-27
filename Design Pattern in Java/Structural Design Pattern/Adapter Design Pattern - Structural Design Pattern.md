# Adapter Design Pattern

> **Goal:** Understand Adapter Design Pattern from scratch, why it exists, how it works internally, Class Adapter vs Object Adapter, Two-Way Adapter, Real-world examples, Spring usage, advantages, disadvantages, and interview questions.

---

# 1. What Problem Does Adapter Pattern Solve?

Suppose you bought a new iPhone charger.

The charger has:

```text
Type-C Connector
```

But your wall socket accepts:

```text
Different Plug Type
```

---

Can you directly connect them?

```text
NO
```

Because:

```text
Interfaces are incompatible.
```

---

What do we use?

```text
Adapter
```

---

### Real Life

```text
Mobile Charger
        ↓
Adapter
        ↓
Wall Socket
```

---

Adapter converts:

```text
One Interface
      ↓
Into
      ↓
Another Interface
```

---

This is exactly what Adapter Design Pattern does.

---

# 2. Definition

> Adapter Pattern converts the interface of one class into another interface expected by the client.

---

# 3. Simple Meaning

Suppose Client expects:

```java
Target
```

But existing class provides:

```java
Adaptee
```

---

Interfaces don't match.

---

Adapter sits between them.

```text
Client
   ↓
Adapter
   ↓
Adaptee
```

---

Client thinks:

```text
I am talking to Target
```

Actually:

```text
Adapter talks to Adaptee
```

---

# 4. Real-Life Examples

---

## Example 1: Mobile Charger

```text
Wall Socket
      ↓
Adapter
      ↓
Mobile Charger
```

---

## Example 2: USB Adapter

Laptop:

```text
USB-C
```

Pendrive:

```text
USB-A
```

Need:

```text
USB Adapter
```

---

## Example 3: Language Translator

You know:

```text
English
```

Friend knows:

```text
Japanese
```

Translator acts as:

```text
Adapter
```

---

# 5. Real Software Example

Suppose old payment provider gives:

```java
makePayment()
```

But new application expects:

```java
pay()
```

Interfaces differ.

Need Adapter.

---

# 6. Structure of Adapter Pattern

```text
Client
   ↓
Target Interface
   ↑
 Adapter
   ↓
Adaptee
```

---

# 7. Terminology

Understanding these terms is important.

---

## 1. Client

Code using service.

```java
PaymentService
```

---

## 2. Target

Expected interface.

```java
PaymentProcessor
```

---

## 3. Adaptee

Existing incompatible class.

```java
OldPaymentGateway
```

---

## 4. Adapter

Converts interfaces.

```java
PaymentAdapter
```

---

# 8. Example: Payment Gateway Integration

---

## Existing Third-Party Gateway

```java
public class OldPaymentGateway {

    public void makePayment(double amount) {

        System.out.println(
            "Payment Done : " + amount
        );
    }
}
```

---

Third-party library already exists.

Cannot modify.

---

# Step 1: Create Target Interface

```java
public interface PaymentProcessor {

    void pay(double amount);
}
```

---

Client expects:

```java
pay()
```

---

# Step 2: Create Adapter

```java
public class PaymentAdapter
        implements PaymentProcessor {

    private OldPaymentGateway gateway;

    public PaymentAdapter(
            OldPaymentGateway gateway) {

        this.gateway = gateway;
    }

    @Override
    public void pay(double amount) {

        gateway.makePayment(amount);
    }
}
```

---

# Step 3: Client Code

```java
OldPaymentGateway gateway =
        new OldPaymentGateway();

PaymentProcessor processor =
        new PaymentAdapter(gateway);

processor.pay(1000);
```

---

Output:

```text
Payment Done : 1000
```

---

# 9. Internal Working

Client calls:

```java
processor.pay(1000);
```

---

Flow:

```text
Client
   ↓
Adapter.pay()
   ↓
gateway.makePayment()
   ↓
Payment Done
```

---

Client never knows:

```java
OldPaymentGateway
```

exists.

---

# 10. Object Adapter Pattern

This is the most common implementation.

---

## Uses Composition

Adapter contains:

```java
private Adaptee adaptee;
```

---

Example:

```java
class Adapter {

    private Adaptee adaptee;
}
```

---

Diagram:

```text
Adapter
   ↓ HAS-A
Adaptee
```

---

### Example

```java
private OldPaymentGateway gateway;
```

This is:

```text
Object Adapter
```

---

# 11. Class Adapter Pattern

Uses:

```text
Inheritance
```

instead of composition.

---

Example:

```java
public class PaymentAdapter
        extends OldPaymentGateway
        implements PaymentProcessor {

    @Override
    public void pay(double amount) {

        makePayment(amount);
    }
}
```

---

Diagram:

```text
PaymentAdapter
       ↑
OldPaymentGateway
```

---

# 12. Object Adapter vs Class Adapter

| Object Adapter | Class Adapter |
|---------------|---------------|
| Uses Composition | Uses Inheritance |
| More Flexible | Less Flexible |
| Preferred | Rarely Used |
| Follows Composition Principle | Tight Coupling |

---

### Interview Answer

In Java:

```text
Object Adapter
```

is preferred.

---

# 13. Two-Way Adapter

Advanced Adapter Pattern.

---

Normal Adapter:

```text
A → B
```

---

Two-Way Adapter:

```text
A ↔ B
```

Both systems can communicate.

---

Rarely used.

Mostly interview concept.

---

# 14. Real-Life Example: Media Player

Suppose application supports:

```java
playMp3()
```

---

Now requirement:

```text
Play MP4
Play VLC
```

---

Without Adapter:

Huge code changes.

---

With Adapter:

```text
MediaPlayer
      ↓
MediaAdapter
      ↓
AdvancedMediaPlayer
```

---

Classic GoF example.

---

# 15. UML Structure

```text
          Client
             ↓
      PaymentProcessor
             ↑
      PaymentAdapter
             ↓
     OldPaymentGateway
```

---

# 16. Why Adapter Is Needed?

Suppose third-party API changes.

Old API:

```java
makePayment()
```

New code expects:

```java
pay()
```

---

Without Adapter:

Change entire codebase.

---

With Adapter:

Only Adapter changes.

---

Benefit:

```text
Minimal Impact
```

---

# 17. Spring Framework Examples

Spring heavily uses Adapter Pattern.

---

## Example 1: HandlerAdapter

In MVC.

:contentReference[oaicite:0]{index=0}

```java
HandlerAdapter
```

adapts different controllers.

---

Flow:

```text
DispatcherServlet
      ↓
HandlerAdapter
      ↓
Controller
```

---

Different controller types.

Same interface to DispatcherServlet.

---

## Example 2: JdbcTemplate

Different JDBC drivers.

Adapter-like behavior.

---

## Example 3: Spring Security

Different authentication providers.

Adapter layers used internally.

---

# 18. Java Library Examples

---

## InputStreamReader

```java
InputStreamReader
```

adapts:

```java
InputStream
```

to

```java
Reader
```

---

Diagram:

```text
InputStream
      ↓
InputStreamReader
      ↓
Reader
```

---

## Arrays.asList()

Acts as adapter.

Converts:

```java
Array
```

to

```java
List
```

---

Example:

```java
String[] names =
        {"A", "B"};

List<String> list =
        Arrays.asList(names);
```

---

# 19. Advantages

---

## 1. Reuse Existing Classes

No modification needed.

---

## 2. Loose Coupling

Client depends on Target interface.

---

## 3. Easy Integration

Works well with third-party systems.

---

## 4. Follows Open/Closed Principle

New adapters can be added.

---

## 5. Legacy System Integration

Very common use case.

---

# 20. Disadvantages

---

## Extra Layer

```text
Client
 ↓
Adapter
 ↓
Adaptee
```

More objects.

---

## Complexity

Too many adapters can make design harder.

---

## Performance Overhead

Small extra method calls.

Usually negligible.

---

# 21. Adapter vs Facade

Very important interview question.

---

## Adapter

Converts interface.

```text
A → B
```

---

Example:

```text
USB Adapter
```

---

## Facade

Simplifies interface.

---

Example:

```text
One Button
     ↓
Starts Entire Home Theater
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

# 22. Adapter vs Decorator

Another favorite interview question.

---

## Adapter

Changes interface.

---

Example:

```java
makePayment()
```

becomes:

```java
pay()
```

---

## Decorator

Keeps interface same.

Adds behavior.

---

Example:

```text
Coffee
  ↓
Add Milk
  ↓
Add Sugar
```

---

### Memory Trick

```text
Adapter
   ↓
Change Interface

Decorator
   ↓
Add Functionality
```

---

# 23. When To Use Adapter

Use Adapter when:

- Third-party library exists
- Legacy code exists
- Interfaces don't match
- Existing code cannot be modified

Examples:

```text
Payment Gateways
Cloud Providers
External APIs
Legacy Systems
Media Players
```

---

# 24. When NOT To Use Adapter

Avoid when:

- Interfaces already compatible
- You can modify source code easily

No need for extra layer.

---

# 25. Interview Questions

### Q1. What is Adapter Pattern?

Adapter converts one interface into another expected interface.

---

### Q2. What problem does Adapter solve?

Incompatible interfaces.

---

### Q3. What are the participants?

```text
Client
Target
Adapter
Adaptee
```

---

### Q4. Difference Between Object Adapter and Class Adapter?

Object Adapter:

```text
Composition
```

Class Adapter:

```text
Inheritance
```

---

### Q5. Which Adapter is preferred?

```text
Object Adapter
```

because composition is preferred over inheritance.

---

### Q6. Give Java Examples.

Examples:

```text
InputStreamReader
Arrays.asList()
Spring HandlerAdapter
```

---

### Q7. Adapter vs Facade?

Adapter:

```text
Converts Interface
```

Facade:

```text
Simplifies Interface
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
Create Product Family

Builder
    ↓
Build Object

Prototype
    ↓
Copy Object

Adapter
    ↓
Convert Interface
```

---

# Quick Revision

```text
Adapter Design Pattern
----------------------

Purpose:
Convert incompatible interfaces.

Structure:

Client
   ↓
Adapter
   ↓
Adaptee

Types:

1. Object Adapter
   ✔ Composition
   ✔ Preferred

2. Class Adapter
   ✔ Inheritance
   ✘ Less Flexible

3. Two-Way Adapter
   ✔ Bidirectional
   ✘ Rare

Advantages:

✔ Reuse Existing Code
✔ Integrate Legacy Systems
✔ Loose Coupling

Disadvantages:

✘ Extra Layer
✘ More Complexity

Examples:

Mobile Charger Adapter
USB Adapter
InputStreamReader
Arrays.asList()
Spring HandlerAdapter

Remember:

Adapter
   ↓
Convert Interface

Facade
   ↓
Simplify Interface

Decorator
   ↓
Add Behavior
```