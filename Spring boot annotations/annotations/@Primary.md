
## Definition

`@Primary` is used to specify the default bean to inject when multiple beans of the same type exist in the Spring IoC Container.

It tells Spring:

> If multiple candidate beans are found for dependency injection and no `@Qualifier` is specified, use this bean by default.

Think of it as:

```text
Default Choice Bean
```

for dependency injection.

---

# Why Do We Need @Primary?

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

}
```

---

Implementation 2:

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

# Solution Using @Primary

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
private NotificationService service;
```

Spring automatically injects:

```java
EmailService
```

because it is marked as:

```java
@Primary
```

---

# Basic Syntax

```java
@Service
@Primary
public class EmailService
        implements NotificationService {

}
```

---

# What Happens Internally?

Spring encounters:

```java
@Autowired
private NotificationService service;
```

---

Step 1:

Find all matching beans.

```text
EmailService
SmsService
```

---

Step 2:

Check if one bean is marked:

```java
@Primary
```

---

Step 3:

Select primary bean.

---

Step 4:

Inject bean.

---

Flow:

```text
Find Bean Type
      │
      ▼
Multiple Beans Found
      │
      ▼
Check @Primary
      │
      ▼
Select Primary Bean
      │
      ▼
Inject Bean
```

---

# Real Example

## Interface

```java
public interface PaymentService {

    void pay();

}
```

---

## Razorpay

```java
@Service
@Primary
public class RazorpayService
        implements PaymentService {

}
```

---

## Stripe

```java
@Service
public class StripeService
        implements PaymentService {

}
```

---

## Injection

```java
@Autowired
private PaymentService paymentService;
```

---

Injected Bean:

```java
RazorpayService
```

---

# Constructor Injection Example

Recommended approach.

---

```java
@RestController
public class PaymentController {

    private final PaymentService paymentService;

    public PaymentController(
            PaymentService paymentService) {

        this.paymentService = paymentService;
    }

}
```

---

Spring automatically injects:

```java
RazorpayService
```

because it is primary.

---

# Field Injection Example

```java
@Autowired
private PaymentService paymentService;
```

Spring injects primary bean.

---

# Setter Injection Example

```java
@Autowired
public void setPaymentService(
        PaymentService paymentService) {

    this.paymentService = paymentService;
}
```

Spring injects primary bean.

---

# @Primary with @Bean

Very common in configuration classes.

---

```java
@Configuration
public class AppConfig {

    @Bean
    @Primary
    public DataSource mysqlDataSource() {

        return new HikariDataSource();

    }

    @Bean
    public DataSource postgresDataSource() {

        return new HikariDataSource();

    }

}
```

---

Injection:

```java
@Autowired
private DataSource dataSource;
```

Spring injects:

```java
mysqlDataSource
```

---

# Multiple DataSource Example

Common enterprise scenario.

---

```java
@Bean
@Primary
public DataSource masterDataSource() {

}
```

---

```java
@Bean
public DataSource reportingDataSource() {

}
```

---

Injection:

```java
@Autowired
private DataSource dataSource;
```

Gets:

```java
masterDataSource
```

---

# @Primary vs @Qualifier

Most important interview question.

---

## @Primary

Specifies:

```text
Default Bean
```

---

```java
@Service
@Primary
public class EmailService
        implements NotificationService {

}
```

---

## @Qualifier

Specifies:

```text
Specific Bean
```

---

```java
@Autowired
@Qualifier("smsService")
private NotificationService service;
```

---

Injects:

```java
SmsService
```

---

# Priority Rules

Important interview topic.

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

---

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

Priority Order:

```text
1. @Qualifier
2. @Primary
3. Bean Name Matching
4. Exception
```

---

# Multiple @Primary Beans

Important interview question.

---

Wrong:

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
@Primary
public class SmsService
        implements NotificationService {

}
```

---

Injection:

```java
@Autowired
private NotificationService service;
```

---

Result:

```text
NoUniqueBeanDefinitionException
```

Spring still cannot decide.

---

Only one primary bean should exist for a type.

---

# @Primary with Interfaces

Most common usage.

---

Interface:

```java
public interface StorageService {

}
```

---

Implementation 1:

```java
@Service
@Primary
public class AwsStorageService
        implements StorageService {

}
```

---

Implementation 2:

```java
@Service
public class AzureStorageService
        implements StorageService {

}
```

---

Injection:

```java
@Autowired
private StorageService storageService;
```

---

Injected Bean:

```java
AwsStorageService
```

---

# Collection Injection

Important concept.

---

```java
@Autowired
private List<NotificationService> services;
```

---

Result:

```text
EmailService
SmsService
```

Both injected.

---

`@Primary` does NOT remove other beans.

It only affects single-bean injection.

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

Both available.

---

# Internal Resolution Algorithm

When Spring sees:

```java
@Autowired
private NotificationService service;
```

Resolution Process:

```text
1. Find Matching Type
2. Count Beans
3. One Bean?
      Yes → Inject
      No
4. Check @Qualifier
      Yes → Inject
      No
5. Check @Primary
      Yes → Inject
      No
6. Throw Exception
```

---

Flow:

```text
@Autowired
      │
      ▼
Find Beans
      │
      ▼
Multiple Beans?
      │
      ▼
Check Qualifier
      │
      ▼
Check Primary
      │
      ▼
Inject Bean
```

---

# Real Enterprise Example

Payment Systems:

```text
PaymentService
     │
     ├── RazorpayService
     ├── StripeService
     └── PaypalService
```

---

Default:

```java
@Primary
RazorpayService
```

---

Most injections:

```java
@Autowired
PaymentService paymentService;
```

use:

```java
RazorpayService
```

---

Special cases:

```java
@Qualifier("stripeService")
```

can override.

---

# Common Mistakes

## Mistake 1

Using multiple primary beans.

---

```java
@Primary
```

on more than one implementation.

---

Result:

```text
NoUniqueBeanDefinitionException
```

---

## Mistake 2

Assuming @Primary wins over @Qualifier.

Wrong.

```text
@Qualifier always wins.
```

---

## Mistake 3

Using @Primary unnecessarily.

If only one bean exists:

```java
@Primary
```

provides no benefit.

---

# Common Interview Questions

## What is @Primary?

Specifies the default bean for dependency injection.

---

## When is it used?

When multiple beans of the same type exist.

---

## What problem does it solve?

```text
NoUniqueBeanDefinitionException
```

---

## Can @Primary be used with @Bean?

Yes.

Very common.

---

## What has higher priority?

```java
@Qualifier
```

or

```java
@Primary
```

Answer:

```java
@Qualifier
```

---

## Can multiple beans be primary?

Technically yes.

But it causes ambiguity and injection failure.

---

## Does @Primary affect collection injection?

No.

All beans are still injected into collections.

---

# Real-World Significance

Enterprise systems frequently have multiple implementations of the same interface:

```text
NotificationService
 ├── EmailService
 ├── SmsService
 └── PushNotificationService
```

```text
PaymentService
 ├── Razorpay
 ├── Stripe
 └── Paypal
```

```text
StorageService
 ├── AWS
 ├── Azure
 └── GCP
```

`@Primary` allows one implementation to become the application's default choice while still allowing specific implementations to be selected using `@Qualifier`.

---

# Key Points To Remember

- Specifies the default bean for dependency injection.
- Used when multiple beans of the same type exist.
- Prevents `NoUniqueBeanDefinitionException`.
- Works with `@Component`, `@Service`, `@Repository`, and `@Bean`.
- Only affects single-bean injection.
- Does not affect collection injection.
- Lower priority than `@Qualifier`.
- Typically only one primary bean should exist per type.
- Commonly used in enterprise applications with multiple implementations.
- One of the most important dependency resolution annotations in Spring.