
## Definition

`@Inject` is a Dependency Injection annotation defined by the Jakarta Dependency Injection (CDI) specification.

Package:

```java
jakarta.inject.Inject
```

It tells the container:

> Inject the required dependency automatically.

Functionally, in Spring applications, `@Inject` is very similar to:

```java
@Autowired
```

and is often considered the standard Java/Jakarta alternative to Spring's proprietary injection annotation.

---

# Import Statement

Spring Boot 3.x+

```java
import jakarta.inject.Inject;
```

---

Older Versions

```java
import javax.inject.Inject;
```

---

# Why Do We Need @Inject?

Without Dependency Injection:

```java
public class EmployeeController {

    private EmployeeService service =
            new EmployeeService();

}
```

Problems:

- Tight Coupling
- Hard Testing
- Difficult Maintenance
- No IoC Benefits
- Manual Object Creation

---

Using:

```java
@Inject
private EmployeeService service;
```

Spring automatically injects the dependency.

---

# Basic Example

## Service

```java
@Service
public class EmployeeService {

}
```

---

## Controller

```java
@RestController
public class EmployeeController {

    @Inject
    private EmployeeService service;

}
```

---

Spring injects:

```java
EmployeeService
```

automatically.

---

# What Happens Internally?

Suppose:

```java
@Inject
private EmployeeService service;
```

Spring:

### Step 1

Creates Controller Bean.

---

### Step 2

Finds:

```java
@Inject
```

---

### Step 3

Looks for matching bean type.

```java
EmployeeService
```

---

### Step 4

Finds bean.

---

### Step 5

Injects bean.

---

Flow:

```text
Create Bean
     │
     ▼
Find @Inject
     │
     ▼
Resolve Dependency
     │
     ▼
Inject Bean
```

---

# Dependency Injection Types

Like `@Autowired`, `@Inject` supports:

## Constructor Injection

## Field Injection

## Setter Injection

---

# Constructor Injection (Recommended)

Most common modern approach.

---

```java
@Service
public class EmployeeService {

}
```

---

```java
@RestController
public class EmployeeController {

    private final EmployeeService service;

    @Inject
    public EmployeeController(
            EmployeeService service) {

        this.service = service;
    }

}
```

---

Spring injects:

```java
EmployeeService
```

---

# Modern Spring Note

Even with:

```java
@Inject
```

constructor injection can often work without the annotation if there is only one constructor.

---

Example:

```java
@RestController
public class EmployeeController {

    private final EmployeeService service;

    public EmployeeController(
            EmployeeService service) {

        this.service = service;
    }

}
```

---

Spring still injects dependency automatically.

---

# Field Injection

---

```java
@Inject
private EmployeeService service;
```

---

Works.

But modern projects generally prefer:

```text
Constructor Injection
```

---

because it provides:

- Better Testing
- Immutability
- Compile-Time Safety

---

# Setter Injection

---

```java
private EmployeeService service;

@Inject
public void setService(
        EmployeeService service) {

    this.service = service;
}
```

---

Spring injects dependency.

---

# Injection Resolution

Important interview topic.

---

```java
@Inject
private EmployeeService service;
```

Spring resolves:

```text
By Type
```

---

Step 1:

Find type:

```java
EmployeeService
```

---

Step 2:

Search container.

---

Step 3:

Inject matching bean.

---

# Example with Interface

---

```java
public interface PaymentService {

}
```

---

```java
@Service
public class RazorpayService
        implements PaymentService {

}
```

---

Injection:

```java
@Inject
private PaymentService paymentService;
```

---

Spring injects:

```java
RazorpayService
```

---

# Multiple Implementations

Important interview question.

---

```java
@Service
public class RazorpayService
        implements PaymentService {

}
```

---

```java
@Service
public class StripeService
        implements PaymentService {

}
```

---

Injection:

```java
@Inject
private PaymentService service;
```

---

Spring finds:

```text
RazorpayService
StripeService
```

---

Result:

```text
NoUniqueBeanDefinitionException
```

---

# Solving Using @Named

Jakarta equivalent of:

```java
@Qualifier
```

---

Implementation:

```java
@Service
@Named("razorpay")
public class RazorpayService
        implements PaymentService {

}
```

---

Injection:

```java
@Inject
@Named("razorpay")
private PaymentService service;
```

---

Spring injects:

```java
RazorpayService
```

---

# @Named

Package:

```java
jakarta.inject.Named
```

---

Equivalent to:

```java
@Qualifier
```

in Spring.

---

Example:

```java
@Inject
@Named("emailService")
private NotificationService service;
```

---

# @Inject with @Primary

Works exactly like:

```java
@Autowired
```

---

Example:

```java
@Service
@Primary
public class EmailService
        implements NotificationService {

}
```

---

```java
@Inject
private NotificationService service;
```

---

Spring injects:

```java
EmailService
```

---

# Optional Dependencies

Important difference from `@Autowired`.

---

`@Autowired` supports:

```java
@Autowired(required = false)
```

---

`@Inject` does NOT have:

```java
required = false
```

---

Alternative:

```java
@Inject
private Optional<EmployeeService> service;
```

---

or

```java
@Inject
Provider<EmployeeService> provider;
```

---

# Provider Injection

Advanced CDI feature.

---

```java
@Inject
private Provider<EmployeeService> provider;
```

---

Usage:

```java
EmployeeService service =
        provider.get();
```

---

Benefits:

- Lazy Loading
- Dynamic Retrieval
- Optional Access

---

# Collection Injection

Spring supports it.

---

```java
@Inject
private List<NotificationService> services;
```

---

Spring injects all matching implementations.

---

Example:

```text
EmailService
SmsService
PushService
```

---

# Map Injection

Spring-specific enhancement.

---

```java
@Inject
private Map<String,
        NotificationService> services;
```

---

Result:

```text
emailService -> EmailService
smsService   -> SmsService
```

---

# @Inject vs @Autowired

Most Important Interview Question

---

## @Autowired

Spring-specific annotation.

```java
@Autowired
```

---

## @Inject

Jakarta standard annotation.

```java
@Inject
```

---

Comparison:

| Feature | @Autowired | @Inject |
|----------|------------|----------|
| Owner | Spring | Jakarta CDI |
| Resolution | Type | Type |
| Constructor Injection | Yes | Yes |
| Field Injection | Yes | Yes |
| Setter Injection | Yes | Yes |
| Optional Dependency | Yes | No |
| Qualifier Support | @Qualifier | @Named |
| Standard Java | No | Yes |

---

# @Inject vs @Resource

---

## @Inject

Resolves:

```text
By Type
```

---

## @Resource

Resolves:

```text
By Name First
```

---

Comparison:

| Feature | @Inject | @Resource |
|----------|----------|-----------|
| Resolution | Type | Name First |
| Standard | Jakarta CDI | Jakarta EE |
| Qualifier | @Named | name attribute |
| Constructor Support | Yes | Rare |

---

# Internal Resolution Process

When Spring sees:

```java
@Inject
private EmployeeService service;
```

---

Process:

```text
Find Bean Type
      │
      ▼
Search Container
      │
      ▼
Matching Bean Found
      │
      ▼
Inject Bean
```

---

Flow:

```text
@Inject
     │
     ▼
Resolve By Type
     │
     ▼
Find Bean
     │
     ▼
Inject Dependency
```

---

# Common Mistakes

## Mistake 1

Assuming @Inject Supports required=false

Wrong.

---

```java
@Inject(required = false)
```

Invalid.

---

# Mistake 2

Multiple Matching Beans

---

```java
@Inject
PaymentService service;
```

---

Multiple implementations.

---

Result:

```text
NoUniqueBeanDefinitionException
```

---

Use:

```java
@Named
```

or

```java
@Primary
```

---

# Mistake 3

Using Field Injection Everywhere

Modern Spring applications prefer:

```text
Constructor Injection
```

---

# Common Interview Questions

## What is @Inject?

A Jakarta CDI dependency injection annotation.

---

## Which package does it belong to?

```java
jakarta.inject.Inject
```

---

## Is it Spring-specific?

No.

It is a Jakarta standard.

---

## How does @Inject resolve dependencies?

```text
By Type
```

---

## Difference Between @Inject and @Autowired?

`@Autowired`

```text
Spring-specific
```

---

`@Inject`

```text
Jakarta standard
```

---

## Can @Inject use qualifiers?

Yes.

Using:

```java
@Named
```

---

## Does @Inject support optional dependencies?

Not directly.

Use:

```java
Optional<T>
```

or

```java
Provider<T>
```

---

# Real-World Significance

Many enterprise organizations prefer:

```java
@Inject
```

because it is:

- Framework Independent
- Jakarta Standard
- Portable Across Containers
- Supported by Spring

It allows code to be less dependent on Spring-specific APIs.

However, modern Spring Boot projects commonly use:

```java
Constructor Injection
```

with no annotation at all when only one constructor exists.

---

# Key Points To Remember

- Jakarta CDI Dependency Injection annotation.
- Package: `jakarta.inject.Inject`.
- Works similarly to `@Autowired`.
- Resolves dependencies by Type.
- Supports constructor, field, and setter injection.
- Uses `@Named` instead of `@Qualifier`.
- Does not support `required=false`.
- Supports `Provider<T>` and `Optional<T>`.
- Framework-independent and portable.
- Frequently asked in Spring and Java enterprise interviews.