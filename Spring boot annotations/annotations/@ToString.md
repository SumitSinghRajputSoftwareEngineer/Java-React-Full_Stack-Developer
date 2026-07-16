
## Definition

`@ToString` is a Lombok annotation used to **automatically generate a `toString()` method** for a class, which returns a readable string representation of the object.

Package:

```java
import lombok.ToString;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
You manually write toString() method
```

---

With Lombok:

```text id="b2"
@ToString automatically generates it
```

---

So:

```text id="c3"
@ToString = auto-generate object readable output
```

---

# Real-Life Analogy

Think of a student ID card:

```text id="d4"
It shows all key details:
- Name
- ID
- Class
```

---

That “identity display format” is:

```text id="e5"
@ToString
```

---

# Basic Example

## Without Lombok

```java id="f6"
public class User {

    private String name;
    private int age;

    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + "}";
    }
}
```

---

## With @ToString

```java id="g7"
import lombok.ToString;

@ToString
public class User {

    private String name;
    private int age;
}
```

---

# What Lombok Generates Internally

```java id="h8"
public String toString() {
    return "User(name=" + name + ", age=" + age + ")";
}
```

---

# Why @ToString is Useful

```text id="i9"
Easy debugging
Better logging
Readable object output
Removes boilerplate code
```

---

# Real Production Example

```java id="j10"
import lombok.ToString;

@ToString
public class Employee {

    private String name;
    private String department;
    private int salary;
}
```

---

## Output Example

```text id="k11"
Employee(name=Amit, department=IT, salary=50000)
```

---

# Advanced Features

## 1. Exclude Fields

```java id="l12"
@ToString(exclude = "salary")
public class Employee {

    private String name;
    private String department;
    private int salary;
}
```

---

## 2. Include Only Selected Fields

```java id="m13"
@ToString(of = {"name", "department"})
```

---

## 3. Call Superclass toString

```java id="n14"
@ToString(callSuper = true)
```

---

# Important Use Cases

## 1. Debugging

```text id="o15"
Quickly print object state in logs
```

---

## 2. Logging

```text id="p16"
Useful in service and controller logs
```

---

## 3. Development Testing

```text id="q17"
Inspect object values easily
```

---

# @ToString vs Manual Method

| Feature | Manual | @ToString |
|----------|--------|-----------|
| Code writing | High | None |
| Readability | Medium | High |
| Maintenance | Hard | Easy |
| Debugging support | Same | Same |

---

# How It Works Internally

```text id="r18"
You add @ToString
   ↓
Lombok processes class at compile time
   ↓
Generates toString() method
   ↓
Final class contains readable object representation
```

---

# Common Mistakes

## Mistake 1: Including sensitive data

```text id="s19"
Password or tokens may be exposed in logs
```

---

## Mistake 2: Infinite recursion (bi-directional relations)

```text id="t20"
Can cause StackOverflowError in JPA entities
```

---

## Mistake 3: Overusing in entities

```text id="u21"
Can slow down logging or expose lazy-loaded fields
```

---

# Best Practices

## Use @ToString for:

```text id="v22"
DTOs
POJOs
Debugging models
Simple entity classes
```

---

## Avoid for:

```text id="w23"
Security-sensitive objects
Large JPA entities with relationships
Performance-critical logging paths
```

---

# Common Interview Questions

## What is @ToString?

Lombok annotation that generates a toString() method.

---

## Why is it used?

For debugging and logging object data.

---

## Can we exclude fields?

Yes, using exclude or of attributes.

---

## Is it safe for entities?

Not always, due to lazy loading and recursion risks.

---

## Does it work at runtime?

No, compile-time only.

---

# Enterprise Best Practice

Use `@ToString` for:

```text id="x24"
DTOs
Logging models
Simple POJOs
Debug-friendly classes
```

---

Avoid for:

```text id="y25"
Sensitive data models
Complex JPA relationships
Large nested object graphs
```

---

# Key Points To Remember

- `@ToString` auto-generates toString() method.
- Helps in debugging and logging.
- Works at compile time via Lombok.
- Supports exclusion and field selection.
- Can include superclass fields if needed.
- Must be used carefully in JPA entities.
- Avoid exposing sensitive information.
- Improves code readability and maintenance.
- Common in Spring Boot applications.
- Important Java interview topic.

---

# Quick Example

```java id="z26"
@ToString
public class Product {

    private String name;
    private double price;
}
```

---

## Output

```text id="a27"
Product(name=Phone, price=50000)
```

---

# Final Summary

`@ToString` is a Lombok annotation that automatically generates a readable string representation of a Java object, making debugging and logging easier while reducing boilerplate code.
```