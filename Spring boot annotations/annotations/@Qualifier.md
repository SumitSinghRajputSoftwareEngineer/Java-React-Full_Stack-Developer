
## Definition

`@Qualifier` is used together with `@Autowired` to specify exactly which bean should be injected when multiple beans of the same type exist.

It tells Spring:

> If more than one matching bean is found, inject the bean with the specified name.

Without `@Qualifier`, Spring may not know which bean to inject and can throw an exception.

---

# Why Do We Need @Qualifier?

Suppose we have:

```java
public interface NotificationService {

    void send();

}
```

---

Implementation 1:

```java
@Service
public class EmailService
        implements NotificationService {

    @Override
    public void send() {

        System.out.println("Email Sent");

    }

}
```

---

Implementation 2:

```java
@Service
public class SmsService
        implements NotificationService {

    @Override
    public void send() {

        System.out.println("SMS Sent");

    }

}
```

---

Now:

```java
@Autowired
private NotificationService service;
```

Spring finds:

```text
EmailService
SmsService
```

Two matching beans.

---

Result:

```text
NoUniqueBeanDefinitionException
```

because Spring cannot decide which bean to inject.

---

# Solution

Use:

```java
@Qualifier
```

---

```java
@Autowired
@Qualifier("emailService")
private NotificationService service;
```

Spring injects:

```java
EmailService
```

only.

---

# Basic Syntax

```java
@Autowired
@Qualifier("emailService")
private NotificationService service;
```

---

# What Happens Internally?

Suppose:

```java
@Autowired
@Qualifier("emailService")
private NotificationService service;
```

Spring:

### Step 1

Looks for type:

```java
NotificationService
```

---

### Step 2

Finds:

```text
EmailService
SmsService
```

---

### Step 3

Checks qualifier:

```java
emailService
```

---

### Step 4

Matches bean name.

---

### Step 5

Injects:

```java
EmailService
```

---

Flow:

```text
Find Type
     │
     ▼
Multiple Beans Found
     │
     ▼
Check @Qualifier
     │
     ▼
Find Matching Bean
     │
     ▼
Inject Bean
```

---

# Bean Names and @Qualifier

Default bean names are generated automatically.

---

Example:

```java
@Service
public class EmailService {

}
```

Bean Name:

```text
emailService
```

---

```java
@Service
public class SmsService {

}
```

Bean Name:

```text
smsService
```

---

Injection:

```java
@Autowired
@Qualifier("emailService")
private NotificationService service;
```

---

# Constructor Injection with @Qualifier

Recommended approach.

---

```java
@RestController
public class NotificationController {

    private final NotificationService service;

    public NotificationController(

            @Qualifier("emailService")
            NotificationService service) {

        this.service = service;
    }

}
```

---

Spring injects:

```java
EmailService
```

---

# Setter Injection with @Qualifier

```java
@Autowired
public void setService(

        @Qualifier("smsService")
        NotificationService service) {

    this.service = service;
}
```

---

# Field Injection with @Qualifier

```java
@Autowired
@Qualifier("smsService")
private NotificationService service;
```

Works, but constructor injection is preferred.

---

# Custom Bean Names

---

```java
@Service("email")
public class EmailService
        implements NotificationService {

}
```

---

```java
@Service("sms")
public class SmsService
        implements NotificationService {

}
```

---

Injection:

```java
@Autowired
@Qualifier("email")
private NotificationService service;
```

---

Spring injects:

```java
EmailService
```

---

# @Qualifier with @Bean

Very common.

---

```java
@Configuration
public class AppConfig {

    @Bean
    public NotificationService emailService() {

        return new EmailService();

    }

    @Bean
    public NotificationService smsService() {

        return new SmsService();

    }

}
```

---

Injection:

```java
@Autowired
@Qualifier("smsService")
private NotificationService service;
```

---

Spring injects:

```java
SmsService
```

---

# @Qualifier with Multiple DataSources

Very common enterprise scenario.

---

```java
@Bean
public DataSource mysqlDataSource() {

    return new HikariDataSource();

}
```

---

```java
@Bean
public DataSource postgresDataSource() {

    return new HikariDataSource();

}
```

---

Injection:

```java
@Autowired
@Qualifier("mysqlDataSource")
private DataSource dataSource;
```

---

Spring injects MySQL datasource.

---

# @Qualifier with Interfaces

Most common usage.

---

Interface:

```java
public interface PaymentService {

}
```

---

Implementation 1:

```java
@Service
public class RazorpayService
        implements PaymentService {

}
```

---

Implementation 2:

```java
@Service
public class StripeService
        implements PaymentService {

}
```

---

Injection:

```java
@Autowired
@Qualifier("stripeService")
private PaymentService service;
```

---

Spring injects:

```java
StripeService
```

---

# @Primary vs @Qualifier

Most important interview question.

---

## @Primary

Provides:

```text
Default Bean
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
@Autowired
private NotificationService service;
```

Spring injects:

```java
EmailService
```

---

# @Qualifier

Provides:

```text
Specific Bean
```

---

```java
@Autowired
@Qualifier("smsService")
private NotificationService service;
```

Spring injects:

```java
SmsService
```

---

# Resolution Order

Important interview question.

---

Suppose:

```java
@Service
@Primary
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
@Autowired
@Qualifier("smsService")
private NotificationService service;
```

Result:

```java
SmsService
```

---

Reason:

```text
@Qualifier overrides @Primary
```

---

# Custom Qualifier Annotation

Advanced Spring feature.

---

Instead of:

```java
@Qualifier("emailService")
```

you can create:

```java
@Target({
        ElementType.FIELD,
        ElementType.PARAMETER,
        ElementType.TYPE
})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Email {
}
```

---

Usage:

```java
@Service
@Email
public class EmailService
        implements NotificationService {

}
```

---

Injection:

```java
@Autowired
@Email
private NotificationService service;
```

---

Spring injects:

```java
EmailService
```

---

# Collection Injection

Important concept.

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

Inject all:

```java
@Autowired
private List<NotificationService> services;
```

Spring injects:

```text
EmailService
SmsService
```

---

No qualifier required.

---

# Internal Resolution Process

When Spring encounters:

```java
@Autowired
@Qualifier("emailService")
```

Resolution Order:

```text
1. Find Matching Type
2. Find Candidate Beans
3. Apply Qualifier Filter
4. Select Matching Bean
5. Inject Bean
```

---

Flow:

```text
@Autowired
      │
      ▼
Find Type
      │
      ▼
Multiple Beans Found
      │
      ▼
Apply @Qualifier
      │
      ▼
Select Bean
      │
      ▼
Inject Bean
```

---

# Common Mistakes

## Mistake 1

Wrong Bean Name.

---

```java
@Qualifier("email")
```

but bean name:

```text
emailService
```

---

Result:

```text
NoSuchBeanDefinitionException
```

---

## Mistake 2

Using @Qualifier Without Matching Bean.

---

```java
@Qualifier("unknown")
```

No bean exists.

---

Injection fails.

---

## Mistake 3

Misspelling Bean Name.

---

```java
@Qualifier("emialService")
```

Typo.

---

Result:

```text
NoSuchBeanDefinitionException
```

---

## Mistake 4

Assuming @Primary Overrides @Qualifier.

Wrong.

```text
@Qualifier always wins.
```

---

# Common Interview Questions

## What is @Qualifier?

Used to specify which bean should be injected when multiple beans of the same type exist.

---

## Why is @Qualifier needed?

To resolve bean ambiguity.

---

## What exception occurs without it?

```text
NoUniqueBeanDefinitionException
```

---

## Can @Qualifier be used with constructor injection?

Yes.

Recommended approach.

---

## Can @Qualifier be used with @Bean methods?

Yes.

Very common.

---

## Difference Between @Primary and @Qualifier?

| @Primary | @Qualifier |
|-----------|------------|
| Default Bean | Explicit Bean |
| Global Preference | Specific Preference |
| Lower Priority | Higher Priority |

---

## Which one has higher priority?

```text
@Qualifier
```

---

## Can we create custom qualifiers?

Yes.

Using:

```java
@Qualifier
```

as a meta-annotation.

---

# Real-World Significance

Enterprise applications frequently have multiple implementations of the same interface:

Examples:

```text
PaymentService
 ├─ RazorpayService
 ├─ StripeService
 └─ PaypalService
```

```text
NotificationService
 ├─ EmailService
 ├─ SmsService
 └─ PushNotificationService
```

```text
DataSource
 ├─ MySQL
 ├─ PostgreSQL
 └─ Oracle
```

`@Qualifier` allows Spring to inject the exact implementation required.

---

# Key Points To Remember

- Used with `@Autowired`.
- Resolves bean ambiguity.
- Selects a specific bean by name or qualifier.
- Commonly used with interfaces having multiple implementations.
- Works with field, constructor, and setter injection.
- Works with `@Bean` methods.
- Has higher priority than `@Primary`.
- Supports custom qualifier annotations.
- Prevents `NoUniqueBeanDefinitionException`.
- One of the most important Dependency Injection annotations in Spring.