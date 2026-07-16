
## Definition

`@Tag` is an **OpenAPI (Swagger)** annotation used in Spring Boot applications to **group and categorize REST APIs in Swagger UI**.

It helps organize endpoints into meaningful sections like:

- User APIs
- Order APIs
- Payment APIs

Package:

```java
import io.swagger.v3.oas.annotations.tags.Tag;
```

---

# Simple Understanding

Normally in Swagger UI:

```text id="a1"
All APIs appear in a long flat list
```

---

With `@Tag`:

```text id="b2"
APIs are grouped into sections
```

---

So:

```text id="c3"
@Tag = API grouping label for Swagger UI
```

---

# Real-Life Analogy

Think of a supermarket:

```text id="d4"
Without tags → all items mixed together
With tags → items grouped:
- Fruits
- Vegetables
- Dairy
```

---

That grouping system is:

```text id="e5"
@Tag
```

---

# Basic Example

## Controller Level Tagging

```java id="f6"
import io.swagger.v3.oas.annotations.tags.Tag;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
@Tag(name = "User APIs", description = "Operations related to user management")
public class UserController {

    @GetMapping
    public String getUsers() {
        return "All users";
    }
}
```

---

# What You See in Swagger UI

```text id="g7"
User APIs
   └── GET /users
```

---

# Why @Tag is Important

```text id="h8"
Improves API readability
Groups related endpoints
Makes Swagger UI clean and structured
Helps frontend teams understand APIs easily
```

---

# Real Production Example

## Order Service

```java id="i9"
@RestController
@RequestMapping("/orders")

@Tag(
    name = "Order APIs",
    description = "APIs for creating, updating and managing orders"
)
public class OrderController {

    @GetMapping
    public String getOrders() {
        return "Orders list";
    }
}
```

---

# Method-Level Tagging (Optional)

You can also tag individual APIs:

```java id="j10"
@Operation(
    summary = "Get user by ID",
    tags = {"User APIs"}
)
@GetMapping("/{id}")
public String getUser(@PathVariable String id) {
    return "User";
}
```

---

# How It Works Internally

```text id="k11"
Spring Boot starts
   ↓
Springdoc scans controllers
   ↓
Reads @Tag annotations
   ↓
Groups endpoints in OpenAPI JSON
   ↓
Swagger UI displays grouped sections
```

---

# @Tag Attributes

## 1. name (mandatory)

```text id="l12"
Group name shown in Swagger UI
```

---

## 2. description (optional)

```text id="m13"
Explains purpose of the API group
```

---

# Real Use Cases

## 1. Microservices

```text id="n14"
Each domain has separate API groups
```

Example:
- User Service
- Order Service
- Payment Service

---

## 2. Large Applications

```text id="o15"
Helps organize hundreds of APIs
```

---

## 3. Team Collaboration

```text id="p16"
Frontend teams easily locate APIs
```

---

# @Tag vs @Operation

| Feature | @Tag | @Operation |
|----------|------|------------|
| Level | Class-level grouping | Method-level API details |
| Purpose | Organize APIs | Describe API behavior |
| Scope | Controller | Endpoint |

---

# Common Mistakes

## Mistake 1: Not using tags in large APIs

```text id="q17"
Swagger UI becomes messy and unreadable
```

---

## Mistake 2: Over-tagging

```text id="r18"
Too many unnecessary groups confuse users
```

---

## Mistake 3: Inconsistent naming

```text id="s19"
User API vs Users API → creates duplication
```

---

# Best Practices

## Use for:

```text id="t20"
Controller-level grouping
Domain-based APIs
Microservices documentation
```

---

## Avoid for:

```text id="u21"
Small applications with few endpoints
Over-granular grouping
```

---

# Common Interview Questions

## What is @Tag?

Used to group and label APIs in Swagger UI.

---

## Where is it used?

On controllers or API methods in Spring Boot.

---

## Difference from @Operation?

@Tag → groups APIs  
@Operation → describes single API

---

## Does it affect runtime?

No, only affects documentation.

---

## Why is it used?

To improve API organization and readability.

---

# Enterprise Best Practice

Use `@Tag` for:

```text id="v22"
All REST controllers in microservices
Domain-based grouping (User, Order, Payment)
Large-scale API documentation
```

---

Avoid for:

```text id="w23"
Very small applications
Over-complicated grouping structures
```

---

# Key Points To Remember

- `@Tag` is used for grouping APIs in Swagger UI.
- Helps organize endpoints into logical sections.
- Improves readability of API documentation.
- Works with Springdoc OpenAPI.
- Applied at controller or method level.
- Does not affect application runtime behavior.
- Common in microservices architecture.
- Makes API navigation easier for developers.
- Important for backend interviews.
- Essential for production-grade API documentation.

---

# Quick Example

```java id="x24"
@Tag(name = "Product APIs", description = "Manage products")
@RestController
@RequestMapping("/products")
public class ProductController {

    @GetMapping
    public String getProducts() {
        return "Products";
    }
}
```

---

# Final Summary

`@Tag` is an OpenAPI annotation used in Spring Boot applications to group and categorize REST APIs in Swagger UI, making API documentation more organized, readable, and developer-friendly.
```