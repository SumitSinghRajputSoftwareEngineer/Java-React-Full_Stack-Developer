
## Definition

`@ResponseStatus` is a Spring MVC annotation used to specify the **HTTP Status Code** that should be returned by a controller method or exception class.

It tells Spring:

> Return the specified HTTP status code in the response instead of using the default status.

Package:

```java
import org.springframework.web.bind.annotation.ResponseStatus;
```

---

# Why Do We Need @ResponseStatus?

Every HTTP response contains:

```text
Status Code
Headers
Body
```

Example:

```http
HTTP/1.1 200 OK
```

---

Different situations require different status codes.

Examples:

| Scenario | Status Code |
|-----------|-------------|
| Successful GET | 200 OK |
| New Resource Created | 201 CREATED |
| No Data Returned | 204 NO CONTENT |
| Invalid Request | 400 BAD REQUEST |
| Unauthorized | 401 UNAUTHORIZED |
| Resource Not Found | 404 NOT FOUND |
| Server Failure | 500 INTERNAL SERVER ERROR |

---

Without `@ResponseStatus`, Spring often returns:

```http
200 OK
```

by default.

---

`@ResponseStatus` allows explicit control.

---

# What Problem Does It Solve?

Suppose an employee is created successfully.

Controller:

```java
@PostMapping("/employees")
public Employee create() {

    return employee;

}
```

---

Response:

```http
200 OK
```

---

REST standards recommend:

```http
201 CREATED
```

for resource creation.

---

Solution:

```java
@ResponseStatus(HttpStatus.CREATED)
```

---

# Basic Syntax

```java
@ResponseStatus(HttpStatus.CREATED)
@PostMapping("/employees")
public Employee create() {

    return employee;

}
```

---

Response:

```http
201 CREATED
```

---

# What Happens Internally?

Request:

```http
POST /employees
```

---

Spring Flow:

```text
DispatcherServlet
      │
      ▼
Controller Method
      │
      ▼
@ResponseStatus Found
      │
      ▼
Set HTTP Status
      │
      ▼
Write Response
```

---

# Using @ResponseStatus on Controller Methods

Most common usage.

---

```java
@PostMapping("/employees")

@ResponseStatus(
        HttpStatus.CREATED
)
public Employee create() {

    return employee;

}
```

---

Response:

```http
201 CREATED
```

---

Body:

```json
{
  "id":1,
  "name":"John"
}
```

---

# Using HttpStatus Enum

Status codes come from:

```java
org.springframework.http.HttpStatus
```

---

Example:

```java
HttpStatus.OK
```

---

Value:

```http
200
```

---

# Common Status Codes

## Success Codes

```java
HttpStatus.OK
```

```http
200 OK
```

---

```java
HttpStatus.CREATED
```

```http
201 CREATED
```

---

```java
HttpStatus.ACCEPTED
```

```http
202 ACCEPTED
```

---

```java
HttpStatus.NO_CONTENT
```

```http
204 NO CONTENT
```

---

# Client Error Codes

```java
HttpStatus.BAD_REQUEST
```

```http
400 BAD REQUEST
```

---

```java
HttpStatus.UNAUTHORIZED
```

```http
401 UNAUTHORIZED
```

---

```java
HttpStatus.FORBIDDEN
```

```http
403 FORBIDDEN
```

---

```java
HttpStatus.NOT_FOUND
```

```http
404 NOT FOUND
```

---

# Server Error Codes

```java
HttpStatus.INTERNAL_SERVER_ERROR
```

```http
500 INTERNAL SERVER ERROR
```

---

```java
HttpStatus.SERVICE_UNAVAILABLE
```

```http
503 SERVICE UNAVAILABLE
```

---

# Example: Created Resource

---

```java
@PostMapping("/employees")

@ResponseStatus(
        HttpStatus.CREATED
)
public Employee create() {

    return service.save();

}
```

---

Response:

```http
201 CREATED
```

---

# Example: Delete Resource

REST Best Practice.

---

```java
@DeleteMapping("/{id}")

@ResponseStatus(
        HttpStatus.NO_CONTENT
)
public void delete(
        @PathVariable Long id) {

    service.delete(id);

}
```

---

Response:

```http
204 NO CONTENT
```

---

Body:

```text
No Body
```

---

# Using reason Attribute

Possible but rarely used.

---

```java
@ResponseStatus(
        value = HttpStatus.NOT_FOUND,
        reason = "Employee Not Found"
)
```

---

Response:

```http
404 Employee Not Found
```

---

Example:

```java
@GetMapping("/{id}")

@ResponseStatus(
        value = HttpStatus.NOT_FOUND,
        reason = "Employee Not Found"
)
public Employee getEmployee() {

    return null;

}
```

---

# Important Note About reason

In modern Spring Boot applications:

```java
reason
```

is discouraged.

---

Why?

Because:

```java
ResponseStatusException
```

or

```java
Global Exception Handling
```

provides better flexibility.

---

# @ResponseStatus on Exceptions

Very Important Interview Topic.

---

Custom Exception:

```java
@ResponseStatus(
        HttpStatus.NOT_FOUND
)
public class EmployeeNotFoundException
        extends RuntimeException {

}
```

---

Controller:

```java
@GetMapping("/{id}")
public Employee getEmployee(
        @PathVariable Long id) {

    throw new EmployeeNotFoundException();

}
```

---

Response:

```http
404 NOT FOUND
```

---

# Exception Handling Flow

```text
Controller
      │
      ▼
Exception Thrown
      │
      ▼
@ResponseStatus Found
      │
      ▼
Set Status Code
      │
      ▼
Send Response
```

---

# Using value Attribute

Most common.

---

```java
@ResponseStatus(
        value =
        HttpStatus.CREATED
)
```

---

Equivalent:

```java
@ResponseStatus(
        HttpStatus.CREATED
)
```

---

# Using code Attribute

Introduced for readability.

---

```java
@ResponseStatus(
        code =
        HttpStatus.CREATED
)
```

---

Equivalent:

```java
@ResponseStatus(
        value =
        HttpStatus.CREATED
)
```

---

# @ResponseStatus vs ResponseEntity

Most Important Interview Question.

---

## ResponseStatus

Static Status Code.

---

```java
@ResponseStatus(
        HttpStatus.CREATED
)
```

---

Always returns:

```http
201 CREATED
```

---

## ResponseEntity

Dynamic Status Code.

---

```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(employee);
```

---

Can change at runtime.

---

Comparison:

| Feature | @ResponseStatus | ResponseEntity |
|----------|----------------|---------------|
| Static Status | Yes | No |
| Dynamic Status | No | Yes |
| Headers Control | No | Yes |
| Response Body Control | Limited | Full |
| Recommended For APIs | Sometimes | Yes |

---

# When To Use ResponseEntity Instead?

Suppose:

```text
Employee Exists → 200
Employee Missing → 404
```

---

Impossible with:

```java
@ResponseStatus
```

because it is static.

---

Use:

```java
ResponseEntity
```

instead.

---

# @ResponseStatus vs ResponseStatusException

---

## ResponseStatus

Compile-Time

```java
@ResponseStatus(
        HttpStatus.NOT_FOUND
)
```

---

## ResponseStatusException

Runtime

```java
throw new ResponseStatusException(
        HttpStatus.NOT_FOUND,
        "Employee Missing"
);
```

---

Comparison:

| Feature | @ResponseStatus | ResponseStatusException |
|----------|----------------|-------------------------|
| Static | Yes | No |
| Dynamic | No | Yes |
| Runtime Message | No | Yes |

---

# Internal Architecture

Very Important.

---

Spring processes:

```java
@ResponseStatus
```

using:

```java
ResponseStatusExceptionResolver
```

---

Flow:

```text
DispatcherServlet
       │
       ▼
ResponseStatusExceptionResolver
       │
       ▼
Set HTTP Status
       │
       ▼
Response
```

---

# Internal Classes

Main Resolver:

```java
ResponseStatusExceptionResolver
```

---

Exception Resolver Chain:

```java
HandlerExceptionResolver
```

---

HTTP Status Enum:

```java
HttpStatus
```

---

# Common Mistakes

## Mistake 1

Using Dynamic Logic

Wrong:

```java
@ResponseStatus(
        HttpStatus.OK
)
```

---

Expecting:

```text
Sometimes 404
Sometimes 200
```

---

Not possible.

---

# Mistake 2

Returning Body With 204

Wrong:

```java
@ResponseStatus(
        HttpStatus.NO_CONTENT
)
```

and

```java
return employee;
```

---

204 means:

```text
No Response Body
```

---

# Mistake 3

Overusing reason Attribute

Avoid:

```java
reason = "Custom Message"
```

---

Prefer:

```java
ResponseStatusException
```

or

```java
@ControllerAdvice
```

---

# Mistake 4

Using ResponseStatus Instead Of ResponseEntity

If response varies:

```java
ResponseEntity
```

is better.

---

# Common Interview Questions

## What is @ResponseStatus?

Used to specify HTTP status code for a response.

---

## Can it be used on exceptions?

Yes.

Example:

```java
@ResponseStatus(
        HttpStatus.NOT_FOUND
)
```

---

## Which component processes @ResponseStatus?

```java
ResponseStatusExceptionResolver
```

---

## Difference Between ResponseEntity and ResponseStatus?

ResponseStatus:

```text
Static
```

ResponseEntity:

```text
Dynamic
```

---

## Can headers be customized using @ResponseStatus?

No.

Use:

```java
ResponseEntity
```

---

## Is @ResponseStatus evaluated at runtime?

The annotation metadata is read at runtime, but the status value itself is fixed/static.

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {

    @PostMapping

    @ResponseStatus(
            HttpStatus.CREATED
    )
    public Employee create(
            @RequestBody EmployeeRequest request) {

        return service.save(request);

    }

}
```

---

Request:

```http
POST /api/employees
```

---

Response:

```http
201 CREATED
```

```json
{
  "id":1,
  "name":"John"
}
```

---

# Exception Example

```java
@ResponseStatus(
        HttpStatus.NOT_FOUND
)
public class EmployeeNotFoundException
        extends RuntimeException {

}
```

---

```java
@GetMapping("/{id}")
public Employee employee(
        @PathVariable Long id) {

    throw new EmployeeNotFoundException();

}
```

---

Response:

```http
404 NOT FOUND
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
@ResponseStatus Found
   │
   ▼
ResponseStatusExceptionResolver
   │
   ▼
Set HTTP Status
   │
   ▼
HTTP Response
```

---

# Key Points To Remember

- `@ResponseStatus` defines the HTTP status code returned by a controller method or exception.
- Commonly used for `201 CREATED`, `204 NO CONTENT`, and `404 NOT FOUND`.
- Can be applied at method level and exception class level.
- Uses `HttpStatus` enum.
- Supports `value`, `code`, and `reason` attributes.
- `reason` is generally discouraged in modern Spring Boot applications.
- Processed internally by `ResponseStatusExceptionResolver`.
- Best for fixed/static status codes.
- For dynamic responses, prefer `ResponseEntity`.
- Frequently asked in Spring MVC, REST API, and Spring Boot interviews.