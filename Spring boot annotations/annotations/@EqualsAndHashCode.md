
## Definition

`@EqualsAndHashCode` is a Lombok annotation used to **automatically generate `equals()` and `hashCode()` methods** for a Java class.

Package:

```java
import lombok.EqualsAndHashCode;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
You manually implement equals() and hashCode()
```

---

With Lombok:

```text id="b2"
@EqualsAndHashCode generates both automatically
```

---

So:

```text id="c3"
@EqualsAndHashCode = object comparison + hashing logic auto-generated
```

---

# Real-Life Analogy

Think of Aadhaar verification:

```text id="d4"
Two people are considered same if:
- Same Aadhaar number
```

---

That identity matching system is:

```text id="e5"
@EqualsAndHashCode
```

---

# Basic Example

## Without Lombok

```java id="f6"
public class User {

    private String name;
    private int age;

    @Override
    public boolean equals(Object o) {
        // complex comparison logic
    }

    @Override
    public int hashCode() {
        // hashing logic
    }
}
```

---

## With @EqualsAndHashCode

```java id="g7"
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class User {

    private String name;
    private int age;
}
```

---

# What Lombok Generates Internally

```java id="h8"
@Override
public boolean equals(Object o) {
    // compares all fields
}

@Override
public int hashCode() {
    // generates hash based on fields
}
```

---

# Why equals() and hashCode() Matter

```text id="i9"
Used in:
- HashMap
- HashSet
- Collections
- Object comparison
```

---

# Real Production Example

```java id="j10"
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class Employee {

    private String id;
    private String name;
}
```

---

## Usage in HashSet

```java id="k11"
Set<Employee> set = new HashSet<>();

set.add(new Employee("101", "Amit"));
set.add(new Employee("101", "Amit"));
```

👉 Only one object stored because equality is based on fields.

---

# How It Works Internally

```text id="l12"
You write @EqualsAndHashCode
   ↓
Lombok scans fields
   ↓
Generates equals() and hashCode()
   ↓
Compiled class uses these methods automatically
```

---

# Advanced Features

## 1. Exclude Fields

```java id="m13"
@EqualsAndHashCode(exclude = "password")
public class User {

    private String username;
    private String password;
}
```

---

## 2. Include Specific Fields Only

```java id="n14"
@EqualsAndHashCode(of = {"id"})
```

👉 Only `id` is used for comparison

---

## 3. Include Superclass Fields

```java id="o15"
@EqualsAndHashCode(callSuper = true)
```

---

# Important Use Cases

## 1. Collections (HashSet / HashMap)

```text id="p16"
Ensures correct duplicate detection
```

---

## 2. Entity Identity

```text id="q17"
Database entity equality based on ID
```

---

## 3. DTO Comparison

```text id="r18"
Compare request/response objects
```

---

# @EqualsAndHashCode vs Manual Implementation

| Feature | Manual | Lombok |
|----------|--------|--------|
| Code writing | High | None |
| Error-prone | Yes | No |
| Maintenance | Hard | Easy |
| Readability | Medium | High |

---

# Important Behavior Rules

## 1. Default behavior

```text id="s19"
Uses all non-static fields
```

---

## 2. Static fields ignored

```text id="t20"
static fields are NOT part of equality
```

---

## 3. Transient fields ignored

```text id="u21"
transient fields are excluded
```

---

# Common Mistakes

## Mistake 1: Using all fields in entities

```text id="v22"
Can break JPA entity identity logic
```

---

## Mistake 2: Including mutable fields

```text id="w23"
Changing fields affects HashMap behavior
```

---

## Mistake 3: Ignoring business key design

```text id="x24"
Equality should be based on ID, not all fields
```

---

# Best Practices

## Use for:

```text id="y25"
DTOs
Value objects
Immutable classes
Entities with stable IDs
```

---

## Avoid for:

```text id="z26"
Entities with complex relationships
Classes with frequently changing fields
Security-sensitive objects
```

---

# Common Interview Questions

## What is @EqualsAndHashCode?

Generates equals() and hashCode() methods automatically.

---

## Why is hashCode important?

Used in HashMap and HashSet for fast lookup.

---

## What fields are included?

All non-static fields by default.

---

## Can we customize fields?

Yes, using of or exclude.

---

## Difference between equals and ==?

equals → logical comparison  
== → reference comparison

---

# Enterprise Best Practice

Use `@EqualsAndHashCode` for:

```text id="a27"
DTOs
Value objects
Simple entities
Collections-based models
```

---

Avoid for:

```text id="b28"
Complex JPA relationships
Mutable domain objects
Security-sensitive models
```

---

# Key Points To Remember

- `@EqualsAndHashCode` generates equals() and hashCode().
- Important for collections like HashSet and HashMap.
- Uses all fields by default (except static/transient).
- Can customize included/excluded fields.
- Must be carefully used in JPA entities.
- Works at compile time via Lombok.
- Helps prevent duplicate object issues.
- Improves code simplicity and maintainability.
- Critical for object identity logic.
- Very common Java interview topic.

---

# Quick Example

```java id="c29"
@EqualsAndHashCode
public class Product {

    private String name;
    private int id;
}
```

---

# Final Summary

`@EqualsAndHashCode` is a Lombok annotation that automatically generates `equals()` and `hashCode()` methods, enabling proper object comparison and hashing behavior in Java collections and applications while reducing boilerplate code.
```