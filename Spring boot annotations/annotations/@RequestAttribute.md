
## Definition

`@RequestAttribute` is a Spring MVC annotation used to access attributes that are already stored in the current **HttpServletRequest** object.

It tells Spring:

> Retrieve an attribute from the request scope and inject it into the controller method parameter.

Package:

```java
import org.springframework.web.bind.annotation.RequestAttribute;
```

---

# What is a Request Attribute?

Before understanding `@RequestAttribute`, understand that there are three different things:

| Type | Example |
|--------|---------|
| Path Variable | `/employees/101` |
| Request Parameter | `?id=101` |
| Request Attribute | Stored internally in request object |

---

Request Attribute is NOT sent directly by the client.

Instead:

```java
request.setAttribute(...)
```

stores data inside the current request.

---

Example:

```java
request.setAttribute("userId", 101L);
```

Later:

```java
@RequestAttribute("userId")
Long userId
```

retrieves it.

---

# Why Do We Need @RequestAttribute?

Imagine a request passes through:

```text
Client
  ↓
Filter
  ↓
Interceptor
  ↓
Controller
```

---

A Filter or Interceptor may compute some data.

Example:

```text
Authenticated User
JWT Claims
Tenant Information
Correlation ID
Request Metadata
```

---

Instead of recomputing it inside controller:

```java
request.setAttribute(...)
```

stores it.

---

Controller accesses it using:

```java
@RequestAttribute
```

---

# What Problem Does It Solve?

Suppose JWT token is validated in an interceptor.

Interceptor extracts:

```text
User ID
```

and stores:

```java
request.setAttribute("userId", 101L);
```

---

Controller can directly access:

```java
@RequestAttribute("userId")
Long userId
```

without parsing JWT again.

---

# Basic Syntax

```java
@GetMapping("/employees")
public String getEmployee(

        @RequestAttribute("userId")
        Long userId) {

    return "User : " + userId;

}
```

---

# How Request Attributes Are Created?

Usually inside:

```java
Filter
Interceptor
Servlet
Controller
```

---

Example:

```java
request.setAttribute(
        "userId",
        101L
);
```

---

Later:

```java
@RequestAttribute("userId")
Long userId
```

retrieves it.

---

# What Happens Internally?

Request Flow:

```text
HTTP Request
      │
      ▼
Filter / Interceptor
      │
      ▼
request.setAttribute()
      │
      ▼
DispatcherServlet
      │
      ▼
RequestAttributeMethodArgumentResolver
      │
      ▼
Controller Method
```

---

# Simple Example

## Interceptor

```java
@Component
public class UserInterceptor
        implements HandlerInterceptor {

    @Override
    public boolean preHandle(
            HttpServletRequest request,
            HttpServletResponse response,
            Object handler) {

        request.setAttribute(
                "userId",
                101L
        );

        return true;
    }

}
```

---

## Controller

```java
@RestController
public class EmployeeController {

    @GetMapping("/employees")
    public String employee(

            @RequestAttribute("userId")
            Long userId) {

        return "User Id : " + userId;

    }

}
```

---

Response:

```text
User Id : 101
```

---

# Request Lifecycle

Important Concept.

Request Attribute exists only during:

```text
Current HTTP Request
```

---

Flow:

```text
Request Starts
      │
      ▼
Attribute Stored
      │
      ▼
Controller Uses It
      │
      ▼
Request Ends
      │
      ▼
Attribute Destroyed
```

---

Unlike Session Attributes:

```text
Not Shared Across Requests
```

---

# Named Request Attribute

---

```java
@RequestAttribute("userId")
Long id
```

---

Maps:

```text
userId
```

to:

```java
id
```

---

# Parameter Name Matching

Possible.

---

```java
@RequestAttribute
Long userId
```

---

If attribute name:

```java
"userId"
```

matches parameter name:

```java
userId
```

Spring can resolve it.

---

Recommended:

```java
@RequestAttribute("userId")
```

for clarity.

---

# Optional Request Attribute

By default:

```java
@RequestAttribute
```

is required.

---

Controller:

```java
@GetMapping("/test")
public String test(

        @RequestAttribute("userId")
        Long userId) {

    return userId.toString();

}
```

---

If attribute doesn't exist:

```java
ServletRequestBindingException
```

occurs.

---

# Making It Optional

---

```java
@GetMapping("/test")
public String test(

        @RequestAttribute(
            value = "userId",
            required = false
        )
        Long userId) {

    return String.valueOf(userId);

}
```

---

Now:

```java
null
```

is injected if absent.

---

# Storing Complex Objects

Very common.

---

Interceptor:

```java
User user =
        new User(1L, "John");

request.setAttribute(
        "loggedInUser",
        user
);
```

---

Controller:

```java
@GetMapping("/profile")
public User profile(

        @RequestAttribute(
            "loggedInUser"
        )
        User user) {

    return user;

}
```

---

Spring injects:

```java
User Object
```

directly.

---

# JWT Authentication Example

Real Production Scenario.

---

Interceptor:

```java
String token =
        request.getHeader(
            "Authorization"
        );

Long userId =
        jwtUtil.extractUserId(token);

request.setAttribute(
        "userId",
        userId
);
```

---

Controller:

```java
@GetMapping("/profile")
public Profile profile(

        @RequestAttribute
        Long userId) {

    return service.profile(userId);

}
```

---

No need to parse JWT again.

---

# Multi-Tenant Example

Very Common.

---

Interceptor:

```java
request.setAttribute(
        "tenantId",
        "TENANT_1"
);
```

---

Controller:

```java
@GetMapping("/employees")
public List<Employee> employees(

        @RequestAttribute
        String tenantId) {

    return service.findAll(
            tenantId);

}
```

---

# Correlation ID Example

Microservices often use:

```text
X-Correlation-Id
```

---

Filter:

```java
request.setAttribute(
        "correlationId",
        UUID.randomUUID()
);
```

---

Controller:

```java
@GetMapping("/employees")
public String employees(

        @RequestAttribute
        UUID correlationId) {

    return correlationId.toString();

}
```

---

# RequestAttribute vs RequestParam

Very Important Interview Question.

---

## RequestParam

Comes from client.

Example:

```http
/employees?id=101
```

---

Code:

```java
@RequestParam Long id
```

---

## RequestAttribute

Comes from server.

Example:

```java
request.setAttribute(
        "id",
        101L
);
```

---

Code:

```java
@RequestAttribute Long id
```

---

Comparison:

| Feature | @RequestParam | @RequestAttribute |
|----------|--------------|-------------------|
| Source | Client Request | Server Request Object |
| Query String | Yes | No |
| Internal Data | No | Yes |
| Filter/Interceptor Data | No | Yes |

---

# RequestAttribute vs RequestHeader

| Feature | @RequestHeader | @RequestAttribute |
|----------|---------------|------------------|
| Source | HTTP Header | Request Scope |
| Sent By Client | Yes | No |
| Created By Server | No | Yes |
| JWT Claims Storage | No | Yes |

---

# RequestAttribute vs SessionAttribute

| Feature | Request Attribute | Session Attribute |
|----------|------------------|-------------------|
| Scope | Single Request | Multiple Requests |
| Lifetime | Request Only | Session Lifetime |
| Memory Usage | Low | Higher |
| User Specific | Current Request | Current Session |

---

# Internal Architecture

Request attributes are resolved by:

```java
RequestAttributeMethodArgumentResolver
```

---

Flow:

```text
DispatcherServlet
       │
       ▼
RequestAttributeMethodArgumentResolver
       │
       ▼
request.getAttribute()
       │
       ▼
Controller Method
```

---

# Internal Classes

Resolver:

```java
RequestAttributeMethodArgumentResolver
```

---

Parent:

```java
AbstractNamedValueMethodArgumentResolver
```

---

Used by:

```java
RequestMappingHandlerAdapter
```

---

# Common Mistakes

## Mistake 1

Confusing with RequestParam

Wrong:

```java
@RequestAttribute Long id
```

when client sends:

```http
?id=101
```

---

Should use:

```java
@RequestParam
```

---

# Mistake 2

Attribute Not Set

Controller:

```java
@RequestAttribute("userId")
Long userId
```

---

No:

```java
request.setAttribute(...)
```

executed.

---

Exception:

```java
ServletRequestBindingException
```

---

# Mistake 3

Wrong Attribute Name

Stored:

```java
request.setAttribute(
        "userId",
        101
);
```

---

Controller:

```java
@RequestAttribute("id")
Long id
```

---

Result:

```text
Attribute Not Found
```

---

# Mistake 4

Using Request Scope for Persistent Data

Request attributes disappear after request completes.

---

Do NOT use them for:

```text
User Session Data
Shopping Cart
Login Persistence
```

---

# Common Interview Questions

## What is @RequestAttribute?

Used to retrieve values stored in request scope.

---

## Where are request attributes stored?

Inside:

```java
HttpServletRequest
```

---

## Which component resolves @RequestAttribute?

```java
RequestAttributeMethodArgumentResolver
```

---

## Who usually creates request attributes?

```text
Filters
Interceptors
Servlets
Controllers
```

---

## Is @RequestAttribute required by default?

Yes.

---

## Can it hold complex objects?

Yes.

Example:

```java
User
Employee
DTO
JWT Claims
```

---

## Difference Between RequestParam and RequestAttribute?

RequestParam:

```text
Comes From Client
```

RequestAttribute:

```text
Comes From Server
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/profile")
public class ProfileController {

    @GetMapping
    public ResponseEntity<UserProfile>
    profile(

            @RequestAttribute(
                "userId"
            )
            Long userId) {

        return ResponseEntity.ok(
                service.profile(userId));

    }

}
```

---

Interceptor:

```java
request.setAttribute(
        "userId",
        101L
);
```

---

# Internal Flow Diagram

```text
Client Request
      │
      ▼
Interceptor
      │
      ▼
request.setAttribute()
      │
      ▼
DispatcherServlet
      │
      ▼
RequestAttributeMethodArgumentResolver
      │
      ▼
request.getAttribute()
      │
      ▼
Controller Method
```

---

# Key Points To Remember

- `@RequestAttribute` retrieves values stored in request scope.
- Data must already exist inside `HttpServletRequest`.
- Commonly used with Filters and Interceptors.
- Frequently used for JWT claims, user information, tenant IDs, and correlation IDs.
- Supports primitive types and complex objects.
- Attributes live only for the duration of the current request.
- Internally resolved by `RequestAttributeMethodArgumentResolver`.
- Different from `@RequestParam`, which reads client-supplied query parameters.
- Different from `@RequestHeader`, which reads HTTP headers.
- Frequently asked in advanced Spring MVC and Spring Security interviews.