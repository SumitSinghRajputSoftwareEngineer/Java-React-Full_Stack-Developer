
## Definition

`@ConditionalOnExpression` is a Spring Boot annotation used to **enable or disable a bean/configuration based on a Spring Expression Language (SpEL) condition**.

It tells Spring:

> “Create this bean only if the given SpEL expression evaluates to true.”

Package:

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnExpression;
```

---

# Simple Understanding

Normally Spring does:

```text id="a1"
Always create beans at startup
```

---

With `@ConditionalOnExpression`:

```text id="b2"
Create bean only if SpEL expression returns true
```

---

So:

```text id="c3"
@ConditionalOnExpression = Dynamic logic-based bean control using SpEL
```

---

# Real-Life Analogy

Think of an app feature system:

```text id="d4"
Enable feature only if:
- environment is prod AND
- cache is enabled AND
- user role is admin
```

---

That complex rule evaluation is:

```text id="e5"
@ConditionalOnExpression
```

---

# Why Do We Need @ConditionalOnExpression?

We use it for:

```text id="f6"
Complex conditional logic
Combining multiple properties
Runtime decision making
Advanced feature toggles
```

---

# Basic Example

```java id="g7"
@Bean
@ConditionalOnExpression("'${feature.cache.enabled}' == 'true'")
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
If expression evaluates to true → bean created
If false → bean skipped
```

---

# SpEL Power (Key Feature)

Spring Expression Language allows:

- Property checks
- Logical operations
- Comparisons
- Method calls
- Environment access

---

# Advanced Examples

## 1. AND Condition

```java id="j10"
@ConditionalOnExpression(
    "'${cache.enabled}' == 'true' && '${env}' == 'prod'"
)
```

---

## 2. OR Condition

```java id="k11"
@ConditionalOnExpression(
    "'${feature.a}' == 'true' || '${feature.b}' == 'true'"
)
```

---

## 3. Numeric Comparison

```java id="l12"
@ConditionalOnExpression("${app.version} >= 2")
```

---

## 4. Environment-Based

```java id="m13"
@ConditionalOnExpression(
    "'${spring.profiles.active}' == 'prod'"
)
```

---

# How It Works Internally

```text id="n14"
Spring Boot starts
   ↓
Reads properties
   ↓
Evaluates SpEL expression
   ↓
If TRUE → register bean
If FALSE → skip bean
```

---

# Real Production Example: Multi-Feature System

```java id="o15"
@Bean
@ConditionalOnExpression(
    "'${payment.enabled}' == 'true' && '${region}' == 'IN'"
)
public PaymentService paymentService() {
    return new RazorpayService();
}
```

---

# Real Production Example: Logging Control

```java id="p16"
@Bean
@ConditionalOnExpression("'${logging.debug}' == 'true'")
public DebugLogger debugLogger() {
    return new DebugLogger();
}
```

---

# Real Production Example: Cache Strategy

```java id="q17"
@Bean
@ConditionalOnExpression(
    "'${cache.type}' == 'redis' && '${cache.enabled}' == 'true'"
)
public CacheService redisCache() {
    return new RedisCacheService();
}
```

---

# Real Use Cases

## 1. Feature Flags

```text id="r18"
Enable features dynamically using expressions
```

---

## 2. Multi-condition Configuration

```text id="s19"
Combine environment + property + flag logic
```

---

## 3. Region-based Features

```text id="t20"
Enable services only for specific regions
```

---

## 4. Advanced Auto Configuration

```text id="u21"
Spring Boot internal conditional logic
```

---

# Real Architecture View

```text id="v22"
Application Starts
     ↓
Load properties
     ↓
Evaluate SpEL expression
     ↓
If TRUE → enable bean
If FALSE → skip bean
     ↓
Final application context ready
```

---

# @ConditionalOnExpression vs Other Conditionals

| Annotation | Condition Type |
|------------|----------------|
| @ConditionalOnExpression | SpEL-based logic |
| @ConditionalOnProperty | Single property check |
| @ConditionalOnClass | Class existence |
| @ConditionalOnBean | Bean existence |

---

# Pros

```text id="w23"
Very flexible
Supports complex logic
Combines multiple conditions
No Java code required
```

---

# Cons

```text id="x24"
Hard to debug
Can become unreadable
Errors only at startup
SpEL syntax sensitive
```

---

# Common Mistakes

## Mistake 1: Incorrect SpEL syntax

```text id="y25"
Small typo → bean not created
```

---

## Mistake 2: Overly complex expressions

```text id="z26"
Hard to maintain in large systems
```

---

## Mistake 3: Assuming runtime change works

```text id="a27"
Expression evaluated only at startup
```

---

# Common Interview Questions

## What is @ConditionalOnExpression?

Enables beans based on SpEL expression.

---

## Why is it used?

For complex conditional logic.

---

## What language does it use?

Spring Expression Language (SpEL).

---

## Difference from @ConditionalOnProperty?

Property = simple key-value  
Expression = complex logic

---

## When is it evaluated?

At application startup.

---

# Enterprise Best Practice

Use `@ConditionalOnExpression` for:

```text id="b28"
Complex feature toggles
Multi-condition logic
Advanced configuration rules
Framework-level auto-configuration
```

---

Avoid for:

```text id="c29"
Simple properties (use @ConditionalOnProperty instead)
Core business logic
Highly complex expressions
```

---

# Key Points To Remember

- `@ConditionalOnExpression` uses Spring Expression Language (SpEL).
- Enables beans based on complex conditions.
- Supports logical, relational, and property-based rules.
- Evaluated at startup time.
- Very flexible but harder to maintain.
- Useful for advanced feature toggles.
- Common in Spring Boot auto-configuration.
- Can combine multiple conditions.
- Should be used carefully in production.
- Important advanced Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| @ConditionalOnExpression | Complex SpEL logic |
| @ConditionalOnProperty | Simple config check |
| @ConditionalOnClass | Dependency check |
| @ConditionalOnBean | Spring context check |

---

# Interview Shortcut

```java id="d30"
@Bean
@ConditionalOnExpression(
    "'${cache.enabled}' == 'true' && '${env}' == 'prod'"
)
public CacheService cacheService() {
    return new RedisCacheService();
}
```

Meaning:

```text id="e31"
Enable bean only if complex conditions are satisfied

Supports advanced feature toggling

Allows dynamic configuration logic
```

---

# Real Production Example

```text id="f32"
Enterprise Microservice System
```

Flow:

```text id="g33"
Application Starts
     ↓
Evaluate SpEL conditions
     ↓
If all conditions match → enable feature
If not → skip feature
     ↓
System adapts dynamically
```

Result:

```text id="h34"
Highly flexible configuration system
Advanced runtime control
Powerful feature management
```

---

# Final Summary

`@ConditionalOnExpression` is a Spring Boot annotation used to conditionally enable beans using Spring Expression Language (SpEL), allowing complex, dynamic, and multi-condition feature control in enterprise applications.
```