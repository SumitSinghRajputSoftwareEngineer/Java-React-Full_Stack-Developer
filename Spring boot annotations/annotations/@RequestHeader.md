
## Definition

`@RequestHeader` is a Spring MVC annotation used to extract values from the **HTTP Request Headers** and bind them to method parameters.

It tells Spring:

> Read the value from an incoming HTTP request header and inject it into the controller method.

Package:

```java
import org.springframework.web.bind.annotation.RequestHeader;
```

---

# Why Do We Need @RequestHeader?

Every HTTP request contains headers.

Example:

```http
GET /employees/101 HTTP/1.1
Host: localhost:8080
User-Agent: PostmanRuntime/7.36.0
Authorization: Bearer abc123
Content-Type: application/json
Accept: application/json
```

---

Sometimes applications need to read:

```text
Authorization Token
Language
Content Type
API Version
Client Information
Correlation IDs
Custom Headers
```

---

To access them inside Spring Controller:

```java
@RequestHeader
```

is used.

---

# What Problem Does It Solve?

Suppose request contains:

```http
Authorization: Bearer xyz123
```

How do we access it?

Without:

```java
@RequestHeader
```

Spring won't automatically inject the value.

---

Using:

```java
@RequestHeader("Authorization")
String token
```

Spring extracts:

```text
Bearer xyz123
```

and injects it into the method.

---

# Basic Syntax

```java
@GetMapping("/employees")
public String getEmployee(
        @RequestHeader("Authorization")
        String token) {

    return token;

}
```

---

Request:

```http
GET /employees

Authorization: Bearer abc123
```

---

Response:

```text
Bearer abc123
```

---

# What Happens Internally?

Request:

```http
GET /employees

Authorization: Bearer abc123
```

---

Spring Flow:

```text
HTTP Request
      │
      ▼
DispatcherServlet
      │
      ▼
Handler Adapter
      │
      ▼
RequestHeaderMethodArgumentResolver
      │
      ▼
Extract Header
      │
      ▼
Convert Type
      │
      ▼
Inject Into Method
```

---

# Simple Example

```java
@RestController
public class EmployeeController {

    @GetMapping("/test")
    public String test(
            @RequestHeader("User-Agent")
            String userAgent) {

        return userAgent;

    }

}
```

---

Request:

```http
GET /test

User-Agent: Chrome
```

---

Response:

```text
Chrome
```

---

# Header Name Matching

Most common usage.

---

```java
@RequestHeader("Authorization")
String token
```

---

Request:

```http
Authorization: Bearer xyz
```

---

Spring injects:

```java
token = "Bearer xyz"
```

---

# Using Parameter Name Matching

Possible if parameter names match.

---

```java
@GetMapping("/test")
public String test(
        @RequestHeader String Authorization) {

    return Authorization;

}
```

---

But not recommended.

Prefer:

```java
@RequestHeader("Authorization")
```

for clarity.

---

# Reading Multiple Headers

---

```java
@GetMapping("/test")
public String test(

        @RequestHeader("Authorization")
        String token,

        @RequestHeader("User-Agent")
        String agent) {

    return token + " " + agent;

}
```

---

Request:

```http
Authorization: Bearer xyz
User-Agent: Chrome
```

---

Response:

```text
Bearer xyz Chrome
```

---

# Optional Headers

By default:

```java
@RequestHeader
```

is required.

---

Example:

```java
@GetMapping("/test")
public String test(
        @RequestHeader("token")
        String token) {

    return token;

}
```

---

Request:

```http
GET /test
```

(no token header)

---

Exception:

```java
MissingRequestHeaderException
```

---

# Making Header Optional

---

```java
@GetMapping("/test")
public String test(

        @RequestHeader(
            value = "token",
            required = false
        )
        String token) {

    return token;

}
```

---

Valid Requests:

```http
GET /test
```

or

```http
GET /test

token: abc
```

---

# Using Default Values

Very common.

---

```java
@GetMapping("/test")
public String test(

        @RequestHeader(
            value = "version",
            defaultValue = "v1"
        )
        String version) {

    return version;

}
```

---

Request:

```http
GET /test
```

---

Response:

```text
v1
```

---

# Automatic Type Conversion

Spring automatically converts header values.

---

Request:

```http
age: 25
```

---

Controller:

```java
@GetMapping("/test")
public Integer test(
        @RequestHeader Integer age) {

    return age;

}
```

---

Conversion:

```text
String → Integer
```

---

Supported Types:

```java
String
Integer
Long
Double
Boolean
Enum
UUID
Date
LocalDate
LocalDateTime
```

---

# Reading All Headers

Useful in debugging.

---

```java
@GetMapping("/test")
public Map<String,String> test(
        @RequestHeader Map<String,String> headers) {

    return headers;

}
```

---

Response:

```json
{
  "host":"localhost:8080",
  "user-agent":"Chrome",
  "accept":"*/*"
}
```

---

# Reading MultiValue Headers

Some headers can have multiple values.

---

Request:

```http
X-Role: ADMIN
X-Role: USER
```

---

Controller:

```java
@GetMapping("/test")
public List<String> test(

        @RequestHeader("X-Role")
        List<String> roles) {

    return roles;

}
```

---

Result:

```java
["ADMIN","USER"]
```

---

# Authorization Header Example

Very Common Interview Topic.

---

Request:

```http
Authorization: Bearer xyz123
```

---

Controller:

```java
@GetMapping("/profile")
public String profile(

        @RequestHeader("Authorization")
        String token) {

    return token;

}
```

---

Response:

```text
Bearer xyz123
```

---

In production:

```text
JWT Token
OAuth Token
Access Token
```

are usually passed this way.

---

# API Versioning Example

---

Request:

```http
API-Version: v2
```

---

Controller:

```java
@GetMapping("/employees")
public String employees(

        @RequestHeader("API-Version")
        String version) {

    return version;

}
```

---

Used in:

```text
API Versioning
```

---

# Correlation ID Example

Microservices commonly use:

```http
X-Correlation-Id
```

---

Request:

```http
X-Correlation-Id: 12345
```

---

Controller:

```java
@GetMapping("/employees")
public String employee(

        @RequestHeader(
            "X-Correlation-Id"
        )
        String correlationId) {

    return correlationId;

}
```

---

Used for:

```text
Distributed Tracing
Logging
Monitoring
Observability
```

---

# @RequestHeader vs @RequestParam

## Request Header

Read from:

```http
Authorization: Bearer xyz
```

---

Code:

```java
@RequestHeader
```

---

## Request Parameter

Read from:

```http
?name=John
```

---

Code:

```java
@RequestParam
```

---

Comparison:

| Feature | @RequestHeader | @RequestParam |
|----------|---------------|---------------|
| Source | Header | Query Parameter |
| Security Tokens | Yes | No |
| Pagination | No | Yes |
| Filtering | No | Yes |

---

# @RequestHeader vs @RequestBody

| Feature | @RequestHeader | @RequestBody |
|----------|---------------|--------------|
| Reads | Headers | Body |
| Authorization | Yes | No |
| JSON Object | No | Yes |
| Metadata | Yes | No |

---

# Validation with @RequestHeader

---

```java
@GetMapping("/employees")
public String employee(

        @NotBlank
        @RequestHeader("token")
        String token) {

    return token;

}
```

---

Requires:

```java
@Validated
```

---

Controller:

```java
@RestController
@Validated
public class EmployeeController {

}
```

---

# Internal Architecture

Headers are resolved by:

```java
RequestHeaderMethodArgumentResolver
```

---

Spring Flow:

```text
DispatcherServlet
        │
        ▼
RequestMappingHandlerAdapter
        │
        ▼
RequestHeaderMethodArgumentResolver
        │
        ▼
Controller Method
```

---

# Internal Classes

Very Important.

---

Resolver:

```java
RequestHeaderMethodArgumentResolver
```

---

Parent:

```java
HandlerMethodArgumentResolver
```

---

Used by:

```java
RequestMappingHandlerAdapter
```

---

# Common Mistakes

## Mistake 1

Missing Required Header

Controller:

```java
@RequestHeader("token")
String token
```

---

Request:

```http
GET /employees
```

---

Exception:

```java
MissingRequestHeaderException
```

---

# Mistake 2

Wrong Header Name

Request:

```http
Authorization: Bearer xyz
```

---

Code:

```java
@RequestHeader("Auth")
```

---

Result:

```text
Header Not Found
```

---

# Mistake 3

Wrong Data Type

Request:

```http
age: abc
```

---

Code:

```java
@RequestHeader Integer age
```

---

Exception:

```java
MethodArgumentTypeMismatchException
```

---

# Mistake 4

Using RequestParam Instead

Wrong:

```java
@RequestParam("Authorization")
```

---

Correct:

```java
@RequestHeader("Authorization")
```

---

# Common Interview Questions

## What is @RequestHeader?

Used to extract HTTP request header values.

---

## Which component resolves @RequestHeader?

```java
RequestHeaderMethodArgumentResolver
```

---

## Is @RequestHeader required by default?

Yes.

---

## How to make it optional?

```java
@RequestHeader(
    required = false
)
```

---

## How to provide default value?

```java
@RequestHeader(
    defaultValue = "v1"
)
```

---

## Can Spring convert header values automatically?

Yes.

Examples:

```java
String → Integer
String → Long
String → Enum
String → UUID
```

---

## Can all headers be captured?

Yes.

Using:

```java
@RequestHeader Map<String,String>
```

---

## Most common real-world use?

```text
Authorization Tokens
JWT
API Versioning
Correlation IDs
Client Metadata
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
@Validated
public class EmployeeController {

    @GetMapping
    public ResponseEntity<List<Employee>>
    employees(

            @RequestHeader(
                value = "Authorization",
                required = false
            )
            String token,

            @RequestHeader(
                value = "API-Version",
                defaultValue = "v1"
            )
            String version) {

        return ResponseEntity.ok(
                service.findAll());

    }

}
```

---

Request:

```http
GET /api/employees

Authorization: Bearer xyz123
API-Version: v2
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
HTTP Request
   │
   ▼
Headers
   │
   ▼
DispatcherServlet
   │
   ▼
RequestHeaderMethodArgumentResolver
   │
   ▼
Extract Header Values
   │
   ▼
Type Conversion
   │
   ▼
Inject Into Method
   │
   ▼
Controller Method
```

---

# Key Points To Remember

- `@RequestHeader` extracts values from HTTP request headers.
- Headers are required by default.
- Supports optional headers using `required = false`.
- Supports default values using `defaultValue`.
- Supports automatic type conversion.
- Can read single headers, multiple headers, all headers, and multi-value headers.
- Commonly used for JWT tokens, API versioning, correlation IDs, and client metadata.
- Internally resolved by `RequestHeaderMethodArgumentResolver`.
- Frequently asked Spring MVC and Spring Boot interview annotation.
- Essential for secure REST API development.