
## Definition

`@Data` is a Lombok annotation that is a **shortcut for generating multiple commonly used methods in a Java class automatically**, including:

- Getters
- Setters
- `toString()`
- `equals()`
- `hashCode()`

Package:

```java
import lombok.Data;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
getters + setters + toString + equals + hashCode written manually
```

---

With Lombok:

```text id="b2"
@Data generates all of them automatically
```

---

So:

```text id="c3"
@Data = Full boilerplate reduction for POJOs/DTOs
```

---

# Real-Life Analogy

Think of a smartphone factory:

```text id="d4"
Without automation → build every component manually
With automation → full assembly done in one step
```

---

That full automation is:

```text id="e5"
@Data
```

---

# What @Data Includes Internally

When you use `@Data`, Lombok generates:

```text id="f6"
1. Getters for all fields
2. Setters for all non-final fields
3. toString()
4. equals()
5. hashCode()
6. RequiredArgsConstructor (if final fields exist)
```

---

# Basic Example

## Without Lombok

```java id="g7"
public class Employee {

    private String name;
    private int age;

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }

    @Override
    public String toString() {
        return name + " " + age;
    }
}
```

---

## With @Data

```java id="h8"
import lombok.Data;

@Data
public class Employee {

    private String name;
    private int age;
}
```

---

# What Lombok Generates Internally

```java id="i9"
public String getName() { return name; }
public void setName(String name) { this.name = name; }

public String toString() {
    return "Employee(name=" + name + ", age=" + age + ")";
}

public boolean equals(Object o) { ... }
public int hashCode() { ... }
```

---

# Why @Data is Powerful

```text id="j10"
Removes 80% boilerplate in POJOs
Improves readability
Speeds up development
Reduces human error
```

---

# Real Production Example

```java id="k11"
import lombok.Data;

@Data
public class UserDTO {

    private String username;
    private String email;
    private int age;
}
```

Used in:

```text id="l12"
REST API request/response objects
```

---

# Where @Data is Commonly Used

## 1. DTO Classes

```text id="m13"
API request/response models
```

---

## 2. Entity Classes (carefully)

```text id="n14"
JPA entities (with caution)
```

---

## 3. Configuration Models

```text id="o15"
Simple config holders
```

---

# Important Internal Behavior

## equals() and hashCode()

```text id="p16"
Generated based on all fields by default
```

---

## toString()

```text id="q17"
Includes all fields in readable format
```

---

## setters

```text id="r18"
Not generated for final fields
```

---

# @Data vs Individual Lombok Annotations

| Feature | @Data | @Getter + @Setter |
|----------|------|------------------|
| Getters | Yes | Yes |
| Setters | Yes | Yes |
| toString | Yes | No |
| equals/hashCode | Yes | No |
| Control | Less | More granular |

---

# When NOT to Use @Data

## 1. Complex Domain Models

```text id="s19"
Business logic classes should avoid auto equals/hashCode
```

---

## 2. JPA Entities (careful)

```text id="t20"
Can cause lazy loading and performance issues
```

---

## 3. Security-sensitive classes

```text id="u21"
toString() may expose sensitive data
```

---

# Common Mistakes

## Mistake 1: Using everywhere blindly

```text id="v22"
Not suitable for all layers
```

---

## Mistake 2: Unexpected equals/hashCode behavior

```text id="w23"
All fields used → may break logic in collections
```

---

## Mistake 3: Debugging confusion

```text id="x24"
Auto-generated methods are not visible in source
```

---

# How It Works Internally

```text id="y25"
You write @Data
   ↓
Lombok processes during compilation
   ↓
Generates getters, setters, equals, hashCode, toString
   ↓
Final compiled class contains full boilerplate code
```

---

# Common Interview Questions

## What is @Data?

Lombok annotation that generates getters, setters, toString, equals, and hashCode.

---

## Is @Data safe for entities?

Not always; must be used carefully.

---

## Does it work at runtime?

No, compile-time only.

---

## Difference from @Getter/@Setter?

@Data = full package  
@Getter/@Setter = selective control

---

## Why avoid in domain models?

Because equals/hashCode may include unwanted fields.

---

# Enterprise Best Practice

Use `@Data` for:

```text id="z26"
DTOs
Simple POJOs
API request/response models
```

---

Avoid for:

```text id="a27"
JPA entities (unless carefully designed)
Complex business domain models
Security-sensitive objects
```

---

# Key Points To Remember

- `@Data` is a Lombok shortcut for multiple annotations.
- Generates getters, setters, toString, equals, hashCode.
- Reduces boilerplate code heavily.
- Works at compile time.
- Best suited for DTOs and simple models.
- Can be risky in domain or entity classes.
- Improves productivity but reduces fine-grained control.
- Should be used thoughtfully in enterprise apps.
- Very common in Spring Boot projects.
- Important Java interview topic.

---

# Quick Example

```java id="b28"
@Data
public class Product {

    private String name;
    private double price;
}
```

---

# Equivalent Code Summary

```text id="c29"
@Getter + @Setter + toString + equals + hashCode
```

---

# Final Summary

`@Data` is a Lombok annotation that automatically generates getters, setters, toString, equals, and hashCode methods for a class, making Java POJOs much simpler and reducing boilerplate code significantly in enterprise applications.
```