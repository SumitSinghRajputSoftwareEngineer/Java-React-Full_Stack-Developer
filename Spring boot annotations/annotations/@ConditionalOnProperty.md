
## Definition

`@ConditionalOnProperty` is a Spring Boot annotation used to **enable or disable a bean/configuration based on the value of a property in `application.properties` or `application.yml`**.

It tells Spring:

> “Create this bean only if a specific configuration property matches the expected value.”

Package:

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
```

---

# Simple Understanding

Normally Spring does:

```text id="a1"
Always create beans at startup
```

---

With `@ConditionalOnProperty`:

```text id="b2"
Create bean only if property value matches condition
```

---

So:

```text id="c3"
@ConditionalOnProperty = Feature toggle using config files
```

---

# Real-Life Analogy

Think of app features:

```text id="d4"
Dark mode → enabled only if config says true
Email service → enabled only if "email.enabled=true"
Cache → enabled only if "cache.enabled=true"
```

---

That configuration-based control is:

```text id="e5"
@ConditionalOnProperty
```

---

# Why Do We Need @ConditionalOnProperty?

We use it for:

```text id="f6"
Feature toggles
Environment-based activation
Optional modules
Runtime configuration control
```

---

# Basic Example

```java id="g7"
@Bean
@ConditionalOnProperty(name = "feature.cache.enabled", havingValue = "true")
public CacheService cacheService() {
    return new RedisCacheService();
}
```

---

# application.properties

```properties id="h8"
feature.cache.enabled=true
```

---

# What Happens?

```text id="i9"
If feature.cache.enabled = true → Bean is created
If false or missing → Bean is NOT created
```

---

# Real Production Example: Email Service

```java id="j10"
@Bean
@ConditionalOnProperty(name = "email.enabled", havingValue = "true")
public EmailService emailService() {
    return new SmtpEmailService();
}
```

---

```properties id="k11"
email.enabled=true
```

---

# How It Works Internally

```text id="l12"
Spring Boot starts
   ↓
Reads application.properties
   ↓
Evaluates property condition
   ↓
If match → register bean
If not match → skip bean
```

---

# Advanced Usage Options

## 1. havingValue

```java id="m13"
@ConditionalOnProperty(
    name = "feature.cache.enabled",
    havingValue = "true"
)
```

---

## 2. matchIfMissing

```java id="n14"
@ConditionalOnProperty(
    name = "feature.cache.enabled",
    havingValue = "true",
    matchIfMissing = true
)
```

Meaning:

```text id="o15"
If property is missing → treat as true
```

---

## 3. multiple properties

```java id="p16"
@ConditionalOnProperty(
    name = {"feature.cache.enabled", "feature.redis.enabled"},
    havingValue = "true"
)
```

---

# Real Production Use Cases

## 1. Feature Toggle System

```text id="q17"
Enable/disable features without redeploy
```

---

## 2. Environment Control

```text id="r18"
Enable debug logs in dev only
Disable email in test environment
```

---

## 3. Microservice Modules

```text id="s19"
Enable Kafka only if kafka.enabled=true
```

---

## 4. Cloud Integrations

```text id="t20"
Enable AWS services only when aws.enabled=true
```

---

# Real Architecture View

```text id="u21"
Application Starts
     ↓
Read configuration properties
     ↓
Check @ConditionalOnProperty
     ↓
If TRUE → enable bean
If FALSE → skip bean
     ↓
Final application context ready
```

---

# Spring Boot Internal Usage

Spring Boot uses it heavily in auto-configuration:

```text id="v22"
Enable Redis only if property is set
Enable caching only if cache.enabled=true
Enable scheduling only if scheduling.enabled=true
```

---

# @ConditionalOnProperty vs Other Conditionals

| Annotation | Condition Type |
|------------|----------------|
| @ConditionalOnProperty | Config file property |
| @ConditionalOnClass | Class in classpath |
| @ConditionalOnBean | Bean exists |
| @ConditionalOnMissingBean | Bean not exists |

---

# Real Production Example: Multi-Feature System

```java id="w23"
@Bean
@ConditionalOnProperty(name = "feature.payment.enabled", havingValue = "true")
public PaymentService paymentService() {
    return new StripePaymentService();
}
```

---

```properties id="x24"
feature.payment.enabled=true
```

---

# Dynamic Behavior

```text id="y25"
Change config → restart app → behavior changes
No code change needed
```

---

# Common Mistakes

## Mistake 1: Wrong property name

```text id="z26"
Typo → bean never created
```

---

## Mistake 2: Missing configuration file

```text id="a27"
Property not found → default behavior triggers
```

---

## Mistake 3: Overusing feature flags

```text id="b28"
Too many toggles → hard to maintain system
```

---

# Common Interview Questions

## What is @ConditionalOnProperty?

Creates bean based on property value.

---

## Where is it used?

Spring Boot auto-configuration and feature toggles.

---

## What happens if property is missing?

Depends on matchIfMissing.

---

## Difference from @ConditionalOnClass?

Property-based vs class-based condition.

---

## Why is it important?

Enables runtime feature control without code changes.

---

# Enterprise Best Practice

Use `@ConditionalOnProperty` for:

```text id="c29"
Feature flags
Environment-specific behavior
Optional modules
Config-driven systems
```

---

Avoid for:

```text id="d30"
Core business logic
Mandatory services
Critical system flows
```

---

# Key Points To Remember

- `@ConditionalOnProperty` enables beans based on config values.
- Used for feature toggles and environment control.
- Works with application.properties or YAML.
- Supports havingValue and matchIfMissing.
- Core part of Spring Boot auto-configuration.
- Enables runtime behavior control without code changes.
- Helps build flexible systems.
- Widely used in microservices.
- Easy way to enable/disable features.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| @ConditionalOnProperty | Config-based control |
| @ConditionalOnClass | Dependency-based control |
| @ConditionalOnBean | Bean dependency control |
| @ConditionalOnMissingBean | Default fallback |

---

# Interview Shortcut

```java id="e31"
@Bean
@ConditionalOnProperty(name = "cache.enabled", havingValue = "true")
public CacheService cacheService() {
    return new RedisCacheService();
}
```

Meaning:

```text id="f32"
Enable bean only if configuration property is true

Supports feature toggling

Allows runtime control via config files
```

---

# Real Production Example

```text id="g33"
Enterprise Microservice System
```

Flow:

```text id="h34"
Application Starts
     ↓
Read configuration
     ↓
Check feature flags
     ↓
Enable/disable modules accordingly
     ↓
Application ready with dynamic features
```

Result:

```text id="i35"
Highly flexible architecture
Config-driven system behavior
No code changes required for feature control
```

---

# Final Summary

`@ConditionalOnProperty` is a Spring Boot annotation used to enable or disable beans based on configuration properties, enabling feature toggles, environment-based control, and dynamic behavior in enterprise applications without changing code.
```