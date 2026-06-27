             # Prototype Design Pattern

> **Goal:** Understand Prototype Design Pattern from scratch, why it exists, shallow copy vs deep copy, Java cloning internals, Cloneable interface, serialization-based cloning, real-world examples, advantages, disadvantages, Spring usage, and interview questions.

---

# 1. What Problem Does Prototype Pattern Solve?

Suppose you are building a game.

Every soldier object requires:

```text
Load Weapon Data
Load Character Data
Load Animation Data
Load Configuration
Load AI Rules
```

Creating one object takes:

```text
5 Seconds
```

---

Now game needs:

```text
1000 Soldiers
```

Without Prototype:

```java
Soldier s1 = new Soldier();
Soldier s2 = new Soldier();
Soldier s3 = new Soldier();
...
```

Each object performs expensive initialization.

---

Result:

```text
Slow Performance
High Memory Usage
Long Startup Time
```

---

Instead:

Create one object.

```java
Soldier original = new Soldier();
```

Then:

```java
Soldier copy1 = original.clone();
Soldier copy2 = original.clone();
Soldier copy3 = original.clone();
```

Much faster.

---

# 2. Definition

> Prototype Pattern creates new objects by copying existing objects instead of creating them from scratch.

---

# 3. Real-Life Example

## Photocopy Machine

Suppose you have:

```text
100 Page Document
```

Need 100 copies.

---

Option 1:

Write document 100 times.

```text
Very Slow
```

---

Option 2:

Create photocopies.

```text
Very Fast
```

---

Prototype Pattern is basically:

```text
Software Photocopy Machine
```

---

# 4. Core Idea

Instead of:

```java
new Object()
```

Use:

```java
existingObject.clone()
```

---

Flow:

```text
Original Object
        ↓
      Clone
        ↓
 New Object Created
```

---

# 5. Structure of Prototype Pattern

```text
Client
   ↓
Prototype Interface
   ↓
Concrete Prototype
   ↓
Clone()
```

---

# 6. Real-Life Scenario

## Employee Management System

Suppose creating Employee requires:

```text
Database Query
API Calls
Configuration Loading
```

---

Create once:

```java
Employee original =
        new Employee();
```

---

Create copies:

```java
Employee copy1 =
        original.clone();

Employee copy2 =
        original.clone();
```

---

No expensive setup repeated.

---

# 7. Java Support For Prototype

Java provides:

```java
Cloneable
```

interface.

---

And:

```java
clone()
```

method from Object class.

---

# 8. First Prototype Example

---

## Employee Class

```java
public class Employee
        implements Cloneable {

    private String name;

    public Employee(String name) {
        this.name = name;
    }

    @Override
    public Employee clone() {

        try {
            return (Employee) super.clone();
        }
        catch (CloneNotSupportedException e) {
            throw new RuntimeException(e);
        }
    }
}
```

---

## Usage

```java
Employee original =
        new Employee("Sumit");

Employee copy =
        original.clone();
```

---

Memory:

```text
original ----> Object 1

copy --------> Object 2
```

---

Different objects.

---

# 9. How clone() Works Internally

When:

```java
super.clone()
```

executes,

JVM:

```text
Allocates Memory
Copies Field Values
Returns New Object
```

---

No constructor called.

Important Interview Point.

---

## Constructor Not Executed

Example:

```java
public Employee() {
    System.out.println("Constructor");
}
```

---

During cloning:

```java
Employee copy =
        original.clone();
```

Output:

```text
(No Constructor Call)
```

---

Because JVM directly copies memory.

---

# 10. Types of Copying

This is the most important Prototype concept.

There are two major types.

```text
1. Shallow Copy
2. Deep Copy
```

---

# 11. Shallow Copy

## Definition

Copies primitive fields.

But reference objects are shared.

---

## Example

### Address

```java
class Address {

    String city;

    Address(String city) {
        this.city = city;
    }
}
```

---

### Employee

```java
class Employee
        implements Cloneable {

    String name;
    Address address;

    @Override
    public Employee clone()
            throws CloneNotSupportedException {

        return (Employee) super.clone();
    }
}
```

---

## Usage

```java
Employee e1 = new Employee();

Employee e2 = e1.clone();
```

---

Memory:

```text
e1
 ↓
Employee Object
 ↓
Address Object

e2
 ↓
Employee Object
 ↓
Same Address Object
```

---

### Diagram

```text
Employee1
    ↓
 Address("Bangalore")

Employee2
    ↓
 Address("Bangalore")
```

Same Address.

---

## Problem

Change:

```java
e2.address.city = "Delhi";
```

---

Result:

```java
e1.address.city
```

also becomes:

```text
Delhi
```

Because both share same Address.

---

# 12. Deep Copy

## Definition

Copies everything.

Creates new nested objects too.

---

### Implementation

```java
@Override
public Employee clone() {

    try {

        Employee cloned =
            (Employee) super.clone();

        cloned.address =
            new Address(
                this.address.city
            );

        return cloned;

    } catch(Exception e) {

        throw new RuntimeException(e);
    }
}
```

---

## Memory

```text
Employee1
    ↓
Address1

Employee2
    ↓
Address2
```

Different Address objects.

---

Now:

```java
e2.address.city = "Delhi";
```

won't affect:

```java
e1.address.city
```

---

# 13. Shallow Copy vs Deep Copy

| Shallow Copy | Deep Copy |
|--------------|-----------|
| Copies references | Copies objects |
| Faster | Slower |
| Less memory | More memory |
| Shared nested objects | Independent nested objects |
| Risky | Safer |

---

### Memory Diagram

---

## Shallow Copy

```text
Employee1 ----+
              |
              ↓
          Address

Employee2 ----+
```

---

## Deep Copy

```text
Employee1
    ↓
 Address1

Employee2
    ↓
 Address2
```

---

# 14. Cloneable Interface

---

## Purpose

Marks class as cloneable.

```java
implements Cloneable
```

---

Without:

```java
implements Cloneable
```

this code:

```java
super.clone();
```

throws:

```java
CloneNotSupportedException
```

---

## Example

```java
public class Employee {

}
```

---

Calling:

```java
employee.clone();
```

causes:

```text
CloneNotSupportedException
```

---

# 15. Prototype Registry

Advanced Prototype Pattern.

---

Instead of creating objects repeatedly:

Store prototypes.

---

### Registry

```java
Map<String, Employee>
```

---

Example:

```java
registry.put(
    "Manager",
    managerPrototype
);
```

---

Usage:

```java
Employee manager =
        registry
          .get("Manager")
          .clone();
```

---

### Flow

```text
Registry
     ↓
Prototype
     ↓
Clone
```

---

# 16. Serialization-Based Deep Copy

Another way to perform deep copy.

---

## Idea

Convert object to bytes.

Then recreate object.

---

Flow:

```text
Object
   ↓
Serialization
   ↓
Byte Stream
   ↓
Deserialization
   ↓
New Object
```

---

### Advantage

Automatic Deep Copy.

---

### Disadvantage

Slow.

---

# 17. Copy Constructor

Modern Java often prefers this.

---

### Example

```java
public Employee(Employee other) {

    this.name = other.name;

    this.address =
        new Address(
            other.address.city
        );
}
```

---

Usage:

```java
Employee copy =
        new Employee(original);
```

---

Advantages:

```text
Readable
Safe
No Cloneable Problems
```

---

# 18. Why Many Developers Avoid Cloneable?

Joshua Bloch (Effective Java) recommends:

```text
Avoid Cloneable
```

---

Reasons:

### 1. Broken Design

```java
clone()
```

is difficult to implement correctly.

---

### 2. Deep Copy Problems

Nested objects must be copied manually.

---

### 3. Exception Handling

```java
CloneNotSupportedException
```

adds complexity.

---

### 4. Mutable Objects

Easy to introduce bugs.

---

Modern preference:

```text
Copy Constructor
or
Builder
```

---

# 19. Prototype Registry Example

Game Character System.

---

Store:

```text
Soldier Prototype
Tank Prototype
Sniper Prototype
```

---

Need new soldier?

```java
prototypeRegistry
    .get("Soldier")
    .clone();
```

---

No expensive creation.

---

# 20. Spring Framework Usage

Spring rarely uses classic Cloneable.

But concept appears in:

```java
Bean Definitions
```

and

```java
Prototype Scope Beans
```

---

### Prototype Bean Scope

```java
@Scope("prototype")
```

Example:

```java
@Component
@Scope("prototype")
public class Employee {
}
```

---

Every request:

```java
context.getBean(Employee.class);
```

creates new object.

---

Important:

```text
Prototype Scope
```

and

```text
Prototype Design Pattern
```

are NOT the same thing.

Interview Favorite Question.

---

# 21. Advantages

---

## 1. Faster Object Creation

Clone faster than complex initialization.

---

## 2. Reduces Expensive Operations

No repeated:

```text
Database Calls
API Calls
Configuration Loading
```

---

## 3. Dynamic Object Creation

Runtime cloning.

---

## 4. Simplifies Creation Logic

Copy existing object.

---

# 22. Disadvantages

---

## 1. Deep Copy Complexity

Hard to implement correctly.

---

## 2. Circular References

Can become complicated.

---

## 3. Cloneable Issues

Old and confusing API.

---

## 4. More Memory Usage

Deep copies consume memory.

---

# 23. Prototype vs Factory

---

## Factory

Creates object from scratch.

```java
factory.create();
```

---

## Prototype

Copies existing object.

```java
prototype.clone();
```

---

### Memory Trick

```text
Factory
   ↓
Create

Prototype
   ↓
Copy
```

---

# 24. Prototype vs Builder

---

## Builder

```text
Build Object Step By Step
```

---

## Prototype

```text
Copy Existing Object
```

---

Example:

Builder:

```java
Car.builder()
   .engine("V8")
   .build();
```

---

Prototype:

```java
Car copy =
        original.clone();
```

---

# 25. When To Use Prototype

Use when:

- Object creation expensive
- Many similar objects needed
- Runtime cloning required
- Initialization cost high

Examples:

```text
Game Characters
Documents
Configuration Objects
Templates
Graphics Objects
```

---

# 26. When NOT To Use Prototype

Avoid when:

- Objects are simple
- Deep copy too complex
- Copy constructors are easier

---

# 27. Interview Questions

### Q1. What is Prototype Pattern?

Prototype creates new objects by copying existing objects.

---

### Q2. What is the difference between Shallow Copy and Deep Copy?

Shallow Copy:

```text
Reference Shared
```

Deep Copy:

```text
New Nested Objects Created
```

---

### Q3. Does clone() call constructor?

No.

JVM copies memory directly.

---

### Q4. Why implement Cloneable?

Without it:

```java
super.clone()
```

throws:

```text
CloneNotSupportedException
```

---

### Q5. Which is safer: Shallow Copy or Deep Copy?

```text
Deep Copy
```

---

### Q6. Why is Cloneable considered broken?

Because:

```text
Complex
Error Prone
Deep Copy Issues
```

---

### Q7. What is Prototype Registry?

A cache of pre-created prototype objects used for cloning.

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
Build Complex Object

Prototype
    ↓
Copy Existing Object
```

---

# Quick Revision

```text
Prototype Design Pattern
------------------------

Purpose:
Create new objects by cloning existing objects.

Techniques:

1. Shallow Copy
   ✔ Fast
   ✘ Shared References

2. Deep Copy
   ✔ Safe
   ✘ More Memory

3. Serialization Copy
   ✔ Automatic Deep Copy
   ✘ Slow

4. Copy Constructor
   ✔ Modern Approach
   ✔ Recommended

Important Concepts:

✔ Cloneable
✔ clone()
✔ Prototype Registry
✔ Deep Copy
✔ Shallow Copy

Advantages:

✔ Fast Object Creation
✔ Reduces Expensive Initialization
✔ Dynamic Object Creation

Disadvantages:

✘ Complex Deep Copy
✘ Cloneable Problems

Remember:

Factory
   ↓
Create

Prototype
   ↓
Copy
```