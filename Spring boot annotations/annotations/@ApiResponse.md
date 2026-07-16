
## Definition

`@ApiResponse` is an **OpenAPI (Swagger)** annotation used in Spring Boot applications to **document the HTTP response of a REST API endpoint**, including status codes, descriptions, and response models.

Package:

```java
import io.swagger.v3.oas.annotations.responses.ApiResponse;
```

---

# Simple Understanding

Normally in APIs:

```text id="a1"
You return response, but documentation is unclear
```

---

With `@ApiResponse`:

```text id="b2"
You explicitly describe what each HTTP response means
```

---

So:

```text id="c3"
@ApiResponse = documentation of API output (success + error cases)
```

---

# Real-Life Analogy

Think of a food delivery order:

```text id="d4"
Possible outcomes:
- Order confirmed (200)
- Food not available (404)
- Payment failed (400)
```

---

That “result explanation system” is:

```text id="e5"
@ApiResponse
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
404 - User not found
500 - Internal server error
```

---

# Why @ApiResponse is Important

```text id="h8"
Clearly defines API behavior
Documents success + error scenarios
Improves frontend-backend communication
Helps in contract-first API design
```

---

# Real Production Example

```java id="i9"
@Operation(summary = "Create order")
@ApiResponses({
    @ApiResponse(responseCode = "201", description = "Order created"),
    @ApiResponse(responseCode = "400", description = "Invalid input data"),
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
Reads @ApiResponse metadata
   ↓
Generates OpenAPI specification JSON
   ↓
Swagger UI displays response documentation
```

---

# @ApiResponse Attributes

## 1. responseCode (mandatory)

```text id="k11"
HTTP status code (200, 201, 400, 404, 500)
```

---

## 2. description

```text id="l12"
Explains meaning of response
```

---

## 3. content (optional)

```text id="m13"
Defines response body schema (DTO, object, etc.)
```

Example:

```java id="n14"
@ApiResponse(
    responseCode = "200",
    description = "Success",
    content = @Content(schema = @Schema(implementation = User.class))
)
```

---

# @ApiResponses

Used to group multiple `@ApiResponse` annotations:

```java id="o15"
@ApiResponses({
    @ApiResponse(responseCode = "200", description = "OK"),
    @ApiResponse(responseCode = "404", description = "Not Found")
})
```

---

# Real Use Cases

## 1. REST API Documentation

```text id="p16"
Clearly defines all API outcomes
```

---

## 2. Microservices

```text id="q17"
Helps service consumers understand contract
```

---

## 3. Frontend Integration

```text id="r18"
Frontend knows how to handle success/failure cases
```

---

# @ApiResponse vs @Operation

| Feature | @ApiResponse | @Operation |
|----------|-------------|------------|
| Purpose | Response documentation | API method description |
| Scope | Output level | Endpoint level |
| Focus | HTTP status codes | API behavior |

---

# Common Mistakes

## Mistake 1: Only documenting success response

```text id="s19"
Ignoring error cases (bad practice)
```

---

## Mistake 2: Wrong HTTP status codes

```text id="t20"
Leads to misleading API contracts
```

---

## Mistake 3: Not updating responses

```text id="u21"
Swagger becomes outdated vs real behavior
```

---

# Best Practices

## Use for:

```text id="v22"
All public APIs
Error handling documentation
Microservice contracts
```

---

## Always include:

```text id="w23"
200 → success
400 → client error
404 → not found
500 → server error
```

---

# Common Interview Questions

## What is @ApiResponse?

Used to document HTTP responses of an API.

---

## Why is it used?

To describe success and failure responses clearly.

---

## Difference between @ApiResponse and @ApiResponses?

@ApiResponse → single response  
@ApiResponses → group of responses

---

## Does it affect runtime?

No, only documentation.

---

## Where is it used?

Inside @Operation on controller methods.

---

# Enterprise Best Practice

Use `@ApiResponse` for:

```text id="x24"
All production REST APIs
Public microservices
External integrations
```

---

Avoid for:

```text id="y25"
Internal helper methods
Non-REST methods
```

---

# Key Points To Remember

- `@ApiResponse` documents HTTP responses in Swagger/OpenAPI.
- Defines status codes like 200, 400, 404, 500.
- Improves API clarity and contract understanding.
- Used with Springdoc OpenAPI in Spring Boot.
- Can define response body schema.
- Often used inside @Operation.
- Supports multiple responses via @ApiResponses.
- Essential for production-grade APIs.
- Helps frontend and backend alignment.
- Important Java interview topic.

---

# Quick Example

```java id="z26"
@ApiResponse(responseCode = "200", description = "Success")
@ApiResponse(responseCode = "404", description = "Not found")
```

---

# Final Summary

`@ApiResponse` is an OpenAPI annotation used in Spring Boot to document HTTP response codes and their meanings for REST APIs, helping create clear, professional, and consumer-friendly API documentation in Swagger UI.
```