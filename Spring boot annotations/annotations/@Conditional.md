
## Definition

`@Conditional` is a Spring Framework annotation used to **conditionally register a bean or configuration based on runtime conditions**.

It tells Spring:

> “Only create this bean if a specific condition is true.”

Package:

```java
import org.springframework.context.annotation.Conditional;
```

---

# Simple Understanding

Normally Spring does:

```text id="a1"
Always create beans at startup
```

---

With `@Conditional`:

```text id="b2"
Create bean only if condition matches
```

---

So:

```text id="c3"
@Conditional = IF-ELSE logic for Spring beans
```

---

# Real-Life Analogy

Think of an app feature:

```text id="d4"
Dark mode → enabled only if user prefers it
Payment gateway → enabled only in production
Mock service → enabled only in test environment
```

---

That decision-making logic is:

```text id="e5"
@Conditional
```

---

# Why Do We Need @Conditional?

We use it to:

```text id="f6"
Enable environment-specific beans
Load different implementations
Control feature toggles
Avoid unnecessary beans in certain environments
```

---

# Basic Example

```java id="g7"
@Configuration
public class AppConfig {

    @Bean
    @Conditional(MyCondition.class)
    public MyService myService() {
        return new MyService();
    }
}
```

---

# What is MyCondition?

You define a custom condition:

```java id="h8"
public class MyCondition implements Condition {

    @Override
    public boolean matches(
        ConditionContext context,
        AnnotatedTypeMetadata metadata) {

        return true; // or false based on logic
    }
}
```

---

# How It Works Internally

```text id="i9"
Spring starts
   ↓
Evaluates @Conditional
   ↓
Checks Condition.matches()
   ↓
If TRUE → Bean created
If FALSE → Bean skipped
```

---

# Real Production Example: Environment-based DB

```java id="j10"
@Bean
@Conditional(ProdDatabaseCondition.class)
public DataSource prodDataSource() {
    return new OracleDataSource();
}
```

---

```java id="k11"
@Bean
@Conditional(DevDatabaseCondition.class)
public DataSource devDataSource() {
    return new H2DataSource();
}
```

---

# Example Condition Classes

## Production Condition

```java id="l12"
public class ProdDatabaseCondition implements Condition {

    @Override
    public boolean matches(
        ConditionContext context,
        AnnotatedTypeMetadata metadata) {

        String env =
            context.getEnvironment()
                   .getProperty("env");

        return "prod".equals(env);
    }
}
```

---

## Development Condition

```java id="m13"
public class DevDatabaseCondition implements Condition {

    @Override
    public boolean matches(
        ConditionContext context,
        AnnotatedTypeMetadata metadata) {

        String env =
            context.getEnvironment()
                   .getProperty("env");

        return "dev".equals(env);
    }
}
```

---

# Spring Boot Uses @Conditional Internally

Spring Boot heavily uses it in:

```text id="n14"
AutoConfiguration
```

Example:

```text id="o15"
@ConditionalOnClass
@ConditionalOnMissingBean
@ConditionalOnProperty
```

---

# Common Built-in Conditional Annotations

## 1. @ConditionalOnProperty

```java id="p16"
@ConditionalOnProperty(name = "feature.cache", havingValue = "true")
```

---

## 2. @ConditionalOnClass

```java id="q17"
@ConditionalOnClass(name = "com.mysql.cj.jdbc.Driver")
```

---

## 3. @ConditionalOnMissingBean

```java id="r18"
@ConditionalOnMissingBean(MyService.class)
```

---

## 4. @ConditionalOnExpression

```java id="s19"
@ConditionalOnExpression("'${env}' == 'prod'")
```

---

# Real Production Use Cases

## 1. Feature Toggle

```text id="t20"
Enable payment gateway only in production
```

---

## 2. Database Switching

```text id="u21"
H2 for dev, Oracle for prod
```

---

## 3. Mock vs Real Services

```text id="v22"
Use mock email service in test environment
```

---

## 4. Cloud vs Local Setup

```text id="w23"
AWS services only when running in cloud
```

---

# @Conditional vs @Profile

| Feature | @Conditional | @Profile |
|----------|-------------|----------|
| Flexibility | High | Medium |
| Complexity | Custom logic | Simple env-based |
| Use Case | Advanced conditions | Environment switching |
| Control | Full Java logic | String-based |

---

# Example Using @Profile (Alternative)

```java id="x24"
@Profile("dev")
@Bean
public DataSource devDataSource() {
    return new H2DataSource();
}
```

---

# Common Mistakes

## Mistake 1: Complex logic in condition

```text id="y25"
Keep condition simple and fast
```

---

## Mistake 2: Not handling environment properly

```text id="z26"
Missing property → bean not created
```

---

## Mistake 3: Using it everywhere

```text id="a27"
Overuse makes configuration hard to debug
```

---

# Common Interview Questions

## What is @Conditional?

It allows bean creation based on runtime conditions.

---

## Where is it used?

Spring configuration and auto-configuration.

---

## What method is used internally?

Condition.matches()

---

## Difference from @Profile?

@Profile = environment-based shortcut  
@Conditional = full custom logic

---

## Does Spring Boot use it?

Yes, heavily in auto-configuration.

---

# Enterprise Best Practice

Use `@Conditional` for:

```text id="b28"
Feature toggles
Environment-based beans
Optional integrations
Library-based auto-config
```

---

Avoid for:

```text id="c29"
Business logic
Controller logic
Service workflows
```

---

# Key Points To Remember

- `@Conditional` controls bean creation dynamically.
- Based on custom logic (Condition interface).
- Evaluated during Spring startup.
- Returns true/false to include/exclude beans.
- Core to Spring Boot auto-configuration.
- Used for environment-based setups.
- More powerful than @Profile.
- Helps build flexible applications.
- Common in production systems.
- Important Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Conditional` | Custom bean creation logic |
| `@Profile` | Environment-based beans |
| `@ConditionalOnProperty` | Property-based control |
| `@ConditionalOnClass` | Class presence check |

---

# Interview Shortcut

```java id="d30"
@Bean
@Conditional(MyCondition.class)
public MyService myService() {
    return new MyService();
}
```

Meaning:

```text id="e31"
Create bean only if condition is true

Enable dynamic configuration

Control application behavior at startup
```

---

# Real Production Example

```text id="f32"
Enterprise Microservice System
```

Flow:

```text id="g33"
Spring Boot Starts
     ↓
Checks conditions
     ↓
Loads only required beans
     ↓
Skips unnecessary components
     ↓
Optimized application context
```

Result:

```text id="h34"
Flexible architecture
Environment-aware configuration
Optimized performance
```

---

# Final Summary

`@Conditional` is a Spring annotation used to control bean creation based on runtime conditions. It enables dynamic, flexible, and environment-aware configuration, forming the foundation of Spring Boot’s auto-configuration system.
```