
## Definition

`@ControllerAdvice` is a Spring MVC annotation used to provide **global behavior** across multiple controllers.

It is commonly used for:

- Global Exception Handling
- Global Data Binding
- Global Model Attributes
- Common Controller Logic

It tells Spring:

> Apply this logic to all controllers (or a selected group of controllers) in the application.

Package:

```java
import org.springframework.web.bind.annotation.ControllerAdvice;
```

---

# Why Do We Need @ControllerAdvice?

Imagine we have 50 REST Controllers.

Without `@ControllerAdvice`, every controller would need its own exception handling logic.

Example:

```java
@RestController
public class EmployeeController {

    @GetMapping("/{id}")
    public Employee getEmployee(
            @PathVariable Long id) {

        throw new RuntimeException(
                "Employee Not Found");

    }

    @ExceptionHandler(RuntimeException.class)
    public String handleException(
            RuntimeException ex) {

        return ex.getMessage();

    }

}
```

---

Now imagine:

```text
50 Controllers
```

Every controller has:

```java
@ExceptionHandler
```

This leads to:

- Code Duplication
- Difficult Maintenance
- Poor Design

---

Solution:

```java
@ControllerAdvice
```

---

# What Does @ControllerAdvice Do?

It acts like:

```text
Global Interceptor
For Controllers
```

---

Spring automatically applies its methods to all controllers.

---

Flow:

```text
Request
   │
   ▼
Controller
   │
Exception?
   │
   ▼
@ControllerAdvice
   │
   ▼
Return Error Response
```

---

# Basic Example

```java
@ControllerAdvice
public class GlobalExceptionHandler {

}
```

---

This class can now handle exceptions globally.

---

# Most Common Use Case

## Global Exception Handling

---

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String>
    handleException(Exception ex) {

        return ResponseEntity
                .status(500)
                .body(ex.getMessage());

    }

}
```

---

Controller:

```java
@RestController
public class EmployeeController {

    @GetMapping("/employees")
    public String employees() {

        throw new RuntimeException(
                "Something Went Wrong");

    }

}
```

---

Request:

```http
GET /employees
```

---

Response:

```http
500 Internal Server Error
```

```json
Something Went Wrong
```

---

# What Happens Internally?

Suppose:

```java
throw new RuntimeException();
```

---

Spring Flow:

```text
Controller Method
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
@ControllerAdvice
       │
       ▼
@ExceptionHandler
       │
       ▼
Response Returned
```

---

# Handling Specific Exceptions

---

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
            EmployeeNotFoundException.class)
    public ResponseEntity<String>
    handleEmployeeNotFound(
            EmployeeNotFoundException ex) {

        return ResponseEntity
                .status(404)
                .body(ex.getMessage());

    }

}
```

---

Controller:

```java
throw new EmployeeNotFoundException(
        "Employee Not Found");
```

---

Response:

```http
404 Not Found
```

---

# Multiple Exception Handlers

---

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
            EmployeeNotFoundException.class)
    public ResponseEntity<String>
    employeeNotFound(
            EmployeeNotFoundException ex) {

        return ResponseEntity.notFound().build();

    }

    @ExceptionHandler(
            RuntimeException.class)
    public ResponseEntity<String>
    runtimeException(
            RuntimeException ex) {

        return ResponseEntity
                .internalServerError()
                .body(ex.getMessage());

    }

}
```

---

Spring automatically selects the most specific handler.

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

    return ResponseEntity.badRequest()
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

# Returning Custom Error Response

Production applications usually return structured JSON.

---

```java
public class ErrorResponse {

    private String message;
    private LocalDateTime timestamp;

}
```

---

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
            EmployeeNotFoundException.class)
    public ResponseEntity<ErrorResponse>
    handleException(
            EmployeeNotFoundException ex) {

        ErrorResponse response =
                new ErrorResponse();

        response.setMessage(
                ex.getMessage());

        response.setTimestamp(
                LocalDateTime.now());

        return ResponseEntity
                .status(404)
                .body(response);

    }

}
```

---

Response:

```json
{
  "message": "Employee Not Found",
  "timestamp": "2026-01-01T10:00:00"
}
```

---

# Validation Exception Handling

Very common.

---

Controller:

```java
@PostMapping("/employees")
public Employee saveEmployee(
        @Valid
        @RequestBody Employee employee) {

    return employee;

}
```

---

Validation Failure:

```java
@NotBlank
private String name;
```

---

Handle Globally:

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
        MethodArgumentNotValidException.class)
    public ResponseEntity<?> handleValidation(
        MethodArgumentNotValidException ex) {

        return ResponseEntity
                .badRequest()
                .body("Validation Failed");

    }

}
```

---

# Handling All Exceptions

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

Acts as a global fallback.

---

# @ControllerAdvice + @ModelAttribute

Less common but important.

---

Used to add data to every controller.

---

```java
@ControllerAdvice
public class GlobalModelAttributes {

    @ModelAttribute("company")
    public String company() {

        return "OpenAI";

    }

}
```

---

Every MVC Controller receives:

```java
company = "OpenAI"
```

---

Useful for:

```text
Header Data
Footer Data
User Information
Common UI Attributes
```

---

# @ControllerAdvice + @InitBinder

Used for global data binding.

---

```java
@ControllerAdvice
public class GlobalBinder {

    @InitBinder
    public void initBinder(
            WebDataBinder binder) {

        binder.registerCustomEditor(
                Date.class,
                new CustomDateEditor(...)
        );

    }

}
```

---

Applied to all controllers.

---

# Limiting Scope

By default:

```java
@ControllerAdvice
```

applies to all controllers.

---

Can restrict it.

---

## Specific Package

```java
@ControllerAdvice(
    basePackages =
        "com.company.controller"
)
```

---

Only controllers inside package.

---

# Specific Controller Classes

---

```java
@ControllerAdvice(
    assignableTypes = {
        EmployeeController.class
    }
)
```

---

Only applies to:

```java
EmployeeController
```

---

# Specific Annotation

---

```java
@ControllerAdvice(
    annotations = RestController.class
)
```

---

Only applies to:

```java
@RestController
```

classes.

---

# @ControllerAdvice vs @RestControllerAdvice

Very Important Interview Topic.

---

## @ControllerAdvice

Works for:

```java
@Controller
@RestController
```

---

Response may require:

```java
@ResponseBody
```

---

Example:

```java
@ControllerAdvice
public class GlobalHandler {

}
```

---

## @RestControllerAdvice

Combination of:

```java
@ControllerAdvice
+
@ResponseBody
```

---

Equivalent:

```java
@ControllerAdvice
@ResponseBody
```

---

Example:

```java
@RestControllerAdvice
public class GlobalHandler {

}
```

---

Most REST APIs prefer:

```java
@RestControllerAdvice
```

---

# Internal Architecture

Spring uses:

```java
ExceptionHandlerExceptionResolver
```

to locate:

```java
@ControllerAdvice
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
@ControllerAdvice
    │
    ▼
Response
```

---

# Common Mistakes

## Mistake 1

Forgetting @ExceptionHandler

---

Wrong:

```java
@ControllerAdvice
public class Handler {

}
```

---

No exception handler methods.

---

Nothing happens.

---

# Mistake 2

Using @ControllerAdvice for REST APIs

---

```java
@ControllerAdvice
```

may return view names.

---

Prefer:

```java
@RestControllerAdvice
```

for REST APIs.

---

# Mistake 3

Using Generic Handler First

---

```java
@ExceptionHandler(Exception.class)
```

---

Can unintentionally catch everything.

---

Create specific handlers first.

---

# Mistake 4

Returning Raw Strings

---

Bad:

```java
return ex.getMessage();
```

---

Better:

```java
return ErrorResponse;
```

---

Structured API responses are preferred.

---

# Common Interview Questions

## What is @ControllerAdvice?

Provides global controller-related functionality.

---

## What is the most common use case?

Global Exception Handling.

---

## Can it handle validation errors?

Yes.

Using:

```java
MethodArgumentNotValidException
```

---

## Difference Between @ControllerAdvice and @RestControllerAdvice?

`@RestControllerAdvice`

```java
@ControllerAdvice
+
@ResponseBody
```

---

## Can it add model attributes globally?

Yes.

Using:

```java
@ModelAttribute
```

---

## Can it customize data binding?

Yes.

Using:

```java
@InitBinder
```

---

## Does it apply to all controllers?

Yes by default.

Can be restricted using:

```java
basePackages
assignableTypes
annotations
```

---

# Real-World Use Cases

## Global Error Handling

```text
404 Errors
500 Errors
Business Exceptions
Validation Errors
```

---

## API Error Standardization

Return:

```json
{
  "timestamp": "...",
  "status": 404,
  "message": "Employee Not Found"
}
```

for every controller.

---

## Validation Handling

```java
@NotNull
@NotBlank
@Email
```

failures handled centrally.

---

## Shared UI Data

```text
Application Name
Current User
Company Information
```

added globally.

---

## Global Date Formatting

Using:

```java
@InitBinder
```

---

# Internal Flow Diagram

```text
HTTP Request
      │
      ▼
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
@ControllerAdvice
      │
      ▼
@ExceptionHandler
      │
      ▼
Response Returned
```

---

# Key Points To Remember

- `@ControllerAdvice` provides global controller functionality.
- Most commonly used for global exception handling.
- Works with `@ExceptionHandler`, `@ModelAttribute`, and `@InitBinder`.
- Applies to all controllers by default.
- Can be limited to specific packages, annotations, or controllers.
- Internally handled by `ExceptionHandlerExceptionResolver`.
- `@RestControllerAdvice` is preferred for REST APIs.
- Helps create centralized and reusable error handling.
- Essential for production-grade Spring Boot applications.
- One of the most frequently asked Spring MVC and Spring Boot interview annotations.