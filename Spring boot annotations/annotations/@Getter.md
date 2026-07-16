
## Definition

`@Getter` is a Lombok annotation used to **automatically generate getter methods for class fields at compile time**, so you don’t need to write boilerplate code manually.

Package:

```java
import lombok.Getter;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
private fields + manual getter methods
```

---

With Lombok:

```text id="b2"
@Getter automatically generates getters
```

---

So:

```text id="c3"
@Getter = Auto-generate read methods for fields
```

---

# Real-Life Analogy

Think of a locker system:

```text id="d4"
Locker → private data (cannot access directly)
Key → getter method (safe access)
```

---

Instead of manually creating keys for every locker, Lombok:

```text id="e5"
automatically generates all keys (@Getter)
```

---

# Basic Example

## Without Lombok

```java id="f6"
public class Employee {

    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

---

## With @Getter

```java id="g7"
import lombok.Getter;

@Getter
public class Employee {

    private String name;
    private int age;
}
```

---

# What Lombok Generates Internally

```java id="h8"
public String getName() {
    return this.name;
}

public int getAge() {
    return this.age;
}
```

---

# Where @Getter Can Be Applied

## 1. On Class Level

```java id="i9"
@Getter
public class Student {
    private String name;
    private int marks;
}
```

👉 Generates getters for ALL fields

---

## 2. On Field Level

```java id="j10"
public class Student {

    @Getter
    private String name;

    private int marks;
}
```

👉 Only `name` gets getter

---

# Advanced Usage

## 1. Access Level Control

```java id="k11"
@Getter
private int id;
```

---

## 2. Combined with @Setter

```java id="l12"
@Getter
@Setter
public class Employee {
    private String name;
}
```

---

## 3. Final Fields Support

```java id="m13"
@Getter
public class Config {

    private final String url = "localhost";
}
```

---

# Real Production Example

```java id="n14"
@Getter
public class UserDTO {

    private String username;
    private String email;
    private int age;
}
```

---

# Why @Getter is Used

```text id="o15"
Reduces boilerplate code
Improves readability
Faster development
Cleaner POJOs / DTOs
```

---

# How It Works Internally

```text id="p16"
You write code with @Getter
   ↓
Lombok processes annotation during compilation
   ↓
It injects getter methods into bytecode
   ↓
Compiled class contains normal Java getters
```

---

# Important Notes

```text id="q17"
- Works at compile time, not runtime
- Requires Lombok dependency + IDE plugin
- Does not change source code, only compiled output
```

---

# Common Use Cases

## 1. DTO Classes

```text id="r18"
API request/response models
```

---

## 2. Entity Classes

```text id="s19"
JPA entities
```

---

## 3. Configuration Classes

```text id="t20"
Immutable config objects
```

---

# @Getter vs Manual Getter

| Feature | Manual | @Getter |
|----------|--------|--------|
| Code writing | High | None |
| Maintenance | Hard | Easy |
| Readability | Medium | High |
| Boilerplate | Yes | No |

---

# Common Mistakes

## Mistake 1: Forgetting Lombok dependency

```text id="u21"
Project fails to compile
```

---

## Mistake 2: IDE not configured

```text id="v22"
Code compiles but IDE shows errors
```

---

## Mistake 3: Expecting runtime behavior change

```text id="w23"
Lombok works only at compile time
```

---

# Common Interview Questions

## What is @Getter?

Lombok annotation that generates getter methods automatically.

---

## Where is it used?

DTOs, entities, model classes.

---

## Does it generate code at runtime?

No, it works at compile time.

---

## Can we apply it on fields?

Yes.

---

## What is alternative?

Manual getter methods or IDE generation.

---

# Enterprise Best Practice

Use `@Getter` for:

```text id="x24"
DTOs
Immutable objects
Entity models
Configuration classes
```

---

Avoid for:

```text id="y25"
Complex business logic classes
Security-sensitive custom access logic
When custom getter logic is required
```

---

# Key Points To Remember

- `@Getter` auto-generates getter methods using Lombok.
- Reduces boilerplate code significantly.
- Can be applied at class or field level.
- Works at compile time, not runtime.
- Requires Lombok dependency and IDE support.
- Commonly used in DTOs and entities.
- Improves code readability and maintainability.
- Does not change business logic.
- Widely used in Spring Boot projects.
- Important Java interview topic.

---

# Quick Example

```java id="z26"
@Getter
public class Product {

    private String name;
    private double price;
}
```

---

# Equivalent Java Code

```java id="a27"
public String getName() {
    return name;
}

public double getPrice() {
    return price;
}
```

---

# Final Summary

`@Getter` is a Lombok annotation that automatically generates getter methods for class fields at compile time, reducing boilerplate code and improving readability in Java applications.
```