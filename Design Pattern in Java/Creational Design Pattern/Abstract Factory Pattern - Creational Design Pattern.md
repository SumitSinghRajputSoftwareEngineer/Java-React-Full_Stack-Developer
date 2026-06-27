# Abstract Factory Design Pattern

> **Goal:** Understand Abstract Factory Design Pattern from scratch, why it exists, how it differs from Factory Method, complete real-world examples, implementation steps, Spring usage, advantages, disadvantages, and interview questions.

---

# 1. What Problem Does Abstract Factory Solve?

Before learning Abstract Factory, let's understand the limitation of Factory Pattern.

Suppose you are building a Furniture Application.

You have:

```text
Chair
Table
Sofa
```

For Modern Style:

```text
ModernChair
ModernTable
ModernSofa
```

For Victorian Style:

```text
VictorianChair
VictorianTable
VictorianSofa
```

For ArtDeco Style:

```text
ArtDecoChair
ArtDecoTable
ArtDecoSofa
```

---

Now imagine client wants:

```text
Entire Modern Furniture Set
```

That means:

```text
ModernChair
ModernTable
ModernSofa
```

must be created together.

---

Without Abstract Factory:

```java
Chair chair = new ModernChair();
Table table = new VictorianTable();
Sofa sofa = new ArtDecoSofa();
```

Result:

```text
Mixed Furniture
```

Looks inconsistent.

---

We need a way to create:

```text
Related Objects Together
```

This is exactly what Abstract Factory solves.

---

# 2. Definition

> Abstract Factory Pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes.

---

# 3. Simple Meaning

Factory Pattern creates:

```text
One Product
```

Example:

```text
Car
```

---

Abstract Factory creates:

```text
Multiple Related Products
```

Example:

```text
Car
Engine
Seat
```

all belonging to the same family.

---

# 4. Real-Life Example

## Vehicle Manufacturing Company

Suppose company manufactures:

### Luxury Vehicles

```text
Luxury Car
Luxury Engine
Luxury Seat
```

---

### Economy Vehicles

```text
Economy Car
Economy Engine
Economy Seat
```

---

If customer chooses:

```text
Luxury Factory
```

He should get:

```text
Luxury Car
Luxury Engine
Luxury Seat
```

Only.

---

Customer should never receive:

```text
Luxury Car
Economy Engine
Luxury Seat
```

This mismatch is prevented by Abstract Factory.

---

# 5. Factory vs Abstract Factory

This is the most important interview question.

---

## Factory Pattern

Creates:

```text
One Product
```

Example:

```java
Payment payment =
        PaymentFactory.getPayment("UPI");
```

Only Payment object.

---

## Abstract Factory

Creates:

```text
Family Of Products
```

Example:

```text
Chair
Table
Sofa
```

all together.

---

### Visual Difference

Factory:

```text
PaymentFactory
       ↓
    Payment
```

---

Abstract Factory:

```text
FurnitureFactory
      ↓
Chair
Table
Sofa
```

---

# 6. Structure of Abstract Factory

```text
                 Abstract Factory
                         ↑
         --------------------------------
         |                              |
ModernFurnitureFactory     VictorianFurnitureFactory

         ↓                              ↓

 ModernChair                VictorianChair
 ModernTable                VictorianTable
 ModernSofa                 VictorianSofa
```

---

# 7. Real-World Example

# Furniture Manufacturing System

---

## Step 1: Create Product Interfaces

---

### Chair

```java
public interface Chair {

    void sitOn();
}
```

---

### Table

```java
public interface Table {

    void use();
}
```

---

### Sofa

```java
public interface Sofa {

    void relax();
}
```

---

# Step 2: Create Modern Products

---

### ModernChair

```java
public class ModernChair
        implements Chair {

    @Override
    public void sitOn() {
        System.out.println(
            "Sitting on Modern Chair");
    }
}
```

---

### ModernTable

```java
public class ModernTable
        implements Table {

    @Override
    public void use() {
        System.out.println(
            "Using Modern Table");
    }
}
```

---

### ModernSofa

```java
public class ModernSofa
        implements Sofa {

    @Override
    public void relax() {
        System.out.println(
            "Relaxing on Modern Sofa");
    }
}
```

---

# Step 3: Create Victorian Products

---

### VictorianChair

```java
public class VictorianChair
        implements Chair {

    @Override
    public void sitOn() {
        System.out.println(
            "Sitting on Victorian Chair");
    }
}
```

---

### VictorianTable

```java
public class VictorianTable
        implements Table {

    @Override
    public void use() {
        System.out.println(
            "Using Victorian Table");
    }
}
```

---

### VictorianSofa

```java
public class VictorianSofa
        implements Sofa {

    @Override
    public void relax() {
        System.out.println(
            "Relaxing on Victorian Sofa");
    }
}
```

---

# Step 4: Create Abstract Factory

```java
public interface FurnitureFactory {

    Chair createChair();

    Table createTable();

    Sofa createSofa();
}
```

---

Notice:

Factory creates:

```text
Chair
Table
Sofa
```

Family of products.

---

# Step 5: Create Modern Factory

```java
public class ModernFurnitureFactory
        implements FurnitureFactory {

    @Override
    public Chair createChair() {
        return new ModernChair();
    }

    @Override
    public Table createTable() {
        return new ModernTable();
    }

    @Override
    public Sofa createSofa() {
        return new ModernSofa();
    }
}
```

---

# Step 6: Create Victorian Factory

```java
public class VictorianFurnitureFactory
        implements FurnitureFactory {

    @Override
    public Chair createChair() {
        return new VictorianChair();
    }

    @Override
    public Table createTable() {
        return new VictorianTable();
    }

    @Override
    public Sofa createSofa() {
        return new VictorianSofa();
    }
}
```

---

# Step 7: Client Code

---

## Modern Furniture

```java
FurnitureFactory factory =
        new ModernFurnitureFactory();

Chair chair =
        factory.createChair();

Table table =
        factory.createTable();

Sofa sofa =
        factory.createSofa();
```

---

Output:

```text
Modern Chair
Modern Table
Modern Sofa
```

---

## Victorian Furniture

```java
FurnitureFactory factory =
        new VictorianFurnitureFactory();
```

Output:

```text
Victorian Chair
Victorian Table
Victorian Sofa
```

---

# 8. Complete Flow

```text
Client
   ↓
Concrete Factory
   ↓
Creates Family
   ↓
Related Objects Returned
```

Example:

```text
Client
   ↓
ModernFurnitureFactory
   ↓
ModernChair
ModernTable
ModernSofa
```

---

# 9. Why Abstract Factory Is Needed?

Suppose tomorrow company adds:

```text
ArtDeco Furniture
```

Create:

```text
ArtDecoChair
ArtDecoTable
ArtDecoSofa
```

and

```java
ArtDecoFurnitureFactory
```

No client code changes.

This follows:

```text
Open Closed Principle
```

---

# 10. Real-World Examples

---

## Example 1: GUI Theme System

### Windows Theme

```text
WindowsButton
WindowsCheckbox
WindowsTextBox
```

---

### Mac Theme

```text
MacButton
MacCheckbox
MacTextBox
```

---

Factory:

```text
WindowsFactory
MacFactory
```

---

## Example 2: Vehicle Manufacturing

Luxury Factory:

```text
LuxuryCar
LuxurySeat
LuxuryEngine
```

---

Economy Factory:

```text
EconomyCar
EconomySeat
EconomyEngine
```

---

## Example 3: Database Drivers

MySQL Factory:

```text
MySQLConnection
MySQLStatement
MySQLResultSet
```

---

Oracle Factory:

```text
OracleConnection
OracleStatement
OracleResultSet
```

---

# 11. UML Structure

```text
                   FurnitureFactory
                          ↑
          ----------------------------------
          |                                |
 ModernFurnitureFactory      VictorianFurnitureFactory

          ↓                                ↓

     Chair                             Chair
     Table                             Table
     Sofa                              Sofa
```

---

# 12. Factory Method Inside Abstract Factory

Important interview concept.

---

Abstract Factory usually uses:

```text
Factory Methods internally
```

Example:

```java
createChair()
createTable()
createSofa()
```

Each method itself is a Factory Method.

---

Therefore:

```text
Abstract Factory
       ↓
Uses Factory Methods
```

---

# 13. Advantages

---

## 1. Consistent Product Families

Prevents mixing.

Example:

```text
Modern Chair
Victorian Table
```

cannot happen accidentally.

---

## 2. Loose Coupling

Client depends on:

```java
FurnitureFactory
```

not concrete classes.

---

## 3. Easy Extension

Add:

```text
ArtDeco Factory
```

without changing client.

---

## 4. Open Closed Principle

New families added easily.

---

## 5. Better Maintainability

All related objects grouped together.

---

# 14. Disadvantages

---

## More Classes

For every family:

```text
Chair
Table
Sofa
Factory
```

More code.

---

## More Complexity

Not suitable for small projects.

---

## Difficult To Add New Product

Adding:

```text
Bed
```

requires updating:

```java
FurnitureFactory
```

and every concrete factory.

---

# 15. Abstract Factory vs Factory Method

| Factory Method | Abstract Factory |
|----------------|------------------|
| Creates one product | Creates family of products |
| One Factory | Multiple Factories |
| Simpler | More Complex |
| Single responsibility | Product family responsibility |

---

### Easy Memory Trick

```text
Factory Method
      ↓
One Product

Abstract Factory
      ↓
Many Related Products
```

---

# 16. Abstract Factory vs Builder

| Abstract Factory | Builder |
|------------------|----------|
| Creates related objects | Creates one complex object |
| Focus on product family | Focus on object construction |
| Multiple objects | Single object |

---

Example:

Abstract Factory:

```text
Chair
Table
Sofa
```

---

Builder:

```text
Build One Car
Step By Step
```

---

# 17. Spring Boot Examples

Spring internally uses Abstract Factory concepts.

---

## BeanFactory

Creates families of beans.

---

## ApplicationContext

Acts as advanced factory.

---

## DataSource Implementations

Switching between:

```text
MySQL
Oracle
PostgreSQL
```

often follows Abstract Factory style.

---

# 18. When To Use Abstract Factory

Use when:

- Multiple product families exist
- Products must be used together
- Need consistency among products
- System should be independent of concrete classes

Examples:

```text
UI Themes
Furniture Systems
Vehicle Manufacturing
Database Drivers
Cloud Providers
```

---

# 19. When NOT To Use

Avoid when:

- Only one product exists
- Product families don't exist
- Small applications

In such cases:

```text
Factory Method
```

is usually enough.

---

# 20. Interview Questions

### Q1. What is Abstract Factory Pattern?

Abstract Factory creates families of related objects without specifying concrete classes.

---

### Q2. Difference Between Factory and Abstract Factory?

Factory:

```text
Creates One Product
```

Abstract Factory:

```text
Creates Family Of Products
```

---

### Q3. Is Abstract Factory Built Using Factory Methods?

Yes.

Most Abstract Factories internally use Factory Methods.

---

### Q4. Which SOLID Principle Does It Follow?

```text
Open Closed Principle
Dependency Inversion Principle
```

---

### Q5. Give Real-Life Example.

Examples:

```text
Furniture Factory
Vehicle Factory
UI Theme Factory
Database Driver Factory
```

---

# Quick Revision

```text
Abstract Factory Design Pattern
-------------------------------

Purpose:
Create families of related objects.

Factory Method:
Creates One Product

Abstract Factory:
Creates Multiple Related Products

Example:

Modern Factory
    ↓
Modern Chair
Modern Table
Modern Sofa

Victorian Factory
    ↓
Victorian Chair
Victorian Table
Victorian Sofa

Advantages:

✔ Consistent Product Families
✔ Loose Coupling
✔ Easy Extension
✔ Open Closed Principle

Disadvantages:

✘ More Classes
✘ More Complexity

Remember:

Factory Method
      ↓
One Product

Abstract Factory
      ↓
Family Of Products
```