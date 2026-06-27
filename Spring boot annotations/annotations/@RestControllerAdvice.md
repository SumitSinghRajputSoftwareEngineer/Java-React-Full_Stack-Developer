# @RestControllerAdvice

## Definition

`@RestControllerAdvice` is a specialized version of `@ControllerAdvice` used in REST APIs.

It provides:

- Global Exception Handling
- Global Data Binding
- Global Model Attributes

for all REST controllers.

It tells Spring:

> Apply this logic globally and automatically return the result as JSON/XML instead of a View.

Package:

```java
import org.springframework.web.bind.annotation.RestControllerAdvice;
```

---

# Why Was @RestControllerAdvice Introduced?

Before Spring Boot, developers typically wrote:

```java
@ControllerAdvice
@ResponseBody
public class GlobalExceptionHandler {

}
```

Since almost every REST application required:

```java
@ResponseBody
```

Spring introduced:

```java
@RestControllerAdvice
```

---

# What Is @RestControllerAdvice Internally?

It is a combination of:

```java
@ControllerAdvice
+
@ResponseBody
```

Equivalent to:

```java
@ControllerAdvice
@ResponseBody
public class GlobalExceptionHandler {

}
```

---

Spring internally treats:

```java
@RestControllerAdvice
```

as:

```java
@ControllerAdvice
@ResponseBody
```

---

# Why Do We Need It?

Imagine an application contains:

```text
EmployeeController
DepartmentController
UserController
ProductController
OrderController
```

---

Every controller may throw exceptions.

Without global handling:

```java
try {
   ...
}
catch(Exception e) {
   ...
}
```

inside every controller.

---

Problems:

- Code Duplication
- Poor Maintainability
- Inconsistent Error Responses

---

Solution:

```java
@RestControllerAdvice
```

---

# Basic Example

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

}
```

---

This class becomes globally active.

---

# Most Common Use Case

## Global Exception Handling

---

Controller:

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @GetMapping("/{id}")
    public Employee getEmployee(
            @PathVariable Long id) {

        throw new RuntimeException(
                "Employee Not Found");

    }

}
```

---

Global Handler:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public String handleException(
            RuntimeException ex) {

        return ex.getMessage();

    }

}
```

---

Request:

```http
GET /employees/1
```

---

Response:

```json
"Employee Not Found"
```

---

# What Happens Internally?

Controller:

```java
throw new RuntimeException();
```

---

Spring Flow:

```text
Controller
     │
     ▼
Exception Thrown
     │
     ▼
DispatcherServlet
     │
     ▼
Exception Resolver
     │
     ▼
@RestControllerAdvice
     │
     ▼
@ExceptionHandler
     │
     ▼
JSON Response
```

---

# Returning Custom Error Objects

Most production APIs return structured JSON.

---

## Error DTO

```java
public class ErrorResponse {

    private String message;
    private int status;
    private LocalDateTime timestamp;

}
```

---

## Global Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
            RuntimeException.class)
    public ResponseEntity<ErrorResponse>
    handleException(
            RuntimeException ex) {

        ErrorResponse error =
                new ErrorResponse();

        error.setMessage(
                ex.getMessage());

        error.setStatus(500);

        error.setTimestamp(
                LocalDateTime.now());

        return ResponseEntity
                .status(500)
                .body(error);

    }

}
```

---

Response:

```json
{
  "message": "Employee Not Found",
  "status": 500,
  "timestamp": "2026-01-01T10:00:00"
}
```

---

# Handling Custom Exceptions

---

## Custom Exception

```java
public class EmployeeNotFoundException
        extends RuntimeException {

    public EmployeeNotFoundException(
            String message) {

        super(message);

    }

}
```

---

## Controller

```java
throw new EmployeeNotFoundException(
        "Employee Not Found");
```

---

## Global Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
        EmployeeNotFoundException.class)
    public ResponseEntity<String>
    handleEmployeeException(
        EmployeeNotFoundException ex) {

        return ResponseEntity
                .status(404)
                .body(ex.getMessage());

    }

}
```

---

Response:

```http
404 Not Found
```

```json
"Employee Not Found"
```

---

# Handling Multiple Exceptions

---

```java
@ExceptionHandler({
    NullPointerException.class,
    IllegalArgumentException.class
})
public ResponseEntity<String>
handleException(Exception ex) {

    return ResponseEntity
            .badRequest()
            .body(ex.getMessage());

}
```

---

Handles:

```text
NullPointerException
IllegalArgumentException
```

---

# Catch-All Exception Handler

---

```java
@ExceptionHandler(Exception.class)
public ResponseEntity<String>
handleAll(Exception ex) {

    return ResponseEntity
            .internalServerError()
            .body("Unexpected Error");

}
```

---

Acts as fallback.

---

# Validation Exception Handling

Very common interview topic.

---

Entity:

```java
public class Employee {

    @NotBlank
    private String name;

}
```

---

Controller:

```java
@PostMapping
public Employee save(
        @Valid
        @RequestBody Employee employee) {

    return employee;

}
```

---

Validation Failure:

```java
MethodArgumentNotValidException
```

is thrown.

---

Handle Globally:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
        MethodArgumentNotValidException.class)
    public ResponseEntity<String>
    handleValidation(
        MethodArgumentNotValidException ex) {

        return ResponseEntity
                .badRequest()
                .body("Validation Failed");

    }

}
```

---

# Production Validation Response

Better approach:

---

```java
@ExceptionHandler(
    MethodArgumentNotValidException.class)
public ResponseEntity<Map<String,String>>
handleValidation(
    MethodArgumentNotValidException ex) {

    Map<String,String> errors =
            new HashMap<>();

    ex.getBindingResult()
      .getFieldErrors()
      .forEach(error ->
          errors.put(
             error.getField(),
             error.getDefaultMessage()
          ));

    return ResponseEntity
            .badRequest()
            .body(errors);

}
```

---

Response:

```json
{
  "name": "must not be blank",
  "email": "must be valid"
}
```

---

# Handling ConstraintViolationException

Used for:

```java
@PathVariable
@RequestParam
```

validation.

---

```java
@ExceptionHandler(
        ConstraintViolationException.class)
public ResponseEntity<String>
handleConstraintViolation(
        ConstraintViolationException ex) {

    return ResponseEntity
            .badRequest()
            .body(ex.getMessage());

}
```

---

# Limiting Scope

By default:

```java
@RestControllerAdvice
```

applies globally.

---

Can restrict.

---

## Package Level

```java
@RestControllerAdvice(
    basePackages =
       "com.company.controller"
)
```

---

## Specific Controllers

```java
@RestControllerAdvice(
    assignableTypes = {
        EmployeeController.class
    }
)
```

---

## Specific Annotation

```java
@RestControllerAdvice(
    annotations = RestController.class
)
```

---

# @RestControllerAdvice vs @ControllerAdvice

Very Important Interview Topic.

---

## @ControllerAdvice

Equivalent:

```java
@ControllerAdvice
```

---

May return:

```text
View Name
JSP Page
Thymeleaf Page
```

---

Often requires:

```java
@ResponseBody
```

for REST responses.

---

## @RestControllerAdvice

Equivalent:

```java
@ControllerAdvice
+
@ResponseBody
```

---

Always returns:

```text
JSON
XML
REST Response
```

---

Comparison:

| Feature | @ControllerAdvice | @RestControllerAdvice |
|----------|------------------|-----------------------|
| Global Advice | Yes | Yes |
| Exception Handling | Yes | Yes |
| Returns View | Yes | No |
| JSON Response | Needs @ResponseBody | Automatic |
| Best For REST APIs | No | Yes |

---

# Internal Architecture

Spring uses:

```java
ExceptionHandlerExceptionResolver
```

to discover:

```java
@RestControllerAdvice
```

classes.

---

Flow:

```text
Controller
    │
    ▼
Exception
    │
    ▼
DispatcherServlet
    │
    ▼
ExceptionHandlerExceptionResolver
    │
    ▼
@RestControllerAdvice
    │
    ▼
JSON Response
```

---

# Common Mistakes

## Mistake 1

Returning Raw Exception Messages

---

Bad:

```java
return ex.getMessage();
```

---

May expose internal details.

---

Better:

```java
return ErrorResponse;
```

---

# Mistake 2

Using Generic Exception Handler Only

---

```java
@ExceptionHandler(Exception.class)
```

---

Can hide useful exception-specific handling.

---

Create specific handlers first.

---

# Mistake 3

Not Setting HTTP Status

---

Wrong:

```java
return errorResponse;
```

---

Better:

```java
return ResponseEntity
        .status(404)
        .body(errorResponse);
```

---

# Mistake 4

Using @ControllerAdvice in REST APIs

---

Prefer:

```java
@RestControllerAdvice
```

---

Cleaner and less boilerplate.

---

# Common Interview Questions

## What is @RestControllerAdvice?

Global advice for REST controllers.

---

## What is it internally?

```java
@ControllerAdvice
+
@ResponseBody
```

---

## What is the primary use case?

Global exception handling.

---

## Can it handle validation errors?

Yes.

Using:

```java
MethodArgumentNotValidException
```

---

## Can it return JSON?

Yes.

Automatically.

---

## Difference Between @ControllerAdvice and @RestControllerAdvice?

`@RestControllerAdvice`

always returns response body data.

---

`@ControllerAdvice`

may return views.

---

## Which Spring component processes it?

```java
ExceptionHandlerExceptionResolver
```

---

# Real-World Use Cases

## Standard API Error Responses

```json
{
  "timestamp": "...",
  "status": 404,
  "message": "Employee Not Found"
}
```

---

## Validation Handling

```json
{
  "name": "must not be blank",
  "email": "must be valid"
}
```

---

## Security Exception Handling

```text
Unauthorized
Forbidden
Token Expired
```

---

## Business Exception Handling

```text
Order Not Found
User Not Found
Payment Failed
```

---

## Centralized Logging

```java
@ExceptionHandler(Exception.class)
```

can log all application errors in one place.

---

# Internal Flow Diagram

```text
HTTP Request
      │
      ▼
@RestController
      │
      ▼
Exception Occurs
      │
      ▼
DispatcherServlet
      │
      ▼
ExceptionHandlerExceptionResolver
      │
      ▼
@RestControllerAdvice
      │
      ▼
JSON Error Response
```

---

# Key Points To Remember

- `@RestControllerAdvice` provides global behavior for REST controllers.
- Internally equals `@ControllerAdvice + @ResponseBody`.
- Most commonly used for global exception handling.
- Automatically returns JSON/XML responses.
- Works with `@ExceptionHandler`, `@ModelAttribute`, and `@InitBinder`.
- Can handle validation exceptions globally.
- Supports package-level and controller-level restrictions.
- Processed by `ExceptionHandlerExceptionResolver`.
- Preferred over `@ControllerAdvice` in REST APIs.
- One of the most important Spring Boot REST API interview annotations.