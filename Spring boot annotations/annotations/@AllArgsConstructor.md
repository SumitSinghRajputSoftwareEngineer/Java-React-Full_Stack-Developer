
## Definition

`@AllArgsConstructor` is a Lombok annotation used to **automatically generate a constructor that includes all fields of a class as parameters**.

Package:

```java
import lombok.AllArgsConstructor;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
You manually write a constructor with all fields as arguments
```

---

With Lombok:

```text id="b2"
@AllArgsConstructor generates it automatically
```

---

So:

```text id="c3"
@AllArgsConstructor = full-parameter constructor generator
```

---

# Real-Life Analogy

Think of ordering a full meal combo:

```text id="d4"
You specify everything at once:
- starter
- main course
- dessert
- drink
```

---

That complete order system is:

```text id="e5"
@AllArgsConstructor
```

---

# Basic Example

## Without Lombok

```java id="f6"
public class User {

    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

---

## With @AllArgsConstructor

```java id="g7"
import lombok.AllArgsConstructor;

@AllArgsConstructor
public class User {

    private String name;
    private int age;
}
```

---

# What Lombok Generates Internally

```java id="h8"
public User(String name, int age) {
    this.name = name;
    this.age = age;
}
```

---

# Why @AllArgsConstructor is Used

```text id="i9"
Avoids manual constructor writing
Useful for DTOs
Supports dependency injection
Improves readability
```

---

# Real Production Example

## DTO Class

```java id="j10"
import lombok.AllArgsConstructor;

@AllArgsConstructor
public class EmployeeDTO {

    private String name;
    private String email;
    private int age;
}
```

---

## Usage

```java id="k11"
EmployeeDTO emp = new EmployeeDTO("Amit", "amit@test.com", 30);
```

---

# How It Works Internally

```text id="l12"
You write @AllArgsConstructor
   ↓
Lombok processes annotation at compile time
   ↓
Generates constructor with all fields
   ↓
Class becomes normal Java class with constructor
```

---

# @AllArgsConstructor Behavior Rules

## 1. Includes all fields

```text id="m13"
Even private, final fields
```

---

## 2. Order matters

```text id="n14"
Parameter order = field declaration order
```

---

## 3. Works with final fields

```text id="o15"
Yes, supports initialization of final fields
```

---

# Advanced Usage

## 1. Combine with @NoArgsConstructor

```java id="p16"
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private int age;
}
```

---

## 2. Combine with @Data

```java id="q17"
@Data
@AllArgsConstructor
public class User {
    private String name;
    private int age;
}
```

---

## 3. Combine with @Builder

```java id="r18"
@AllArgsConstructor
@Builder
public class User {
    private String name;
    private int age;
}
```

---

# @AllArgsConstructor vs Other Constructors

| Annotation | Purpose |
|------------|--------|
| @NoArgsConstructor | Empty constructor |
| @AllArgsConstructor | Constructor with all fields |
| @RequiredArgsConstructor | Only final/@NonNull fields |

---

# Real Use Cases

## 1. DTO Creation

```text id="s19"
Used for API request/response objects
```

---

## 2. Dependency Injection (Constructor-based)

```text id="t20"
Preferred in Spring services
```

---

## 3. Data Mapping

```text id="u21"
Mapping database/entity → DTO
```

---

# Common Mistakes

## Mistake 1: Too many fields

```text id="v22"
Constructor becomes hard to maintain
```

---

## Mistake 2: Using for mutable domain models

```text id="w23"
Breaks encapsulation if misused
```

---

## Mistake 3: Confusing with @RequiredArgsConstructor

```text id="x24"
@AllArgsConstructor = all fields  
@RequiredArgsConstructor = only final/@NonNull fields
```

---

# Common Interview Questions

## What is @AllArgsConstructor?

Generates constructor with all class fields as parameters.

---

## Difference from @NoArgsConstructor?

AllArgsConstructor → full initialization  
NoArgsConstructor → empty constructor

---

## Where is it used?

DTOs, services, mapping objects.

---

## Does it support final fields?

Yes.

---

## What is parameter order?

Same as field declaration order.

---

# Enterprise Best Practice

Use `@AllArgsConstructor` for:

```text id="y25"
DTOs
Service injection constructors
Immutable object creation
Data mapping layers
```

---

Avoid for:

```text id="z26"
Classes with many optional fields (use @Builder instead)
Overly complex domain objects
```

---

# Key Points To Remember

- `@AllArgsConstructor` generates a constructor with all fields.
- Reduces boilerplate constructor code.
- Field order determines parameter order.
- Supports final fields initialization.
- Commonly used in DTOs and Spring services.
- Works at compile time using Lombok.
- Often combined with @NoArgsConstructor and @Builder.
- Improves code clarity and development speed.
- Not suitable for highly complex object creation (use builder instead).
- Important Java and Spring Boot interview topic.

---

# Quick Example

```java id="a27"
@AllArgsConstructor
public class Product {

    private String name;
    private double price;
}
```

---

# Equivalent Code

```java id="b28"
public Product(String name, double price) {
    this.name = name;
    this.price = price;
}
```

---

# Final Summary

`@AllArgsConstructor` is a Lombok annotation that automatically generates a constructor containing all class fields as parameters, making object creation simple and reducing boilerplate code in Java applications.
```