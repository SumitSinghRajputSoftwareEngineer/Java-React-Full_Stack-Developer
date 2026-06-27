
## Definition

`@CrossOrigin` is a Spring MVC / Spring Boot annotation used to enable **Cross-Origin Resource Sharing (CORS)**.

It tells Spring:

> Allow requests coming from another domain, port, or protocol.

Package:

```java
import org.springframework.web.bind.annotation.CrossOrigin;
```

---

# Why Do We Need @CrossOrigin?

Modern applications are usually separated into:

```text
Frontend (React/Angular/Vue)
```

and

```text
Backend (Spring Boot)
```

Example:

Frontend:

```text
http://localhost:3000
```

Backend:

```text
http://localhost:8080
```

Even though both run on the same machine, browsers treat them as different origins because:

```text
Port is different
```

---

When React tries to call Spring Boot:

```javascript
fetch("http://localhost:8080/api/employees")
```

Browser blocks it.

Error:

```text
Access to fetch at
'http://localhost:8080'
from origin
'http://localhost:3000'
has been blocked by CORS policy
```

---

To allow the request:

```java
@CrossOrigin
```

is used.

---

# What is Same-Origin Policy?

Browsers implement a security mechanism called:

```text
Same-Origin Policy (SOP)
```

---

An Origin consists of:

```text
Protocol + Domain + Port
```

Example:

```text
http://localhost:8080
```

---

Protocol:

```text
http
```

Domain:

```text
localhost
```

Port:

```text
8080
```

---

# Same Origin Examples

## Same Origin

```text
http://localhost:8080
http://localhost:8080
```

Allowed.

---

## Different Port

```text
http://localhost:3000
http://localhost:8080
```

Different Origin.

Blocked.

---

## Different Protocol

```text
http://example.com
https://example.com
```

Different Origin.

Blocked.

---

## Different Domain

```text
https://google.com
https://facebook.com
```

Different Origin.

Blocked.

---

# Basic Usage

---

```java
@RestController
@CrossOrigin
public class EmployeeController {

}
```

---

Spring allows requests from any origin.

---

# Example

```java
@RestController
@RequestMapping("/employees")
@CrossOrigin
public class EmployeeController {

    @GetMapping
    public String getEmployees() {

        return "Employees";

    }

}
```

---

Frontend:

```javascript
fetch("http://localhost:8080/employees")
```

Works successfully.

---

# What Happens Internally?

Without:

```java
@CrossOrigin
```

Response:

```http
No Access-Control-Allow-Origin Header
```

Browser blocks request.

---

With:

```java
@CrossOrigin
```

Spring automatically adds:

```http
Access-Control-Allow-Origin: *
```

Browser allows request.

---

Flow:

```text
Browser Request
        │
        ▼
Spring Controller
        │
        ▼
@CrossOrigin
        │
        ▼
Add CORS Headers
        │
        ▼
Browser Accepts Response
```

---

# Allow Specific Origin

Most common production configuration.

---

```java
@CrossOrigin(
    origins = "http://localhost:3000"
)
```

---

Example:

```java
@RestController
@RequestMapping("/employees")
@CrossOrigin(
    origins = "http://localhost:3000"
)
public class EmployeeController {

}
```

---

Allowed:

```text
http://localhost:3000
```

---

Blocked:

```text
http://localhost:4200
```

---

# Multiple Origins

---

```java
@CrossOrigin(
    origins = {
        "http://localhost:3000",
        "http://localhost:4200"
    }
)
```

---

Allowed:

```text
React App
Angular App
```

---

# Method-Level Usage

Can be applied to specific endpoints.

---

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @CrossOrigin(
        origins = "http://localhost:3000"
    )
    @GetMapping
    public List<Employee> getEmployees() {

        return List.of();

    }

}
```

---

Only this endpoint supports CORS.

---

# Class-Level Usage

---

```java
@RestController
@CrossOrigin(
    origins = "http://localhost:3000"
)
public class EmployeeController {

}
```

---

All endpoints support CORS.

---

# Allow Specific HTTP Methods

---

```java
@CrossOrigin(
    methods = {
        RequestMethod.GET,
        RequestMethod.POST
    }
)
```

---

Allowed:

```http
GET
POST
```

---

Blocked:

```http
PUT
DELETE
```

---

# Example

```java
@CrossOrigin(
    origins = "http://localhost:3000",
    methods = {
        RequestMethod.GET,
        RequestMethod.POST
    }
)
```

---

# Allow Specific Headers

---

```java
@CrossOrigin(
    allowedHeaders = {
        "Authorization",
        "Content-Type"
    }
)
```

---

Allowed:

```http
Authorization
Content-Type
```

headers.

---

# Expose Headers

Normally browser hides many response headers.

---

```java
@CrossOrigin(
    exposedHeaders = {
        "Authorization"
    }
)
```

---

Frontend can access:

```javascript
response.headers.get("Authorization")
```

---

# Allow Credentials

Used when:

```text
Cookies
Sessions
JWT Headers
```

are involved.

---

```java
@CrossOrigin(
    allowCredentials = "true"
)
```

---

Spring adds:

```http
Access-Control-Allow-Credentials: true
```

---

# Important Rule

Wrong:

```java
@CrossOrigin(
    origins="*",
    allowCredentials="true"
)
```

---

Not allowed by CORS specification.

---

Correct:

```java
@CrossOrigin(
    origins="http://localhost:3000",
    allowCredentials="true"
)
```

---

# maxAge

Controls browser caching of CORS configuration.

---

```java
@CrossOrigin(
    maxAge = 3600
)
```

---

Meaning:

```text
Cache CORS Settings
For 1 Hour
```

---

Reduces preflight requests.

---

# Complete Example

```java
@RestController
@RequestMapping("/employees")
@CrossOrigin(
    origins = "http://localhost:3000",
    methods = {
        RequestMethod.GET,
        RequestMethod.POST,
        RequestMethod.PUT,
        RequestMethod.DELETE
    },
    allowedHeaders = "*",
    allowCredentials = "true",
    maxAge = 3600
)
public class EmployeeController {

}
```

---

# Preflight Requests

Very Important Interview Topic.

Before certain requests, browser sends:

```http
OPTIONS
```

request.

Called:

```text
Preflight Request
```

---

Example:

```http
OPTIONS /employees
```

---

Browser asks:

```text
Can I send PUT request?
Can I send Authorization header?
```

---

Spring responds:

```http
Access-Control-Allow-Origin
Access-Control-Allow-Methods
Access-Control-Allow-Headers
```

---

Then actual request is sent.

---

Flow:

```text
Browser
   │
   ▼
OPTIONS Request
   │
   ▼
Spring Response
   │
   ▼
Actual GET/POST/PUT Request
```

---

# Global CORS Configuration

Instead of using:

```java
@CrossOrigin
```

everywhere.

---

Use:

```java
@Configuration
public class CorsConfig
        implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(
            CorsRegistry registry) {

        registry.addMapping("/**")
                .allowedOrigins(
                    "http://localhost:3000"
                )
                .allowedMethods("*");

    }

}
```

---

Applies globally.

---

# Spring Security + CORS

Very Important.

If using:

```java
Spring Security
```

you must enable CORS.

---

```java
@Bean
SecurityFilterChain security(
        HttpSecurity http)
        throws Exception {

    http.cors();

    return http.build();
}
```

---

Otherwise:

```java
@CrossOrigin
```

may not work properly.

---

# @CrossOrigin vs CORS Filter

## @CrossOrigin

Applied:

```text
Controller Level
Method Level
```

---

## CORS Filter

Applied:

```text
Application Level
```

---

Large applications generally use:

```text
Global CORS Configuration
```

---

# Common Mistakes

## Mistake 1

Using Wildcard in Production

---

```java
@CrossOrigin(origins="*")
```

---

Security risk.

---

Prefer:

```java
@CrossOrigin(
    origins="https://mycompany.com"
)
```

---

# Mistake 2

Using Credentials with *

---

Wrong:

```java
origins="*"
allowCredentials="true"
```

---

Fails.

---

# Mistake 3

Forgetting Spring Security CORS

---

```text
CORS Configured
But Security Blocks Request
```

---

Common issue.

---

# Mistake 4

Thinking CORS Is Server Security

CORS protects:

```text
Browser Clients
```

---

Not:

```text
Postman
Curl
Backend Servers
```

---

# Common Interview Questions

## What is CORS?

Cross-Origin Resource Sharing.

Allows browser requests across different origins.

---

## Why is @CrossOrigin used?

To enable cross-origin requests.

---

## What is an Origin?

Combination of:

```text
Protocol + Domain + Port
```

---

## What is Same-Origin Policy?

Browser security mechanism that blocks requests to different origins.

---

## What is a Preflight Request?

An:

```http
OPTIONS
```

request sent before actual requests.

---

## Can @CrossOrigin be applied on methods?

Yes.

---

## Can it be applied on controllers?

Yes.

---

## How do you allow credentials?

```java
@CrossOrigin(
    allowCredentials = "true"
)
```

---

## Difference Between Global CORS and @CrossOrigin?

`@CrossOrigin`

```text
Controller Specific
```

---

Global Configuration

```text
Application Wide
```

---

# Real-World Use Cases

## React + Spring Boot

```text
React
http://localhost:3000

Spring Boot
http://localhost:8080
```

---

## Angular + Spring Boot

```text
Angular
http://localhost:4200

Spring Boot
http://localhost:8080
```

---

## Mobile Frontend + Backend API

```text
Flutter
React Native
iOS
Android
```

calling backend APIs.

---

## Microservices UI Gateway

```text
Frontend Domain
       ↓
Spring Boot APIs
```

Requires proper CORS configuration.

---

# Internal Flow Diagram

```text
Browser
    │
    ▼
Cross-Origin Request
    │
    ▼
Spring Controller
    │
    ▼
@CrossOrigin
    │
    ▼
Add CORS Headers
    │
    ▼
Browser Validation
    │
    ▼
Request Allowed
```

---

# Key Points To Remember

- `@CrossOrigin` enables Cross-Origin Resource Sharing (CORS).
- Used when frontend and backend run on different origins.
- Supports specific origins, methods, headers, and credentials.
- Can be applied at class level or method level.
- Handles browser preflight (`OPTIONS`) requests.
- Frequently used with React, Angular, Vue, and mobile frontends.
- Global CORS configuration is preferred for large applications.
- Must be integrated properly with Spring Security.
- CORS is a browser security feature, not server-side authorization.
- One of the most important annotations for modern Spring Boot REST APIs.