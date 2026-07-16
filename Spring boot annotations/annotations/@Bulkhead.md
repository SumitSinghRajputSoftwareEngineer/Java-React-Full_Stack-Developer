
## Definition

`@Bulkhead` is a **Resilience4j** annotation used to isolate resources and prevent one failing or overloaded component from consuming all available system resources.

It tells Spring:

> Limit the number of concurrent executions for a method so that failures or heavy traffic in one area do not impact the rest of the application.

Package:

```java
import io.github.resilience4j.bulkhead.annotation.Bulkhead;
```

---

# Why Do We Need @Bulkhead?

Imagine an e-commerce application:

```text
User Service
Order Service
Payment Service
Inventory Service
```

---

Suddenly:

```text
Payment Service
```

receives:

```text
50,000 Requests
```

---

Without Bulkhead:

```text
All Threads Occupied
CPU Exhausted
Memory Pressure
Application Slow
Entire System Affected
```

---

Result:

```text
Order Service Slow
Inventory Service Slow
User Service Slow
```

---

One component affects everyone.

---

# Solution

```java
@Bulkhead
```

---

Reserve resources.

Example:

```text
Payment Service
Gets Only 20 Threads
```

---

Even if:

```text
Payment Service Overloaded
```

---

Other services continue working.

---

# Real-Life Analogy

Think of a ship.

---

Ships contain:

```text
Separate Compartments
```

called:

```text
Bulkheads
```

---

If one compartment floods:

```text
Water Stays There
```

---

Entire ship doesn't sink.

---

Exactly the same idea.

---

# Problem Without Bulkhead

```text
User Requests
      ↓
Shared Thread Pool
      ↓
Payment Service Slow
```

---

All threads become occupied:

```text
Thread #1 Busy
Thread #2 Busy
Thread #3 Busy
...
```

---

New requests:

```text
Cannot Execute
```

---

Entire application suffers.

---

# Solution With Bulkhead

```text
Payment Pool → 20 Threads

Order Pool → 20 Threads

Inventory Pool → 20 Threads
```

---

If Payment fails:

```text
Only Payment Pool Impacted
```

---

Other services continue.

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

# Types of Bulkhead

Most Important Interview Question.

Resilience4j provides:

```text
Semaphore Bulkhead

Thread Pool Bulkhead
```

---

# 1. Semaphore Bulkhead

Default.

Controls:

```text
Concurrent Executions
```

---

Example:

```text
Max 10 Requests
```

---

11th request:

```text
Rejected
```

---

Think:

```text
Permission Counter
```

---

# 2. Thread Pool Bulkhead

Uses:

```text
Dedicated Thread Pool
```

---

Example:

```text
Payment Service
Own Thread Pool

Inventory Service
Own Thread Pool
```

---

Provides stronger isolation.

---

# Basic Example

```java
@Service
public class PaymentService {

    @Bulkhead(
        name = "paymentService"
    )
    public String pay() {

        return "Success";
    }
}
```

---

Meaning:

```text
Apply Bulkhead
Protection
```

---

# Example With Fallback

```java
@Bulkhead(
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

    return "System Busy";
}
```

---

Execution:

```text
Bulkhead Full
      ↓
Fallback
```

---

# Configuration Example

application.yml

```yaml
resilience4j:
  bulkhead:
    instances:

      paymentService:

        max-concurrent-calls: 5

        max-wait-duration: 2s
```

---

Meaning:

```text
Only 5 Requests
Can Execute Simultaneously
```

---

Request:

```text
#1 Allowed
#2 Allowed
#3 Allowed
#4 Allowed
#5 Allowed

#6 Waits
```

---

After:

```text
2 Seconds
```

if still no slot:

```text
Rejected
```

---

# Understanding Configuration

## max-concurrent-calls

```yaml
max-concurrent-calls: 5
```

Meaning:

```text
Maximum 5 Active Requests
```

---

## max-wait-duration

```yaml
max-wait-duration: 2s
```

Meaning:

```text
Wait 2 Seconds
For Free Slot
```

---

Then:

```text
Fail
```

---

# Internal Workflow

```text
Request Arrives
      ↓
Available Slot?
      ↓

YES → Execute

NO  → Wait

Slot Available?
      ↓

YES → Execute

NO  → Reject
```

---

# Example Timeline

Configuration:

```yaml
max-concurrent-calls: 2
```

---

Timeline:

```text
Request 1 → Running

Request 2 → Running

Request 3 → Waiting

Request 4 → Waiting
```

---

If timeout reached:

```text
Request 3 Rejected

Request 4 Rejected
```

---

# Thread Pool Bulkhead Example

```java
@Bulkhead(
    name = "paymentService",
    type = Bulkhead.Type.THREADPOOL
)
public CompletableFuture<String>
pay() {

    return CompletableFuture
            .completedFuture(
                 "Success"
            );
}
```

---

Creates:

```text
Dedicated Thread Pool
```

---

For:

```text
Payment Service Only
```

---

# Real Production Scenario

```text
Order Service
      ↓
Payment Service
```

---

Payment provider becomes slow:

```text
30 Seconds Response Time
```

---

Without Bulkhead:

```text
All Threads Occupied
```

---

Application freezes.

---

With Bulkhead:

```text
Only Payment Threads Blocked
```

---

Inventory:

```text
Still Working
```

---

Orders:

```text
Still Working
```

---

Users:

```text
Still Browse Products
```

---

# Bulkhead vs Circuit Breaker

Interview Favorite.

---

## Bulkhead

Purpose:

```text
Resource Isolation
```

---

Protects:

```text
Threads
Connections
Resources
```

---

## Circuit Breaker

Purpose:

```text
Failure Isolation
```

---

Protects:

```text
From Repeated Failures
```

---

Comparison:

| Feature | Bulkhead | CircuitBreaker |
|----------|-----------|---------------|
| Resource Isolation | ✅ | ❌ |
| Failure Detection | ❌ | ✅ |
| Uses States | ❌ | ✅ |
| Limits Concurrency | ✅ | ❌ |

---

# Bulkhead vs RateLimiter

## Bulkhead

Controls:

```text
Concurrent Requests
```

---

Example:

```text
Only 10 Active Requests
```

---

## RateLimiter

Controls:

```text
Requests Per Time
```

---

Example:

```text
100 Requests/Minute
```

---

Comparison:

| Feature | Bulkhead | RateLimiter |
|----------|-----------|------------|
| Concurrent Control | ✅ | ❌ |
| Time Window Control | ❌ | ✅ |
| Thread Isolation | ✅ | ❌ |
| Traffic Control | ❌ | ✅ |

---

# Bulkhead vs Retry

## Retry

```text
Try Again
```

---

## Bulkhead

```text
Protect Resources
```

---

Completely different purposes.

---

# Common Use Cases

## Payment Gateway Calls

```text
Payment Service
```

---

## External APIs

```text
Maps
Weather
SMS
```

---

## Database Calls

```text
Slow Queries
```

---

## Microservice Communication

```text
Order → Payment
```

---

## High Traffic Systems

```text
E-Commerce
Banking
Trading
```

---

# Common Mistakes

## Mistake 1

Too Small Limit

Wrong:

```yaml
max-concurrent-calls: 1
```

---

Creates bottleneck.

---

# Mistake 2

Too Large Limit

Wrong:

```yaml
max-concurrent-calls: 10000
```

---

Provides no protection.

---

# Mistake 3

Using Bulkhead Instead of RateLimiter

Bulkhead:

```text
Concurrent Requests
```

---

RateLimiter:

```text
Requests Per Time Window
```

---

Different concepts.

---

# Common Interview Questions

## What is @Bulkhead?

Limits concurrent executions and isolates resources.

---

## Which Library Provides It?

```text
Resilience4j
```

---

## Why Use It?

Prevent resource exhaustion.

---

## Types of Bulkhead?

```text
Semaphore Bulkhead

Thread Pool Bulkhead
```

---

## Difference Between Bulkhead and Circuit Breaker?

Resource isolation vs failure isolation.

---

## Difference Between Bulkhead and RateLimiter?

Concurrency control vs traffic control.

---

# Enterprise Best Practice

Use Bulkhead on:

```text
Payment APIs
External Services
Database Calls
Slow Operations
```

---

Combine with:

```java
@CircuitBreaker
@Retry
@RateLimiter
@TimeLimiter
```

---

for complete resilience.

---

# Key Points To Remember

- `@Bulkhead` is provided by Resilience4j.
- Prevents resource exhaustion.
- Limits concurrent executions.
- Inspired by ship bulkhead compartments.
- Supports Semaphore and ThreadPool bulkheads.
- Protects microservices from overload.
- Works with fallback methods.
- Different from RateLimiter and CircuitBreaker.
- Essential cloud-native resilience pattern.
- Frequently asked microservices interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Bulkhead` | Resource Isolation |
| `@RateLimiter` | Traffic Control |
| `@Retry` | Retry Failures |
| `@CircuitBreaker` | Failure Isolation |
| `@TimeLimiter` | Timeout Protection |

---

# Interview Shortcut

```java
@Bulkhead(
    name = "payment",
    fallbackMethod = "fallback"
)
```

Meaning:

```text
Allow Only A Limited Number
Of Concurrent Requests

Extra Requests
      ↓
Rejected Or Wait
```

---

# Real Production Example

```java
@Service
public class PaymentService {

    @Bulkhead(
        name = "payment",
        fallbackMethod = "fallback"
    )
    public String pay() {

        return paymentClient.pay();
    }

    public String fallback(
            Exception ex) {

        return "System Busy";
    }
}
```

Execution:

```text
5 Requests Running

6th Request
      ↓
Waits

No Slot Available
      ↓
Fallback
```

Result:

```text
Resource Protection
Thread Isolation
Better Stability
High Availability
```

`@Bulkhead` is a Resilience4j annotation that isolates resources by limiting concurrent executions, ensuring that failures or heavy load in one component do not consume all available system resources and affect the rest of the application.