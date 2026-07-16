
## Definition

`@Slf4j` is a Lombok annotation used to **automatically create a logger object** using the SLF4J logging framework, so you don’t need to manually declare a logger in every class.

Package:

```java
import lombok.extern.slf4j.Slf4j;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
private static final Logger log = LoggerFactory.getLogger(ClassName.class);
```

---

With Lombok:

```text id="b2"
@Slf4j automatically generates the logger
```

---

So:

```text id="c3"
@Slf4j = ready-to-use logger without boilerplate
```

---

# Real-Life Analogy

Think of a CCTV system in a building:

```text id="d4"
Without setup → you manually install camera in every room
With @Slf4j → camera is auto-installed everywhere
```

---

That automatic logging system is:

```text id="e5"
@Slf4j
```

---

# Basic Example

## Without Lombok

```java id="f6"
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserService {

    private static final Logger log =
            LoggerFactory.getLogger(UserService.class);

    public void createUser() {
        log.info("Creating user");
    }
}
```

---

## With @Slf4j

```java id="g7"
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class UserService {

    public void createUser() {
        log.info("Creating user");
    }
}
```

---

# What Lombok Generates Internally

```java id="h8"
private static final org.slf4j.Logger log =
    org.slf4j.LoggerFactory.getLogger(UserService.class);
```

---

# Why @Slf4j is Important

```text id="i9"
Reduces boilerplate
Standard logging across services
Cleaner code
Easy debugging in production
```

---

# Log Levels Used with @Slf4j

```text id="j10"
log.trace()
log.debug()
log.info()
log.warn()
log.error()
```

---

# Real Production Example

```java id="k11"
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class OrderService {

    public void placeOrder(String orderId) {

        log.info("Order received: {}", orderId);

        try {
            // business logic
            log.debug("Processing order: {}", orderId);

        } catch (Exception e) {
            log.error("Error while processing order", e);
        }
    }
}
```

---

# How It Works Internally

```text id="l12"
You write @Slf4j
   ↓
Lombok generates static logger field
   ↓
Uses SLF4J LoggerFactory
   ↓
Logger becomes available as 'log'
```

---

# Supported Logging Frameworks

`@Slf4j` works with:

```text id="m13"
- Logback (most common in Spring Boot)
- Log4j2 (via SLF4J bridge)
- Simple logger implementations
```

---

# Advanced Variants

## 1. Named Logger Access

```text id="n14"
logger name = class name automatically
```

---

## 2. Custom Logger (rare case)

```java id="o15"
@Slf4j(topic = "CUSTOM_LOGGER")
```

---

# Real Use Cases

## 1. Spring Boot Services

```text id="p16"
Logging business flow
```

---

## 2. REST Controllers

```text id="q17"
Request/response tracking
```

---

## 3. Microservices Debugging

```text id="r18"
Distributed system tracing
```

---

## 4. Exception Handling

```text id="s19"
Centralized error logging
```

---

# @Slf4j vs Manual Logger

| Feature | Manual | @Slf4j |
|----------|--------|--------|
| Boilerplate | High | None |
| Readability | Medium | High |
| Setup effort | Required | Automatic |
| Consistency | Variable | Standard |

---

# Common Mistakes

## Mistake 1: Logging sensitive data

```text id="t20"
Passwords, tokens, PII should not be logged
```

---

## Mistake 2: Overusing log.info

```text id="u21"
Use correct log levels (debug/info/warn/error)
```

---

## Mistake 3: Heavy logging in loops

```text id="v22"
Can degrade performance in production
```

---

# Best Practices

## Use:

```text id="w23"
log.info → business flow
log.debug → debugging details
log.warn → unexpected but recoverable issues
log.error → failures/exceptions
```

---

## Avoid:

```text id="x24"
Logging sensitive information
Excessive debug logs in production
```

---

# Common Interview Questions

## What is @Slf4j?

Lombok annotation that generates SLF4J logger automatically.

---

## What is SLF4J?

Simple Logging Facade for Java.

---

## Why use @Slf4j?

To remove manual logger boilerplate.

---

## What logging levels are available?

trace, debug, info, warn, error.

---

## Does it work at runtime?

No, compile-time generation via Lombok.

---

# Enterprise Best Practice

Use `@Slf4j` for:

```text id="y25"
All Spring Boot services
Controllers
Microservices
Utility classes
```

---

Avoid for:

```text id="z26"
Very performance-critical loops
Security-sensitive logging contexts
```

---

# Key Points To Remember

- `@Slf4j` auto-generates SLF4J logger.
- Removes need for manual LoggerFactory code.
- Provides standard logging across applications.
- Supports multiple log levels.
- Works with Spring Boot default logging (Logback).
- Compile-time generation via Lombok.
- Improves readability and maintainability.
- Essential for production-grade applications.
- Very common in Java/Spring interviews.
- Helps with debugging and monitoring.

---

# Quick Example

```java id="a27"
@Slf4j
public class PaymentService {

    public void pay() {
        log.info("Payment started");
    }
}
```

---

# Final Summary

`@Slf4j` is a Lombok annotation that automatically creates an SLF4J logger for a class, enabling clean, consistent, and efficient logging in Java applications without manual boilerplate code.
```