
## Definition

`@Builder` is a Lombok annotation that implements the **Builder Design Pattern automatically**, allowing you to create objects in a clean, readable, and flexible way without writing constructor boilerplate.

Package:

```java
import lombok.Builder;
```

---

# Simple Understanding

Normally Java object creation looks like:

```text id="a1"
new Object(all parameters in constructor)
```

Problem:

```text id="b2"
Hard to read, error-prone when many parameters exist
```

---

With `@Builder`:

```text id="c3"
Object creation becomes step-by-step and readable
```

---

So:

```text id="d4"
@Builder = Fluent object construction pattern
```

---

# Real-Life Analogy

Think of ordering a customized burger:

```text id="e5"
Instead of fixed combo → you choose step by step:
- bun
- cheese
- sauce
- toppings
```

---

That customizable ordering system is:

```text id="f6"
@Builder
```

---

# Basic Example

## Without Builder

```java id="g7"
public class User {

    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

Usage:

```java id="h8"
User user = new User("Sumit", 25);
```

---

## With @Builder

```java id="i9"
import lombok.Builder;

@Builder
public class User {

    private String name;
    private int age;
}
```

---

## Object Creation

```java id="j10"
User user = User.builder()
        .name("Sumit")
        .age(25)
        .build();
```

---

# What Lombok Generates Internally

```java id="k11"
public static class UserBuilder {

    private String name;
    private int age;

    public UserBuilder name(String name) {
        this.name = name;
        return this;
    }

    public UserBuilder age(int age) {
        this.age = age;
        return this;
    }

    public User build() {
        return new User(name, age);
    }
}
```

---

# Why @Builder is Powerful

```text id="l12"
Avoids constructor confusion
Improves readability
Supports optional parameters
Prevents parameter ordering issues
```

---

# Real Production Example

```java id="m13"
import lombok.Builder;

@Builder
public class EmployeeDTO {

    private String name;
    private String email;
    private int age;
    private String department;
}
```

---

## Usage

```java id="n14"
EmployeeDTO emp = EmployeeDTO.builder()
        .name("Amit")
        .email("amit@test.com")
        .department("IT")
        .build();
```

---

# Key Features

## 1. Fluent API

```text id="o15"
Method chaining style
```

---

## 2. Optional Fields

```text id="p16"
No need to pass all parameters
```

---

## 3. Readable Code

```text id="q17"
Self-explanatory object creation
```

---

# Advanced Usage

## 1. Default Values

```java id="r18"
@Builder
public class Product {

    @Builder.Default
    private String category = "General";
}
```

---

## 2. Combine with @Getter

```java id="s19"
@Getter
@Builder
public class User {
    private String name;
}
```

---

## 3. Builder on Constructor

```java id="t20"
public class User {

    private String name;
    private int age;

    @Builder
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

---

# @Builder vs Constructor

| Feature | Constructor | @Builder |
|----------|------------|----------|
| Readability | Low | High |
| Optional params | Hard | Easy |
| Parameter order issues | Yes | No |
| Flexibility | Low | High |

---

# Real Use Cases

## 1. DTO Creation

```text id="u21"
REST API request/response objects
```

---

## 2. Microservices Payloads

```text id="v22"
Kafka / REST / Feign requests
```

---

## 3. Complex Object Creation

```text id="w23"
Objects with many optional fields
```

---

# Common Mistakes

## Mistake 1: Overusing builder everywhere

```text id="x24"
Not needed for simple objects
```

---

## Mistake 2: Ignoring validation

```text id="y25"
Builder does NOT enforce business rules automatically
```

---

## Mistake 3: Expecting immutability automatically

```text id="z26"
Objects are mutable unless designed otherwise
```

---

# Common Interview Questions

## What is @Builder?

Lombok annotation that implements builder pattern automatically.

---

## Why use Builder pattern?

To simplify object creation with many parameters.

---

## Difference between constructor and builder?

Builder improves readability and flexibility.

---

## Does it support default values?

Yes, using @Builder.Default.

---

## Is it thread-safe?

Depends on object design, not builder itself.

---

# How It Works Internally

```text id="a27"
You write @Builder
   ↓
Lombok generates inner Builder class
   ↓
Provides chaining methods
   ↓
build() creates final object
```

---

# Enterprise Best Practice

Use `@Builder` for:

```text id="b28"
DTOs
API payloads
Complex object creation
Microservice communication objects
```

---

Avoid for:

```text id="c29"
Simple objects (1–2 fields)
Highly mutable domain entities
Performance-critical tight loops
```

---

# Key Points To Remember

- `@Builder` implements builder design pattern.
- Improves readability for object creation.
- Prevents constructor overload problems.
- Supports optional parameters easily.
- Works at compile time using Lombok.
- Common in DTO and microservice design.
- Generates fluent API for object creation.
- Helps avoid parameter confusion.
- Can be combined with @Getter and @Setter.
- Very common in Spring Boot projects.

---

# Quick Example

```java id="d30"
@Builder
public class Order {

    private String product;
    private int quantity;
}
```

---

## Usage

```java id="e31"
Order order = Order.builder()
        .product("Laptop")
        .quantity(2)
        .build();
```

---

# Final Summary

`@Builder` is a Lombok annotation that automatically implements the Builder design pattern, enabling clean, readable, and flexible object creation in Java, especially useful for DTOs and complex objects in enterprise applications.
```