
## Definition

`@Autowired` is Spring's Dependency Injection (DI) annotation.

It tells Spring:

> Find the required bean from the Spring IoC Container and inject it automatically.

Instead of manually creating objects using:

```java
new EmployeeService()
```

Spring automatically creates and injects dependencies.

---

# Why Do We Need @Autowired?

Without Spring Dependency Injection:

```java
public class EmployeeController {

    private EmployeeService service =
            new EmployeeService();

}
```

Problems:

- Tight Coupling
- Difficult Testing
- Difficult Maintenance
- No IoC Benefits
- Manual Object Management

---

Using Spring:

```java
@Autowired
private EmployeeService service;
```

Spring injects the bean automatically.

---

# What Is Dependency Injection?

Suppose:

```java
EmployeeController
```

needs:

```java
EmployeeService
```

and

```java
EmployeeService
```

needs:

```java
EmployeeRepository
```

---

Dependency Graph:

```text
EmployeeController
         │
         ▼
EmployeeService
         │
         ▼
EmployeeRepository
```

Spring creates and injects everything automatically.

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

    @Autowired
    private EmployeeService service;

}
```

Spring injects:

```java
EmployeeService
```

automatically.

---

# What Happens Internally?

During startup:

```java
@Service
public class EmployeeService {

}
```

Bean created.

---

```java
@RestController
public class EmployeeController {

    @Autowired
    private EmployeeService service;

}
```

Spring:

### Step 1

Creates EmployeeController.

---

### Step 2

Finds:

```java
@Autowired
```

---

### Step 3

Searches Bean Container.

---

### Step 4

Finds EmployeeService Bean.

---

### Step 5

Injects Bean.

---

Flow:

```text
Create Controller
        │
        ▼
Find @Autowired
        │
        ▼
Search Bean Container
        │
        ▼
Find Matching Bean
        │
        ▼
Inject Bean
```

---

# Types of Dependency Injection

Spring supports:

1. Constructor Injection
2. Setter Injection
3. Field Injection

---

# 1. Constructor Injection (Recommended)

Most important interview topic.

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

    public EmployeeController(
            EmployeeService service) {

        this.service = service;
    }

}
```

---

Spring automatically injects dependency.

No need for:

```java
@Autowired
```

when there is a single constructor.

---

# Why Constructor Injection Is Best

Advantages:

- Immutable Dependencies
- Easier Unit Testing
- Better Design
- Prevents Null Dependencies
- Compile-Time Safety

---

Recommended by:

```text
Spring Team
```

---

# Constructor Injection with @Autowired

Optional in modern Spring.

---

```java
@Autowired
public EmployeeController(
        EmployeeService service) {

    this.service = service;
}
```

Works but usually unnecessary.

---

# 2. Field Injection

---

```java
@RestController
public class EmployeeController {

    @Autowired
    private EmployeeService service;

}
```

Works perfectly.

---

But drawbacks:

- Hard to Test
- Reflection Based
- Hidden Dependency
- Not Immutable

---

Generally avoided in modern projects.

---

# 3. Setter Injection

---

```java
@RestController
public class EmployeeController {

    private EmployeeService service;

    @Autowired
    public void setService(
            EmployeeService service) {

        this.service = service;
    }

}
```

---

Useful when:

- Dependency is optional
- Dependency can change later

---

# Injection Resolution Process

Suppose:

```java
@Autowired
private EmployeeService service;
```

Spring resolves in order:

### Step 1

Type Matching

```java
EmployeeService
```

---

### Step 2

Bean Lookup

Find bean of same type.

---

### Step 3

Inject Bean

Success.

---

# Example

```java
@Service
public class EmployeeService {

}
```

---

```java
@Autowired
private EmployeeService service;
```

Injection succeeds.

---

# What If Multiple Beans Exist?

Important interview question.

---

Example:

```java
@Service
public class EmailService
        implements NotificationService {
}
```

---

```java
@Service
public class SmsService
        implements NotificationService {
}
```

---

```java
@Autowired
private NotificationService service;
```

Spring sees:

```text
EmailService
SmsService
```

Two matches.

---

Result:

```text
NoUniqueBeanDefinitionException
```

---

# Solving Using @Qualifier

```java
@Autowired
@Qualifier("emailService")
private NotificationService service;
```

Now Spring injects:

```java
EmailService
```

only.

---

# Solving Using @Primary

```java
@Service
@Primary
public class EmailService
        implements NotificationService {

}
```

---

Now:

```java
@Autowired
private NotificationService service;
```

injects:

```java
EmailService
```

by default.

---

# Optional Dependency

By default:

```java
@Autowired
```

is required.

---

If bean missing:

```text
NoSuchBeanDefinitionException
```

---

Make optional:

```java
@Autowired(required = false)
private EmailService service;
```

---

Now:

```java
service == null
```

if bean not found.

---

# Optional<T> Injection

Modern approach.

---

```java
@Autowired
private Optional<EmailService> service;
```

---

Usage:

```java
service.ifPresent(...);
```

---

# ObjectProvider Injection

Advanced Spring feature.

---

```java
@Autowired
private ObjectProvider<EmployeeService>
        provider;
```

---

Usage:

```java
EmployeeService service =
        provider.getIfAvailable();
```

---

Useful for:

- Lazy Retrieval
- Optional Dependencies
- Dynamic Lookups

---

# Collection Injection

Very important.

---

Suppose:

```java
@Service
public class EmailService
        implements NotificationService {
}
```

---

```java
@Service
public class SmsService
        implements NotificationService {
}
```

---

Inject all implementations:

```java
@Autowired
private List<NotificationService> services;
```

Spring injects:

```java
EmailService
SmsService
```

into list.

---

# Map Injection

```java
@Autowired
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

# Lazy Injection

```java
@Autowired
@Lazy
private EmployeeService service;
```

---

Spring injects proxy.

Actual bean created later.

---

# Circular Dependency

Very important interview topic.

---

Example:

```java
@Service
public class A {

    @Autowired
    private B b;

}
```

---

```java
@Service
public class B {

    @Autowired
    private A a;

}
```

---

Spring may throw:

```text
BeanCurrentlyInCreationException
```

especially with constructor injection.

---

Solution:

```java
@Lazy
```

or redesign.

---

# @Autowired with Interfaces

Most common usage.

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

```java
@Autowired
private PaymentService paymentService;
```

Spring injects implementation.

---

# How Spring Finds Beans?

Through:

```java
@Component
@Service
@Repository
@Controller
@Configuration
@Bean
```

All create beans.

---

# Internal Working

```text
Application Start
        │
        ▼
Create Bean Definitions
        │
        ▼
Instantiate Beans
        │
        ▼
Find @Autowired
        │
        ▼
Resolve Dependency
        │
        ▼
Inject Bean
        │
        ▼
Application Ready
```

---

# @Autowired vs @Inject

---

## @Autowired

Spring Specific.

```java
@Autowired
```

---

## @Inject

Jakarta Standard.

```java
@Inject
```

---

Functionally similar.

---

# @Autowired vs @Resource

---

## @Autowired

Resolves by:

```text
Type
```

first.

---

## @Resource

Resolves by:

```text
Name
```

first.

---

# Common Mistakes

## Mistake 1

Manual Object Creation.

Wrong:

```java
EmployeeService service =
        new EmployeeService();
```

Bypasses Spring.

---

## Mistake 2

Multiple Beans Without Qualifier.

```java
@Autowired
NotificationService service;
```

Multiple implementations.

Result:

```text
NoUniqueBeanDefinitionException
```

---

## Mistake 3

Using Field Injection Everywhere.

Modern recommendation:

```text
Constructor Injection
```

---

## Mistake 4

Injecting Non-Spring Classes.

```java
@Autowired
private Random random;
```

Not a Spring Bean.

Injection fails.

---

# Common Interview Questions

## What is @Autowired?

Used for automatic dependency injection.

---

## How does Spring resolve dependencies?

Primarily by:

```text
Type
```

---

## What happens if multiple beans exist?

```text
NoUniqueBeanDefinitionException
```

unless:

```java
@Qualifier
```

or

```java
@Primary
```

is used.

---

## Is @Autowired required for constructor injection?

No.

For a single constructor, Spring injects automatically.

---

## Which injection type is recommended?

```text
Constructor Injection
```

---

## Can @Autowired inject collections?

Yes.

```java
List<T>
Map<String,T>
Set<T>
```

---

## Can @Autowired inject interfaces?

Yes.

Spring injects the matching implementation.

---

## Is @Autowired mandatory?

No.

Constructor injection often works without it.

---

# Real-World Significance

Dependency Injection is one of the core principles of Spring.

Almost every Spring Boot application uses:

```java
@Autowired
```

or constructor injection for:

- Services
- Repositories
- Controllers
- Configurations
- Event Publishers
- Kafka Producers
- REST Clients
- Security Components

Without Dependency Injection, Spring's IoC container would have little value.

---

# Key Points To Remember

- Performs Dependency Injection.
- Resolves beans primarily by type.
- Can be used on fields, constructors, and setters.
- Constructor Injection is recommended.
- Supports interfaces and implementations.
- Supports collections and maps.
- Can work with `@Qualifier` and `@Primary`.
- Supports optional dependencies.
- Can create circular dependency issues.
- One of the most fundamental annotations in the Spring Framework.