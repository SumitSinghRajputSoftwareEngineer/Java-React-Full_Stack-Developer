# Factory Method Design Pattern

> **Goal:** Understand Factory Method Design Pattern from scratch, why it exists, how it works internally, real-world use cases, advantages, disadvantages, Spring Boot usage, and interview questions.

---

# 1. What Problem Does Factory Pattern Solve?

Suppose you are building an E-Commerce application.

Initially, the application supports:

```java
CreditCardPayment payment = new CreditCardPayment();
```

Later business says:

```text
Add UPI Payment
```

You write:

```java
UPIPayment payment = new UPIPayment();
```

Later:

```text
Add PayPal
```

Again:

```java
PayPalPayment payment = new PayPalPayment();
```

Soon your code becomes:

```java
if(type.equals("CARD"))
    payment = new CreditCardPayment();

else if(type.equals("UPI"))
    payment = new UPIPayment();

else if(type.equals("PAYPAL"))
    payment = new PayPalPayment();
```

This logic gets duplicated everywhere.

Problems:

- Tight coupling
- Difficult maintenance
- Difficult extension
- Violates Open/Closed Principle

---

# 2. Definition

> Factory Pattern provides an interface for creating objects but lets a factory class decide which object to create.

Instead of:

```java
new Object()
```

everywhere, object creation is delegated to a Factory.

---

# 3. Real-Life Example

## Restaurant Example

When you visit a restaurant:

You say:

```text
I want a Pizza
```

You don't prepare:

- Dough
- Cheese
- Vegetables
- Oven

Your order goes to:

```text
Kitchen (Factory)
```

Kitchen creates the Pizza and returns it.

---

### Without Factory

```text
Customer
    ↓
Makes Pizza Himself
```

---

### With Factory

```text
Customer
    ↓
Kitchen (Factory)
    ↓
Pizza
```

This is exactly how Factory Pattern works.

---

# 4. Core Idea

Factory Pattern says:

```text
Client should not create objects directly.
```

Instead:

```text
Client
   ↓
Factory
   ↓
Object
```

---

# 5. Basic Structure

```text
Client
   ↓
Factory
   ↓
Concrete Classes
```

Example:

```text
PaymentService
      ↓
PaymentFactory
      ↓
CardPayment
UPIPayment
PayPalPayment
```

---

# 6. Real-Life Scenario: Payment System

---

## Step 1: Create Common Interface

```java
public interface Payment {

    void pay(double amount);
}
```

---

## Step 2: Create Implementations

### Credit Card Payment

```java
public class CreditCardPayment implements Payment {

    @Override
    public void pay(double amount) {
        System.out.println(
            "Paid " + amount + " using Credit Card");
    }
}
```

---

### UPI Payment

```java
public class UPIPayment implements Payment {

    @Override
    public void pay(double amount) {
        System.out.println(
            "Paid " + amount + " using UPI");
    }
}
```

---

### PayPal Payment

```java
public class PayPalPayment implements Payment {

    @Override
    public void pay(double amount) {
        System.out.println(
            "Paid " + amount + " using PayPal");
    }
}
```

---

# 7. Create Factory Class

```java
public class PaymentFactory {

    public static Payment getPayment(String type) {

        if(type.equalsIgnoreCase("CARD")) {
            return new CreditCardPayment();
        }

        if(type.equalsIgnoreCase("UPI")) {
            return new UPIPayment();
        }

        if(type.equalsIgnoreCase("PAYPAL")) {
            return new PayPalPayment();
        }

        throw new IllegalArgumentException(
            "Invalid Payment Type");
    }
}
```

---

# 8. Client Code

Instead of:

```java
Payment payment =
        new CreditCardPayment();
```

Use:

```java
Payment payment =
        PaymentFactory.getPayment("UPI");

payment.pay(1000);
```

Output:

```text
Paid 1000 using UPI
```

---

# 9. Complete Flow

```text
Client
  ↓
PaymentFactory
  ↓
Checks Type
  ↓
Creates Correct Object
  ↓
Returns Payment
```

---

# 10. Why Factory Is Better?

Without Factory:

```java
new CreditCardPayment()
new UPIPayment()
new PayPalPayment()
```

Client knows implementation classes.

---

With Factory:

```java
PaymentFactory.getPayment("UPI");
```

Client knows only:

```java
Payment
```

Not implementation.

---

# 11. Real-World Examples

---

## Example 1: Notification System

Interface:

```java
Notification
```

Implementations:

```java
EmailNotification
SMSNotification
PushNotification
```

Factory:

```java
NotificationFactory
```

Usage:

```java
Notification notification =
        NotificationFactory.getNotification("EMAIL");
```

---

## Example 2: Vehicle System

Interface:

```java
Vehicle
```

Implementations:

```java
Bike
Car
Truck
```

Factory:

```java
VehicleFactory
```

Usage:

```java
Vehicle vehicle =
        VehicleFactory.getVehicle("CAR");
```

---

## Example 3: Document Generator

Interface:

```java
Document
```

Implementations:

```java
PDFDocument
ExcelDocument
WordDocument
```

Factory:

```java
DocumentFactory
```

---

# 12. UML Structure

```text
          Payment
             ↑
   -------------------
   |        |        |
 Card      UPI    PayPal

             ↑
      PaymentFactory

             ↑
           Client
```

---

# 13. Types of Factory Patterns

Many developers say "Factory Pattern".

Actually there are multiple variants:

```text
1. Simple Factory
2. Factory Method Pattern
3. Abstract Factory Pattern
```

Most beginners start with Simple Factory.

---

# 14. Simple Factory

This is what we implemented.

Factory contains:

```java
if-else
switch
```

and creates objects.

Example:

```java
PaymentFactory.getPayment("UPI");
```

---

### Structure

```text
Client
   ↓
Simple Factory
   ↓
Concrete Classes
```

---

# 15. Factory Method Pattern (Actual GoF Pattern)

In GoF Factory Method Pattern:

Factory itself becomes an abstraction.

---

### Example

```java
public interface PaymentFactory {

    Payment createPayment();
}
```

---

### Card Factory

```java
public class CardFactory
        implements PaymentFactory {

    @Override
    public Payment createPayment() {
        return new CreditCardPayment();
    }
}
```

---

### UPI Factory

```java
public class UPIFactory
        implements PaymentFactory {

    @Override
    public Payment createPayment() {
        return new UPIPayment();
    }
}
```

---

### Usage

```java
PaymentFactory factory =
        new UPIFactory();

Payment payment =
        factory.createPayment();
```

---

### Flow

```text
Client
   ↓
Concrete Factory
   ↓
Concrete Product
```

---

# 16. Difference Between Simple Factory and Factory Method

| Simple Factory | Factory Method |
|---------------|----------------|
| One Factory Class | Multiple Factory Classes |
| Uses if-else/switch | Uses inheritance |
| Easier | More flexible |
| Not GoF Pattern | Official GoF Pattern |

---

# 17. Factory Method Real-Life Example

Suppose company manufactures vehicles.

Factories:

```text
Bike Factory
Car Factory
Truck Factory
```

Each factory knows only one vehicle.

```text
Bike Factory → Bike

Car Factory → Car

Truck Factory → Truck
```

Client chooses factory.

Factory chooses object.

---

# 18. Advantages of Factory Pattern

---

## 1. Loose Coupling

Client knows:

```java
Payment
```

not:

```java
CreditCardPayment
```

---

## 2. Easy Maintenance

Object creation centralized.

---

## 3. Easy Extension

Add:

```java
CryptoPayment
```

without changing client code.

---

## 4. Open/Closed Principle

Open for extension.

Closed for modification.

---

## 5. Better Testing

Can easily mock interfaces.

---

# 19. Disadvantages

---

## More Classes

Factory Pattern increases:

```text
Interfaces
Factories
Implementations
```

---

## Additional Complexity

For small applications:

```java
new Object()
```

may be simpler.

---

# 20. Factory Pattern in Spring Boot

Spring uses Factory Pattern heavily.

---

## BeanFactory

:contentReference[oaicite:0]{index=0}

```java
BeanFactory
```

creates beans.

---

Instead of:

```java
new EmployeeService();
```

Spring creates:

```java
EmployeeService service =
        context.getBean(EmployeeService.class);
```

---

### Flow

```text
Developer
      ↓
Spring BeanFactory
      ↓
Bean Creation
      ↓
Bean Returned
```

---

## ApplicationContext

Also acts as Factory.

```java
ApplicationContext
```

creates and manages beans.

---

# 21. Factory vs Singleton

| Factory | Singleton |
|----------|-----------|
| Creates objects | Restricts objects |
| Focus on creation | Focus on uniqueness |
| Many objects possible | One object only |
| Returns object | Controls object count |

---

### Easy Memory Trick

```text
Factory = Create

Singleton = Restrict
```

---

# 22. Factory vs Builder

| Factory | Builder |
|----------|---------|
| Decides WHAT to create | Decides HOW to create |
| One-step creation | Step-by-step creation |
| Returns ready object | Builds gradually |

---

### Example

Factory:

```java
VehicleFactory.getVehicle("CAR");
```

Builder:

```java
Car.builder()
   .engine("V8")
   .color("Black")
   .build();
```

---

# 23. When To Use Factory Pattern

Use Factory when:

- Object creation logic is complex
- Multiple implementations exist
- Client should not know implementation details
- New types may be added frequently

Examples:

```text
Payment Systems
Notification Systems
Vehicle Creation
Document Generators
Database Drivers
Spring Beans
```

---

# 24. When NOT To Use Factory Pattern

Avoid when:

- Only one implementation exists
- Object creation is simple
- Small application

Example:

```java
Student student = new Student();
```

No factory needed.

---

# 25. Interview Questions

### Q1. What is Factory Pattern?

Factory Pattern delegates object creation responsibility to a factory class instead of creating objects directly.

---

### Q2. Why Use Factory Pattern?

To achieve:

- Loose Coupling
- Better Maintainability
- Better Extensibility

---

### Q3. Difference Between Factory and Factory Method?

| Factory | Factory Method |
|----------|--------------|
| Generic term | Specific GoF Pattern |
| Single Factory | Multiple Factories |
| Often uses if-else | Uses inheritance |

---

### Q4. Is BeanFactory a Factory Pattern?

Yes.

Spring's BeanFactory is one of the best real-world examples of Factory Pattern.

---

### Q5. Which SOLID Principle Does Factory Support?

```text
Open Closed Principle (OCP)
Dependency Inversion Principle (DIP)
```

---

# Quick Revision

```text
Factory Method Design Pattern
-----------------------------

Purpose:
Hide object creation logic.

Flow:

Client
   ↓
Factory
   ↓
Object

Types:

1. Simple Factory
   -> One Factory Class

2. Factory Method
   -> Multiple Factory Classes

Advantages:

✔ Loose Coupling
✔ Easy Maintenance
✔ Easy Extension
✔ Better Testing

Disadvantages:

✘ More Classes
✘ More Complexity

Examples:

PaymentFactory
NotificationFactory
VehicleFactory
BeanFactory

Remember:

Singleton = One Object

Factory = Create Object

Builder = Build Object

Abstract Factory = Create Family Of Objects
```