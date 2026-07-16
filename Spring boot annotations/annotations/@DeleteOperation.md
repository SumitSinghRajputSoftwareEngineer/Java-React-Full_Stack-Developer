
## Definition

`@DeleteOperation` is a Spring Boot Actuator annotation used to expose a **custom endpoint operation that removes or resets state**.

It tells Spring:

> This method performs a delete/reset operation (similar to HTTP DELETE) in a custom Actuator endpoint.

Package:

```java
import org.springframework.boot.actuate.endpoint.annotation.DeleteOperation;
```

---

# Simple Understanding

In Spring Boot Actuator custom endpoints:

| Annotation | Meaning |
|------------|--------|
| @ReadOperation | Read data (GET) |
| @WriteOperation | Modify/trigger action (POST/PUT) |
| @DeleteOperation | Remove/reset data (DELETE) |

---

So:

```text
@DeleteOperation = Remove or Reset System State
```

---

# Real-Life Analogy

Think of an admin dashboard:

```text
View Logs → ReadOperation
Restart Service → WriteOperation
Clear Logs → DeleteOperation
```

---

Or:

```text
Recycle Bin Empty → DeleteOperation
```

---

# Why Do We Need @DeleteOperation?

Sometimes in production systems we need to:

```text
Clear cache
Delete temporary data
Reset counters
Remove logs
Reset metrics
```

---

Instead of exposing REST APIs, we can expose controlled **Actuator admin operations**.

---

# Basic Example

```java
@Component
@Endpoint(id = "cache")
public class CacheEndpoint {

    @DeleteOperation
    public String clearCache() {
        return "Cache cleared successfully";
    }
}
```

---

Now exposed at:

```text
DELETE /actuator/cache
```

---

# What Happens Internally?

Request:

```text
DELETE /actuator/cache
```

---

Spring Actuator flow:

```text
Find @Endpoint(id="cache")
        ↓
Locate @DeleteOperation method
        ↓
Execute method
        ↓
Perform deletion/reset action
        ↓
Return response
```

---

# Example: Reset Metrics

```java
@Component
@Endpoint(id = "metrics")
public class MetricsEndpoint {

    @DeleteOperation
    public String resetMetrics() {

        metricsService.resetAll();

        return "Metrics reset successfully";
    }
}
```

---

# Example: Clear Logs

```java
@Component
@Endpoint(id = "logs")
public class LogEndpoint {

    @DeleteOperation
    public String clearLogs() {

        logService.clearLogs();

        return "Logs cleared";
    }
}
```

---

# Example: Reset Cache Counter

```java
@Component
@Endpoint(id = "counter")
public class CounterEndpoint {

    @DeleteOperation
    public String resetCounter() {

        counterService.reset();

        return "Counter reset";
    }
}
```

---

# Request Flow

```text
Client (Admin Tool)
        │
        ▼

DELETE /actuator/cache
        │
        ▼

Spring Actuator Dispatcher
        │
        ▼

@Endpoint(id="cache")
        │
        ▼

@DeleteOperation
        │
        ▼

Business Logic Executes
        │
        ▼

Response Returned
```

---

# HTTP Mapping Behavior

| Actuator Operation | HTTP Equivalent |
|--------------------|----------------|
| @ReadOperation | GET |
| @WriteOperation | POST/PUT |
| @DeleteOperation | DELETE |

---

# @DeleteOperation vs REST Controller

| Feature | @DeleteOperation | @DeleteMapping |
|----------|----------------|----------------|
| Framework | Actuator | Spring MVC |
| Purpose | Admin/system reset | Business API |
| Audience | DevOps/Admin | Users |
| Scope | Monitoring tools | Application logic |
| URL Base | /actuator | /api |

---

# Real Production Use Cases

## Cache Management

```text
Clear all cache entries
Reset cache statistics
```

---

## Logging Systems

```text
Delete logs
Clear audit trails (controlled)
```

---

## Metrics Systems

```text
Reset counters
Clear historical metrics
```

---

## Temporary Data

```text
Remove temp files
Cleanup staging data
```

---

## Feature Flags

```text
Remove outdated flags
Reset feature states
```

---

# Security Warning ⚠️

`@DeleteOperation` is **very sensitive** because it deletes/reset system state.

Always secure it:

```java
@PreAuthorize("hasRole('ADMIN')")
@DeleteOperation
public String clearCache() {
    return "Cache cleared";
}
```

---

Recommended security:

```text
Spring Security
JWT / OAuth2
Role-based access control
Audit logging
```

---

# Common Mistakes

## Mistake 1: No Security

```text
Anyone can delete logs or cache
```

---

## Mistake 2: Misuse for Business APIs

Wrong:

```java
@DeleteOperation
public void deleteUser()
```

Correct:

```java
@RestController + @DeleteMapping
```

---

## Mistake 3: Missing Endpoint Exposure

```properties
management.endpoints.web.exposure.include=*
```

If not exposed:

```text
404 Not Found
```

---

# Common Interview Questions

## What is @DeleteOperation?

Used in Actuator endpoints to perform delete/reset operations.

---

## What is it similar to?

```text
@DeleteMapping
```

---

## Does it remove data?

Yes, typically resets or clears system state.

---

## Where is it used?

Spring Boot Actuator custom endpoints.

---

## Is it safe?

Only when properly secured.

---

# Enterprise Best Practice

Use `@DeleteOperation` for:

```text
Cache clearing
Log cleanup
Metrics reset
System cleanup tasks
Admin maintenance
```

---

Avoid for:

```text
Business CRUD operations
User-facing APIs
External service calls
```

---

# Key Points To Remember

- `@DeleteOperation` is part of Spring Boot Actuator.
- Used to define delete/reset operations in custom endpoints.
- Equivalent to HTTP DELETE behavior.
- Works with `@Endpoint`.
- Used for system/admin maintenance tasks.
- Must be heavily secured.
- Not for business APIs.
- Common in DevOps monitoring tools.
- Works at `/actuator/{id}`.
- Frequently asked Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Endpoint` | Define actuator endpoint |
| `@ReadOperation` | Read data |
| `@WriteOperation` | Modify state |
| `@DeleteOperation` | Remove/reset state |

---

# Interview Shortcut

```java
@Endpoint(id = "logs")
public class LogEndpoint {

    @DeleteOperation
    public String clearLogs() {
        return "Logs cleared";
    }
}
```

Meaning:

```text
Expose admin endpoint

Allow system cleanup operation

Provide controlled delete/reset functionality
```

---

# Real Production Example

```text
DevOps Control System
```

Flow:

```text
Admin sends DELETE /actuator/logs
            ↓
@Endpoint
            ↓
@DeleteOperation
            ↓
Logs removed
            ↓
System cleaned
```

Result:

```text
Operational maintenance without redeploy
Controlled system cleanup
Improved observability management
```

---

# Final Summary

`@DeleteOperation` is a Spring Boot Actuator annotation used to expose custom endpoints that perform **system cleanup, reset, or deletion operations**, typically for DevOps and administrative tasks such as clearing cache, resetting metrics, or deleting logs in a controlled and secure manner.
```