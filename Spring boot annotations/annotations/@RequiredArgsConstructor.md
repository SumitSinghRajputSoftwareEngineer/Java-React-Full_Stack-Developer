
## Definition

`@RequiredArgsConstructor` is a Lombok annotation used to **generate a constructor only for required fields**, i.e.:

- `final` fields  
- fields annotated with `@NonNull`

Package:

```java
import lombok.RequiredArgsConstructor;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
You manually create constructor only for mandatory fields
```

---

With Lombok:

```text id="b2"
@RequiredArgsConstructor automatically generates it
```

---

So:

```text id="c3"
@RequiredArgsConstructor = constructor for mandatory fields only
```

---

# Real-Life Analogy

Think of opening a bank account:

```text id="d4"
Required:
- Aadhaar
- PAN

Optional:
- Email
- Phone number
```

---

That “only mandatory fields constructor” system is:

```text id="e5"
@RequiredArgsConstructor
```

---

# Basic Example

## Without Lombok

```java id="f6"
public class User {

    private final String name;
    private final int age;
    private String email;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

---

## With @RequiredArgsConstructor

```java id="g7"
import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
public class User {

    private final String name;
    private final int age;
    private String email;
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

# What Fields Are Included?

## 1. final fields

```java id="i9"
private final String name;
```

---

## 2. @NonNull fields

```java id="j10"
@NonNull
private String email;
```

---

Generated constructor:

```java id="k11"
public User(String name, String email) {
    if (email == null) throw new NullPointerException("email");
    this.name = name;
    this.email = email;
}
```

---

# Why @RequiredArgsConstructor is Important

```text id="l12"
Avoids constructor clutter
Ensures mandatory fields are initialized
Improves immutability design
Works well with Spring DI
```

---

# Real Production Example

## Service Class (Spring Boot)

```java id="m13"
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class UserService {

    private final UserRepository userRepository;

    public void saveUser(User user) {
        userRepository.save(user);
    }
}
```

---

### Why it works well?

```text id="n14"
Spring injects dependencies via constructor automatically
Lombok generates that constructor
```

---

# How It Works Internally

```text id="o15"
You write @RequiredArgsConstructor
   ↓
Lombok scans class fields
   ↓
Selects final + @NonNull fields
   ↓
Generates constructor
   ↓
Compiler produces normal Java class
```

---

# @RequiredArgsConstructor Rules

## 1. Includes only required fields

```text id="p16"
final fields + @NonNull fields
```

---

## 2. Ignores normal fields

```java id="q17"
private String optionalField; // NOT included
```

---

## 3. Null checks for @NonNull

```text id="r18"
Automatically adds validation
```

---

# Advanced Usage

## 1. With @NonNull

```java id="s19"
@RequiredArgsConstructor
public class User {

    private final String name;

    @NonNull
    private String email;
}
```

---

## 2. With Spring Dependency Injection

```java id="t20"
@Service
@RequiredArgsConstructor
public class OrderService {

    private final OrderRepository repo;
}
```

---

## 3. With @Data

```java id="u21"
@Data
@RequiredArgsConstructor
public class User {

    private final String name;
}
```

---

# @RequiredArgsConstructor vs Other Constructors

| Annotation | Includes |
|------------|----------|
| @NoArgsConstructor | No fields |
| @AllArgsConstructor | All fields |
| @RequiredArgsConstructor | Only final + @NonNull |

---

# Real Use Cases

## 1. Spring Services

```text id="v22"
Constructor injection for dependencies
```

---

## 2. Immutable Objects

```text id="w23"
Ensures required fields are set
```

---

## 3. DTOs with mandatory fields

```text id="x24"
API request validation structure
```

---

# Common Mistakes

## Mistake 1: Assuming all fields included

```text id="y25"
Only final + @NonNull are included
```

---

## Mistake 2: Forgetting final keyword

```text id="z26"
Field won't be included in constructor
```

---

## Mistake 3: Overusing @NonNull

```text id="a27"
Can lead to unnecessary null checks
```

---

# Common Interview Questions

## What is @RequiredArgsConstructor?

Generates constructor for required fields only.

---

## Which fields are considered required?

final fields and @NonNull fields.

---

## Why is it used in Spring?

For constructor-based dependency injection.

---

## Difference from @AllArgsConstructor?

AllArgsConstructor = all fields  
RequiredArgsConstructor = only mandatory fields

---

## Does it perform runtime checks?

Yes, for @NonNull fields (null validation).

---

# Enterprise Best Practice

Use `@RequiredArgsConstructor` for:

```text id="b28"
Spring services
Dependency injection
Immutable domain models
Mandatory configuration classes
```

---

Avoid for:

```text id="c29"
Simple DTOs without final fields
Classes requiring full control over constructor logic
```

---

# Key Points To Remember

- `@RequiredArgsConstructor` generates constructor for required fields only.
- Required fields = final + @NonNull fields.
- Ideal for Spring constructor injection.
- Helps enforce immutability.
- Works at compile time via Lombok.
- Reduces boilerplate constructor code.
- Safer than @AllArgsConstructor in many cases.
- Automatically adds null checks for @NonNull.
- Commonly used in enterprise Spring Boot apps.
- Important Java interview topic.

---

# Quick Example

```java id="d30"
@RequiredArgsConstructor
public class Product {

    private final String name;
    private double price;
}
```

---

# Equivalent Code

```java id="e31"
public Product(String name) {
    this.name = name;
}
```

---

# Final Summary

`@RequiredArgsConstructor` is a Lombok annotation that generates a constructor only for required fields (final and @NonNull), making it ideal for dependency injection and enforcing mandatory field initialization in Java applications.
```