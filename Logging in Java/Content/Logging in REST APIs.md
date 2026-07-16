# Logging in REST APIs

> **Goal:** Understand REST API Logging, Request Logging, Response Logging, Headers Logging, Payload Logging, implementation using Filters, Interceptors, and AOP, production best practices, security considerations, and interview concepts.

---

# 1. Introduction to Logging in REST APIs

REST APIs are the backbone of modern applications.

Examples:

```text
Banking APIs

E-Commerce APIs

Payment APIs

Healthcare APIs

Microservices APIs
```

---

Every API request generates valuable information.

Examples:

```text
Who Called The API?

Which Endpoint Was Called?

What Data Was Sent?

What Response Was Returned?

How Long Did It Take?
```

---

Without logging:

```text
Difficult Debugging

Poor Monitoring

No Audit Trail

Slow Issue Resolution
```

---

# Example API Request

```http
POST /employees
```

---

Request Body:

```json
{
  "name":"John",
  "department":"IT"
}
```

---

Response:

```json
{
  "id":101,
  "status":"CREATED"
}
```

---

Proper logging captures:

```text
Request

Response

Headers

Execution Time

Errors
```

---

# Why REST API Logging Is Important?

Provides:

```text
Monitoring

Debugging

Audit Trails

Security Tracking

Performance Analysis
```

---

# Logging Flow

```text
Client Request

      ↓

REST API

      ↓

Request Log

      ↓

Business Logic

      ↓

Response Log

      ↓

Client Response
```

---

# 2. Request Logging

One of the most common logging requirements.

---

## What is Request Logging?

Logging details of incoming API requests.

---

# Information Typically Logged

```text
HTTP Method

URL

Query Parameters

Headers

Request Body

Request Time

Correlation ID
```

---

# Example Request

```http
POST /employees
```

---

Request Body:

```json
{
  "name":"John"
}
```

---

Logged Information:

```text
POST /employees

Request Received

Body:
{
  "name":"John"
}
```

---

# Why Request Logging?

Helps answer:

```text
Who Called The API?

What Data Was Sent?

When Was It Called?
```

---

# Example

```java
log.info(
    "Incoming Request: {} {}",
    request.getMethod(),
    request.getRequestURI()
);
```

---

Output:

```text
Incoming Request:
POST /employees
```

---

# Benefits

```text
Debugging

Monitoring

Audit Tracking
```

---

# Request Logging Flow

```text
Client

   ↓

Request

   ↓

Request Logger

   ↓

Controller
```

---

# 3. Response Logging

Another important production requirement.

---

## What is Response Logging?

Logging information returned by the API.

---

# Information Typically Logged

```text
HTTP Status

Response Body

Response Time

Response Headers
```

---

# Example

Response:

```json
{
  "id":101,
  "status":"CREATED"
}
```

---

Log:

```text
Response Status: 201

Response Body:
{
  "id":101,
  "status":"CREATED"
}
```

---

# Why Response Logging?

Helps determine:

```text
What Was Returned?

Did Request Succeed?

Was Response Correct?
```

---

# Example

```java
log.info(
    "Response Status: {}",
    response.getStatus()
);
```

---

Output:

```text
Response Status: 201
```

---

# Benefits

```text
Validation

Troubleshooting

Audit Trail
```

---

# Response Logging Flow

```text
Controller

    ↓

Response

    ↓

Response Logger

    ↓

Client
```

---

# 4. Headers Logging

Very important in enterprise applications.

---

## What are HTTP Headers?

Metadata associated with requests and responses.

---

# Examples

```http
Authorization

Content-Type

Accept

User-Agent

X-Correlation-ID
```

---

# Example Request

```http
POST /employees

Authorization: Bearer token

Content-Type: application/json

X-Correlation-ID: CORR-12345
```

---

# Logging Headers

```java
log.info(
    "Correlation ID: {}",
    request.getHeader(
        "X-Correlation-ID"
    )
);
```

---

Output:

```text
Correlation ID:
CORR-12345
```

---

# Why Log Headers?

Useful for:

```text
Tracing

Authentication

Security

Debugging
```

---

# Commonly Logged Headers

```text
X-Correlation-ID

Content-Type

User-Agent
```

---

# Sensitive Headers

Avoid logging:

```text
Authorization

JWT Tokens

API Keys

Passwords
```

---

# Good Practice

Mask sensitive values.

---

Example:

```text
Authorization=******
```

---

# 5. Payload Logging

Advanced but extremely useful.

---

## What is Payload Logging?

Logging:

```text
Request Body

Response Body
```

---

# Example Request Payload

```json
{
  "name":"John",
  "department":"IT"
}
```

---

# Logged Payload

```text
Request Payload:

{
  "name":"John",
  "department":"IT"
}
```

---

# Example Response Payload

```json
{
  "id":101,
  "status":"CREATED"
}
```

---

# Logged Response

```text
Response Payload:

{
  "id":101,
  "status":"CREATED"
}
```

---

# Benefits

```text
Debugging

Issue Reproduction

Audit Trail
```

---

# Risks

Payload may contain:

```text
Passwords

Credit Cards

Personal Data
```

---

# Production Recommendation

Mask:

```text
Sensitive Information
```

---

Example

Instead of:

```json
{
  "password":"secret123"
}
```

Log:

```json
{
  "password":"******"
}
```

---

# 6. Logging Using Filters

Most common production implementation.

---

## What is a Filter?

A Servlet component that executes:

```text
Before Controller

After Controller
```

---

# Request Flow

```text
Client

   ↓

Filter

   ↓

Controller

   ↓

Response

   ↓

Filter
```

---

# Why Use Filters?

Can log:

```text
Request

Response

Headers

Execution Time
```

---

# Example

```java
@Component
public class RequestLoggingFilter
        implements Filter {

}
```

---

# Typical Flow

```text
Request Arrives

      ↓

Log Request

      ↓

Controller

      ↓

Log Response

      ↓

Return Response
```

---

# Advantages

```text
Centralized Logging

Runs Early

Framework Independent
```

---

# Common Use Cases

```text
Request Logging

Response Logging

Correlation IDs

Execution Time Logging
```

---

# 7. Logging Using Interceptors

Spring MVC specific approach.

---

## What is an Interceptor?

A component that intercepts requests before and after controller execution.

---

# Request Flow

```text
Request

   ↓

Interceptor

   ↓

Controller

   ↓

Interceptor

   ↓

Response
```

---

# Key Methods

---

## preHandle()

Executed before controller.

---

Used for:

```text
Request Logging

Authentication

Correlation IDs
```

---

## postHandle()

Executed after controller.

---

Used for:

```text
Response Logging
```

---

## afterCompletion()

Executed after request completion.

---

Used for:

```text
Cleanup

Execution Time Logging
```

---

# Example

```java
@Component
public class LoggingInterceptor
        implements HandlerInterceptor {

}
```

---

# Advantages

```text
Spring Integration

Controller Awareness

Flexible Logging
```

---

# Limitations

Works only for:

```text
Spring MVC Requests
```

---

# 8. Logging Using AOP

Advanced logging technique.

---

## What is AOP?

AOP stands for:

```text
Aspect Oriented Programming
```

---

Used for:

```text
Cross-Cutting Concerns
```

such as:

```text
Logging

Security

Auditing

Transactions
```

---

# Why Use AOP?

Avoids repetitive logging code.

---

Instead of:

```java
log.info(...);
```

inside every method,

use:

```java
@Aspect
```

---

# Example

```java
@Aspect
@Component
public class LoggingAspect {

}
```

---

# AOP Logging Flow

```text
Controller Method

      ↓

Aspect

      ↓

Log Request

      ↓

Method Execution

      ↓

Log Response
```

---

# Common AOP Annotations

---

## @Before

Before method execution.

---

## @After

After method execution.

---

## @Around

Before and after execution.

---

Most commonly used.

---

# Example

```java
@Around(
    "execution(* com.company..*(..))"
)
```

---

# Advantages

```text
Centralized Logging

Reusable

Less Boilerplate
```

---

# Limitations

Less suitable for:

```text
Raw HTTP Request Logging
```

compared to Filters.

---

# 9. Filter vs Interceptor vs AOP

Very common interview question.

---

| Feature | Filter | Interceptor | AOP |
|----------|----------|------------|-----|
| Servlet Level | Yes | No | No |
| Spring Specific | No | Yes | Yes |
| Request Logging | Excellent | Good | Limited |
| Response Logging | Excellent | Good | Good |
| Header Logging | Excellent | Good | Limited |
| Payload Logging | Excellent | Moderate | Limited |
| Method Logging | No | Limited | Excellent |
| Correlation ID | Excellent | Good | Poor |

---

# When to Use What?

---

## Filter

Best for:

```text
Request Logging

Response Logging

Header Logging

Correlation IDs
```

---

## Interceptor

Best for:

```text
Spring MVC Request Processing

Authentication

Execution Time Logging
```

---

## AOP

Best for:

```text
Service Layer Logging

Method Entry/Exit

Business Logging
```

---

# 10. Logging Execution Time

Common production requirement.

---

Example:

```text
Request Started

10:00:00
```

---

Response Returned:

```text
10:00:02
```

---

Execution Time:

```text
2000 ms
```

---

Log:

```text
Request Completed
Execution Time: 2000 ms
```

---

Benefits:

```text
Performance Monitoring

Slow API Detection
```

---

# 11. Best Practices

---

## Log Requests

Useful for:

```text
Audit

Debugging
```

---

## Log Responses

Useful for:

```text
Validation

Troubleshooting
```

---

## Log Correlation IDs

Recommended:

```text
Every Request
```

---

## Mask Sensitive Data

Never log:

```text
Passwords

Tokens

Credit Cards

API Keys
```

---

## Log Execution Time

Helps identify:

```text
Performance Issues
```

---

## Avoid Excessive Payload Logging

Large payloads may:

```text
Increase Storage

Reduce Performance
```

---

# 12. Common Mistakes

---

## Logging Passwords

Bad:

```json
{
  "password":"secret"
}
```

---

## Logging JWT Tokens

Bad:

```text
Authorization:
Bearer eyJ...
```

---

## Logging Huge Payloads

Problem:

```text
Large Log Files
```

---

## Duplicate Logging

Controller + Filter + Aspect.

---

Result:

```text
Repeated Logs
```

---

## Logging Sensitive Customer Data

Can violate:

```text
Security Policies

Compliance Requirements
```

---

# 13. Interview Questions

### Q1. What is Request Logging?

Logging details of incoming API requests.

---

### Q2. What is Response Logging?

Logging information returned by an API.

---

### Q3. Why log HTTP Headers?

For:

```text
Tracing

Debugging

Authentication Analysis
```

---

### Q4. What is Payload Logging?

Logging request and response bodies.

---

### Q5. What are risks of Payload Logging?

```text
Sensitive Data Exposure

Performance Impact
```

---

### Q6. Which component is best for Request Logging?

```text
Filter
```

---

### Q7. Which component is best for Controller-Level Logging?

```text
Interceptor
```

or

```text
AOP
```

depending on requirement.

---

### Q8. What is AOP Logging?

Logging implemented using aspects instead of manually writing logging code.

---

### Q9. What is the difference between Filter and Interceptor?

```text
Filter

→ Servlet Layer

Interceptor

→ Spring MVC Layer
```

---

### Q10. What should never be logged?

```text
Passwords

Tokens

API Keys

Credit Card Information
```

---

# Quick Revision

```text
Logging in REST APIs
--------------------

What To Log?

✔ Request
✔ Response
✔ Headers
✔ Payload
✔ Execution Time
✔ Correlation ID

--------------------------------

Request Logging

HTTP Method
URL
Headers
Body

--------------------------------

Response Logging

Status Code
Response Body
Execution Time

--------------------------------

Headers Logging

Useful Headers

✔ Content-Type
✔ User-Agent
✔ X-Correlation-ID

Avoid

✘ Authorization
✘ Tokens

--------------------------------

Payload Logging

Request Body
Response Body

Mask Sensitive Data

--------------------------------

Implementation Options

1. Filter

Best For

✔ Request Logging
✔ Response Logging
✔ Header Logging
✔ Correlation IDs

--------------------------------

2. Interceptor

Best For

✔ Spring MVC Requests
✔ Authentication
✔ Timing

--------------------------------

3. AOP

Best For

✔ Service Logging
✔ Method Logging
✔ Business Events

--------------------------------

Best Practices

✔ Log Requests
✔ Log Responses
✔ Log Correlation IDs
✔ Log Execution Time
✔ Mask Sensitive Data

--------------------------------

Never Log

✘ Passwords
✘ JWT Tokens
✘ API Keys
✘ Credit Card Data

--------------------------------

Remember

Filter
    ↓
HTTP Request/Response Logging

Interceptor
    ↓
Spring MVC Logging

AOP
    ↓
Method-Level Logging
```