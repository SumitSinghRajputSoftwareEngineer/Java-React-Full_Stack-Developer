
## Definition

`@Setter` is a Lombok annotation used to **automatically generate setter methods for class fields at compile time**, removing the need to write boilerplate code manually.

Package:

```java
import lombok.Setter;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
private fields + manual setter methods
```

---

With Lombok:

```text id="b2"
@Setter automatically generates setter methods
```

---

So:

```text id="c3"
@Setter = Auto-generate write/update methods for fields
```

---

# Real-Life Analogy

Think of a bank account:

```text id="d4"
Account balance → private data
Deposit/withdraw method → setter (updates value safely)
```

---

Instead of manually writing update methods for every field, Lombok:

```text id="e5"
automatically generates all setters (@Setter)
```

---

# Basic Example

## Without Lombok

```java id="f6"
public class Employee {

    private String name;
    private int age;

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

---

## With @Setter

```java id="g7"
import lombok.Setter;

@Setter
public class Employee {

    private String name;
    private int age;
}
```

---

# What Lombok Generates Internally

```java id="h8"
public void setName(String name) {
    this.name = name;
}

public void setAge(int age) {
    this.age = age;
}
```

---

# Where @Setter Can Be Applied

## 1. On Class Level

```java id="i9"
@Setter
public class Student {
    private String name;
    private int marks;
}
```

👉 Generates setters for ALL fields

---

## 2. On Field Level

```java id="j10"
public class Student {

    @Setter
    private String name;

    private int marks;
}
```

👉 Only `name` gets setter

---

# Advanced Usage

## 1. Access Level Control

```java id="k11"
@Setter(AccessLevel.PRIVATE)
private int id;
```

---

## 2. Combined with @Getter

```java id="l12"
@Getter
@Setter
public class Employee {
    private String name;
}
```

---

## 3. Preventing Modification

You can skip setter for immutability:

```java id="m13"
@Getter
public class Config {
    private final String url = "localhost";
}
```

---

# Real Production Example

```java id="n14"
@Setter
public class UserDTO {

    private String username;
    private String email;
    private int age;
}
```

Used in:

```text id="o15"
Request payload mapping in REST APIs
```

---

# Why @Setter is Used

```text id="p16"
Reduces boilerplate code
Speeds up development
Keeps POJO classes clean
Supports quick object modification
```

---

# How It Works Internally

```text id="q17"
You write @Setter
   ↓
Lombok processes it at compile time
   ↓
It generates setter methods in bytecode
   ↓
Final class contains normal Java setters
```

---

# Important Notes

```text id="r18"
- Works at compile time, not runtime
- Requires Lombok dependency + IDE plugin
- Does not modify source code directly
```

---

# Common Use Cases

## 1. DTO Classes

```text id="s19"
Used for request/response mapping in APIs
```

---

## 2. Entity Classes

```text id="t20"
JPA entities where fields may change
```

---

## 3. Service Models

```text id="u21"
Mutable data objects in business logic
```

---

# @Setter vs Manual Setter

| Feature | Manual | @Setter |
|----------|--------|----------|
| Code writing | High | None |
| Maintenance | Hard | Easy |
| Boilerplate | Yes | No |
| Readability | Medium | High |

---

# Common Mistakes

## Mistake 1: Using @Setter everywhere

```text id="v22"
Breaks immutability and increases risk of bugs
```

---

## Mistake 2: Forgetting Lombok setup

```text id="w23"
Compilation errors or IDE warnings
```

---

## Mistake 3: Exposing sensitive fields

```text id="x24"
Security risk if setters allow unwanted modification
```

---

# Common Interview Questions

## What is @Setter?

Lombok annotation that generates setter methods automatically.

---

## Where is it used?

DTOs, entities, and mutable models.

---

## Does it work at runtime?

No, it works at compile time.

---

## Can it be applied to fields?

Yes.

---

## Can we control access level?

Yes, using AccessLevel (e.g., PRIVATE, PROTECTED).

---

# Enterprise Best Practice

Use `@Setter` for:

```text id="y25"
DTOs (request objects)
Mutable entities
Internal models requiring updates
```

---

Avoid for:

```text id="z26"
Immutable objects
Security-sensitive fields
Core business domain models (prefer controlled methods)
```

---

# Key Points To Remember

- `@Setter` auto-generates setter methods using Lombok.
- Reduces boilerplate code significantly.
- Can be applied at class or field level.
- Works at compile time.
- Supports access-level control.
- Common in DTOs and JPA entities.
- Should be avoided in immutable designs.
- Improves development speed and readability.
- Requires Lombok dependency and IDE plugin.
- Important Java/Spring interview topic.

---

# Quick Example

```java id="a27"
@Setter
public class Product {

    private String name;
    private double price;
}
```

---

# Equivalent Java Code

```java id="b28"
public void setName(String name) {
    this.name = name;
}

public void setPrice(double price) {
    this.price = price;
}
```

---

# Final Summary

`@Setter` is a Lombok annotation that automatically generates setter methods for class fields at compile time, reducing boilerplate code and enabling easy object modification in Java applications.
```