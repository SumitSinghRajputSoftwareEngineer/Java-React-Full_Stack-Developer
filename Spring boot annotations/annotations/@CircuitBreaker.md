
## Definition

`@CircuitBreaker` is a **Resilience4j** annotation used to prevent cascading failures in distributed systems and microservices.

It tells Spring:

> Monitor calls to an external service. If too many failures occur, stop calling the service temporarily and return a fallback response.

Package:

```java
import io.github.resilience4j.circuitbreaker.annotation.CircuitBreaker;
```

---

# Why Do We Need @CircuitBreaker?

Imagine:

```text
Order Service
      ↓
Payment Service
```

---

Payment Service suddenly becomes unavailable.

Without Circuit Breaker:

```text
Request 1 → Fail
Request 2 → Fail
Request 3 → Fail
...
Thousands of Requests → Fail
```

---

Result:

```text
Thread Pool Exhaustion
Slow Response Time
System Crash
Cascading Failure
```

---

Solution:

```java
@CircuitBreaker
```

---

# Real-Life Analogy

Think of an electrical circuit breaker.

---

Normal:

```text
Electricity Flowing
```

---

Short Circuit:

```text
Circuit Breaker Trips
```

---

Electricity Stops Temporarily.

---

After some time:

```text
Circuit Tested Again
```

---

If safe:

```text
Electricity Restored
```

---

Exactly how Resilience4j Circuit Breaker works.

---

# Problem Without Circuit Breaker

```text
User
  ↓
Order Service
  ↓
Payment Service (DOWN)
```

---

Every request:

```text
Waits For Timeout
```

Example:

```text
5 Seconds Timeout
```

1000 requests:

```text
5000 Seconds Wasted
```

---

# Solution

```java
@CircuitBreaker
```

After repeated failures:

```text
Stop Calling Payment Service
```

Immediately return:

```text
Fallback Response
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

    @CircuitBreaker(
        name = "paymentService",
        fallbackMethod = "fallback"
    )
    public String processPayment() {

        return restTemplate.getForObject(
            "http://PAYMENT-SERVICE/pay",
            String.class
        );
    }

    public String fallback(
            Exception ex) {

        return "Payment Service Unavailable";
    }
}
```

---

# Execution Flow

Normal:

```text
Request
   ↓
Payment Service
   ↓
Success
```

---

Failure:

```text
Request
   ↓
Payment Service
   ↓
Exception
   ↓
Fallback Method
```

---

# Circuit Breaker States

Most Important Interview Question.

---

Resilience4j has:

```text
CLOSED
OPEN
HALF_OPEN
```

---

# 1. CLOSED State

Normal state.

```text
Requests Allowed
```

---

Example:

```text
100 Requests
95 Success
5 Failure
```

---

Circuit remains:

```text
CLOSED
```

---

Flow:

```text
Request
    ↓
External Service
```

---

# 2. OPEN State

Too many failures.

Circuit trips.

---

Example:

```text
100 Requests
60 Failures
```

---

Failure threshold exceeded.

---

Circuit becomes:

```text
OPEN
```

---

Flow:

```text
Request
    ↓
Blocked Immediately
    ↓
Fallback
```

---

No external call happens.

---

# 3. HALF_OPEN State

After waiting period.

Circuit allows:

```text
Few Trial Requests
```

---

If successful:

```text
HALF_OPEN
      ↓
CLOSED
```

---

If failures continue:

```text
HALF_OPEN
      ↓
OPEN
```

---

# State Transition

```text
          Success
CLOSED  ─────────► CLOSED

     Too Many Failures
            ▼

          OPEN

      Wait Duration
            ▼

        HALF_OPEN

Success ▼      ▼ Failure

 CLOSED         OPEN
```

---

# Example Configuration

application.yml

```yaml
resilience4j:
  circuitbreaker:
    instances:

      paymentService:

        failure-rate-threshold: 50

        wait-duration-in-open-state: 10s

        sliding-window-size: 10

        permitted-number-of-calls-in-half-open-state: 3
```

---

Meaning:

```text
50% Failure Rate
      ↓
Open Circuit

Wait 10 Seconds
      ↓
Half Open

Allow 3 Requests
      ↓
Decide Recovery
```

---

# Fallback Method Rules

Original Method:

```java
public String getPayment()
```

---

Fallback:

```java
public String fallback(
        Exception ex)
```

---

If parameters exist:

```java
public String getPayment(
        Long id)
```

Fallback:

```java
public String fallback(
        Long id,
        Exception ex)
```

---

Must match signature.

---

# Example With Feign Client

```java
@Service
public class OrderService {

    @CircuitBreaker(
      name = "payment",
      fallbackMethod = "fallback"
    )
    public Payment getPayment() {

        return paymentClient
                .getPayment();
    }

    public Payment fallback(
            Exception ex) {

        return new Payment(
            "Unavailable"
        );
    }
}
```

---

# Example With RestTemplate

```java
@CircuitBreaker(
   name="userService",
   fallbackMethod="fallback"
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

# Example With WebClient

```java
@CircuitBreaker(
    name = "inventory",
    fallbackMethod = "fallback"
)
public String inventory() {

    return webClient.get()
            .uri("/inventory")
            .retrieve()
            .bodyToMono(String.class)
            .block();
}
```

---

# Real Production Scenario

E-commerce:

```text
User
 ↓
Order Service
 ↓
Payment Service
```

---

Payment Service Down.

Without Circuit Breaker:

```text
Every Request Waits
Timeouts Occur
Threads Exhausted
```

---

With Circuit Breaker:

```text
Few Failures
      ↓
Circuit Opens
      ↓
Immediate Fallback
```

---

Application remains responsive.

---

# @CircuitBreaker vs Retry

Interview Favorite.

---

## CircuitBreaker

Purpose:

```text
Stop Calling Failed Service
```

---

Example:

```text
Service Down
Circuit Opens
```

---

## Retry

Purpose:

```text
Try Again
```

---

Example:

```text
Attempt 1 Fail
Attempt 2 Success
```

---

Comparison:

| Feature | CircuitBreaker | Retry |
|----------|---------------|--------|
| Stops Calls | ✅ | ❌ |
| Reattempts Calls | ❌ | ✅ |
| Prevents Cascading Failure | ✅ | ❌ |
| Handles Temporary Errors | ❌ | ✅ |

---

Often used together.

---

# @CircuitBreaker + @Retry

```java
@Retry(name="payment")
@CircuitBreaker(
   name="payment",
   fallbackMethod="fallback"
)
```

---

Flow:

```text
Retry 3 Times
      ↓
Still Fails
      ↓
Circuit Breaker Opens
```

---

# Common Use Cases

## External APIs

```text
Payment Gateway
```

---

## Microservices

```text
Order Service
User Service
```

---

## Third-Party Services

```text
Stripe
Razorpay
PayPal
```

---

## Database Proxy Services

```text
Cloud APIs
```

---

# Common Mistakes

## Mistake 1

Missing Fallback

Wrong:

```java
@CircuitBreaker(
    name="payment"
)
```

---

No fallback response.

---

# Mistake 2

Wrong Fallback Signature

Wrong:

```java
public String fallback()
```

---

Must match parameters.

---

# Mistake 3

Opening Circuit Too Early

Wrong:

```yaml
failure-rate-threshold: 5
```

---

Circuit opens too aggressively.

---

# Common Interview Questions

## What is Circuit Breaker Pattern?

A resilience pattern that prevents repeated calls to failing services.

---

## Which Library Provides @CircuitBreaker?

```text
Resilience4j
```

---

## Circuit Breaker States?

```text
CLOSED
OPEN
HALF_OPEN
```

---

## Why Use It?

Prevent cascading failures.

---

## What Happens In OPEN State?

Requests are blocked and fallback is executed.

---

## Difference Between Retry and Circuit Breaker?

Retry retries failures.

Circuit Breaker stops calls.

---

# Enterprise Best Practice

Use `@CircuitBreaker` on:

```text
Feign Clients
RestTemplate Calls
WebClient Calls
External APIs
```

---

Combine with:

```java
@Retry
@TimeLimiter
@RateLimiter
@Bulkhead
```

for complete resilience.

---

# Key Points To Remember

- `@CircuitBreaker` is provided by Resilience4j.
- Protects applications from cascading failures.
- Has three states: CLOSED, OPEN, HALF_OPEN.
- Uses fallback methods for graceful degradation.
- Commonly used in microservices.
- Works with Feign, RestTemplate, and WebClient.
- Often combined with Retry.
- Prevents thread exhaustion and long timeouts.
- Essential cloud-native resilience pattern.
- Frequently asked microservices interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@CircuitBreaker` | Stop Calls To Failing Service |
| `@Retry` | Retry Failed Requests |
| `@RateLimiter` | Limit Request Rate |
| `@Bulkhead` | Isolate Resources |
| `@TimeLimiter` | Timeout Long Calls |

---

# Interview Shortcut

```java
@CircuitBreaker(
    name = "payment",
    fallbackMethod = "fallback"
)
```

Meaning:

```text
If Payment Service Keeps Failing
       ↓
Stop Calling It
       ↓
Return Fallback Response
       ↓
Try Again Later
```

---

# Real Production Example

```java
@Service
public class PaymentService {

    @CircuitBreaker(
        name = "payment",
        fallbackMethod = "fallback"
    )
    public String pay() {

        return paymentClient.pay();
    }

    public String fallback(
            Exception ex) {

        return "Payment Service Temporarily Unavailable";
    }
}
```

Flow:

```text
Payment Service Healthy
        ↓
Requests Pass

Payment Service Down
        ↓
Failures Increase
        ↓
Circuit Opens
        ↓
Fallback Executed
        ↓
Application Remains Stable
```

Result:

```text
High Availability
Fault Tolerance
Better User Experience
Microservice Resilience
```

`@CircuitBreaker` is a Resilience4j annotation that implements the Circuit Breaker pattern by monitoring failures, temporarily blocking calls to unhealthy services, and providing fallback responses to prevent cascading failures in distributed systems.