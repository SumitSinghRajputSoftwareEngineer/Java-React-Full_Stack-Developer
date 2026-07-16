
## Definition

`@ConditionalOnMissingBean` is a Spring Boot annotation used to **create a bean only if no other bean of the same type (or name) already exists in the Spring Application Context**.

It tells Spring:

> “Only register this bean if no existing bean matches it.”

Package:

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
```

---

# Simple Understanding

Normally Spring does:

```text id="a1"
Create all configured beans
```

---

With `@ConditionalOnMissingBean`:

```text id="b2"
Create bean ONLY if it is not already defined anywhere
```

---

So:

```text id="c3"
@ConditionalOnMissingBean = Fallback / Default bean creation
```

---

# Real-Life Analogy

Think of a system:

```text id="d4"
Email Service:
- If custom SMTP service exists → use it
- If not → use default email service
```

---

Or:

```text id="e5"
Payment Gateway:
- If Stripe configured → use Stripe
- Else → use default mock gateway
```

---

That fallback behavior is:

```text id="f6"
@ConditionalOnMissingBean
```

---

# Why Do We Need @ConditionalOnMissingBean?

We use it for:

```text id="g7"
Providing default implementations
Allowing user overrides
Writing Spring Boot auto-configuration
Building extensible frameworks
```

---

# Basic Example

```java id="h8"
@Configuration
public class EmailConfig {

    @Bean
    @ConditionalOnMissingBean
    public EmailService defaultEmailService() {
        return new SmtpEmailService();
    }
}
```

---

# What Happens Here?

```text id="i9"
If EmailService bean already exists → skip this bean
If NOT exists → create defaultEmailService
```

---

# Real Production Example: Cache Service

```java id="j10"
@Bean
@ConditionalOnMissingBean(CacheService.class)
public CacheService defaultCacheService() {
    return new InMemoryCacheService();
}
```

---

# How It Works Internally

```text id="k11"
Spring starts
   ↓
Scans beans
   ↓
Checks if bean of same type exists
   ↓
If EXISTS → skip creation
If NOT EXISTS → create bean
```

---

# Bean Matching Rules

You can check:

## 1. By Type

```java id="l12"
@ConditionalOnMissingBean(DataSource.class)
```

---

## 2. By Name

```java id="m13"
@ConditionalOnMissingBean(name = "dataSource")
```

---

## 3. Multiple Beans

```java id="n14"
@ConditionalOnMissingBean({CacheManager.class, CacheService.class})
```

---

# Real Production Use Cases

## 1. Default Service Implementation

```text id="o15"
If no custom service exists → provide default
```

---

## 2. Auto-Configuration (Spring Boot Core Use)

```text id="p16"
Spring Boot provides default beans unless user overrides
```

---

## 3. Testing / Mocking

```text id="q17"
Use mock bean if real one is not present
```

---

## 4. Framework Libraries

```text id="r18"
Libraries provide fallback beans for extensibility
```

---

# Example: Payment System

```java id="s19"
@Bean
@ConditionalOnMissingBean(PaymentGateway.class)
public PaymentGateway defaultGateway() {
    return new FakePaymentGateway();
}
```

---

# Override Scenario

If user defines:

```java id="t20"
@Bean
public PaymentGateway stripeGateway() {
    return new StripeGateway();
}
```

Then:

```text id="u21"
Default bean is NOT created
Stripe bean is used instead
```

---

# Spring Boot Auto-Configuration Example

Spring Boot internally does:

```text id="v22"
If DataSource NOT defined → create HikariDataSource
If RestTemplate NOT defined → create default RestTemplate
```

---

# @ConditionalOnMissingBean vs @ConditionalOnBean

| Feature | MissingBean | OnBean |
|----------|-------------|--------|
| Condition | Bean does NOT exist | Bean exists |
| Purpose | Provide default | Enable dependent bean |
| Use Case | Fallback logic | Dependency logic |

---

# Real Architecture View

```text id="w23"
Custom Bean Provided?
        ↓ YES
Use Custom Bean

        ↓ NO
Create Default Bean (@ConditionalOnMissingBean)
```

---

# Common Mistakes

## Mistake 1: Wrong assumption

```text id="x24"
Thinking both beans will coexist ❌
```

---

## Mistake 2: Type mismatch

```text id="y25"
Bean exists but different type → condition still triggers
```

---

## Mistake 3: Overusing defaults

```text id="z26"
Too many fallback beans → hidden behavior
```

---

# Common Interview Questions

## What is @ConditionalOnMissingBean?

Creates bean only if no matching bean exists.

---

## Why is it used?

To provide default implementations.

---

## Where is it used?

Spring Boot auto-configuration.

---

## Difference from @ConditionalOnBean?

MissingBean = no bean should exist  
OnBean = bean must exist

---

## What happens if bean already exists?

Default bean is skipped.

---

# Enterprise Best Practice

Use `@ConditionalOnMissingBean` for:

```text id="a27"
Default implementations
Library auto-configuration
Extensible frameworks
Fallback services
```

---

Avoid for:

```text id="b28"
Core business logic
Mandatory services
Critical system components
```

---

# Key Points To Remember

- `@ConditionalOnMissingBean` creates a bean only if no matching bean exists.
- Used for fallback or default implementations.
- Core part of Spring Boot auto-configuration.
- Enables extensibility in frameworks.
- Supports type and name-based matching.
- Prevents duplicate bean definitions.
- Common in libraries and starter modules.
- Works at application startup time.
- Helps override default behavior easily.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@ConditionalOnMissingBean` | Create fallback bean |
| `@ConditionalOnBean` | Create dependent bean |
| `@ConditionalOnProperty` | Property-based control |
| `@Conditional` | Custom logic |

---

# Interview Shortcut

```java id="c29"
@Bean
@ConditionalOnMissingBean(EmailService.class)
public EmailService defaultEmailService() {
    return new SmtpEmailService();
}
```

Meaning:

```text id="d30"
Create default bean only if no custom implementation exists

Enable override-friendly architecture

Support flexible Spring configuration
```

---

# Real Production Example

```text id="e31"
Spring Boot Microservice System
```

Flow:

```text id="f32"
Application Starts
     ↓
Check if custom beans exist
     ↓
If NOT → create default beans
     ↓
If YES → use custom beans
     ↓
Final application context ready
```

Result:

```text id="g33"
Flexible architecture
Easy customization
Clean auto-configuration
```

---

# Final Summary

`@ConditionalOnMissingBean` is a Spring Boot annotation used to create a default bean only when no other bean of the same type exists in the application context, enabling flexible, override-friendly, and extensible system design in enterprise applications and Spring Boot auto-configuration.
```