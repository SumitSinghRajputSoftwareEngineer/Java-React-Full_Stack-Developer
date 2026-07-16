
## Definition

`@Operation` is an **OpenAPI (Swagger)** annotation used in Spring Boot applications to **describe a single REST API endpoint**.

It provides detailed metadata like:

- Summary
- Description
- Response codes
- Tags
- Parameters

Package:

```java
import io.swagger.v3.oas.annotations.Operation;
```

---

# Simple Understanding

Normally in Swagger UI:

```text id="a1"
Endpoints appear without much explanation
```

---

With `@Operation`:

```text id="b2"
Each API becomes self-documented with clear details
```

---

So:

```text id="c3"
@Operation = detailed documentation for one API endpoint
```

---

# Real-Life Analogy

Think of a product listing:

```text id="d4"
Without details → just product name
With @Operation → full product info:
- description
- features
- price
- reviews
```

---

That “single API explanation card” is:

```text id="e5"
@Operation
```

---

# Basic Example

```java id="f6"
import io.swagger.v3.oas.annotations.Operation;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @Operation(
        summary = "Get all users",
        description = "Returns a list of all registered users in the system"
    )
    @GetMapping
    public String getUsers() {
        return "All users";
    }
}
```

---

# What You See in Swagger UI

```text id="g7"
GET /users
Summary: Get all users
Description: Returns a list of all registered users
```

---

# Why @Operation is Important

```text id="h8"
Makes APIs self-explanatory
Improves Swagger documentation quality
Helps frontend developers understand APIs
Standardizes API documentation
```

---

# Real Production Example

```java id="i9"
@Operation(
    summary = "Get user by ID",
    description = "Fetch user details using unique user ID"
)
@GetMapping("/{id}")
public User getUser(@PathVariable String id) {
    return new User();
}
```

---

# Advanced Features

## 1. Response Documentation

```java id="j10"
@Operation(
    summary = "Get user",
    responses = {
        @ApiResponse(responseCode = "200", description = "User found"),
        @ApiResponse(responseCode = "404", description = "User not found")
    }
)
```

---

## 2. Adding Tags

```java id="k11"
@Operation(
    summary = "Create user",
    tags = {"User APIs"}
)
```

---

## 3. Request Parameters

```java id="l12"
@Operation(
    summary = "Search users",
    description = "Search users by name or email"
)
```

---

# How It Works Internally

```text id="m13"
Spring Boot starts
   ↓
Springdoc scans controllers
   ↓
Reads @Operation metadata
   ↓
Generates OpenAPI JSON spec
   ↓
Swagger UI renders detailed endpoint info
```

---

# @Operation Attributes

## 1. summary

```text id="n14"
Short description shown in Swagger UI
```

---

## 2. description

```text id="o15"
Detailed explanation of the API
```

---

## 3. tags

```text id="p16"
Groups API into categories
```

---

## 4. responses

```text id="q17"
Defines HTTP response codes and messages
```

---

# Real Use Cases

## 1. REST API Documentation

```text id="r18"
Makes endpoints understandable
```

---

## 2. Microservices Communication

```text id="s19"
Helps teams consume APIs easily
```

---

## 3. Frontend Integration

```text id="t20"
Frontend developers use it for API contract understanding
```

---

# @Operation vs @Tag

| Feature | @Operation | @Tag |
|----------|------------|------|
| Level | Endpoint level | Controller level |
| Purpose | Describe API behavior | Group APIs |
| Scope | Single method | Whole controller |

---

# Common Mistakes

## Mistake 1: Missing summaries

```text id="u21"
Swagger UI becomes unclear
```

---

## Mistake 2: Overloading description

```text id="v22"
Too much unnecessary detail
```

---

## Mistake 3: Not updating documentation

```text id="w23"
Swagger becomes outdated vs real API
```

---

# Best Practices

## Use for:

```text id="x24"
All public APIs
Microservices endpoints
Critical business operations
```

---

## Avoid for:

```text id="y25"
Internal/private helper APIs
Overly verbose descriptions
```

---

# Common Interview Questions

## What is @Operation?

Used to describe a single API endpoint in OpenAPI.

---

## Difference from @Tag?

@Operation → endpoint-level  
@Tag → controller-level grouping

---

## Does it affect runtime?

No, only documentation.

---

## Why is it used?

To improve API clarity and documentation.

---

## Where is it used?

On controller methods in Spring Boot.

---

# Enterprise Best Practice

Use `@Operation` for:

```text id="z26"
All REST endpoints in production APIs
Public microservices
External integrations
```

---

Avoid for:

```text id="a27"
Internal helper methods
Non-API methods
```

---

# Key Points To Remember

- `@Operation` documents a single REST API endpoint.
- Used in Swagger/OpenAPI (Springdoc).
- Provides summary, description, responses, tags.
- Improves API clarity and usability.
- Works at method level in controllers.
- Does not affect runtime logic.
- Essential for professional API documentation.
- Widely used in microservices architecture.
- Helps frontend and backend teams align.
- Important Java/Spring interview topic.

---

# Quick Example

```java id="b28"
@Operation(
    summary = "Delete user",
    description = "Deletes a user by ID"
)
@DeleteMapping("/{id}")
public void deleteUser(@PathVariable String id) {
}
```

---

# Final Summary

`@Operation` is an OpenAPI annotation used in Spring Boot to describe individual REST API endpoints with metadata like summary, description, and responses, making APIs more readable and well-documented in Swagger UI.
```