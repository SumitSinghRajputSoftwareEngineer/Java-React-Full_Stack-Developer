
## Definition

`@ExceptionHandler` is a Spring MVC annotation used to handle exceptions thrown by controller methods.

It tells Spring:

> When a specific exception occurs, execute this method instead of letting the exception propagate to the client.

Package:

```java
import org.springframework.web.bind.annotation.ExceptionHandler;
```

---

# Why Do We Need @ExceptionHandler?

Without exception handling:

```java
@GetMapping("/employee/{id}")
public Employee getEmployee(
        @PathVariable Long id) {

    return service.findById(id);

}
```

---

Suppose:

```java
findById()
```

throws:

```java
EmployeeNotFoundException
```

---

Client receives:

```http
500 INTERNAL SERVER ERROR
```

and a large stack trace.

---

Not ideal.

---

We want:

```http
404 NOT FOUND
```

```json
{
  "message":"Employee not found"
}
```

---

This is where:

```java
@ExceptionHandler
```

comes in.

---

# What Problem Does It Solve?

Instead of:

```text
Application Crash
```

or

```text
Raw Stack Trace
```

we can return:

```text
Meaningful Error Response
```

---

Example:

```json
{
  "error":"Employee Not Found"
}
```

---

# Basic Syntax

```java
@ExceptionHandler(
        EmployeeNotFoundException.class
)
public String handleException(
        EmployeeNotFoundException ex) {

    return ex.getMessage();

}
```

---

Whenever:

```java
EmployeeNotFoundException
```

is thrown,

Spring executes:

```java
handleException()
```

---

# What Happens Internally?

Request:

```http
GET /employees/101
```

---

Flow:

```text
Controller
     │
     ▼
Exception Thrown
     │
     ▼
ExceptionHandlerExceptionResolver
     │
     ▼
@ExceptionHandler Method
     │
     ▼
Response Returned
```

---

# Simple Example

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
@RestController
public class EmployeeController {

    @GetMapping("/employees/{id}")
    public Employee employee(
            @PathVariable Long id) {

        throw new EmployeeNotFoundException(
                "Employee Not Found"
        );

    }

    @ExceptionHandler(
            EmployeeNotFoundException.class
    )
    public String handleException(
            EmployeeNotFoundException ex) {

        return ex.getMessage();

    }

}
```

---

Response:

```text
Employee Not Found
```

---

# Returning Custom Response

Most common approach.

---

Error DTO:

```java
public class ErrorResponse {

    private String message;
    private LocalDateTime timestamp;

}
```

---

Handler:

```java
@ExceptionHandler(
        EmployeeNotFoundException.class
)
public ErrorResponse handleException(
        EmployeeNotFoundException ex) {

    return new ErrorResponse(
            ex.getMessage(),
            LocalDateTime.now()
    );

}
```

---

Response:

```json
{
  "message":"Employee Not Found",
  "timestamp":"2026-06-26T10:00:00"
}
```

---

# Returning ResponseEntity

Most recommended.

---

```java
@ExceptionHandler(
        EmployeeNotFoundException.class
)
public ResponseEntity<String>
handleException(
        EmployeeNotFoundException ex) {

    return ResponseEntity
            .status(HttpStatus.NOT_FOUND)
            .body(ex.getMessage());

}
```

---

Response:

```http
404 NOT FOUND
```

```text
Employee Not Found
```

---

# Handling Multiple Exceptions

---

```java
@ExceptionHandler({
        IllegalArgumentException.class,
        NullPointerException.class
})
public String handleException(
        Exception ex) {

    return ex.getMessage();

}
```

---

Handles:

```java
IllegalArgumentException
NullPointerException
```

---

with one method.

---

# Catch-All Exception Handler

Useful as fallback.

---

```java
@ExceptionHandler(Exception.class)
public String handleException(
        Exception ex) {

    return "Something went wrong";

}
```

---

Handles:

```text
All Exceptions
```

---

Use carefully.

---

# Exception Handler Method Parameters

Spring can inject:

```java
Exception
HttpServletRequest
HttpServletResponse
WebRequest
Locale
Principal
```

---

Example:

```java
@ExceptionHandler(
        Exception.class
)
public String handleException(
        Exception ex,
        HttpServletRequest request) {

    return request.getRequestURI();

}
```

---

# Exception Handler Return Types

Allowed:

```java
String
Object
DTO
Map
ModelAndView
ResponseEntity
ProblemDetail
void
```

---

Example:

```java
@ExceptionHandler(
        Exception.class
)
public ResponseEntity<String>
handle(Exception ex) {

    return ResponseEntity
            .internalServerError()
            .body("Error");

}
```

---

# Method-Level Scope

Very Important.

---

```java
@RestController
public class EmployeeController {

    @ExceptionHandler(
            EmployeeNotFoundException.class
    )
    public String handle() {

        return "Not Found";

    }

}
```

---

This handler works only for:

```java
EmployeeController
```

---

Not for other controllers.

---

# Global Exception Handling

Most common in production.

Use:

```java
@ControllerAdvice
```

or

```java
@RestControllerAdvice
```

---

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
            EmployeeNotFoundException.class
    )
    public ResponseEntity<String>
    handleException(
            EmployeeNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());

    }

}
```

---

Now every controller uses it.

---

# Spring Boot 3 ProblemDetail

Modern approach.

---

```java
@ExceptionHandler(
        EmployeeNotFoundException.class
)
public ProblemDetail handle(
        EmployeeNotFoundException ex) {

    ProblemDetail detail =
            ProblemDetail.forStatus(
                    HttpStatus.NOT_FOUND
            );

    detail.setDetail(
            ex.getMessage()
    );

    return detail;

}
```

---

Response:

```json
{
  "type":"about:blank",
  "title":"Not Found",
  "status":404,
  "detail":"Employee Not Found"
}
```

---

# Exception Hierarchy Resolution

Suppose:

```java
RuntimeException
      ▲
      │
EmployeeNotFoundException
```

---

Handlers:

```java
@ExceptionHandler(RuntimeException.class)
```

and

```java
@ExceptionHandler(EmployeeNotFoundException.class)
```

---

Spring chooses:

```java
EmployeeNotFoundException
```

because it is more specific.

---

# @ExceptionHandler vs try-catch

## try-catch

```java
@GetMapping
public String test() {

    try {

    } catch(Exception ex) {

    }

}
```

---

Problems:

```text
Code Duplication
Messy Controllers
Hard Maintenance
```

---

## ExceptionHandler

Centralized.

```java
@ExceptionHandler
```

---

Cleaner architecture.

---

# @ExceptionHandler vs @ResponseStatus

---

## ResponseStatus

```java
@ResponseStatus(
        HttpStatus.NOT_FOUND
)
```

---

Static.

---

## ExceptionHandler

```java
@ExceptionHandler
```

---

Dynamic.

Can return:

```java
Body
Headers
Custom DTO
ResponseEntity
```

---

Comparison:

| Feature | @ResponseStatus | @ExceptionHandler |
|----------|----------------|------------------|
| Status Code | Yes | Yes |
| Response Body | Limited | Full |
| Headers | No | Yes |
| Dynamic Logic | No | Yes |

---

# @ExceptionHandler vs ControllerAdvice

---

## ExceptionHandler

Controller-specific.

---

```java
@RestController
public class EmployeeController {

}
```

---

Only affects:

```java
EmployeeController
```

---

## ControllerAdvice

Global.

---

```java
@RestControllerAdvice
```

---

Affects:

```text
Entire Application
```

---

# Internal Architecture

Very Important Interview Topic.

---

Spring uses:

```java
ExceptionHandlerExceptionResolver
```

to process:

```java
@ExceptionHandler
```

---

Flow:

```text
DispatcherServlet
       │
       ▼
Controller Method
       │
       ▼
Exception Thrown
       │
       ▼
ExceptionHandlerExceptionResolver
       │
       ▼
Find Matching Handler
       │
       ▼
Invoke Handler Method
       │
       ▼
Response
```

---

# Internal Classes

Resolver:

```java
ExceptionHandlerExceptionResolver
```

---

Superclass:

```java
AbstractHandlerMethodExceptionResolver
```

---

Used by:

```java
HandlerExceptionResolver
```

---

# Common Mistakes

## Mistake 1

Using Generic Exception First

Wrong:

```java
@ExceptionHandler(Exception.class)
```

only.

---

Specific exceptions should also be handled.

---

# Mistake 2

Returning 200 OK

Wrong:

```java
return "Employee Not Found";
```

---

Response:

```http
200 OK
```

---

Better:

```java
ResponseEntity.notFound()
```

---

# Mistake 3

Creating Many Duplicate Handlers

Bad:

```java
@Controller
```

with handlers everywhere.

---

Use:

```java
@RestControllerAdvice
```

---

# Mistake 4

Using try-catch Everywhere

Avoid:

```java
try {
}
catch(Exception ex) {
}
```

inside controllers.

---

Prefer:

```java
@ExceptionHandler
```

---

# Common Interview Questions

## What is @ExceptionHandler?

Handles exceptions thrown by controller methods.

---

## Which component processes @ExceptionHandler?

```java
ExceptionHandlerExceptionResolver
```

---

## Can one method handle multiple exceptions?

Yes.

```java
@ExceptionHandler({
    A.class,
    B.class
})
```

---

## Can it return ResponseEntity?

Yes.

Recommended.

---

## What is the difference between ExceptionHandler and ControllerAdvice?

ExceptionHandler:

```text
Local
```

ControllerAdvice:

```text
Global
```

---

## Can Spring choose the most specific handler?

Yes.

Spring picks the closest matching exception type.

---

## What is the modern Spring Boot 3 error response mechanism?

```java
ProblemDetail
```

---

# Real-World Example

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
            EmployeeNotFoundException.class
    )
    public ResponseEntity<ErrorResponse>
    handleEmployeeNotFound(
            EmployeeNotFoundException ex) {

        ErrorResponse error =
                new ErrorResponse(
                        ex.getMessage(),
                        LocalDateTime.now()
                );

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(error);

    }

}
```

---

Response:

```http
404 NOT FOUND
```

```json
{
  "message":"Employee Not Found",
  "timestamp":"2026-06-26T10:00:00"
}
```

---

# Internal Flow Diagram

```text
Request
   │
   ▼
Controller Method
   │
   ▼
Exception Thrown
   │
   ▼
ExceptionHandlerExceptionResolver
   │
   ▼
Matching @ExceptionHandler
   │
   ▼
ResponseEntity / DTO
   │
   ▼
HTTP Response
```

---

# Key Points To Remember

- `@ExceptionHandler` handles exceptions thrown by controller methods.
- Supports local and global exception handling.
- Can return DTOs, ResponseEntity, ProblemDetail, Maps, and Strings.
- Processed internally by `ExceptionHandlerExceptionResolver`.
- Spring chooses the most specific exception handler available.
- Often used together with `@RestControllerAdvice`.
- Preferred over repetitive try-catch blocks in controllers.
- Supports multiple exception types in a single handler.
- Essential for building production-grade REST APIs.
- One of the most frequently asked Spring MVC and Spring Boot interview annotations.