
## Definition

`@RateLimiter` is a **Resilience4j** annotation used to limit the number of requests that can execute within a specific time period.

It tells Spring:

> Allow only a configured number of calls during a time window. Reject or delay additional requests.

Package:

```java
import io.github.resilience4j.ratelimiter.annotation.RateLimiter;
```

---

# Why Do We Need @RateLimiter?

Imagine:

```text
10 Users
```

using your API.

Everything works fine.

---

Suddenly:

```text
100,000 Requests/Second
```

hit your service.

---

Without rate limiting:

```text
CPU Exhaustion
Memory Issues
Database Overload
Application Crash
```

---

Solution:

```java
@RateLimiter
```

---

# Real-Life Analogy

Think of a movie theater.

---

Theater capacity:

```text
100 Seats
```

---

When seat limit is reached:

```text
No More Entries
```

---

People must:

```text
Wait
OR
Be Rejected
```

---

That's exactly what a Rate Limiter does.

---

# Problem Without Rate Limiting

```text
Client
  ↓
Order Service
  ↓
Database
```

---

Traffic Spike:

```text
50,000 Requests
```

---

Result:

```text
Database Overloaded
Slow Response
System Failure
```

---

# Solution

```java
@RateLimiter
```

---

Example:

```text
Allow 100 Requests/Second
```

---

Extra requests:

```text
Rejected
Or Wait
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

    @RateLimiter(
        name = "paymentService"
    )
    public String pay() {

        return "Payment Success";
    }
}
```

---

Meaning:

```text
Apply Rate Limiting
To pay()
```

---

# Internal Workflow

```text
Request Arrives
      ↓
Rate Limit Check
      ↓

Permit Available?
      ↓

YES → Execute

NO  → Reject
```

---

# Configuration Example

application.yml

```yaml
resilience4j:
  ratelimiter:
    instances:

      paymentService:

        limit-for-period: 5

        limit-refresh-period: 10s

        timeout-duration: 2s
```

---

Meaning:

```text
5 Requests Allowed
Every 10 Seconds
```

---

Example:

```text
Request 1 → Allowed
Request 2 → Allowed
Request 3 → Allowed
Request 4 → Allowed
Request 5 → Allowed

Request 6 → Blocked
```

---

Until:

```text
10 Seconds Pass
```

---

Counter resets.

---

# Understanding Configuration

## limit-for-period

```yaml
limit-for-period: 5
```

Meaning:

```text
Maximum 5 Calls
```

during refresh period.

---

## limit-refresh-period

```yaml
limit-refresh-period: 10s
```

Meaning:

```text
Reset Counter
Every 10 Seconds
```

---

## timeout-duration

```yaml
timeout-duration: 2s
```

Meaning:

```text
Wait Up To 2 Seconds
For Permit
```

before failing.

---

# Example Timeline

Configuration:

```yaml
limit-for-period: 3

limit-refresh-period: 10s
```

---

Timeline:

```text
0 sec

Request 1 → Allowed
Request 2 → Allowed
Request 3 → Allowed

Request 4 → Rejected
Request 5 → Rejected
```

---

At:

```text
10 sec
```

counter resets.

---

Again:

```text
3 Requests Allowed
```

---

# Rate Limiter With Fallback

Most common production setup.

```java
@RateLimiter(
    name = "paymentService",
    fallbackMethod = "fallback"
)
public String pay() {

    return "Success";
}
```

---

Fallback:

```java
public String fallback(
        Exception ex) {

    return "Too Many Requests";
}
```

---

Execution:

```text
Limit Reached
      ↓
Fallback
```

---

# Example With External API

```java
@RateLimiter(
    name = "weatherApi",
    fallbackMethod = "fallback"
)
public Weather getWeather() {

    return weatherClient
            .getWeather();
}
```

---

Useful because:

```text
External APIs
Often Have Request Limits
```

---

# Example With Feign Client

```java
@RateLimiter(
    name = "payment",
    fallbackMethod = "fallback"
)
public Payment getPayment() {

    return paymentClient
           .getPayment();
}
```

---

Protects:

```text
Remote Service
```

from overload.

---

# Example With REST Endpoint

```java
@GetMapping("/pay")
@RateLimiter(
    name = "paymentLimiter",
    fallbackMethod = "fallback"
)
public String pay() {

    return "Success";
}
```

---

Traffic:

```text
10000 Requests
```

---

Limiter:

```text
100 Requests Allowed
```

---

Remaining:

```text
Rejected
```

---

# Real Production Scenario

Payment Gateway:

```text
Order Service
      ↓
Razorpay
```

---

Gateway limit:

```text
100 Requests/Second
```

---

Your application:

```java
@RateLimiter
```

ensures:

```text
Never Exceed Provider Limit
```

---

Prevents:

```text
API Ban
Throttling
Penalties
```

---

# @RateLimiter vs @Retry

Interview Favorite.

---

## Retry

Purpose:

```text
Retry Failed Request
```

---

Example:

```text
Failure
 ↓
Try Again
```

---

## RateLimiter

Purpose:

```text
Control Request Rate
```

---

Example:

```text
Too Many Requests
 ↓
Block
```

---

Comparison:

| Feature | Retry | RateLimiter |
|----------|--------|------------|
| Retries Calls | ✅ | ❌ |
| Limits Traffic | ❌ | ✅ |
| Handles Temporary Failure | ✅ | ❌ |
| Prevents Overload | ❌ | ✅ |

---

# @RateLimiter vs @CircuitBreaker

## CircuitBreaker

```text
Protects Against Failures
```

---

## RateLimiter

```text
Protects Against Excessive Traffic
```

---

Comparison:

| Feature | CircuitBreaker | RateLimiter |
|----------|---------------|------------|
| Monitors Failures | ✅ | ❌ |
| Limits Requests | ❌ | ✅ |
| Uses OPEN State | ✅ | ❌ |
| Uses Permits | ❌ | ✅ |

---

# Common Use Cases

## Public APIs

```text
REST APIs
```

---

## Payment Gateways

```text
Razorpay
Stripe
PayPal
```

---

## External Services

```text
Weather API
Maps API
SMS API
```

---

## Login Endpoints

```text
Prevent Brute Force
```

---

## Microservices

```text
Protect Downstream Services
```

---

# Common Mistakes

## Mistake 1

Very Small Limits

Wrong:

```yaml
limit-for-period: 1
```

---

Blocks legitimate traffic.

---

# Mistake 2

No Fallback

Wrong:

```java
@RateLimiter
```

without:

```java
fallbackMethod
```

---

Users receive exceptions.

---

# Mistake 3

Confusing Rate Limiter With Authentication

Rate Limiter:

```text
Controls Traffic
```

---

Authentication:

```text
Controls Access
```

---

Different concepts.

---

# Common Interview Questions

## What is @RateLimiter?

Limits number of calls during a specific period.

---

## Which Library Provides It?

```text
Resilience4j
```

---

## Why Use It?

Protect systems from overload.

---

## Important Configurations?

```text
limit-for-period
limit-refresh-period
timeout-duration
```

---

## Difference Between Retry and RateLimiter?

Retry retries failures.

RateLimiter controls traffic.

---

## Difference Between CircuitBreaker and RateLimiter?

Failure protection vs traffic protection.

---

# Enterprise Best Practice

Apply `@RateLimiter` on:

```text
Public APIs
Payment APIs
External Services
Authentication APIs
High Traffic Endpoints
```

---

Combine with:

```java
@CircuitBreaker
@Retry
@Bulkhead
@TimeLimiter
```

for full resilience.

---

# Key Points To Remember

- `@RateLimiter` is provided by Resilience4j.
- Limits requests per time window.
- Protects systems from overload.
- Uses permits internally.
- Supports fallback methods.
- Commonly used for APIs and microservices.
- Useful for third-party API quotas.
- Different from Retry and Circuit Breaker.
- Improves application stability.
- Frequently asked microservices interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@RateLimiter` | Limit Request Rate |
| `@Retry` | Retry Failed Calls |
| `@CircuitBreaker` | Stop Calling Failed Service |
| `@Bulkhead` | Isolate Resources |
| `@TimeLimiter` | Timeout Control |

---

# Interview Shortcut

```java
@RateLimiter(
    name = "payment",
    fallbackMethod = "fallback"
)
```

Meaning:

```text
Allow Only A Fixed Number
Of Requests Per Time Window

Extra Requests
      ↓
Rejected Or Delayed
```

---

# Real Production Example

```java
@Service
public class PaymentService {

    @RateLimiter(
        name = "payment",
        fallbackMethod = "fallback"
    )
    public String pay() {

        return paymentClient.pay();
    }

    public String fallback(
            Exception ex) {

        return "Too Many Requests";
    }
}
```

Execution:

```text
Allowed Requests
        ↓
Processed Normally

Limit Exceeded
        ↓
Fallback Executed
```

Result:

```text
API Protection
Controlled Traffic
System Stability
Better Availability
```

`@RateLimiter` is a Resilience4j annotation that controls the rate of incoming requests by allowing only a configured number of executions within a specified time period, protecting applications and downstream services from traffic spikes and overload.