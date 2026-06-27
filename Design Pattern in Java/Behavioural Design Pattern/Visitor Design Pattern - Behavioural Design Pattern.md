# Visitor Design Pattern

> **Goal:** Understand Visitor Design Pattern from scratch, why it exists, how it works internally, Visitor, Element, Double Dispatch, Accept Method, Object Structure, Acyclic Visitor, real-world examples, Java examples, advantages, disadvantages, interview questions, and how it differs from Strategy and Decorator.

---

# 1. What Problem Does Visitor Pattern Solve?

Suppose we have different document elements.

```text
Text

Image

Table
```

---

Classes:

```java
TextElement

ImageElement

TableElement
```

---

Now new requirement:

```text
Export To PDF
```

---

We add:

```java
exportToPdf()
```

inside every class.

---

Later:

```text
Export To HTML
```

Again:

```java
exportToHtml()
```

inside every class.

---

Later:

```text
Export To XML
```

Again modify every class.

---

Problem:

Every new operation requires changing existing classes.

---

Violates:

```text
Open/Closed Principle
```

---

Need a way to:

```text
Add New Operations
Without Modifying Existing Classes
```

---

This is exactly what Visitor Pattern solves.

---

# 2. Definition

> Visitor Pattern lets you define new operations on a set of objects without changing their classes.

---

# 3. Simple Meaning

Suppose hospital has:

```text
Patient

Doctor

Nurse
```

---

Now an Auditor visits.

---

Auditor performs:

```text
Audit Patient

Audit Doctor

Audit Nurse
```

---

Hospital classes don't change.

---

Visitor performs operation.

---

# 4. Real-Life Examples

---

## Example 1: Tax Calculation

Objects:

```text
Book

Food

Electronics
```

---

Visitor:

```text
Tax Calculator
```

---

# Example 2: Shopping Cart

Items:

```text
Laptop

Mobile

Book
```

---

Visitor:

```text
Price Calculator
```

---

# Example 3: Compiler

Nodes:

```text
If Statement

For Loop

Expression
```

---

Visitors:

```text
Optimization Visitor

Validation Visitor

Code Generation Visitor
```

---

# Example 4: File System

Elements:

```text
File

Directory
```

---

Visitors:

```text
Size Calculator

Search Visitor

Backup Visitor
```

---

# 5. Core Idea

Move operations from:

```text
Objects
```

to

```text
Visitors
```

---

Objects remain stable.

---

New operations added via visitors.

---

# 6. Components

---

## 1. Visitor

Defines operations.

---

## 2. Concrete Visitor

Actual implementation.

---

## 3. Element

Object being visited.

---

## 4. Concrete Element

Actual objects.

---

## 5. Object Structure

Collection of elements.

---

# 7. UML Structure

```text
               Visitor
                    ↑

      --------------------------

      ↑                      ↑

 PDFVisitor         HTMLVisitor


               Element
                    ↑

      --------------------------

      ↑          ↑          ↑

    Text      Image      Table
```

---

# 8. Real-World Example

# Shopping Cart Tax Calculator

---

## Step 1: Visitor Interface

```java
public interface Visitor {

    void visit(Book book);

    void visit(Laptop laptop);
}
```

---

Notice:

```java
visit(Book)

visit(Laptop)
```

---

Different method for each type.

---

# Step 2: Element Interface

```java
public interface Item {

    void accept(
            Visitor visitor);
}
```

---

# Step 3: Book

```java
public class Book
        implements Item {

    private int price;

    public Book(int price) {

        this.price = price;
    }

    public int getPrice() {

        return price;
    }

    @Override
    public void accept(
            Visitor visitor) {

        visitor.visit(this);
    }
}
```

---

# Step 4: Laptop

```java
public class Laptop
        implements Item {

    private int price;

    public Laptop(int price) {

        this.price = price;
    }

    public int getPrice() {

        return price;
    }

    @Override
    public void accept(
            Visitor visitor) {

        visitor.visit(this);
    }
}
```

---

# Step 5: Concrete Visitor

```java
public class TaxVisitor
        implements Visitor {

    @Override
    public void visit(
            Book book) {

        System.out.println(
                "Book Tax = "
                        + book.getPrice()
                        * 0.05);
    }

    @Override
    public void visit(
            Laptop laptop) {

        System.out.println(
                "Laptop Tax = "
                        + laptop.getPrice()
                        * 0.18);
    }
}
```

---

# Step 6: Client

```java
Visitor taxVisitor =
        new TaxVisitor();

Item book =
        new Book(1000);

Item laptop =
        new Laptop(50000);

book.accept(
        taxVisitor);

laptop.accept(
        taxVisitor);
```

---

Output:

```text
Book Tax = 50

Laptop Tax = 9000
```

---

# 9. Internal Working

Flow:

```text
book.accept(visitor)
       ↓
visitor.visit(book)
```

---

Different method chosen automatically.

---

This leads to:

```text
Double Dispatch
```

---

# 10. Double Dispatch

Most important interview topic.

---

Normally Java uses:

```text
Single Dispatch
```

---

Method chosen based on:

```text
Object Calling Method
```

---

Visitor uses:

```text
Two Objects
```

---

Decision based on:

```text
Visitor Type
+
Element Type
```

---

Hence:

```text
Double Dispatch
```

---

# 11. Single Dispatch Example

```java
animal.makeSound();
```

---

Chosen by:

```text
Animal Type
```

only.

---

# 12. Double Dispatch Example

```java
book.accept(
        taxVisitor);
```

---

Decision based on:

```text
Book
+
TaxVisitor
```

---

Two types involved.

---

This is the heart of Visitor Pattern.

---

# 13. Accept Method

Most important method.

---

Element contains:

```java
accept(Visitor visitor)
```

---

Purpose:

```text
Allow Visitor Entry
```

---

Example:

```java
visitor.visit(this);
```

---

# 14. Object Structure

Collection of elements.

---

Example:

```java
List<Item>
```

---

Visit all:

```java
for(Item item : items) {

    item.accept(visitor);
}
```

---

# 15. New Operation Example

Suppose we need:

```text
Discount Calculation
```

---

Create:

```java
DiscountVisitor
```

---

No changes to:

```java
Book

Laptop
```

---

Big advantage.

---

# 16. File System Example

Elements:

```text
File

Folder
```

---

Visitors:

```text
SizeVisitor

SearchVisitor

BackupVisitor
```

---

New operations added easily.

---

# 17. Compiler Example

Very common usage.

---

AST Nodes:

```text
IfNode

ForNode

ExpressionNode
```

---

Visitors:

```text
OptimizationVisitor

ValidationVisitor

CodeGenerationVisitor
```

---

Compilers heavily use Visitor.

---

# 18. Java Examples

No direct Visitor API.

---

Concept appears in:

```text
Compiler APIs

Annotation Processing

AST Libraries
```

---

# 19. Spring Example

Not a direct Visitor implementation.

---

But many frameworks use:

```text
Traversal + Operation
```

concepts.

---

# 20. Acyclic Visitor

Advanced interview topic.

---

Problem:

Visitor interface becomes huge.

---

Example:

```java
visit(Book)

visit(Laptop)

visit(Mobile)

visit(Car)

visit(...)
```

---

Solution:

Split visitor interfaces.

---

Called:

```text
Acyclic Visitor
```

---

Used in large systems.

---

# 21. Advantages

---

## 1. Open/Closed Principle

Add new operations easily.

---

## 2. Single Responsibility

Operations separated from objects.

---

## 3. Related Logic Together

Tax logic in one class.

---

## 4. Great For Stable Object Structures

Objects rarely change.

---

# 22. Disadvantages

---

## 1. Hard To Add New Element Types

Biggest drawback.

---

Suppose:

```java
Mobile
```

added.

---

Must modify:

```java
Visitor Interface

All Visitors
```

---

Painful.

---

## 2. Breaks Encapsulation Sometimes

Visitor may require access to internals.

---

## 3. Complex Design

Difficult to understand initially.

---

# 23. Visitor vs Strategy

Most asked interview question.

---

## Strategy

Changes algorithm.

---

Example:

```text
Payment Methods
```

---

Client chooses strategy.

---

## Visitor

Adds new operations.

---

Example:

```text
Tax Calculation

Discount Calculation

Export PDF
```

---

### Memory Trick

```text
Strategy
      ↓
Different Algorithm

Visitor
      ↓
Different Operation
```

---

# 24. Visitor vs Decorator

---

## Decorator

Adds behavior to object.

---

Object changes.

---

## Visitor

Adds external operation.

---

Object unchanged.

---

### Memory Trick

```text
Decorator
      ↓
Enhance Object

Visitor
      ↓
Visit Object
```

---

# 25. Visitor vs Command

---

## Command

Encapsulates request.

---

## Visitor

Performs operation on object structure.

---

Different goals.

---

# 26. When To Use Visitor

Use when:

- Object structure stable
- Operations change frequently
- Need multiple reports/calculations

Examples:

```text
Compilers

Tax Engines

File Systems

AST Processing

Document Export
```

---

# 27. When NOT To Use

Avoid when:

- New element types added frequently
- Simple operations exist
- Small systems

---

# 28. Interview Questions

### Q1. What problem does Visitor solve?

Add operations without modifying existing classes.

---

### Q2. What are participants?

```text
Visitor

Concrete Visitor

Element

Concrete Element
```

---

### Q3. What is accept()?

Entry point for visitors.

---

### Q4. What is Double Dispatch?

Method selection based on:

```text
Visitor Type
+
Element Type
```

---

### Q5. Biggest advantage?

Adding new operations.

---

### Q6. Biggest disadvantage?

Adding new element types.

---

### Q7. Give real-world examples.

```text
Tax Calculation

Compiler AST

File System

Document Export
```

---

### Q8. Difference Between Visitor and Strategy?

Strategy:

```text
Algorithm Selection
```

Visitor:

```text
Operation Addition
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
Fixed Workflow

Visitor
     ↓
Add New Operations
```

---

# Quick Revision

```text
Visitor Design Pattern
----------------------

Purpose:
Add operations without modifying classes.

Participants:

1. Visitor
2. Concrete Visitor
3. Element
4. Concrete Element

Key Concepts:

✔ accept()
✔ Double Dispatch
✔ Object Structure

Examples:

✔ Tax Calculation
✔ Compiler AST
✔ File System
✔ Document Export

Advantages:

✔ Open/Closed Principle
✔ Add Operations Easily
✔ Separate Concerns

Disadvantages:

✘ Hard To Add New Elements
✘ Complex Design

Remember:

Visitor
      ↓
Add New Operation

Strategy
      ↓
Change Algorithm

Decorator
      ↓
Add Behavior To Object
```