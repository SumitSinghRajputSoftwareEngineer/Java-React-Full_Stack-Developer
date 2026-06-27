
## Definition

`@Lazy` is used to enable lazy initialization of Spring Beans.

It tells Spring:

> Do not create this bean during application startup. Create it only when it is first requested.

By default, Spring uses:

```text
Eager Initialization
```

meaning singleton beans are created when the application starts.

With:

```java
@Lazy
```

bean creation is postponed until the bean is actually needed.

---

# Why Do We Need @Lazy?

Suppose we have:

```java
@Service
public class ReportService {

    public ReportService() {
        System.out.println("ReportService Created");
    }

}
```

Even if nobody uses:

```java
ReportService
```

Spring creates it during startup.

For large enterprise applications with:

- Hundreds of beans
- Heavy initialization logic
- Expensive database connections
- Third-party integrations

startup time can become slow.

Solution:

```java
@Lazy
```

---

# Default Spring Behavior (Eager Initialization)

```java
@Service
public class EmployeeService {

    public EmployeeService() {
        System.out.println("EmployeeService Created");
    }

}
```

Application Startup:

```text
EmployeeService Created
```

Bean is created immediately.

---

# Basic Syntax

```java
@Service
@Lazy
public class EmployeeService {

}
```

Now Spring waits until the bean is first accessed.

---

# Example

```java
@Service
@Lazy
public class EmployeeService {

    public EmployeeService() {
        System.out.println("Bean Created");
    }

}
```

---

Application starts.

Output:

```text
(No Output)
```

Bean not created yet.

---

First usage:

```java
@Autowired
private EmployeeService employeeService;
```

or

```java
context.getBean(EmployeeService.class);
```

Output:

```text
Bean Created
```

---

# What Happens Internally?

Without:

```java
@Lazy
```

Startup:

```text
Create Bean Definition
       │
       ▼
Instantiate Bean
       │
       ▼
Inject Dependencies
       │
       ▼
Bean Ready
```

---

With:

```java
@Lazy
```

Startup:

```text
Create Bean Definition
       │
       ▼
Store Metadata
       │
       ▼
Wait Until First Usage
       │
       ▼
Instantiate Bean
       │
       ▼
Inject Dependencies
       │
       ▼
Bean Ready
```

---

# @Lazy on Component Classes

```java
@Service
@Lazy
public class NotificationService {

}
```

Bean is created only when needed.

---

# @Lazy on @Bean Methods

Very common.

---

```java
@Configuration
public class AppConfig {

    @Bean
    @Lazy
    public EmployeeService employeeService() {

        return new EmployeeService();

    }

}
```

Spring delays bean creation.

---

# Example

```java
@Bean
@Lazy
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

Bean created only when injected.

---

# @Lazy on @Configuration Class

Entire configuration becomes lazy.

---

```java
@Configuration
@Lazy
public class AppConfig {

    @Bean
    public EmployeeService employeeService() {
        return new EmployeeService();
    }

}
```

All beans inside become lazy by default.

---

# Lazy Initialization and Dependency Injection

Important interview topic.

---

## Scenario

```java
@Service
@Lazy
public class EmployeeService {

}
```

---

```java
@RestController
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;

}
```

Question:

Will EmployeeService still be lazy?

Answer:

```text
No
```

because controller requires it immediately.

Spring creates it during dependency injection.

---

# Real Lazy Injection

Use:

```java
@Lazy
@Autowired
private EmployeeService employeeService;
```

---

or

```java
private final EmployeeService employeeService;

public EmployeeController(
        @Lazy EmployeeService employeeService) {

    this.employeeService = employeeService;
}
```

Spring injects a proxy instead of the actual object.

Actual bean is created later.

---

# Proxy-Based Lazy Loading

Very important concept.

---

Example:

```java
@Service
@Lazy
public class EmployeeService {

}
```

Spring does NOT inject actual bean.

Instead:

```java
EmployeeService$$SpringProxy
```

is injected.

---

When first method is called:

```java
employeeService.getEmployee();
```

proxy creates actual bean.

---

Flow:

```text
Application Start
        │
        ▼
Create Proxy
        │
        ▼
Inject Proxy
        │
        ▼
First Method Call
        │
        ▼
Create Actual Bean
        │
        ▼
Execute Method
```

---

# Lazy Initialization Globally

Spring Boot supports global lazy initialization.

---

## application.properties

```properties
spring.main.lazy-initialization=true
```

Now:

```text
All Beans Become Lazy
```

unless overridden.

---

# Override Global Lazy

Suppose:

```properties
spring.main.lazy-initialization=true
```

---

Force eager bean:

```java
@Service
@Lazy(false)
public class CriticalService {

}
```

Created immediately during startup.

---

# @Lazy(false)

Rare but supported.

---

```java
@Lazy(false)
@Service
public class SecurityService {

}
```

Overrides inherited lazy configuration.

---

# Performance Benefits

Without Lazy:

```text
100 Beans
All created at startup
```

Startup:

```text
15 seconds
```

---

With Lazy:

```text
Only 20 beans used initially
```

Startup:

```text
3 seconds
```

Potentially much faster.

---

# Drawbacks of Lazy Initialization

Important interview topic.

---

## Delayed Errors

Without lazy:

```java
@Bean
public EmployeeService service() {
}
```

Misconfiguration detected during startup.

---

With lazy:

```java
@Lazy
```

Error appears only when bean is first used.

---

Example:

```text
NoSuchBeanDefinitionException
```

may occur hours later.

---

## First Request Slower

Startup faster.

But:

```text
First request slower
```

because bean creation occurs on-demand.

---

# Real Enterprise Use Cases

---

## Heavy Reporting Engines

```java
@Lazy
@Service
public class JasperReportService {

}
```

Only loaded when reports are generated.

---

## AWS SDK Clients

```java
@Lazy
@Bean
public AmazonS3 amazonS3() {
}
```

---

## Large ML Models

```java
@Lazy
@Service
public class AIModelService {

}
```

---

## Optional Features

```java
@Lazy
@Service
public class AnalyticsService {

}
```

---

# @Lazy vs Prototype Scope

Common interview question.

---

## @Lazy

Controls:

```text
WHEN bean is created
```

---

## Prototype

Controls:

```text
HOW MANY bean instances exist
```

---

Example:

```java
@Lazy
```

One bean instance.

Created later.

---

Example:

```java
@Scope("prototype")
```

Many bean instances.

Created every request.

---

# @Lazy vs Eager Initialization

| Eager | Lazy |
|---------|---------|
| Startup Creation | On-Demand Creation |
| Faster First Request | Slower First Request |
| Slower Startup | Faster Startup |
| Early Error Detection | Delayed Error Detection |
| Default Spring Behavior | Optional |

---

# Internal Working

```text
Application Start
        │
        ▼
Find @Lazy Bean
        │
        ▼
Register Bean Definition
        │
        ▼
Create Proxy
        │
        ▼
Wait
        │
        ▼
First Bean Access
        │
        ▼
Create Actual Bean
        │
        ▼
Inject Dependencies
        │
        ▼
Execute Logic
```

---

# Common Mistakes

## Mistake 1

Expecting startup errors.

```java
@Lazy
```

may delay configuration errors.

---

## Mistake 2

Making everything lazy.

```properties
spring.main.lazy-initialization=true
```

for huge applications.

Can cause unexpected runtime delays.

---

## Mistake 3

Assuming lazy bean is never created.

If injected into an eager bean:

```java
@Autowired
private EmployeeService employeeService;
```

it may still be initialized immediately.

---

# Common Interview Questions

## What is @Lazy?

Delays bean creation until first usage.

---

## What is default Spring behavior?

```text
Eager Initialization
```

---

## Can @Lazy be used on classes?

Yes.

```java
@Service
@Lazy
```

---

## Can @Lazy be used on @Bean methods?

Yes.

```java
@Bean
@Lazy
```

---

## Can @Lazy be applied to Configuration classes?

Yes.

```java
@Configuration
@Lazy
```

All contained beans become lazy.

---

## What does Spring inject for lazy beans?

A proxy object.

---

## Does @Lazy improve startup performance?

Usually yes.

Because fewer beans are created initially.

---

## What is the downside of @Lazy?

- Delayed errors
- Slower first access
- Harder debugging

---

## How to enable lazy initialization globally?

```properties
spring.main.lazy-initialization=true
```

---

# Real-World Significance

Large enterprise applications often contain:

- Hundreds of Services
- Messaging Clients
- Kafka Producers
- AWS Clients
- Reporting Engines
- External Integrations

Many are rarely used.

Using:

```java
@Lazy
```

can significantly reduce:

- Startup time
- Memory usage at startup
- Initial resource consumption

while keeping functionality available on demand.

---

# Key Points To Remember

- Delays bean creation until first use.
- Opposite of eager initialization.
- Can be used on classes, methods, and configuration classes.
- Works through Spring proxies.
- Improves startup performance.
- Can delay configuration errors.
- First access may be slower.
- Supports global lazy initialization.
- Can be overridden using `@Lazy(false)`.
- Frequently used for expensive or optional beans.
- Important optimization annotation in Spring and Spring Boot.