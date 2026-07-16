
## Definition

`@TimeLimiter` is a **Resilience4j** annotation used to limit how long an operation is allowed to run before it is considered failed.

It tells Spring:

> If a method does not complete within the configured time, cancel it and return a timeout error or fallback response.

Package:

```java
import io.github.resilience4j.timelimiter.annotation.TimeLimiter;
```

---

# Why Do We Need @TimeLimiter?

Imagine:

```text
Order Service
      ↓
Payment Service
```

---

Normally:

```text
Response = 500 ms
```

---

One day:

```text
Payment Service
```

becomes slow:

```text
30 Seconds
```

---

Without TimeLimiter:

```text
User Waits 30 Seconds
Threads Stay Occupied
Application Becomes Slow
```

---

Result:

```text
Bad User Experience
Resource Exhaustion
Possible System Failure
```

---

Solution:

```java
@TimeLimiter
```

---

# Real-Life Analogy

Imagine ordering coffee.

---

You decide:

```text
Wait Maximum 5 Minutes
```

---

If coffee isn't ready:

```text
Leave The Shop
```

---

You don't wait forever.

---

That's exactly what TimeLimiter does.

---

# Problem Without TimeLimiter

```text
User Request
      ↓
Order Service
      ↓
External Service
```

---

External service hangs:

```text
Waiting...
Waiting...
Waiting...
```

---

Thread remains occupied:

```text
Blocked
```

---

Thousands of such requests:

```text
Thread Pool Exhaustion
```

---

# Solution

```java
@TimeLimiter
```

---

Example:

```text
Maximum Time = 2 Seconds
```

---

If operation exceeds:

```text
Timeout
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

# Important Requirement

Unlike:

```java
@Retry
@CircuitBreaker
@RateLimiter
```

---

`@TimeLimiter` works with:

```java
CompletableFuture
```

or

```java
CompletionStage
```

---

It does NOT work directly with:

```java
String
List
Object
```

---

# Basic Example

```java
@Service
public class PaymentService {

    @TimeLimiter(
        name = "paymentService"
    )
    public CompletableFuture<String>
    pay() {

        return CompletableFuture
                .supplyAsync(() -> {

                    sleep(5000);

                    return "Success";
                });
    }
}
```

---

# Configuration Example

application.yml

```yaml
resilience4j:
  timelimiter:
    instances:

      paymentService:

        timeout-duration: 2s
```

---

Meaning:

```text
If Method Takes
More Than 2 Seconds

Fail It
```

---

# Execution Example

Method:

```java
sleep(5 seconds)
```

Configuration:

```yaml
timeout-duration: 2s
```

---

Result:

```text
Timeout Exception
```

after:

```text
2 Seconds
```

---

# Internal Workflow

```text
Method Starts
      ↓
Timer Starts
      ↓

Completed Before Timeout?
      ↓

YES → Return Result

NO  → Timeout
```

---

# TimeLimiter With Fallback

Most common production setup.

```java
@TimeLimiter(
    name = "payment",
    fallbackMethod = "fallback"
)
public CompletableFuture<String>
pay() {

    return CompletableFuture
            .supplyAsync(() -> {

                sleep(5000);

                return "Success";
            });
}
```

---

Fallback:

```java
public CompletableFuture<String>
fallback(Exception ex) {

    return CompletableFuture
            .completedFuture(
                "Payment Timeout"
            );
}
```

---

Execution:

```text
5 Second Response

Timeout Limit = 2 Seconds

Fallback Executed
```

---

# Example With External API

```java
@TimeLimiter(
    name = "weatherApi",
    fallbackMethod = "fallback"
)
public CompletableFuture<Weather>
getWeather() {

    return CompletableFuture
        .supplyAsync(() ->
             weatherClient
                 .getWeather()
        );
}
```

---

Useful when:

```text
External APIs
Become Slow
```

---

# Example With Feign Client

```java
@TimeLimiter(
    name="payment",
    fallbackMethod="fallback"
)
public CompletableFuture<Payment>
getPayment() {

    return CompletableFuture
            .supplyAsync(
                 () -> paymentClient
                        .getPayment()
            );
}
```

---

Protects:

```text
Order Service
```

from waiting forever.

---

# Example With WebClient

```java
@TimeLimiter(
    name="inventory",
    fallbackMethod="fallback"
)
public CompletableFuture<String>
inventory() {

    return webClient.get()
            .uri("/inventory")
            .retrieve()
            .bodyToMono(String.class)
            .toFuture();
}
```

---

Very common in reactive applications.

---

# Real Production Scenario

```text
Order Service
      ↓
Payment Gateway
```

---

Gateway issue:

```text
Response Time = 45 Seconds
```

---

Without TimeLimiter:

```text
User Waits 45 Seconds
```

---

With TimeLimiter:

```text
Timeout After 3 Seconds
```

---

User gets:

```text
Payment Service Unavailable
```

instead of waiting forever.

---

# TimeLimiter vs CircuitBreaker

Interview Favorite.

---

## TimeLimiter

Purpose:

```text
Control Execution Time
```

---

Example:

```text
Timeout After 2 Seconds
```

---

## CircuitBreaker

Purpose:

```text
Stop Calling Failed Service
```

---

Example:

```text
50% Failures
Circuit Opens
```

---

Comparison:

| Feature | TimeLimiter | CircuitBreaker |
|----------|------------|---------------|
| Timeout Protection | ✅ | ❌ |
| Failure Monitoring | ❌ | ✅ |
| OPEN State | ❌ | ✅ |
| Prevent Long Waits | ✅ | ❌ |

---

# TimeLimiter vs Retry

## Retry

```text
Try Again
```

---

## TimeLimiter

```text
Stop Waiting
```

---

Comparison:

| Feature | Retry | TimeLimiter |
|----------|--------|------------|
| Retry Requests | ✅ | ❌ |
| Timeout Control | ❌ | ✅ |
| Handles Temporary Failure | ✅ | ❌ |
| Prevents Long Waits | ❌ | ✅ |

---

# TimeLimiter + CircuitBreaker

Very common.

```java
@TimeLimiter(
    name = "payment"
)

@CircuitBreaker(
    name = "payment",
    fallbackMethod = "fallback"
)
```

---

Flow:

```text
Service Slow
      ↓
TimeLimiter Timeout
      ↓
Failure Recorded
      ↓
Circuit Breaker Opens
```

---

# TimeLimiter + Retry

```java
@Retry(name="payment")

@TimeLimiter(
   name="payment"
)
```

---

Flow:

```text
Attempt 1 Timeout

Retry

Attempt 2 Timeout

Retry

Attempt 3 Success
```

---

# Configuration Options

```yaml
timeout-duration: 3s

cancel-running-future: true
```

---

Meaning:

```text
Timeout After 3 Seconds

Cancel Background Task
```

---

# Common Use Cases

## Payment Gateways

```text
Razorpay
Stripe
PayPal
```

---

## External APIs

```text
Weather
Maps
SMS
```

---

## Slow Databases

```text
Long Running Queries
```

---

## Microservices

```text
Order → Payment
```

---

## Reactive Applications

```text
WebClient
CompletableFuture
```

---

# Common Mistakes

## Mistake 1

Returning String

Wrong:

```java
@TimeLimiter
public String getData()
```

---

Correct:

```java
public CompletableFuture<String>
```

---

# Mistake 2

Very Small Timeout

Wrong:

```yaml
timeout-duration: 100ms
```

---

Creates unnecessary failures.

---

# Mistake 3

No Fallback

Users receive timeout exception.

---

# Common Interview Questions

## What is @TimeLimiter?

Limits maximum execution time of a method.

---

## Which Library Provides It?

```text
Resilience4j
```

---

## Why Use It?

Prevent long-running operations from blocking threads.

---

## Works With Which Return Types?

```java
CompletableFuture
CompletionStage
```

---

## Difference Between TimeLimiter and CircuitBreaker?

Timeout protection vs failure protection.

---

## Difference Between TimeLimiter and Retry?

Stop waiting vs retrying.

---

# Enterprise Best Practice

Apply `@TimeLimiter` on:

```text
Feign Clients
WebClient Calls
External APIs
Database Calls
Remote Services
```

---

Combine with:

```java
@Retry
@CircuitBreaker
@Bulkhead
@RateLimiter
```

for complete resiliency.

---

# Key Points To Remember

- `@TimeLimiter` is provided by Resilience4j.
- Controls maximum execution time.
- Works with `CompletableFuture`.
- Prevents long waits and thread exhaustion.
- Supports fallback methods.
- Often used with Circuit Breaker and Retry.
- Essential for microservices and cloud-native systems.
- Improves responsiveness.
- Protects against hanging services.
- Frequently asked microservices interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@TimeLimiter` | Timeout Control |
| `@Retry` | Retry Failures |
| `@CircuitBreaker` | Stop Failed Calls |
| `@Bulkhead` | Resource Isolation |
| `@RateLimiter` | Traffic Control |

---

# Interview Shortcut

```java
@TimeLimiter(
    name = "payment",
    fallbackMethod = "fallback"
)
```

Meaning:

```text
If Payment Service
Takes Too Long

Stop Waiting
      ↓
Timeout
      ↓
Fallback Response
```

---

# Real Production Example

```java
@Service
public class PaymentService {

    @TimeLimiter(
        name = "payment",
        fallbackMethod = "fallback"
    )
    public CompletableFuture<String>
    processPayment() {

        return CompletableFuture
                .supplyAsync(() -> {

                    sleep(5000);

                    return "Success";
                });
    }

    public CompletableFuture<String>
    fallback(Exception ex) {

        return CompletableFuture
                .completedFuture(
                    "Payment Timed Out"
                );
    }
}
```

Execution:

```text
Response Time = 5 Seconds

Timeout Limit = 2 Seconds

Request Terminated
Fallback Returned
```

Result:

```text
Faster Failure Detection
Better User Experience
Thread Protection
Higher Availability
```

`@TimeLimiter` is a Resilience4j annotation that enforces a maximum execution time for asynchronous operations, preventing applications from waiting indefinitely for slow or unresponsive services and enabling graceful timeout handling through fallback methods.