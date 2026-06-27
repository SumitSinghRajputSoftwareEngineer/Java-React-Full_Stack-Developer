# Template Method Design Pattern

> **Goal:** Understand Template Method Pattern from scratch, why it exists, how it works internally, Abstract Class, Template Method, Concrete Methods, Abstract Methods, Hooks, Hollywood Principle, Java examples, Spring examples, advantages, disadvantages, interview questions, and how it differs from Strategy Pattern.

---

# 1. What Problem Does Template Method Pattern Solve?

Suppose we are building an application that generates reports.

Steps:

```text
Read Data
Process Data
Generate Report
```

---

For PDF Report:

```text
Read Data
Process Data
Generate PDF
```

---

For Excel Report:

```text
Read Data
Process Data
Generate Excel
```

---

Without Template Method:

```java
class PdfReport {

    void generateReport() {

        readData();

        processData();

        generatePdf();
    }
}
```

---

```java
class ExcelReport {

    void generateReport() {

        readData();

        processData();

        generateExcel();
    }
}
```

---

Problem:

```text
Duplicate Code

Same Algorithm Structure Repeated

Hard To Maintain
```

---

Need a way to:

```text
Define the algorithm once
and allow subclasses to customize certain steps.
```

---

This is exactly what Template Method Pattern solves.

---

# 2. Definition

> Template Method Pattern defines the skeleton of an algorithm in a superclass but lets subclasses redefine certain steps without changing the algorithm's overall structure.

---

# 3. Simple Meaning

Think of a recipe.

---

Making Tea:

```text
Boil Water
Add Tea Leaves
Pour Into Cup
Add Sugar
```

---

Making Coffee:

```text
Boil Water
Add Coffee Powder
Pour Into Cup
Add Sugar
```

---

Structure:

```text
Same
```

---

Some steps:

```text
Different
```

---

Template Method Pattern.

---

# 4. Real-Life Examples

---

## Example 1: Tea and Coffee

Common Steps:

```text
Boil Water

Pour Into Cup
```

---

Different Steps:

```text
Tea Leaves

Coffee Powder
```

---

# Example 2: Online Order Processing

Common:

```text
Validate Order

Calculate Price

Generate Invoice
```

---

Different:

```text
Payment Method
```

---

# Example 3: Report Generation

Common:

```text
Read Data

Process Data
```

---

Different:

```text
Generate PDF

Generate Excel
```

---

# Example 4: Game Development

Common:

```text
Start Game

Play

End Game
```

---

Different game logic.

---

# 5. Core Idea

Move common algorithm into:

```text
Abstract Class
```

---

Allow subclasses to customize specific steps.

---

# 6. Components

---

## 1. Abstract Class

Contains template method.

---

## 2. Template Method

Defines algorithm flow.

---

## 3. Abstract Methods

Implemented by subclasses.

---

## 4. Concrete Subclasses

Provide specific behavior.

---

# 7. UML Structure

```text
          AbstractClass

               ↑

      --------------------

      ↑                ↑

   TeaMaker      CoffeeMaker
```

---

# 8. Real-World Example

# Tea and Coffee

---

## Step 1: Abstract Class

```java
public abstract class Beverage {

    public final void prepare() {

        boilWater();

        addMainIngredient();

        pourIntoCup();

        addExtras();
    }

    private void boilWater() {

        System.out.println(
                "Boiling Water");
    }

    private void pourIntoCup() {

        System.out.println(
                "Pouring Into Cup");
    }

    protected abstract void
            addMainIngredient();

    protected abstract void
            addExtras();
}
```

---

Notice:

```java
final
```

on:

```java
prepare()
```

---

Reason:

Algorithm structure must not change.

---

# Step 2: Tea

```java
public class Tea
        extends Beverage {

    @Override
    protected void
    addMainIngredient() {

        System.out.println(
                "Adding Tea Leaves");
    }

    @Override
    protected void addExtras() {

        System.out.println(
                "Adding Sugar");
    }
}
```

---

# Step 3: Coffee

```java
public class Coffee
        extends Beverage {

    @Override
    protected void
    addMainIngredient() {

        System.out.println(
                "Adding Coffee");
    }

    @Override
    protected void addExtras() {

        System.out.println(
                "Adding Milk");
    }
}
```

---

# Step 4: Client

```java
Beverage tea =
        new Tea();

tea.prepare();
```

---

Output:

```text
Boiling Water

Adding Tea Leaves

Pouring Into Cup

Adding Sugar
```

---

# 9. Internal Working

Template Method:

```java
prepare()
```

---

Flow:

```text
Boil Water
      ↓
Add Ingredient
      ↓
Pour Into Cup
      ↓
Add Extras
```

---

Subclasses only customize:

```text
Add Ingredient

Add Extras
```

---

Structure remains fixed.

---

# 10. Template Method

Most important concept.

---

Method defining algorithm skeleton.

---

Example:

```java
prepare()
```

---

Contains:

```text
Common Steps

Variable Steps
```

---

# 11. Hooks

Very important interview topic.

---

Hooks are:

```text
Optional Methods
```

---

Subclasses may override.

---

Example:

```java
protected boolean
addCondiments() {

    return true;
}
```

---

Template:

```java
if(addCondiments()) {

    addSugar();
}
```

---

Subclass decides.

---

# 12. Hook Example

```java
protected boolean
customerWantsSugar() {

    return true;
}
```

---

Tea:

```java
@Override
protected boolean
customerWantsSugar() {

    return false;
}
```

---

Sugar skipped.

---

Hook = Optional Step.

---

# 13. Hollywood Principle

Most important theory question.

---

Template Method follows:

```text
Don't Call Us,
We'll Call You
```

---

Meaning:

Subclasses do NOT control flow.

---

Superclass controls flow.

---

Example:

```java
prepare()
```

calls subclass methods.

---

This is Hollywood Principle.

---

# 14. Why Use Abstract Class?

Common interview question.

---

Need:

```text
Common Code
+
Abstract Methods
```

---

Interface alone cannot hold full algorithm structure effectively (before Java 8).

---

Template Method usually uses:

```java
Abstract Class
```

---

# 15. Java Examples

---

## InputStream

:contentReference[oaicite:0]{index=0}

Methods:

```java
read()
```

implemented in subclasses.

---

Template Method concept.

---

## AbstractList

:contentReference[oaicite:1]{index=1}

Defines common behavior.

---

Subclasses implement specifics.

---

Template Method.

---

# 16. Spring Framework Examples

:contentReference[oaicite:2]{index=2}

Most famous example:

```java
JdbcTemplate
```

---

Template Method is literally in the name.

---

Flow:

```text
Open Connection
Execute Query
Handle Exception
Close Connection
```

---

Common steps managed by framework.

---

You provide:

```java
RowMapper
```

---

Perfect Template Method.

---

# 17. JdbcTemplate Example

```java
jdbcTemplate.query(
        sql,
        rowMapper);
```

---

Framework handles:

```text
Connection

Statement

ResultSet

Exception

Cleanup
```

---

You implement:

```text
Mapping Logic
```

---

Classic Template Method.

---

# 18. Advantages

---

## 1. Eliminates Duplicate Code

Biggest benefit.

---

## 2. Reuse Common Logic

Shared in superclass.

---

## 3. Open/Closed Principle

Add subclasses easily.

---

## 4. Controls Algorithm Structure

Prevents accidental changes.

---

## 5. Enforces Workflow

Same process everywhere.

---

# 19. Disadvantages

---

## 1. Inheritance-Based

Can create tight coupling.

---

## 2. Difficult To Change Structure

Algorithm fixed.

---

## 3. Deep Hierarchies

Can become complex.

---

# 20. Template Method vs Strategy

Most asked interview question.

---

## Template Method

Uses:

```text
Inheritance
```

---

Behavior varies through:

```java
extends
```

---

Example:

```java
Tea

Coffee
```

---

## Strategy

Uses:

```text
Composition
```

---

Behavior varies through:

```java
setStrategy(...)
```

---

Example:

```java
UPI

Card

PayPal
```

---

### Memory Trick

```text
Template Method
       ↓
IS-A

Strategy
       ↓
HAS-A
```

---

# 21. Template Method vs Factory Method

---

## Template Method

Controls algorithm flow.

---

## Factory Method

Controls object creation.

---

Different goals.

---

# 22. Template Method vs Command

---

## Template Method

Workflow structure.

---

## Command

Request encapsulation.

---

Different responsibilities.

---

# 23. When To Use Template Method

Use when:

- Algorithm structure fixed
- Some steps vary
- Duplicate workflow exists

Examples:

```text
Report Generation

Data Processing

Game Engines

Spring Templates

Order Processing
```

---

# 24. When NOT To Use

Avoid when:

- Algorithms differ completely
- Runtime switching needed

Use:

```text
Strategy Pattern
```

instead.

---

# 25. Interview Questions

### Q1. What problem does Template Method solve?

Defines algorithm skeleton while allowing subclasses to customize steps.

---

### Q2. What is Template Method?

Method defining workflow structure.

---

### Q3. Why is Template Method usually final?

To prevent changing algorithm flow.

---

### Q4. What are Hooks?

Optional overridable methods.

---

### Q5. What is Hollywood Principle?

```text
Don't Call Us,
We'll Call You
```

---

### Q6. Difference Between Template Method and Strategy?

Template:

```text
Inheritance
```

Strategy:

```text
Composition
```

---

### Q7. Give Spring example.

```text
JdbcTemplate
```

---

### Q8. Give Java example.

```text
InputStream

AbstractList
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

Template Method
     ↓
Fixed Workflow, Variable Steps
```

---

# Quick Revision

```text
Template Method Design Pattern
------------------------------

Purpose:
Define workflow skeleton.

Participants:

1. Abstract Class
2. Template Method
3. Abstract Methods
4. Concrete Subclasses

Important Concepts:

✔ Algorithm Skeleton
✔ Hooks
✔ Hollywood Principle

Examples:

✔ Tea/Coffee
✔ Report Generation
✔ JdbcTemplate
✔ InputStream

Advantages:

✔ Reuse Common Logic
✔ Remove Duplication
✔ Enforce Workflow

Disadvantages:

✘ Inheritance Coupling
✘ Fixed Structure

Remember:

Template Method
      ↓
Inheritance

Strategy
      ↓
Composition

Template Method
      ↓
Fixed Flow

Factory Method
      ↓
Object Creation
```