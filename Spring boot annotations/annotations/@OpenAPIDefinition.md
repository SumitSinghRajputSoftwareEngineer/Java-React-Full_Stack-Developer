
## Definition

`@OpenAPIDefinition` is an annotation used in **Spring Boot / Java applications with OpenAPI (Swagger)** to define **global metadata for your API documentation**.

It comes from:

```java
import io.swagger.v3.oas.annotations.OpenAPIDefinition;
```

---

# Simple Understanding

Normally Swagger/OpenAPI shows API docs, but:

```text id="a1"
It needs basic project-level information like:
- Title
- Version
- Description
- Contact
```

---

With `@OpenAPIDefinition`:

```text id="b2"
You define global API metadata in one place
```

---

So:

```text id="c3"
@OpenAPIDefinition = API documentation configuration at application level
```

---

# Real-Life Analogy

Think of a book:

```text id="d4"
Without it → only chapters exist
With @OpenAPIDefinition → book has:
- Title
- Author
- Edition
- Summary
```

---

That “book-level info for APIs” is:

```text id="e5"
@OpenAPIDefinition
```

---

# Basic Example

```java id="f6"
import io.swagger.v3.oas.annotations.OpenAPIDefinition;
import io.swagger.v3.oas.annotations.info.Info;
import io.swagger.v3.oas.annotations.info.Contact;

@OpenAPIDefinition(
    info = @Info(
        title = "User Management API",
        version = "1.0",
        description = "APIs for managing users in the system",
        contact = @Contact(
            name = "Sumit",
            email = "support@example.com"
        )
    )
)
public class OpenApiConfig {
}
```

---

# What It Does

```text id="g7"
Adds global metadata to Swagger UI
```

Example output in Swagger UI:

```text id="h8"
Title: User Management API
Version: 1.0
Description: APIs for managing users
Contact: support team
```

---

# Where It Is Used

Typically placed on:

```text id="i9"
Main Spring Boot Application class OR Configuration class
```

---

# Real Production Example

```java id="j10"
import io.swagger.v3.oas.annotations.OpenAPIDefinition;
import io.swagger.v3.oas.annotations.info.Info;

@OpenAPIDefinition(
    info = @Info(
        title = "Order Service API",
        version = "2.0",
        description = "Microservice for order processing"
    )
)
public class ApiDocConfig {
}
```

---

# How It Works Internally

```text id="k11"
Spring Boot starts
   ↓
Springdoc OpenAPI scans annotations
   ↓
Reads @OpenAPIDefinition
   ↓
Generates OpenAPI JSON spec
   ↓
Swagger UI renders documentation
```

---

# Key Elements Inside @OpenAPIDefinition

## 1. info

```text id="l12"
API metadata (title, version, description)
```

---

## 2. servers

```text id="m13"
Defines base URLs (dev, prod, staging)
```

Example:

```java id="n14"
servers = {
    @Server(url = "http://localhost:8080", description = "Local"),
    @Server(url = "https://api.prod.com", description = "Production")
}
```

---

## 3. security

```text id="o15"
Defines authentication schemes (JWT, OAuth2)
```

---

## 4. tags

```text id="p16"
Groups APIs in Swagger UI
```

---

# Why It Is Important

```text id="q17"
Improves API documentation clarity
Standardizes metadata
Helps frontend & backend integration
Supports API governance in microservices
```

---

# @OpenAPIDefinition vs @Operation

| Feature | @OpenAPIDefinition | @Operation |
|----------|-------------------|------------|
| Scope | Global API metadata | Single API endpoint |
| Level | Application | Method |
| Purpose | API info | API behavior |

---

# Common Use Cases

## 1. Microservices Documentation

```text id="r18"
Each service has its own Swagger metadata
```

---

## 2. API Gateway Integration

```text id="s19"
Helps unify API documentation across services
```

---

## 3. Client Integration

```text id="t20"
Frontend teams use metadata for understanding APIs
```

---

# Common Mistakes

## Mistake 1: Missing dependency

```text id="u21"
Swagger UI not generated
```

---

## Mistake 2: Not placing config class correctly

```text id="v22"
Annotation not picked up by Spring Boot scan
```

---

## Mistake 3: Overloading metadata

```text id="w23"
Too many unnecessary details in API docs
```

---

# Best Practices

## Use for:

```text id="x24"
API title/version
Microservice documentation
Standardizing API metadata
```

---

## Avoid for:

```text id="y25"
Business logic
Endpoint-specific configuration
Runtime logic
```

---

# Common Interview Questions

## What is @OpenAPIDefinition?

Defines global metadata for OpenAPI documentation.

---

## Where is it used?

Spring Boot applications with Swagger/OpenAPI.

---

## Difference from @Operation?

Global API info vs endpoint-level info.

---

## What tool uses it?

Springdoc OpenAPI / Swagger UI.

---

## Is it runtime or compile-time?

It is used at runtime for documentation generation.

---

# Key Points To Remember

- `@OpenAPIDefinition` provides global API metadata.
- Used in Swagger/OpenAPI (Springdoc).
- Defines title, version, description, servers, security.
- Helps generate Swagger UI documentation.
- Applied at application/config class level.
- Improves API clarity and governance.
- Works with Spring Boot microservices.
- Complemented by @Operation for endpoints.
- Very common in REST API projects.
- Important for backend interviews.

---

# Quick Example

```java id="z26"
@OpenAPIDefinition(
    info = @Info(
        title = "Product API",
        version = "1.0"
    )
)
public class ApiConfig {
}
```

---

# Final Summary

`@OpenAPIDefinition` is a Swagger/OpenAPI annotation used in Spring Boot applications to define global API metadata like title, version, description, and server details, enabling standardized and professional API documentation.
```