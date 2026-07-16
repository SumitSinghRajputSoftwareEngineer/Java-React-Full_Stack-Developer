
## Definition

`@ConditionalOnWebApplication` is a Spring Boot annotation used to **enable or register beans/configurations only if the application is a web application** (Servlet-based or Reactive-based).

It tells Spring:

> “Create this bean only if the application is running in a web environment.”

Package:

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnWebApplication;
```

---

# Simple Understanding

Normally Spring does:

```text id="a1"
Load all beans regardless of app type
```

---

With `@ConditionalOnWebApplication`:

```text id="b2"
Load bean only if it's a web application (Spring MVC / WebFlux)
```

---

So:

```text id="c3"
@ConditionalOnWebApplication = Web-environment detection condition
```

---

# Real-Life Analogy

Think of application features:

```text id="d4"
Login page → only exists in web apps
REST APIs → only exist in server apps
Console logging tools → not needed in web UI apps
```

---

That environment-based activation is:

```text id="e5"
@ConditionalOnWebApplication
```

---

# Why Do We Need @ConditionalOnWebApplication?

We use it for:

```text id="f6"
Web-specific configurations
Servlet/Controller setup
WebFlux or MVC auto configuration
Avoid loading web beans in batch/CLI apps
```

---

# Basic Example

```java id="g7"
@Configuration
@ConditionalOnWebApplication
public class WebConfig {

    @Bean
    public WebLoggingFilter loggingFilter() {
        return new WebLoggingFilter();
    }
}
```

---

# What Happens Here?

```text id="h8"
If app is a web application → bean is created
If NOT web app → bean is skipped
```

---

# Types of Web Applications

Spring Boot supports:

## 1. Servlet-based Web App (Spring MVC)

```text id="i9"
Traditional REST APIs using Tomcat/Jetty
```

---

## 2. Reactive Web App (WebFlux)

```text id="j10"
Non-blocking reactive applications
```

---

# Advanced Usage

## 1. Servlet-based only

```java id="k11"
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
```

---

## 2. Reactive only

```java id="l12"
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.REACTIVE)
```

---

## 3. Both (default)

```java id="m13"
@ConditionalOnWebApplication
```

---

# How It Works Internally

```text id="n14"
Spring Boot starts
   ↓
Detects application type
   ↓
Is it web application?
   ↓
If YES → register beans
If NO → skip configuration
```

---

# Real Production Example: Web Logging

```java id="o15"
@Configuration
@ConditionalOnWebApplication
public class RequestLoggingConfig {

    @Bean
    public Filter loggingFilter() {
        return new RequestLoggingFilter();
    }
}
```

---

# Real Production Example: MVC Configuration

```java id="p16"
@Configuration
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
public class MvcConfig {

    @Bean
    public ViewResolver viewResolver() {
        return new ThymeleafViewResolver();
    }
}
```

---

# Real Production Example: WebFlux Configuration

```java id="q17"
@Configuration
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.REACTIVE)
public class ReactiveConfig {

    @Bean
    public RouterFunction<?> routes() {
        return RouterFunctions.route();
    }
}
```

---

# Real Use Cases

## 1. Web-only Filters

```text id="r18"
Logging, authentication, request tracing
```

---

## 2. Controller-related Beans

```text id="s19"
Only needed in REST applications
```

---

## 3. UI/Template Engines

```text id="t20"
Thymeleaf, JSP configuration
```

---

## 4. Web Security

```text id="u21"
Spring Security web filters
```

---

# Real Architecture View

```text id="v22"
Application Starts
     ↓
Detect application type
     ↓
If web app → enable web beans
If non-web app → skip them
     ↓
Context is optimized
```

---

# @ConditionalOnWebApplication vs @ConditionalOnClass

| Feature | OnWebApplication | OnClass |
|----------|------------------|--------|
| Checks | Application type | Class presence |
| Level | Runtime app environment | Dependency level |
| Use Case | Web vs non-web apps | Optional libraries |

---

# @ConditionalOnWebApplication vs @ConditionalOnProperty

| Feature | OnWebApplication | OnProperty |
|----------|------------------|-------------|
| Based on | App type | Config value |
| Control | Environment | External settings |

---

# Common Mistakes

## Mistake 1: Using in non-web projects

```text id="w23"
Bean never gets created in batch apps
```

---

## Mistake 2: Assuming it checks HTTP request

```text id="x24"
It checks app type, not runtime requests
```

---

## Mistake 3: Confusing servlet vs reactive

```text id="y25"
Wrong type → configuration skipped
```

---

# Common Interview Questions

## What is @ConditionalOnWebApplication?

Creates beans only if app is a web application.

---

## What types are supported?

Servlet and Reactive.

---

## When is it evaluated?

At application startup.

---

## Difference from @ConditionalOnClass?

Web app check vs class existence check.

---

## Why is it used?

To separate web and non-web configurations.

---

# Enterprise Best Practice

Use `@ConditionalOnWebApplication` for:

```text id="z26"
Web filters
Controller-related configs
Security layers
Web MVC / WebFlux setups
```

---

Avoid for:

```text id="a27"
Core business logic
Backend services independent of web
Batch processing systems
```

---

# Key Points To Remember

- `@ConditionalOnWebApplication` enables beans only in web applications.
- Supports Servlet and Reactive types.
- Used for web-specific configurations.
- Prevents loading unnecessary web beans in non-web apps.
- Evaluated at startup time.
- Common in Spring Boot auto-configuration.
- Helps separate web vs non-web logic.
- Improves modular architecture.
- Ensures safe deployment across app types.
- Important Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| @ConditionalOnWebApplication | Web app detection |
| @ConditionalOnClass | Dependency detection |
| @ConditionalOnBean | Bean detection |
| @ConditionalOnProperty | Config-based control |

---

# Interview Shortcut

```java id="b28"
@Configuration
@ConditionalOnWebApplication
public class WebConfig {

    @Bean
    public Filter loggingFilter() {
        return new RequestLoggingFilter();
    }
}
```

Meaning:

```text id="c29"
Enable configuration only if application is running in web mode

Support MVC or WebFlux environments

Ensure web-specific beans are loaded safely
```

---

# Real Production Example

```text id="d30"
Enterprise Microservice System
```

Flow:

```text id="e31"
Application Starts
     ↓
Detect web environment
     ↓
If web app → enable controllers and filters
If not web app → skip web configs
     ↓
Application runs in correct mode
```

Result:

```text id="f32"
Environment-aware architecture
Clean separation of concerns
Optimized application startup
```

---

# Final Summary

`@ConditionalOnWebApplication` is a Spring Boot annotation used to conditionally enable beans or configurations only when the application is running as a web application (Servlet or Reactive), enabling environment-aware and modular web architecture.
```