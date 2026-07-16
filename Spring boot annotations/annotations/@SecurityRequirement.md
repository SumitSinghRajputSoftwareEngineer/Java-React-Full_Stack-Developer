
## Definition

`@SecurityRequirement` is an **OpenAPI (Swagger)** annotation used in Spring Boot to **declare that an API endpoint requires authentication/authorization**.

It tells Swagger UI that a particular API is **secured using a defined security scheme** (like JWT, OAuth2, API Key).

Package:

```java
import io.swagger.v3.oas.annotations.security.SecurityRequirement;
```

---

# Simple Understanding

Normally in APIs:

```text id="a1"
Some APIs are public
Some APIs require login (JWT token)
```

---

With `@SecurityRequirement`:

```text id="b2"
You explicitly tell Swagger: this API needs authentication
```

---

So:

```text id="c3"
@SecurityRequirement = marks API as secured in documentation
```

---

# Real-Life Analogy

Think of a gated community:

```text id="d4"
Public areas → anyone can enter
Restricted areas → need access card
```

---

That “access card required area marking” is:

```text id="e5"
@SecurityRequirement
```

---

# Basic Example (JWT Security)

```java id="f6"
import io.swagger.v3.oas.annotations.security.SecurityRequirement;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/orders")
@SecurityRequirement(name = "bearerAuth")
public class OrderController {

    @GetMapping
    public String getOrders() {
        return "Protected Orders";
    }
}
```

---

# What It Does in Swagger UI

```text id="g7"
GET /orders
🔒 Requires: bearerAuth (JWT token)
```

---

# Why @SecurityRequirement is Important

```text id="h8"
Shows which APIs are protected
Improves API contract clarity
Helps frontend understand authentication needs
Standardizes security documentation
```

---

# How It Works Internally

```text id="i9"
Spring Boot starts
   ↓
Springdoc scans security annotations
   ↓
Reads @SecurityRequirement
   ↓
Links API with security scheme
   ↓
Swagger UI shows lock icon (🔒)
```

---

# Security Scheme Setup (Required)

Before using `@SecurityRequirement`, you must define security scheme:

```java id="j10"
import io.swagger.v3.oas.annotations.security.SecurityScheme;
import io.swagger.v3.oas.annotations.enums.SecuritySchemeType;

@SecurityScheme(
    name = "bearerAuth",
    type = SecuritySchemeType.HTTP,
    scheme = "bearer",
    bearerFormat = "JWT"
)
public class OpenApiConfig {
}
```

---

# Real Production Example

## Secure Controller

```java id="k11"
@SecurityRequirement(name = "bearerAuth")
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping
    public String getUsers() {
        return "Protected Users";
    }
}
```

---

# Class-Level vs Method-Level

## Class-Level

```java id="l12"
@SecurityRequirement(name = "bearerAuth")
@RestController
public class UserController {
}
```

👉 applies to all endpoints

---

## Method-Level

```java id="m13"
@SecurityRequirement(name = "bearerAuth")
@GetMapping("/profile")
public String profile() {
}
```

👉 applies only to this API

---

# Types of Security Supported

## 1. HTTP Bearer (JWT)

```text id="n14"
Authorization: Bearer <token>
```

---

## 2. API Key

```text id="o15"
api-key: abc123
```

---

## 3. OAuth2

```text id="p16"
Login-based authorization flows
```

---

# @SecurityRequirement vs @SecurityScheme

| Feature | @SecurityRequirement | @SecurityScheme |
|----------|---------------------|-----------------|
| Purpose | Apply security to APIs | Define security type |
| Level | API/controller | Global config |
| Function | Enforces requirement | Declares scheme |

---

# Real Use Cases

## 1. Microservices Security

```text id="q17"
Secure internal/external APIs using JWT
```

---

## 2. Enterprise APIs

```text id="r18"
Restrict sensitive endpoints
```

---

## 3. API Gateway Systems

```text id="s19"
Centralized authentication documentation
```

---

# Common Mistakes

## Mistake 1: Not defining SecurityScheme

```text id="t20"
Swagger will not recognize security
```

---

## Mistake 2: Wrong security name

```text id="u21"
Mismatch between @SecurityScheme and @SecurityRequirement
```

---

## Mistake 3: Forgetting to configure Spring Security

```text id="v22"
Swagger shows lock, but API is actually open or vice versa
```

---

# Best Practices

## Use for:

```text id="w23"
All protected APIs
Microservices with authentication
Enterprise-grade applications
```

---

## Avoid for:

```text id="x24"
Public APIs (no login required)
Health check endpoints
Open documentation APIs
```

---

# Common Interview Questions

## What is @SecurityRequirement?

Marks an API as requiring authentication in OpenAPI.

---

## Why is it used?

To document secured endpoints in Swagger UI.

---

## Difference from @SecurityScheme?

Scheme defines security type, requirement applies it.

---

## Does it secure the API?

No, it only documents security; Spring Security enforces it.

---

## Where is it used?

On controllers or API methods.

---

# Enterprise Best Practice

Use `@SecurityRequirement` for:

```text id="y25"
All secured REST APIs
JWT-based microservices
OAuth2 protected systems
```

---

Avoid for:

```text id="z26"
Public endpoints
Unauthenticated services
Static or health APIs
```

---

# Key Points To Remember

- `@SecurityRequirement` marks APIs as secured in Swagger/OpenAPI.
- Used to indicate authentication is required.
- Works with JWT, OAuth2, API Key systems.
- Requires `@SecurityScheme` definition.
- Does NOT enforce security (Spring Security does).
- Shows lock icon in Swagger UI.
- Can be applied at class or method level.
- Essential for enterprise API documentation.
- Common in microservices architecture.
- Important Spring Boot interview topic.

---

# Quick Example

```java id="a27"
@SecurityRequirement(name = "bearerAuth")
@GetMapping("/profile")
public String profile() {
    return "Secure Data";
}
```

---

# Final Summary

`@SecurityRequirement` is an OpenAPI annotation used in Spring Boot to indicate that an API endpoint requires authentication (like JWT or OAuth2), making Swagger UI clearly show which APIs are protected while working alongside Spring Security for actual enforcement.
```