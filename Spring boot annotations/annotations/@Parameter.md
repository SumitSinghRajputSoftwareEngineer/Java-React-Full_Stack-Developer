
## Definition

`@Parameter` is an **OpenAPI (Swagger)** annotation used in Spring Boot to **document individual input parameters of a REST API**, such as:

- Path variables
- Query parameters
- Request parameters

Package:

```java
import io.swagger.v3.oas.annotations.Parameter;
```

---

# Simple Understanding

Normally in APIs:

```text id="a1"
You pass inputs like:
- /users/{id}
- /users?name=John
```

But Swagger may not clearly explain them.

---

With `@Parameter`:

```text id="b2"
You explicitly describe each input parameter
```

---

So:

```text id="c3"
@Parameter = documentation for API inputs
```

---

# Real-Life Analogy

Think of a form:

```text id="d4"
Without labels → confusing fields
With labels → clear instructions for each field
```

---

That “label for API input fields” is:

```text id="e5"
@Parameter
```

---

# Basic Example (Path Variable)

```java id="f6"
import io.swagger.v3.oas.annotations.Parameter;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public String getUser(
            @Parameter(description = "Unique user ID", example = "101")
            @PathVariable String id) {

        return "User " + id;
    }
}
```

---

# Example (Query Parameter)

```java id="g7"
@GetMapping("/search")
public String searchUser(
        @Parameter(description = "Name of the user to search", example = "Amit")
        @RequestParam String name) {

    return "Searching " + name;
}
```

---

# What You See in Swagger UI

```text id="h8"
id (path param)
- description: Unique user ID
- example: 101

name (query param)
- description: Name of the user to search
- example: Amit
```

---

# Why @Parameter is Important

```text id="i9"
Makes APIs self-explanatory
Improves Swagger UI readability
Helps frontend understand input expectations
Reduces API misuse
```

---

# Real Production Example

```java id="j10"
@Operation(summary = "Get order by ID")
@GetMapping("/orders/{orderId}")
public String getOrder(
        @Parameter(description = "Order ID", example = "ORD123")
        @PathVariable String orderId) {

    return "Order details";
}
```

---

# @Parameter Attributes

## 1. description

```text id="k11"
Explains what the parameter means
```

---

## 2. example

```text id="l12"
Shows sample value in Swagger UI
```

---

## 3. required

```text id="m13"
Marks parameter as mandatory
```

Example:

```java id="n14"
@Parameter(required = true, description = "User ID")
```

---

## 4. name

```text id="o15"
Overrides parameter name in documentation
```

---

# How It Works Internally

```text id="p16"
Spring Boot starts
   ↓
Springdoc scans controller methods
   ↓
Reads @Parameter annotations
   ↓
Builds OpenAPI specification
   ↓
Swagger UI displays enriched parameter details
```

---

# Where @Parameter is Used

## 1. Path Variables

```text id="q17"
/api/{id}
```

---

## 2. Query Parameters

```text id="r18"
?name=John
```

---

## 3. Request Params

```text id="s19"
@RequestParam, @PathVariable, @RequestHeader
```

---

# @Parameter vs Spring Annotations

| Feature | @Parameter | @PathVariable/@RequestParam |
|----------|------------|-----------------------------|
| Purpose | Documentation | Runtime binding |
| Required | No | Yes (for binding) |
| Swagger UI | Yes | Limited |
| Runtime effect | No | Yes |

---

# Real Use Cases

## 1. REST APIs

```text id="t20"
Clear documentation of inputs
```

---

## 2. Microservices

```text id="u21"
Helps other services consume APIs correctly
```

---

## 3. Frontend Integration

```text id="v22"
Frontend developers understand required inputs
```

---

# Common Mistakes

## Mistake 1: Not documenting parameters

```text id="w23"
Swagger UI becomes unclear
```

---

## Mistake 2: Wrong examples

```text id="x24"
Confuses API consumers
```

---

## Mistake 3: Overusing description

```text id="y25"
Too verbose → reduces readability
```

---

# Best Practices

## Use for:

```text id="z26"
All public API parameters
Path variables
Query parameters
Request headers
```

---

## Always include:

```text id="a27"
description + example
```

---

# Common Interview Questions

## What is @Parameter?

Used to document API input parameters in OpenAPI.

---

## Where is it used?

With @PathVariable, @RequestParam, etc.

---

## Does it affect runtime?

No, only documentation.

---

## Why is it needed?

To make APIs self-explanatory in Swagger UI.

---

## Difference from @RequestParam?

@RequestParam → runtime binding  
@Parameter → documentation only

---

# Enterprise Best Practice

Use `@Parameter` for:

```text id="b28"
All REST API inputs
Microservices communication APIs
External-facing APIs
```

---

Avoid for:

```text id="c29"
Internal methods
Non-API logic
```

---

# Key Points To Remember

- `@Parameter` documents API input parameters.
- Works with path, query, and header parameters.
- Improves Swagger UI clarity.
- Does not affect runtime behavior.
- Used in Springdoc OpenAPI.
- Helps frontend understand API inputs.
- Supports examples, descriptions, required flags.
- Common in microservices architecture.
- Essential for production-grade APIs.
- Important Java/Spring interview topic.

---

# Quick Example

```java id="d30"
@GetMapping("/users/{id}")
public String getUser(
    @Parameter(description = "User ID", example = "101")
    @PathVariable String id) {
    return "User";
}
```

---

# Final Summary

`@Parameter` is an OpenAPI annotation used in Spring Boot to document API input parameters like path variables and query params, making APIs more readable, descriptive, and consumer-friendly in Swagger UI.
```