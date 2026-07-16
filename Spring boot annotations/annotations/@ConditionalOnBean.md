
## Definition

`@ConditionalOnBean` is a Spring Boot conditional annotation used to **create or enable a bean only if another specific bean already exists in the Spring Application Context**.

It tells Spring:

> “Only register this bean if the required bean is already present.”

Package:

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnBean;
```

---

# Simple Understanding

Normally Spring does:

```text id="a1"
Create all beans at startup
```

---

With `@ConditionalOnBean`:

```text id="b2"
Create this bean ONLY if another bean exists
```

---

So:

```text id="c3"
@ConditionalOnBean = Dependency-based bean creation
```

---

# Real-Life Analogy

Think of a system feature:

```text id="d4"
Payment Service → only enabled if Payment Gateway exists
Notification Service → only enabled if Email/SMS service exists
Caching Layer → only enabled if Cache Manager exists
```

---

That dependency check is:

```text id="e5"
@ConditionalOnBean
```

---

# Why Do We Need @ConditionalOnBean?

We use it to:

```text id="f6"
Enable dependent services only when required beans exist
Avoid null dependencies
Enable auto-configuration safely
Build modular systems
```

---

# Basic Example

```java id="g7"
@Configuration
public class AppConfig {

    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }

    @Bean
    @ConditionalOnBean(DataSource.class)
    public JdbcTemplate jdbcTemplate(DataSource ds) {
        return new JdbcTemplate(ds);
    }
}
```

---

# What Happens Here?

```text id="h8"
If DataSource exists → JdbcTemplate is created
If DataSource does NOT exist → JdbcTemplate is skipped
```

---

# Real Production Example: Cache Dependency

```java id="i9"
@Bean
public CacheManager cacheManager() {
    return new ConcurrentMapCacheManager();
}

@Bean
@ConditionalOnBean(CacheManager.class)
public CacheService cacheService(CacheManager cm) {
    return new CacheService(cm);
}
```

---

# How It Works Internally

```text id="j10"
Spring starts
   ↓
Creates beans
   ↓
Checks if required bean exists in context
   ↓
If YES → creates conditional bean
If NO → skips bean
```

---

# Matching Rules

You can specify:

## 1. By Class

```java id="k11"
@ConditionalOnBean(DataSource.class)
```

---

## 2. By Bean Name

```java id="l12"
@ConditionalOnBean(name = "dataSource")
```

---

## 3. Multiple Beans

```java id="m13"
@ConditionalOnBean({DataSource.class, EntityManager.class})
```

---

# Real Production Use Cases

## 1. Database Layer

```text id="n14"
Enable JPA only if DataSource exists
```

---

## 2. Cache Layer

```text id="o15"
Enable Redis service only if RedisTemplate exists
```

---

## 3. Messaging Systems

```text id="p16"
Enable Kafka listeners only if KafkaTemplate exists
```

---

## 4. Cloud Integrations

```text id="q17"
Enable AWS services only if AWS client bean exists
```

---

# Spring Boot Internal Usage

Spring Boot uses it in auto-configuration:

```text id="r18"
If DataSource bean exists → configure JPA
If RestTemplate exists → configure HTTP clients
```

---

# @ConditionalOnBean vs @ConditionalOnMissingBean

| Feature | @ConditionalOnBean | @ConditionalOnMissingBean |
|----------|--------------------|----------------------------|
| Condition | Bean must exist | Bean must NOT exist |
| Use Case | Dependency-based setup | Default fallback bean |
| Direction | Positive check | Negative check |

---

# Example: Fallback Mechanism

```java id="s19"
@Bean
@ConditionalOnMissingBean
public EmailService defaultEmailService() {
    return new SmtpEmailService();
}
```

Meaning:

```text id="t20"
If no EmailService exists → create default one
```

---

# Example: Dependency Chain

```java id="u21"
@Bean
public ServiceA serviceA() {
    return new ServiceA();
}

@Bean
@ConditionalOnBean(ServiceA.class)
public ServiceB serviceB(ServiceA a) {
    return new ServiceB(a);
}
```

---

# Real Architecture View

```text id="v22"
ServiceA exists
   ↓
ServiceB gets enabled
   ↓
ServiceC depends on ServiceB
   ↓
Chain builds dynamically
```

---

# Common Mistakes

## Mistake 1: Circular Dependency

```text id="w23"
Bean A depends on B, B depends on A → failure
```

---

## Mistake 2: Wrong Bean Name

```text id="x24"
Bean not found → condition fails silently
```

---

## Mistake 3: Overusing conditions

```text id="y25"
Too many conditional beans → hard to debug context
```

---

# Common Interview Questions

## What is @ConditionalOnBean?

Creates bean only if another bean exists.

---

## When is it used?

For dependency-based configuration.

---

## Difference from @ConditionalOnMissingBean?

OnBean = depends on presence  
MissingBean = fallback creation

---

## Where is it used in Spring Boot?

Auto-configuration modules.

---

## What happens if bean is not found?

Bean is NOT created.

---

# Enterprise Best Practice

Use `@ConditionalOnBean` for:

```text id="z26"
Optional dependencies
Modular architecture
Auto-configuration libraries
Integration-based services
```

---

Avoid for:

```text id="a27"
Core business logic
Mandatory services
Complex runtime decisions
```

---

# Key Points To Remember

- `@ConditionalOnBean` activates a bean only if another bean exists.
- Used for dependency-based configuration.
- Helps build modular and flexible systems.
- Common in Spring Boot auto-configuration.
- Supports class-based and name-based checks.
- Prevents bean initialization errors.
- Works at application startup.
- Often paired with @ConditionalOnMissingBean.
- Important for microservices and integrations.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@ConditionalOnBean` | Create bean if dependency exists |
| `@ConditionalOnMissingBean` | Create fallback bean |
| `@ConditionalOnProperty` | Property-based control |
| `@Conditional` | Custom logic |

---

# Interview Shortcut

```java id="b28"
@Bean
@ConditionalOnBean(DataSource.class)
public JdbcTemplate jdbcTemplate(DataSource ds) {
    return new JdbcTemplate(ds);
}
```

Meaning:

```text id="c29"
Create bean only if required dependency exists

Ensure safe initialization

Enable modular Spring configuration
```

---

# Real Production Example

```text id="d30"
Enterprise Microservice System
```

Flow:

```text id="e31"
Spring starts
     ↓
DataSource bean created
     ↓
JdbcTemplate enabled (because DataSource exists)
     ↓
Repository layer activated
     ↓
Service layer becomes functional
```

Result:

```text id="f32"
Safe dependency resolution
Modular architecture
Flexible auto-configuration
```

---

# Final Summary

`@ConditionalOnBean` is a Spring Boot annotation used to conditionally create beans only when a required dependency bean exists in the application context, enabling safe, modular, and dependency-aware configuration in enterprise applications.
```