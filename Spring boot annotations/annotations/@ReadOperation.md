## Definition

`@ReadOperation` is a Spring Boot Actuator annotation used to expose a **read-only custom endpoint operation** over HTTP.

It tells Spring:

> This method provides data (GET-like operation) for a custom Actuator endpoint.

Package:

```java
import org.springframework.boot.actuate.endpoint.annotation.ReadOperation;
```

---

# Simple Understanding

Think of Spring Actuator endpoints like:

```text
/actuator/health
/actuator/metrics
```

---

Now if you create your own actuator endpoint:

```java
@Endpoint(id = "appInfo")
```

You use:

```java
@ReadOperation
```

to expose **GET-style data**.

---

# Why Do We Need @ReadOperation?

Spring Actuator supports custom endpoints, but needs a way to define operations like:

| Operation Type | Meaning |
|----------------|--------|
| ReadOperation | GET (fetch data) |
| WriteOperation | POST/PUT (modify data) |
| DeleteOperation | DELETE (remove data) |

---

So:

```java
@ReadOperation → Read Data
```

---

# Real-Life Analogy

Think of a dashboard:

```text
Admin Panel
```

---

You click:

```text
View Server Status
```

---

You are not changing anything.

Only viewing.

---

That is:

```text
@ReadOperation
```

---

# Basic Example

```java
@Component
@Endpoint(id = "appInfo")
public class AppInfoEndpoint {

    @ReadOperation
    public String info() {
        return "Application is running fine";
    }
}
```

---

Now exposed at:

```text
/actuator/appInfo
```

---

# What Happens Internally?

Request:

```text
GET /actuator/appInfo
```

---

Spring Actuator:

```text
Find @Endpoint
      ↓
Match id = appInfo
      ↓
Find @ReadOperation method
      ↓
Execute method
      ↓
Return response
```

---

# Output Example

```text
Application is running fine
```

---

# JSON Response Example

```java
@ReadOperation
public Map<String, Object> info() {

    Map<String, Object> data = new HashMap<>();

    data.put("status", "UP");
    data.put("version", "1.0.0");

    return data;
}
```

---

Response:

```json
{
  "status": "UP",
  "version": "1.0.0"
}
```

---

# Creating Custom Actuator Endpoint

```java
@Component
@Endpoint(id = "serverStatus")
public class ServerStatusEndpoint {

    @ReadOperation
    public Map<String, String> status() {

        Map<String, String> map =
            new HashMap<>();

        map.put("server", "running");
        map.put("db", "connected");

        return map;
    }
}
```

---

Access:

```text
/actuator/serverStatus
```

---

# Types of Operations

## 1. @ReadOperation

```java
GET-like operation
```

Used for:

```text
Fetching data
Monitoring
Status checks
```

---

## 2. @WriteOperation

```java
POST-like operation
```

Used for:

```text
Updating configuration
Triggering jobs
```

---

## 3. @DeleteOperation

```java
DELETE-like operation
```

Used for:

```text
Clearing cache
Removing state
```

---

# Example with All Operations

```java
@Component
@Endpoint(id = "cache")
public class CacheEndpoint {

    @ReadOperation
    public String getCache() {
        return "Cache Data";
    }

    @WriteOperation
    public String updateCache() {
        return "Cache Updated";
    }

    @DeleteOperation
    public String clearCache() {
        return "Cache Cleared";
    }
}
```

---

# Real Production Use Cases

## System Monitoring

```text
Server Health
DB Status
Memory Usage
```

---

## Application Info

```text
Version
Build Info
Environment
```

---

## Cache Monitoring

```text
Cache Hit Ratio
Cache Size
```

---

## Feature Flags

```text
Enable/Disable Features
```

---

# Actuator URL Mapping

If:

```java
@Endpoint(id = "metricsInfo")
```

Then:

```text
/actuator/metricsInfo
```

---

And:

```java
@ReadOperation
```

maps to:

```text
GET /actuator/metricsInfo
```

---

# @ReadOperation vs REST Controller

| Feature | @ReadOperation | @GetMapping |
|----------|---------------|-------------|
| Framework | Actuator | Spring MVC |
| Purpose | Monitoring | Business API |
| URL Base | /actuator | /api |
| Audience | DevOps/Admin | Users/Clients |
| Security | Internal | External |

---

# Internal Flow

```text
Client Request
      ↓
Actuator Endpoint Registry
      ↓
@Endpoint(id)
      ↓
@ReadOperation Method
      ↓
Response Serialization
      ↓
HTTP Response
```

---

# Security Consideration

Actuator endpoints are sensitive.

Usually protected via:

```properties
management.endpoints.web.exposure.include=health,info,metrics
```

---

Or:

```text
Spring Security
Role-based Access
```

---

# Common Mistakes

## Mistake 1

Not exposing endpoint:

```properties
management.endpoints.web.exposure.include=*
```

or specific endpoint missing.

---

Result:

```text
404 Not Found
```

---

## Mistake 2

Missing @Endpoint annotation

Without it:

```text
@ReadOperation is ignored
```

---

## Mistake 3

Using @RestController instead

Actuator won't detect it.

---

# Common Interview Questions

## What is @ReadOperation?

Defines a read-only Actuator endpoint operation.

---

## Where is it used?

Spring Boot Actuator custom endpoints.

---

## What is it similar to?

```text
@GetMapping
```

---

## What does it return?

Any object → JSON response.

---

## Is it for business logic?

No. It is for monitoring/admin purposes.

---

# Enterprise Best Practice

Use:

```java
@ReadOperation
```

for:

```text
Monitoring
Health Checks
System Info
Diagnostics
```

---

Avoid using it for:

```text
Business APIs
Frontend APIs
User-facing services
```

---

# Key Points To Remember

- `@ReadOperation` is used in Spring Boot Actuator.
- It exposes read-only custom endpoints.
- Works with `@Endpoint`.
- Equivalent to GET operation.
- Used for monitoring and diagnostics.
- Returns JSON or plain response.
- Not used for business APIs.
- Works at `/actuator/{id}`.
- Part of Spring Boot Actuator framework.
- Frequently asked DevOps/Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Endpoint` | Define custom actuator endpoint |
| `@ReadOperation` | Read data (GET) |
| `@WriteOperation` | Modify data |
| `@DeleteOperation` | Remove data |

---

# Interview Shortcut

```java
@Endpoint(id = "appInfo")
public class AppInfo {

    @ReadOperation
    public Map<String, Object> info() {
        return Map.of("status", "UP");
    }
}
```

Meaning:

```text
Expose custom Actuator endpoint

Provide read-only system information

Return monitoring data via HTTP GET
```

---

# Real Production Example

```text
Production System Monitoring
```

Flow:

```text
DevOps Calls /actuator/appInfo
            ↓
@Endpoint
            ↓
@ReadOperation
            ↓
System Data Returned
            ↓
Monitoring Dashboard Updated
```

Result:

```text
Real-time System Visibility
Health Monitoring
Operational Insights
```

`@ReadOperation` is a Spring Boot Actuator annotation used to expose a read-only custom endpoint that returns monitoring or diagnostic data via HTTP GET requests under the `/actuator` path.