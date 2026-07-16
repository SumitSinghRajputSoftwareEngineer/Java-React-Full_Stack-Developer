## Definition

`@Retry` is a **Resilience4j** annotation used to automatically retry a failed operation before declaring it as failed.

It tells Spring:

> If a method fails, try executing it again a configured number of times before giving up.

Package:

```java
import io.github.resilience4j.retry.annotation.Retry;
```

---

# Why Do We Need @Retry?

In distributed systems, many failures are temporary:

```text
Network Glitch
Temporary Timeout
Database Connection Issue
Service Startup Delay
Third-Party API Slow Response
```

---

Example:

```text
Request #1 → Fail
Request #2 → Success
```

---

Without Retry:

```text
User Sees Failure
```

---

With Retry:

```text
Automatically Try Again
```

---

Result:

```text
Request Succeeds
```

---

# Real-Life Example

Imagine calling a friend.

---

First call:

```text
Busy
```

---

Second call:

```text
Connected
```

---

Would you permanently give up after one failed attempt?

```text
No
```

---

That's exactly what Retry does.

---

# Problem Without Retry

```text
Order Service
       ↓
Payment Service
```

---

Temporary network issue:

```text
Fail
```

---

Application immediately returns:

```text
Payment Failed
```

---

Even though:

```text
Second Attempt
Would Have Worked
```

---

# Solution

```java
@Retry
```

---

# Required Dependency

Spring Boot 3+

```xml
<dependency>
    <groupId>
        io.github.resilience4j
    </groupId>
    <artifactId>
        resilience4j-spring-boot3
    </artifactId>
</dependency>
```

---

# Basic Example

```java
@Service
public class PaymentService {

    @Retry(
        name = "paymentService"
    )
    public String pay() {

        return paymentClient.pay();
    }
}
```

---

Execution:

```text
Attempt 1 → Fail
Attempt 2 → Fail
Attempt 3 → Success
```

---

User receives:

```text
Success
```

---

# Retry With Fallback

Most common usage.

```java
@Service
public class PaymentService {

    @Retry(
        name = "paymentService",
        fallbackMethod = "fallback"
    )
    public String pay() {

        return paymentClient.pay();
    }

    public String fallback(
            Exception ex) {

        return "Payment Service Down";
    }
}
```

---

Flow:

```text
Attempt 1 Fail
Attempt 2 Fail
Attempt 3 Fail
       ↓
Fallback
```

---

# Internal Workflow

```text
Method Called
      ↓
Exception?
      ↓
Retry
      ↓
Success?
      ↓
Return Result

OR

Max Attempts Reached
      ↓
Fallback
```

---

# Default Behavior

If configuration not provided:

```text
3 Attempts
```

(Default Resilience4j behavior)

---

Example:

```text
Attempt 1
Attempt 2
Attempt 3
```

---

Then:

```text
Failure
```

---

# Configuration Example

application.yml

```yaml
resilience4j:
  retry:
    instances:

      paymentService:

        max-attempts: 5

        wait-duration: 2s
```

---

Meaning:

```text
Attempt 1
Wait 2 sec

Attempt 2
Wait 2 sec

Attempt 3
Wait 2 sec

Attempt 4
Wait 2 sec

Attempt 5
```

---

# Example With RestTemplate

```java
@Retry(
    name = "userService",
    fallbackMethod = "fallback"
)
public String getUsers() {

    return restTemplate
            .getForObject(
               "http://USER-SERVICE/users",
               String.class
            );
}
```

---

Execution:

```text
Call User Service
     ↓
Failure
     ↓
Retry
```

---

# Example With Feign Client

```java
@Retry(
    name="payment",
    fallbackMethod="fallback"
)
public Payment getPayment() {

    return paymentClient
            .getPayment();
}
```

---

Flow:

```text
Feign Call
    ↓
Failure
    ↓
Retry
```

---

# Example With Database

```java
@Retry(
    name = "databaseRetry",
    fallbackMethod = "fallback"
)
public User getUser() {

    return repository.findById(1L)
                     .orElseThrow();
}
```

---

Useful for:

```text
Temporary DB Connectivity Issues
```

---

# Fallback Rules

Original:

```java
public String pay()
```

---

Fallback:

```java
public String fallback(
        Exception ex)
```

---

Original:

```java
public String pay(Long id)
```

---

Fallback:

```java
public String fallback(
        Long id,
        Exception ex)
```

---

Signature must match.

---

# Retry Specific Exceptions

Example:

```yaml
resilience4j:
  retry:
    instances:

      paymentService:

        retry-exceptions:

          - java.io.IOException

          - java.net.ConnectException
```

---

Meaning:

```text
Retry Only For These Exceptions
```

---

# Ignore Certain Exceptions

Example:

```yaml
ignore-exceptions:

  - java.lang.IllegalArgumentException
```

---

Reason:

```text
Bad Input Won't Become Valid
By Retrying
```

---

# Real Production Scenario

```text
Order Service
      ↓
Payment Gateway
```

---

Payment Gateway returns:

```text
Timeout
```

---

Retry:

```text
Attempt 1 Fail

Attempt 2 Success
```

---

Customer sees:

```text
Successful Payment
```

instead of:

```text
Payment Failed
```

---

# @Retry vs @CircuitBreaker

Most Important Interview Question.

---

## Retry

Purpose:

```text
Try Again
```

---

Example:

```text
Temporary Failure
```

---

Flow:

```text
Fail
 ↓
Retry
 ↓
Success
```

---

## CircuitBreaker

Purpose:

```text
Stop Calling Failed Service
```

---

Flow:

```text
Repeated Failures
 ↓
Open Circuit
 ↓
No More Calls
```

---

Comparison:

| Feature | Retry | CircuitBreaker |
|----------|--------|---------------|
| Reattempt Request | ✅ | ❌ |
| Stops Calls | ❌ | ✅ |
| Handles Temporary Failures | ✅ | ❌ |
| Prevents Cascading Failures | ❌ | ✅ |

---

# Using Retry + Circuit Breaker Together

Very common.

```java
@Retry(name = "payment")
@CircuitBreaker(
    name = "payment",
    fallbackMethod = "fallback"
)
```

---

Flow:

```text
Call Service
      ↓
Fail

Retry 3 Times
      ↓
Still Fail
      ↓
Circuit Breaker Opens
      ↓
Fallback
```

---

# Retry + Backoff

Example:

```yaml
max-attempts: 5

wait-duration: 1s
```

---

Execution:

```text
Attempt 1

Wait 1s

Attempt 2

Wait 1s

Attempt 3
```

---

Prevents:

```text
Immediate Retry Storm
```

---

# Common Use Cases

## External APIs

```text
Stripe
Razorpay
PayPal
```

---

## Service-to-Service Calls

```text
Order → Payment
```

---

## Database Connections

```text
Temporary Outages
```

---

## Cloud Services

```text
AWS APIs
Azure APIs
```

---

## Network Timeouts

```text
Transient Failures
```

---

# Common Mistakes

## Mistake 1

Retrying Validation Errors

Wrong:

```text
Invalid Email
```

Retry won't help.

---

# Mistake 2

Too Many Retries

Wrong:

```yaml
max-attempts: 100
```

---

Can overload system.

---

# Mistake 3

No Backoff

Wrong:

```yaml
wait-duration: 0
```

---

Creates retry storms.

---

# Common Interview Questions

## What is @Retry?

Automatically retries failed operations.

---

## Which Library Provides It?

```text
Resilience4j
```

---

## Why Use Retry?

To recover from temporary failures.

---

## Default Attempts?

Usually:

```text
3 Attempts
```

---

## Difference Between Retry and Circuit Breaker?

Retry retries failures.

Circuit Breaker stops calls.

---

## When Not To Retry?

```text
Validation Errors
Business Exceptions
Bad Requests
```

---

# Enterprise Best Practice

Use Retry for:

```text
Network Errors
Timeouts
Temporary Outages
Cloud APIs
```

---

Do NOT Retry:

```text
Validation Failures
Illegal Arguments
Authentication Failures
```

---

Combine with:

```java
@CircuitBreaker
@TimeLimiter
@RateLimiter
@Bulkhead
```

---

# Key Points To Remember

- `@Retry` is provided by Resilience4j.
- Automatically retries failed operations.
- Useful for temporary failures.
- Supports fallback methods.
- Works with Feign, RestTemplate, WebClient.
- Configurable using YAML.
- Can retry specific exceptions only.
- Often used with Circuit Breaker.
- Improves system resiliency.
- Frequently asked microservices interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Retry` | Retry Failed Calls |
| `@CircuitBreaker` | Stop Calling Failed Service |
| `@RateLimiter` | Limit Requests |
| `@Bulkhead` | Resource Isolation |
| `@TimeLimiter` | Timeout Control |

---

# Interview Shortcut

```java
@Retry(
    name = "payment",
    fallbackMethod = "fallback"
)
```

Meaning:

```text
If Payment Service Fails
       ↓
Try Again Automatically
       ↓
If Still Fails
       ↓
Execute Fallback
```

---

# Real Production Example

```java
@Service
public class PaymentService {

    @Retry(
        name = "payment",
        fallbackMethod = "fallback"
    )
    public String processPayment() {

        return paymentClient.pay();
    }

    public String fallback(
            Exception ex) {

        return "Payment Temporarily Unavailable";
    }
}
```

Execution:

```text
Attempt 1 → Timeout

Attempt 2 → Timeout

Attempt 3 → Success
```

Result:

```text
Better User Experience
Automatic Recovery
Fewer Transient Failures
Higher Availability
```

`@Retry` is a Resilience4j annotation that automatically re-executes failed operations a configurable number of times, helping applications recover from temporary failures such as network issues, timeouts, and transient service outages.