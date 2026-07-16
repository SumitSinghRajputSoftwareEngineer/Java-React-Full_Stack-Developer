
## Definition

`@ConditionalOnResource` is a Spring Boot annotation used to **create or enable a bean/configuration only if a specific resource exists in the classpath or filesystem**.

It tells Spring:

> “Activate this bean only if a required file/resource is available.”

Package:

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnResource;
```

---

# Simple Understanding

Normally Spring does:

```text id="a1"
Load all beans regardless of external files
```

---

With `@ConditionalOnResource`:

```text id="b2"
Load bean only if a required resource file exists
```

---

So:

```text id="c3"
@ConditionalOnResource = File/resource-based bean activation
```

---

# Real-Life Analogy

Think of application features:

```text id="d4"
Logging config → enabled only if logback.xml exists
Security config → enabled only if security.properties exists
Mail service → enabled only if mail templates exist
```

---

That file-based decision is:

```text id="e5"
@ConditionalOnResource
```

---

# Why Do We Need @ConditionalOnResource?

We use it for:

```text id="f6"
Optional configuration files
External template-based features
Safe initialization of file-dependent services
Avoiding missing-file errors
```

---

# Basic Example

```java id="g7"
@Configuration
@ConditionalOnResource(resources = "classpath:mail.properties")
public class MailConfig {

    @Bean
    public MailService mailService() {
        return new SmtpMailService();
    }
}
```

---

# What Happens Here?

```text id="h8"
If mail.properties exists → MailService bean created
If NOT exists → configuration skipped
```

---

# Resource Types Supported

## 1. Classpath Resource

```java id="i9"
@ConditionalOnResource("classpath:config/app.properties")
```

---

## 2. File System Resource

```java id="j10"
@ConditionalOnResource("file:/opt/config/app.properties")
```

---

## 3. Multiple Resources

```java id="k11"
@ConditionalOnResource({
    "classpath:db.properties",
    "classpath:cache.properties"
})
```

---

# How It Works Internally

```text id="l12"
Spring Boot starts
   ↓
Checks resource existence
   ↓
If file exists → register bean
If file missing → skip bean
```

---

# Real Production Example: Logging System

```java id="m13"
@Configuration
@ConditionalOnResource("classpath:logback-spring.xml")
public class LoggingConfig {

    @Bean
    public Logger logger() {
        return LoggerFactory.getLogger("APP");
    }
}
```

---

# Real Production Example: Mail Templates

```java id="n14"
@Configuration
@ConditionalOnResource("classpath:templates/email-template.html")
public class EmailTemplateConfig {

    @Bean
    public TemplateEngine templateEngine() {
        return new TemplateEngine();
    }
}
```

---

# Real Production Example: Security Config

```java id="o15"
@Configuration
@ConditionalOnResource("classpath:security.properties")
public class SecurityConfig {
    // security beans
}
```

---

# Real Use Cases

## 1. Template-Based Systems

```text id="p16"
Enable feature only if template file exists
```

---

## 2. Optional Configurations

```text id="q17"
Load DB config only if db.properties exists
```

---

## 3. Multi-Environment Support

```text id="r18"
Different configs per deployment environment
```

---

## 4. Plugin Systems

```text id="s19"
Enable plugin only if plugin config file is present
```

---

# Real Architecture View

```text id="t20"
Application Starts
     ↓
Check resource files
     ↓
If file exists → enable module
If file missing → skip module
     ↓
Application runs safely
```

---

# @ConditionalOnResource vs @ConditionalOnClass

| Feature | OnResource | OnClass |
|----------|-----------|--------|
| Checks | File existence | Class in classpath |
| Level | External config/files | Library dependency |
| Use Case | Config/template-based | Dependency-based |

---

# @ConditionalOnResource vs @ConditionalOnProperty

| Feature | OnResource | OnProperty |
|----------|------------|-------------|
| Based on | File existence | Config value |
| Example | logback.xml exists | cache.enabled=true |

---

# Common Mistakes

## Mistake 1: Wrong path

```text id="u21"
classpath vs file path confusion
```

---

## Mistake 2: Missing resource packaging

```text id="v22"
File exists in code but not in build output
```

---

## Mistake 3: Assuming runtime reload

```text id="w23"
Condition is evaluated only at startup
```

---

# Common Interview Questions

## What is @ConditionalOnResource?

Creates bean only if a resource exists.

---

## Where is it used?

Spring Boot auto-configuration and optional configs.

---

## What types of resources are supported?

Classpath and filesystem resources.

---

## Difference from @ConditionalOnClass?

OnClass → Java class  
OnResource → file/resource

---

## When is it evaluated?

During application startup.

---

# Enterprise Best Practice

Use `@ConditionalOnResource` for:

```text id="x24"
Optional configuration files
Template-based modules
Plugin-based architecture
Environment-specific resources
```

---

Avoid for:

```text id="y25"
Core business logic
Mandatory configurations
Runtime dynamic decisions
```

---

# Key Points To Remember

- `@ConditionalOnResource` activates beans only if a file/resource exists.
- Works with classpath and filesystem resources.
- Useful for optional configuration modules.
- Prevents missing file errors.
- Evaluated at startup time.
- Common in Spring Boot auto-configuration.
- Helps build plugin-like systems.
- Supports flexible deployment setups.
- File-based conditional logic.
- Important Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| @ConditionalOnResource | File/resource-based activation |
| @ConditionalOnClass | Class-based activation |
| @ConditionalOnProperty | Config-based activation |
| @ConditionalOnBean | Bean-based activation |

---

# Interview Shortcut

```java id="z26"
@Configuration
@ConditionalOnResource("classpath:mail.properties")
public class MailConfig {

    @Bean
    public MailService mailService() {
        return new SmtpMailService();
    }
}
```

Meaning:

```text id="a27"
Enable configuration only if required file exists

Supports file-driven feature activation

Ensures safe initialization of resource-dependent beans
```

---

# Real Production Example

```text id="b28"
Enterprise Microservice System
```

Flow:

```text id="c29"
Application Starts
     ↓
Check required config files
     ↓
If present → enable module
If missing → skip module
     ↓
Final application context is stable and flexible
```

Result:

```text id="d30"
Flexible architecture
Safe file-dependent configuration
Plugin-style system behavior
```

---

# Final Summary

`@ConditionalOnResource` is a Spring Boot annotation used to conditionally load beans or configurations only when a specific resource (file or classpath resource) exists, enabling flexible, file-driven, and modular application design in enterprise systems.
```