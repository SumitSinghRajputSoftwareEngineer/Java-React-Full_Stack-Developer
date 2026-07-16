
## Definition

`@ApiResponses` is an **OpenAPI (Swagger)** annotation used in Spring Boot to **group multiple `@ApiResponse` annotations together** for a single REST API endpoint.

Package:

```java
import io.swagger.v3.oas.annotations.responses.ApiResponses;
```

---

# Simple Understanding

Normally for an API:

```text id="a1"
You may have multiple outcomes:
- Success (200)
- Bad request (400)
- Not found (404)
- Server error (500)
```

---

With OpenAPI:

```text id="b2"
You group all these responses using @ApiResponses
```

---

So:

```text id="c3"
@ApiResponses = container for multiple @ApiResponse definitions
```

---

# Real-Life Analogy

Think of a railway system:

```text id="d4"
One train journey can result in:
- Arrived on time
- Delayed
- Cancelled
- Diverted route
```

---

That “bundle of possible outcomes” is:

```text id="e5"
@ApiResponses
```

---

# Basic Example

```java id="f6"
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @Operation(summary = "Get user by ID")
    @ApiResponses(value = {
        @ApiResponse(responseCode = "200", description = "User found successfully"),
        @ApiResponse(responseCode = "400", description = "Invalid user ID supplied"),
        @ApiResponse(responseCode = "404", description = "User not found"),
        @ApiResponse(responseCode = "500", description = "Internal server error")
    })
    @GetMapping("/{id}")
    public String getUser(@PathVariable String id) {
        return "User";
    }
}
```

---

# What You See in Swagger UI

```text id="g7"
GET /users/{id}

Responses:
200 - User found successfully
400 - Invalid user ID supplied
404 - User not found
500 - Internal server error
```

---

# Why @ApiResponses is Important

```text id="h8"
Provides full API response contract
Improves clarity for API consumers
Documents success + failure scenarios together
Standardizes API documentation
```

---

# Real Production Example

```java id="i9"
@Operation(summary = "Create order")
@ApiResponses(value = {
    @ApiResponse(responseCode = "201", description = "Order created successfully"),
    @ApiResponse(responseCode = "400", description = "Invalid order data"),
    @ApiResponse(responseCode = "409", description = "Order already exists"),
    @ApiResponse(responseCode = "500", description = "Server error")
})
@PostMapping("/orders")
public String createOrder() {
    return "Order created";
}
```

---

# How It Works Internally

```text id="j10"
Spring Boot starts
   ↓
Springdoc scans controller annotations
   ↓
Reads @ApiResponses block
   ↓
Extracts all @ApiResponse entries
   ↓
Generates OpenAPI JSON specification
   ↓
Swagger UI displays grouped responses
```

---

# @ApiResponses Structure

```text id="k11"
@ApiResponses
   ├── @ApiResponse (200)
   ├── @ApiResponse (400)
   ├── @ApiResponse (404)
   └── @ApiResponse (500)
```

---

# Difference Between @ApiResponse and @ApiResponses

| Feature | @ApiResponse | @ApiResponses |
|----------|-------------|---------------|
| Type | Single response | Multiple responses |
| Usage | Individual status | Group of statuses |
| Scope | One response | Full response set |

---

# Real Use Cases

## 1. REST API Documentation

```text id="l12"
Clearly defines all possible outputs of an endpoint
```

---

## 2. Microservices

```text id="m13"
Helps consumers understand service contracts
```

---

## 3. Frontend Integration

```text id="n14"
Frontend developers handle all response scenarios correctly
```

---

# Common Mistakes

## Mistake 1: Only documenting success response

```text id="o15"
Bad practice → incomplete API contract
```

---

## Mistake 2: Missing error responses

```text id="p16"
Frontend cannot handle failures properly
```

---

## Mistake 3: Incorrect status codes

```text id="q17"
Leads to confusion in API usage
```

---

# Best Practices

## Always include:

```text id="r18"
200/201 → Success
400 → Client error
404 → Not found
500 → Server error
```

---

## Use for:

```text id="s19"
All public REST APIs
Microservice endpoints
External integrations
```

---

## Avoid for:

```text id="t20"
Internal helper methods
Non-API logic
```

---

# Common Interview Questions

## What is @ApiResponses?

Used to group multiple API response definitions.

---

## Why is it used?

To document all possible HTTP responses for an endpoint.

---

## Difference between @ApiResponse and @ApiResponses?

@ApiResponse → single response  
@ApiResponses → collection of responses

---

## Does it affect runtime?

No, only documentation.

---

## Where is it used?

Inside controller methods in Spring Boot with OpenAPI.

---

# Enterprise Best Practice

Use `@ApiResponses` for:

```text id="u21"
All production-grade REST APIs
Microservices communication contracts
Public APIs
```

---

Avoid for:

```text id="v22"
Internal methods
Non-HTTP business logic
```

---

# Key Points To Remember

- `@ApiResponses` groups multiple `@ApiResponse` annotations.
- Defines complete API response behavior.
- Improves Swagger/OpenAPI documentation quality.
- Helps frontend and backend teams align expectations.
- Used at method level in controllers.
- Supports full HTTP response contract definition.
- Common in microservices architecture.
- Works with Springdoc OpenAPI in Spring Boot.
- Does not affect runtime behavior.
- Important backend interview topic.

---

# Quick Example

```java id="w23"
@ApiResponses({
    @ApiResponse(responseCode = "200", description = "Success"),
    @ApiResponse(responseCode = "404", description = "Not found"),
    @ApiResponse(responseCode = "500", description = "Error")
})
```

---

# Final Summary

`@ApiResponses` is an OpenAPI annotation used in Spring Boot to group multiple `@ApiResponse` definitions for a single API endpoint, providing a complete and structured documentation of all possible HTTP responses in Swagger UI.
```