
## Definition

`@Schema` is an **OpenAPI (Swagger)** annotation used in Spring Boot to **describe data models (DTOs, request/response objects, fields)** in API documentation.

Package:

```java
import io.swagger.v3.oas.annotations.media.Schema;
```

---

# Simple Understanding

Normally in APIs:

```text id="a1"
Swagger shows only field names
```

---

With `@Schema`:

```text id="b2"
Each field gets a clear description, example, and constraints
```

---

So:

```text id="c3"
@Schema = documentation for API models (data structure)
```

---

# Real-Life Analogy

Think of a product catalog:

```text id="d4"
Without schema → only product name listed
With schema → full details:
- description
- price
- category
- rating
```

---

That “data structure explanation system” is:

```text id="e5"
@Schema
```

---

# Basic Example

## DTO Class

```java id="f6"
import io.swagger.v3.oas.annotations.media.Schema;

public class UserDTO {

    @Schema(description = "Unique user ID", example = "101")
    private String id;

    @Schema(description = "Name of the user", example = "Amit")
    private String name;

    @Schema(description = "User age", example = "25")
    private int age;
}
```

---

# What You See in Swagger UI

```text id="g7"
UserDTO
- id: Unique user ID (example: 101)
- name: Name of the user (example: Amit)
- age: User age (example: 25)
```

---

# Why @Schema is Important

```text id="h8"
Makes API models self-explanatory
Improves frontend understanding
Provides validation hints
Enhances Swagger documentation quality
```

---

# Real Production Example

## Request DTO

```java id="i9"
public class CreateUserRequest {

    @Schema(description = "User full name", example = "Amit Sharma", required = true)
    private String name;

    @Schema(description = "User email address", example = "amit@test.com")
    private String email;

    @Schema(description = "User age", example = "30", minimum = "18")
    private int age;
}
```

---

# Advanced Features of @Schema

## 1. description

```text id="j10"
Explains field meaning
```

---

## 2. example

```text id="k11"
Shows sample value in Swagger UI
```

---

## 3. required

```text id="l12"
Marks field as mandatory
```

---

## 4. minLength / maxLength

```java id="m13"
@Schema(minLength = "3", maxLength = "50")
```

---

## 5. minimum / maximum

```java id="n14"
@Schema(minimum = "18", maximum = "60")
```

---

## 6. hidden

```java id="o15"
@Schema(hidden = true)
```

👉 hides field from Swagger UI

---

# How It Works Internally

```text id="p16"
Spring Boot starts
   ↓
Springdoc scans DTO classes
   ↓
Reads @Schema annotations
   ↓
Generates OpenAPI JSON schema
   ↓
Swagger UI renders structured model docs
```

---

# Where @Schema is Used

## 1. DTOs (Request/Response)

```text id="q17"
Most common use case
```

---

## 2. API Models

```text id="r18"
Defines structure of request/response body
```

---

## 3. Validation Documentation

```text id="s19"
Helps describe constraints visually
```

---

# @Schema vs @Parameter

| Feature | @Schema | @Parameter |
|----------|--------|------------|
| Scope | Model/DTO fields | API method parameters |
| Purpose | Data structure | Input parameters |
| Level | Class/field | Method argument |
| Example | UserDTO fields | Path/query params |

---

# Real Use Cases

## 1. Microservices APIs

```text id="t20"
Clear contract between services
```

---

## 2. Frontend Integration

```text id="u21"
Frontend knows exact data format
```

---

## 3. API Validation Understanding

```text id="v22"
Shows constraints like min/max, required fields
```

---

# Common Mistakes

## Mistake 1: Not using @Schema in DTOs

```text id="w23"
Swagger becomes unclear and incomplete
```

---

## Mistake 2: Overloading descriptions

```text id="x24"
Too much detail reduces readability
```

---

## Mistake 3: Missing examples

```text id="y25"
Frontend developers struggle to understand APIs
```

---

# Best Practices

## Use for:

```text id="z26"
All DTO classes
Request/response models
Public API schemas
```

---

## Always include:

```text id="a27"
description + example
```

---

# Common Interview Questions

## What is @Schema?

Used to document API models in OpenAPI.

---

## Where is it used?

On DTO classes and fields.

---

## Difference from @Parameter?

@Schema → data model  
@Parameter → API input parameter

---

## Does it affect runtime?

No, only documentation.

---

## Why is it important?

Improves API clarity and consumer understanding.

---

# Enterprise Best Practice

Use `@Schema` for:

```text id="b28"
All request/response DTOs
Microservice contracts
External APIs
```

---

Avoid for:

```text id="c29"
Internal helper objects
Non-API classes
```

---

# Key Points To Remember

- `@Schema` documents API data models in OpenAPI.
- Used for DTOs, request/response objects.
- Adds description, example, constraints.
- Improves Swagger UI readability.
- Works with Springdoc OpenAPI.
- Does not affect runtime behavior.
- Helps frontend understand API structure.
- Supports validation hints (min/max, required).
- Essential for microservices and REST APIs.
- Important Java/Spring interview topic.

---

# Quick Example

```java id="d30"
public class ProductDTO {

    @Schema(description = "Product ID", example = "P101")
    private String id;

    @Schema(description = "Product name", example = "Laptop")
    private String name;
}
```

---

# Final Summary

`@Schema` is an OpenAPI annotation used in Spring Boot to document API data models (DTOs) by describing fields, examples, and constraints, making APIs more structured, understandable, and developer-friendly in Swagger UI.
```