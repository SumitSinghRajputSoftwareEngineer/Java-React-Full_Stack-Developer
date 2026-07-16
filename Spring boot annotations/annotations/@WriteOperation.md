
## Definition

`@WriteOperation` is a Spring Boot Actuator annotation used to expose a **custom endpoint operation that modifies state or triggers an action**.

It tells Spring:

> This method performs a write/action operation (similar to POST/PUT), not just reading data.

Package:

```java
import org.springframework.boot.actuate.endpoint.annotation.WriteOperation;
```

---

# Simple Understanding

In Spring Actuator custom endpoints:

| Annotation | Meaning |
|------------|--------|
| @ReadOperation | GET (fetch data) |
| @WriteOperation | POST/PUT (change or trigger action) |
| @DeleteOperation | DELETE (remove/reset) |

---

So:

```text
@WriteOperation = Perform Action / Modify State
```

---

# Real-Life Analogy

Think of an admin dashboard:

```text
View Server Status → Read Operation
Restart Server → Write Operation
Clear Cache → Write Operation
```

---

# Why Do We Need @WriteOperation?

Spring Boot Actuator is not just for viewing system data.

Sometimes we need to:

```text
Trigger jobs
Reset cache
Reload configuration
Start/stop tasks
Change system state
```

---

That is where:

```java
@WriteOperation
```

is used.

---

# Basic Example

```java
@Component
@Endpoint(id = "cache")
public class CacheEndpoint {

    @WriteOperation
    public String clearCache() {
        return "Cache cleared successfully";
    }
}
```

---

Now exposed at:

```text
POST /actuator/cache
```

---

# What Happens Internally?

Request:

```text
POST /actuator/cache
```

---

Spring Actuator flow:

```text
Find @Endpoint(id="cache")
        ↓
Locate @WriteOperation method
        ↓
Invoke method
        ↓
Execute action (state change)
        ↓
Return response
```

---

# Example: Cache Management

```java
@Component
@Endpoint(id = "cache")
public class CacheEndpoint {

    @WriteOperation
    public String clearCache() {

        CacheManager.clearAll();

        return "Cache cleared";
    }
}
```

---

# Example: Restart Job Trigger

```java
@Component
@Endpoint(id = "job")
public class JobEndpoint {

    @WriteOperation
    public String triggerJob() {

        jobService.runJob();

        return "Job started";
    }
}
```

---

# Example: Reload Configuration

```java
@Component
@Endpoint(id = "config")
public class ConfigEndpoint {

    @WriteOperation
    public String reloadConfig() {

        configService.reload();

        return "Configuration reloaded";
    }
}
```

---

# Request Flow

```text
Client (DevOps Tool)
        │
        ▼

POST /actuator/cache
        │
        ▼

Spring Actuator Dispatcher
        │
        ▼

@Endpoint(id="cache")
        │
        ▼

@WriteOperation
        │
        ▼

Business Logic Executes
        │
        ▼

Response Returned
```

---

# @WriteOperation vs REST Controller

| Feature | @WriteOperation | @PostMapping |
|----------|----------------|-------------|
| Framework | Actuator | Spring MVC |
| Purpose | Admin/Monitoring | Business APIs |
| Audience | DevOps/Admin | Users |
| Action Type | System operations | Application logic |
| URL Base | /actuator | /api |

---

# Types of Actuator Operations

## 1. @ReadOperation

```text
Read system data
(GET-like)
```

---

## 2. @WriteOperation

```text
Modify system state
(POST/PUT-like)
```

---

## 3. @DeleteOperation

```text
Remove/reset data
(DELETE-like)
```

---

# Example with All Operations

```java
@Component
@Endpoint(id = "system")
public class SystemEndpoint {

    @ReadOperation
    public String status() {
        return "Running";
    }

    @WriteOperation
    public String restart() {
        return "System restarted";
    }

    @DeleteOperation
    public String reset() {
        return "System reset";
    }
}
```

---

# Real Production Use Cases

## Cache Management

```text
Clear cache
Warm cache
Refresh cache
```

---

## Job Control

```text
Start batch job
Trigger ETL pipeline
```

---

## Configuration Management

```text
Reload configs
Switch environment settings
```

---

## Feature Flags

```text
Enable/Disable features dynamically
```

---

## System Maintenance

```text
Restart services
Cleanup temp data
```

---

# HTTP Mapping Behavior

Even though it's not Spring MVC:

| Operation | HTTP Equivalent |
|------------|----------------|
| @ReadOperation | GET |
| @WriteOperation | POST |
| @DeleteOperation | DELETE |

---

# Security Consideration

⚠️ Very important

Write operations can change system state.

So they must be secured:

```text
Spring Security
Role-based access (ADMIN only)
OAuth2 / JWT
```

---

Example:

```java
@PreAuthorize("hasRole('ADMIN')")
@WriteOperation
public String restartSystem() {
    return "Restarted";
}
```

---

# Common Mistakes

## Mistake 1: Exposing Without Security

```text
Anyone can trigger system restart
```

---

## Mistake 2: Using for Business APIs

Wrong:

```java
@WriteOperation
public String createUser()
```

Correct:

```java
@RestController + @PostMapping
```

---

## Mistake 3: Not Exposing Endpoint

```properties
management.endpoints.web.exposure.include=*
```

Without exposure:

```text
404 Not Found
```

---

# Common Interview Questions

## What is @WriteOperation?

Used to define a write/action operation in Actuator endpoints.

---

## What is it similar to?

```text
@PostMapping / @PutMapping
```

---

## Does it change state?

Yes.

---

## Where is it used?

Spring Boot Actuator custom endpoints.

---

## Is it safe?

Only if secured properly.

---

# Enterprise Best Practice

Use `@WriteOperation` for:

```text
Admin operations
System triggers
Maintenance tasks
Cache control
Configuration reload
```

---

Avoid using for:

```text
User-facing CRUD APIs
Frontend interactions
Business transactions
```

---

# Key Points To Remember

- `@WriteOperation` is part of Spring Boot Actuator.
- Used to modify system state or trigger actions.
- Works with `@Endpoint`.
- Equivalent to POST/PUT in behavior.
- Used in admin/DevOps scenarios.
- Must be secured properly.
- Not used for normal REST APIs.
- Common in system monitoring tools.
- Works at `/actuator/{id}`.
- Frequently asked DevOps/Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Endpoint` | Define actuator endpoint |
| `@ReadOperation` | Read data |
| `@WriteOperation` | Modify/trigger action |
| `@DeleteOperation` | Remove/reset data |

---

# Interview Shortcut

```java
@Endpoint(id = "cache")
public class CacheEndpoint {

    @WriteOperation
    public String clearCache() {
        return "Cache cleared";
    }
}
```

Meaning:

```text
Expose actuator endpoint

Allow system state modification

Trigger backend/admin operations
```

---

# Real Production Example

```text
DevOps System Control Panel
```

Flow:

```text
Admin triggers POST /actuator/cache
            ↓
@Endpoint
            ↓
@WriteOperation
            ↓
Cache cleared / refreshed
            ↓
System updated in real time
```

Result:

```text
Operational control over system
Dynamic management
No redeploy required
```

---

# Final Summary

`@WriteOperation` is a Spring Boot Actuator annotation used to expose custom endpoints that perform **state-changing or action-triggering operations**, typically used for administrative or DevOps tasks such as cache clearing, job triggering, or system reconfiguration.

---