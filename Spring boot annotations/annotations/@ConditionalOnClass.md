
## Definition

`@ConditionalOnClass` is a Spring Boot annotation used to **create or enable a bean/configuration only if a specific class is present in the classpath**.

It tells Spring:

> “Only activate this bean if the required library/class exists in the project.”

Package:

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
```

---

# Simple Understanding

Normally Spring does:

```text id="a1"
Load all beans regardless of libraries
```

---

With `@ConditionalOnClass`:

```text id="b2"
Load bean ONLY if a specific dependency is available in classpath
```

---

So:

```text id="c3"
@ConditionalOnClass = Dependency-based feature activation
```

---

# Real-Life Analogy

Think of a mobile app:

```text id="d4"
Camera feature → works only if camera hardware exists
Payment feature → works only if payment SDK is installed
Analytics → works only if analytics library is present
```

---

That dependency check is:

```text id="e5"
@ConditionalOnClass
```

---

# Why Do We Need @ConditionalOnClass?

We use it for:

```text id="f6"
Optional integrations
Auto-configuration in Spring Boot
Avoid ClassNotFoundException
Enable features based on libraries
```

---

# Basic Example

```java id="g7"
@Configuration
@ConditionalOnClass(name = "com.mysql.cj.jdbc.Driver")
public class MySqlConfig {

    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }
}
```

---

# What Happens Here?

```text id="h8"
If MySQL driver is present → DataSource bean created
If NOT present → configuration ignored
```

---

# Real Production Example: Redis

```java id="i9"
@Configuration
@ConditionalOnClass(name = "org.springframework.data.redis.core.RedisTemplate")
public class RedisConfig {

    @Bean
    public CacheService cacheService() {
        return new RedisCacheService();
    }
}
```

---

# How It Works Internally

```text id="j10"
Spring Boot starts
   ↓
Checks classpath for required class
   ↓
If class exists → register beans
If class missing → skip configuration
```

---

# Two Ways to Use It

## 1. By Class Reference (Recommended)

```java id="k11"
@ConditionalOnClass(RedisTemplate.class)
```

---

## 2. By Class Name (String-based)

```java id="l12"
@ConditionalOnClass(name = "org.redis.RedisTemplate")
```

---

# Real Production Use Cases

## 1. Database Auto Configuration

```text id="m13"
Enable JPA only if Hibernate is present
Enable MySQL config only if driver exists
```

---

## 2. Cache Systems

```text id="n14"
Enable Redis caching only if Redis library exists
```

---

## 3. Messaging Systems

```text id="o15"
Enable Kafka listener only if Kafka classes exist
```

---

## 4. Cloud Integrations

```text id="p16"
Enable AWS services only if AWS SDK is present
```

---

# Spring Boot Internal Usage

Spring Boot heavily uses it:

```text id="q17"
AutoConfiguration classes
```

Example:

```text id="r18"
If Tomcat class exists → embedded server enabled
If Jackson class exists → JSON auto-config enabled
```

---

# @ConditionalOnClass vs @ConditionalOnBean

| Feature | OnClass | OnBean |
|----------|--------|--------|
| Checks | Class in classpath | Bean in Spring context |
| Level | Compile/library level | Runtime Spring level |
| Use Case | Library availability | Bean dependency |

---

# @ConditionalOnClass vs @ConditionalOnMissingClass

| Feature | OnClass | OnMissingClass |
|----------|--------|----------------|
| Condition | Class exists | Class does NOT exist |
| Use Case | Enable feature | Disable feature if library present |

---

# Real Architecture View

```text id="s19"
Project Starts
   ↓
Check dependencies (classpath)
   ↓
If library exists → enable feature
   ↓
If not → skip configuration
   ↓
Application starts safely
```

---

# Common Mistakes

## Mistake 1: Wrong class name

```text id="t20"
Typo → condition fails silently
```

---

## Mistake 2: Confusing with @ConditionalOnBean

```text id="u21"
Class ≠ Bean
```

---

## Mistake 3: Overusing conditions

```text id="v22"
Too many conditions → complex debugging
```

---

# Common Interview Questions

## What is @ConditionalOnClass?

It enables bean only if a class exists in classpath.

---

## Why is it used?

For optional dependencies and auto-configuration.

---

## Difference from @ConditionalOnBean?

OnClass → checks library  
OnBean → checks Spring bean

---

## What happens if class is missing?

Bean/configuration is skipped safely.

---

## Where is it used?

Spring Boot auto-configuration.

---

# Enterprise Best Practice

Use `@ConditionalOnClass` for:

```text id="w23"
Optional integrations
Library-based features
Auto-configuration modules
Plugin-based systems
```

---

Avoid for:

```text id="x24"
Core business logic
Mandatory services
Critical workflows
```

---

# Key Points To Remember

- `@ConditionalOnClass` activates configuration only if a class exists in classpath.
- Prevents ClassNotFoundException.
- Core to Spring Boot auto-configuration.
- Used for optional features and integrations.
- Works at compile/runtime dependency level.
- Supports both class reference and class name.
- Enables modular architecture.
- Common in starter libraries.
- Improves application flexibility.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@ConditionalOnClass` | Enable if class exists |
| `@ConditionalOnBean` | Enable if bean exists |
| `@ConditionalOnMissingBean` | Provide fallback bean |
| `@ConditionalOnProperty` | Property-based control |

---

# Interview Shortcut

```java id="y25"
@Configuration
@ConditionalOnClass(RedisTemplate.class)
public class RedisConfig {

    @Bean
    public CacheService cacheService() {
        return new RedisCacheService();
    }
}
```

Meaning:

```text id="z26"
Enable configuration only if required library is available

Support optional feature activation

Ensure safe startup without missing dependencies
```

---

# Real Production Example

```text id="a27"
Enterprise Microservice System
```

Flow:

```text id="b28"
Application starts
     ↓
Check if Redis library exists
     ↓
If YES → enable caching
If NO → skip caching config
     ↓
Application runs safely in both cases
```

Result:

```text id="c29"
Flexible architecture
Optional integrations
Safe dependency handling
```

---

# Final Summary

`@ConditionalOnClass` is a Spring Boot annotation used to conditionally load beans or configurations only when a specific class exists in the classpath, enabling safe, modular, and dependency-aware auto-configuration in enterprise applications.
```