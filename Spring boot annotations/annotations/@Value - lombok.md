
## Definition

`@Value` is a Lombok annotation used to create an **immutable (read-only) class**, where all fields are **final**, and Lombok automatically generates:

- Getters
- `toString()`
- `equals()`
- `hashCode()`
- All-args constructor

Package:

```java
import lombok.Value;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
final fields + constructor + getters + equals + hashCode + toString
```

---

With Lombok:

```text id="b2"
@Value generates everything automatically for immutable objects
```

---

So:

```text id="c3"
@Value = immutable @Data
```

---

# Real-Life Analogy

Think of a printed ticket (like train ticket):

```text id="d4"
Once issued:
- You cannot change name
- You cannot change seat
- You cannot modify details
```

---

That “fixed, unchangeable object” is:

```text id="e5"
@Value
```

---

# Basic Example

## Without Lombok

```java id="f6"
public final class User {

    private final String name;
    private final int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }
    public int getAge() { return age; }

    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + "}";
    }
}
```

---

## With @Value

```java id="g7"
import lombok.Value;

@Value
public class User {

    String name;
    int age;
}
```

---

# What Lombok Generates Internally

```java id="h8"
private final String name;
private final int age;

public User(String name, int age) {
    this.name = name;
    this.age = age;
}

public String getName() { return name; }
public int getAge() { return age; }

public boolean equals(Object o) { ... }
public int hashCode() { ... }

public String toString() {
    return "User(name=" + name + ", age=" + age + ")";
}
```

---

# Key Features of @Value

## 1. Immutable Class

```text id="i9"
All fields become final
No setters generated
```

---

## 2. Thread-Safe by Design

```text id="j10"
Immutability reduces concurrency issues
```

---

## 3. Auto-generated Constructor

```text id="k11"
All-args constructor is created
```

---

# Real Production Example

```java id="l12"
import lombok.Value;

@Value
public class EmployeeDTO {

    String name;
    String email;
    int age;
}
```

---

## Usage

```java id="m13"
EmployeeDTO emp = new EmployeeDTO("Amit", "amit@test.com", 30);
```

---

# Why @Value is Important

```text id="n14"
Ensures immutability
Prevents accidental modification
Improves thread safety
Reduces bugs in enterprise systems
```

---

# @Value vs @Data

| Feature | @Value | @Data |
|----------|--------|-------|
| Mutability | Immutable | Mutable |
| Setters | ❌ No | ✅ Yes |
| Fields | final | non-final |
| Thread safety | High | Medium |
| Use case | DTO/value objects | general POJOs |

---

# Advanced Behavior

## 1. Fields are final by default

```text id="o15"
You cannot change values after creation
```

---

## 2. Class becomes final

```text id="p16"
Cannot be extended
```

---

## 3. No setters generated

```text id="q17"
Ensures immutability
```

---

# Real Use Cases

## 1. DTOs

```text id="r18"
API response objects
```

---

## 2. Value Objects (DDD)

```text id="s19"
Objects defined by value, not identity
```

---

## 3. Thread-safe data transfer

```text id="t20"
Used in concurrent systems
```

---

## 4. Microservice communication

```text id="u21"
Kafka / REST payloads
```

---

# Common Mistakes

## Mistake 1: Trying to modify fields

```text id="v22"
Compilation error or design confusion
```

---

## Mistake 2: Using in JPA entities

```text id="w23"
Hibernate requires mutability
```

---

## Mistake 3: Expecting setters

```text id="x24"
@Value does NOT generate setters
```

---

# Common Interview Questions

## What is @Value?

Lombok annotation for immutable classes.

---

## How is it different from @Data?

@Data = mutable  
@Value = immutable

---

## Are fields final?

Yes, by default.

---

## Does it generate constructor?

Yes, all-args constructor.

---

## Is it thread-safe?

Yes, due to immutability.

---

# Enterprise Best Practice

Use `@Value` for:

```text id="y25"
DTOs
Value objects
Event messages
Read-only models
```

---

Avoid for:

```text id="z26"
JPA entities
Mutable business objects
Classes requiring updates
```

---

# Key Points To Remember

- `@Value` creates immutable classes.
- All fields become final automatically.
- No setters are generated.
- Generates constructor, getters, equals, hashCode, toString.
- Class becomes final and cannot be extended.
- Ideal for DTOs and value objects.
- Improves thread safety and reliability.
- Works at compile time via Lombok.
- Common in modern microservices design.
- Important Java interview topic.

---

# Quick Example

```java id="a27"
@Value
public class Product {

    String name;
    double price;
}
```

---

## Usage

```java id="b28"
Product p = new Product("Laptop", 50000);
```

---

# Final Summary

`@Value` is a Lombok annotation that creates immutable classes by generating final fields, constructor, getters, equals, hashCode, and toString automatically, making it ideal for thread-safe and read-only data models in Java applications.
```