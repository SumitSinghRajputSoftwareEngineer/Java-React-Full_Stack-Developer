
## Definition

`@Resource` is a Dependency Injection annotation from the Jakarta EE specification (formerly Java EE).

Package:

```java
jakarta.annotation.Resource
```

It tells Spring:

> Inject a bean by name first, and if not found, try by type.

Unlike:

```java
@Autowired
```

which primarily works by:

```text
Type
```

`@Resource` primarily works by:

```text
Name
```

This is the most important difference.

---

# Import Statement

Spring Boot 3.x+

```java
import jakarta.annotation.Resource;
```

---

Older Spring Versions

```java
import javax.annotation.Resource;
```

---

# Why Do We Need @Resource?

Suppose we have:

```java
@Service
public class EmailService {

}
```

---

```java
@Service
public class SmsService {

}
```

---

Injection:

```java
@Resource
private EmailService emailService;
```

Spring searches for bean named:

```text
emailService
```

and injects it.

---

# Basic Syntax

```java
@Resource
private EmployeeService employeeService;
```

---

# What Happens Internally?

Suppose:

```java
@Resource
private EmployeeService employeeService;
```

Spring:

### Step 1

Looks at field name.

```text
employeeService
```

---

### Step 2

Searches bean with same name.

```text
employeeService
```

---

### Step 3

If found → inject.

---

### Step 4

If not found → try type matching.

---

Flow:

```text
@Resource
      │
      ▼
Find Bean By Name
      │
      ▼
Found?
   /     \
 Yes      No
  │        │
  ▼        ▼
Inject   Find By Type
```

---

# Simple Example

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

    @Resource
    private EmployeeService employeeService;

}
```

---

Spring injects:

```java
EmployeeService
```

---

# Injection by Name

Important concept.

---

Bean:

```java
@Service
public class EmailService {

}
```

Bean name:

```text
emailService
```

---

Injection:

```java
@Resource
private EmailService emailService;
```

---

Spring matches:

```text
emailService
```

↓

```text
emailService
```

Injection succeeds.

---

# Explicit Bean Name

You can specify bean name manually.

---

```java
@Resource(name = "emailService")
private NotificationService service;
```

---

Spring injects bean named:

```text
emailService
```

---

# Example with Multiple Beans

---

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

Injection:

```java
@Resource(name = "smsService")
private NotificationService service;
```

---

Spring injects:

```java
SmsService
```

---

# @Resource Resolution Strategy

Very important interview topic.

---

Resolution order:

```text
1. By Name
2. By Type
3. Exception
```

---

# Detailed Example

---

Bean:

```java
@Service
public class EmployeeService {

}
```

---

Injection:

```java
@Resource
private EmployeeService employeeService;
```

---

Field Name:

```text
employeeService
```

---

Bean Name:

```text
employeeService
```

---

Injection succeeds.

---

# If Name Doesn't Match

---

Bean:

```java
@Service
public class EmployeeService {

}
```

---

Injection:

```java
@Resource
private EmployeeService service;
```

---

Field Name:

```text
service
```

---

No bean named:

```text
service
```

exists.

---

Spring tries:

```text
Type Matching
```

Finds:

```java
EmployeeService
```

Injection succeeds.

---

# If Multiple Types Exist

---

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

Injection:

```java
@Resource
private NotificationService service;
```

---

Spring:

### Name Search

```text
service
```

No bean.

---

### Type Search

Finds:

```text
EmailService
SmsService
```

Two beans.

---

Result:

```text
NoUniqueBeanDefinitionException
```

---

# Solving Ambiguity

Use:

```java
@Resource(name = "emailService")
```

---

```java
@Resource(name = "smsService")
```

---

Example:

```java
@Resource(name = "emailService")
private NotificationService service;
```

---

Spring injects:

```java
EmailService
```

---

# @Resource vs @Autowired

Most Important Interview Question

---

## @Autowired

Resolution:

```text
Type First
```

---

```java
@Autowired
private EmployeeService service;
```

---

Spring:

```text
Find Bean By Type
```

---

## @Resource

Resolution:

```text
Name First
```

---

```java
@Resource
private EmployeeService employeeService;
```

---

Spring:

```text
Find Bean By Name
```

---

Comparison:

| Feature | @Autowired | @Resource |
|----------|------------|------------|
| Package | Spring | Jakarta |
| Resolution | Type First | Name First |
| Supports @Qualifier | Yes | No |
| Supports required=false | Yes | No |
| Standard Java Annotation | No | Yes |

---

# @Resource vs @Inject

---

## @Inject

```java
@Inject
```

Works primarily by:

```text
Type
```

---

## @Resource

Works primarily by:

```text
Name
```

---

# Constructor Injection Support

Important point.

---

`@Resource` is generally used on:

```java
Field
Setter
```

---

Example:

```java
@Resource
private EmployeeService service;
```

---

Unlike:

```java
@Autowired
```

it is not commonly used for constructor injection.

---

# Setter Injection Example

```java
private EmployeeService service;

@Resource
public void setService(
        EmployeeService service) {

    this.service = service;
}
```

---

Spring injects dependency.

---

# @Resource with @Bean

Example:

```java
@Bean
public EmployeeService employeeService() {

    return new EmployeeService();

}
```

---

Injection:

```java
@Resource(name = "employeeService")
private EmployeeService service;
```

---

Spring injects bean.

---

# Internal Resolution Flow

```text
@Resource
      │
      ▼
Check Name
      │
      ▼
Bean Found?
  /      \
Yes       No
 │         │
 ▼         ▼
Inject   Check Type
             │
             ▼
        Bean Found?
         /      \
       Yes       No
        │         │
        ▼         ▼
      Inject   Exception
```

---

# Common Mistakes

## Mistake 1

Assuming it behaves exactly like @Autowired.

Wrong.

```text
@Resource = Name First
@Autowired = Type First
```

---

## Mistake 2

Wrong Bean Name.

```java
@Resource(name="abc")
```

No bean exists.

---

Result:

```text
NoSuchBeanDefinitionException
```

---

## Mistake 3

Multiple Matching Types.

```java
@Resource
private NotificationService service;
```

---

Multiple implementations.

---

Result:

```text
NoUniqueBeanDefinitionException
```

---

# Common Interview Questions

## What is @Resource?

A Jakarta EE dependency injection annotation used for bean injection.

---

## How does @Resource resolve dependencies?

```text
Name First
Type Second
```

---

## What package does it belong to?

```java
jakarta.annotation.Resource
```

---

## Difference Between @Autowired and @Resource?

`@Autowired`

```text
Type First
```

---

`@Resource`

```text
Name First
```

---

## Can @Resource inject Spring Beans?

Yes.

Spring fully supports it.

---

## Is @Resource Spring-specific?

No.

It is a Jakarta EE standard annotation.

---

## Does @Resource support @Qualifier?

No.

Use:

```java
@Resource(name="beanName")
```

instead.

---

# Real-World Significance

Although most modern Spring Boot applications prefer:

```java
@Autowired
```

or constructor injection,

many enterprise applications still use:

```java
@Resource
```

because:

- It is framework-independent.
- It follows Jakarta EE standards.
- It works well when bean names are important.
- It improves portability across Java frameworks.

You will frequently encounter it in legacy enterprise systems.

---

# @Resource vs @Autowired vs @Inject

| Feature | @Autowired | @Resource | @Inject |
|----------|------------|------------|----------|
| Owner | Spring | Jakarta EE | Jakarta CDI |
| Resolution | Type First | Name First | Type First |
| Qualifier Support | Yes | No | Yes |
| Constructor Injection | Excellent | Rare | Excellent |
| Optional Dependency | Yes | No | No |
| Standard Java | No | Yes | Yes |

---

# Key Points To Remember

- Jakarta EE Dependency Injection annotation.
- Package: `jakarta.annotation.Resource`.
- Resolves dependencies by Name first, then Type.
- Can inject Spring Beans.
- Commonly used on fields and setters.
- Supports explicit bean selection using `name`.
- Different from `@Autowired` (Type-first).
- Does not support `@Qualifier`.
- Useful in framework-independent applications.
- Commonly found in legacy enterprise applications.
- One of the most frequently asked Spring interview topics.