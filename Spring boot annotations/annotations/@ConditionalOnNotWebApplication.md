
## Definition

`@ConditionalOnNotWebApplication` is a Spring Boot annotation used to **create or enable beans/configurations only when the application is NOT a web application**.

It is essentially the opposite of `@ConditionalOnWebApplication`.

It tells Spring:

> “Enable this bean only for non-web applications (like batch, CLI, background services).”

Package:

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnNotWebApplication;
```

---

# Simple Understanding

Normally Spring Boot does:

```text id="a1"
Load all beans regardless of app type
```

---

With `@ConditionalOnNotWebApplication`:

```text id="b2"
Load bean only if application is NOT a web app
```

---

So:

```text id="c3"
@ConditionalOnNotWebApplication = Non-web environment detection
```

---

# Real-Life Analogy

Think of application modes:

```text id="d4"
Web app → REST APIs, Controllers, Filters
Batch app → Scheduled jobs, file processing
CLI tool → Command execution utilities
```

---

That separation is:

```text id="e5"
@ConditionalOnNotWebApplication
```

---

# Why Do We Need @ConditionalOnNotWebApplication?

We use it for:

```text id="f6"
Batch processing applications
CLI tools
Background jobs
Disabling web-only beans
```

---

# Basic Example

```java id="g7"
@Configuration
@ConditionalOnNotWebApplication
public class BatchConfig {

    @Bean
    public JobProcessor jobProcessor() {
        return new FileJobProcessor();
    }
}
```

---

# What Happens Here?

```text id="h8"
If NOT a web application → bean is created
If web application → configuration is skipped
```

---

# Types of Applications (Context)

## 1. Web Application

```text id="i9"
Spring MVC / WebFlux apps
```

---

## 2. Non-Web Application

```text id="j10"
Batch jobs, scheduled tasks, CLI tools, microservices without HTTP layer
```

---

# How It Works Internally

```text id="k11"
Spring Boot starts
   ↓
Detects application type
   ↓
Is it NOT a web app?
   ↓
If YES → register beans
If NO → skip configuration
```

---

# Real Production Example: Batch Processing

```java id="l12"
@Configuration
@ConditionalOnNotWebApplication
public class BatchJobConfig {

    @Bean
    public JobRunner jobRunner() {
        return new CsvJobRunner();
    }
}
```

---

# Real Production Example: CLI Tool

```java id="m13"
@Configuration
@ConditionalOnNotWebApplication
public class CliConfig {

    @Bean
    public CommandExecutor executor() {
        return new FileCommandExecutor();
    }
}
```

---

# Real Use Cases

## 1. Batch Systems

```text id="n14"
ETL pipelines, file processing jobs
```

---

## 2. CLI Applications

```text id="o15"
Command-line tools built using Spring Boot
```

---

## 3. Background Workers

```text id="p16"
Non-HTTP microservices
```

---

## 4. Scheduler-only Services

```text id="q17"
Apps that only run scheduled jobs (no REST API)
```

---

# Real Architecture View

```text id="r18"
Application Starts
     ↓
Detect app type
     ↓
If web app → skip non-web beans
If non-web app → enable batch/CLI beans
     ↓
Context initialized accordingly
```

---

# @ConditionalOnNotWebApplication vs @ConditionalOnWebApplication

| Feature | OnWebApplication | OnNotWebApplication |
|----------|------------------|---------------------|
| Condition | Web app exists | Web app NOT exists |
| Use Case | REST APIs, MVC, WebFlux | Batch, CLI, workers |
| Beans loaded | Web components | Non-web components |

---

# Real Production Scenario

## Microservice System Split

```text id="s19"
Service A → REST API (Web Application)
Service B → Batch processing (Non-Web Application)
```

---

## Behavior

### Service A

```text id="t20"
@ConditionalOnWebApplication → active
@ConditionalOnNotWebApplication → skipped
```

---

### Service B

```text id="u21"
@ConditionalOnWebApplication → skipped
@ConditionalOnNotWebApplication → active
```

---

# Common Mistakes

## Mistake 1: Using in web apps accidentally

```text id="v22"
Bean never loads in MVC applications
```

---

## Mistake 2: Misunderstanding "non-web"

```text id="w23"
It does NOT mean "no HTTP calls at runtime"
It means no web server at all
```

---

## Mistake 3: Mixing both annotations incorrectly

```text id="x24"
Confusing conditions can make beans unpredictable
```

---

# Common Interview Questions

## What is @ConditionalOnNotWebApplication?

Creates beans only if the app is NOT a web application.

---

## Where is it used?

Batch apps, CLI tools, background services.

---

## Difference from @ConditionalOnWebApplication?

One enables web beans, the other disables them.

---

## When is it evaluated?

At application startup.

---

## What types of apps are considered non-web?

Batch jobs, CLI apps, worker services.

---

# Enterprise Best Practice

Use `@ConditionalOnNotWebApplication` for:

```text id="y25"
Batch processing systems
CLI tools
Background workers
Non-HTTP microservices
```

---

Avoid for:

```text id="z26"
Web applications
Core business logic
Shared services without clear separation
```

---

# Key Points To Remember

- `@ConditionalOnNotWebApplication` activates beans only in non-web applications.
- Opposite of `@ConditionalOnWebApplication`.
- Used for batch, CLI, and background services.
- Helps separate web and non-web architectures.
- Evaluated at application startup.
- Common in Spring Boot auto-configuration.
- Prevents unnecessary web dependencies.
- Improves modular design.
- Supports multiple deployment models.
- Important Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| @ConditionalOnWebApplication | Enable web apps only |
| @ConditionalOnNotWebApplication | Enable non-web apps only |
| @ConditionalOnClass | Dependency-based |
| @ConditionalOnProperty | Config-based |

---

# Interview Shortcut

```java id="a27"
@Configuration
@ConditionalOnNotWebApplication
public class BatchConfig {

    @Bean
    public JobProcessor jobProcessor() {
        return new CsvJobProcessor();
    }
}
```

Meaning:

```text id="b28"
Enable configuration only when running outside web environment

Supports batch/CLI architecture

Ensures proper separation of application types
```

---

# Real Production Example

```text id="c29"
Enterprise System
```

Flow:

```text id="d30"
Application Starts
     ↓
Detect application type
     ↓
If web app → skip batch configs
If non-web app → enable batch/worker configs
     ↓
System runs in correct mode
```

Result:

```text id="e31"
Clean separation of web vs non-web systems
Flexible deployment architecture
Optimized resource usage
```

---

# Final Summary

`@ConditionalOnNotWebApplication` is a Spring Boot annotation used to conditionally enable beans only when the application is NOT running as a web application, enabling clean separation between web-based and non-web (batch/CLI/worker) systems.
```