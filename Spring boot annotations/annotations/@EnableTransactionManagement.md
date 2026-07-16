
## Definition

`@EnableTransactionManagement` is a Spring annotation used to enable **declarative transaction management** in a Spring application.

It tells Spring:

> Scan the application for `@Transactional` annotations and automatically manage transactions using AOP proxies.

Package:

```java
import org.springframework.transaction.annotation.EnableTransactionManagement;
```

---

# Why Do We Need @EnableTransactionManagement?

Without transaction management:

```java
Connection connection =
    dataSource.getConnection();

connection.setAutoCommit(false);

try {

    // Database operations

    connection.commit();

} catch(Exception e) {

    connection.rollback();
}
```

---

Lots of boilerplate code.

---

Spring provides:

```java
@Transactional
```

---

But Spring must know:

```text
Transaction Management Is Enabled
```

---

That is the purpose of:

```java
@EnableTransactionManagement
```

---

# Basic Example

```java
@Configuration

@EnableTransactionManagement
public class DatabaseConfig {
}
```

---

Now Spring will process:

```java
@Transactional
```

annotations.

---

# What Happens Internally?

When Spring starts:

```java
@EnableTransactionManagement
```

---

Triggers:

```text
Transaction Infrastructure
```

---

Spring creates:

```text
AOP Proxies
```

around beans.

---

Example:

```java
@Service
public class EmployeeService {

    @Transactional
    public void saveEmployee() {
    }
}
```

---

Spring creates:

```text
Proxy
   ↓
EmployeeService
```

---

Flow:

```text
Method Call
      ↓
Proxy Intercepts
      ↓
Begin Transaction
      ↓
Execute Method
      ↓
Commit/Rollback
```

---

# Without @EnableTransactionManagement

Suppose:

```java
@Transactional
public void save() {
}
```

---

Spring treats it as:

```text
Normal Annotation
```

---

No transaction is created.

---

# With @EnableTransactionManagement

```java
@EnableTransactionManagement
```

---

Now Spring recognizes:

```java
@Transactional
```

---

Transactions become active.

---

# Example

## Service

```java
@Service
public class EmployeeService {

    @Transactional
    public void saveEmployee() {

        repository.save(emp1);

        repository.save(emp2);
    }
}
```

---

Generated:

```sql
INSERT ...
INSERT ...
COMMIT
```

---

If exception occurs:

```java
int x = 10 / 0;
```

---

Generated:

```sql
ROLLBACK
```

---

Because transaction management is enabled.

---

# Internal Components

When enabled:

```java
@EnableTransactionManagement
```

---

Spring registers:

```java
TransactionInterceptor
```

---

and

```java
BeanFactoryTransactionAttributeSourceAdvisor
```

---

and

```java
PlatformTransactionManager
```

---

These work together to provide:

```java
@Transactional
```

support.

---

# Relationship Between Annotations

Most Important Interview Topic.

---

```java
@EnableTransactionManagement
```

---

Enables:

```java
@Transactional
```

---

Which uses:

```java
PlatformTransactionManager
```

---

Flow:

```text
@EnableTransactionManagement
               ↓
Recognizes @Transactional
               ↓
Uses Transaction Manager
               ↓
Commit/Rollback
```

---

# Transaction Manager

Required for transaction support.

---

Example:

```java
@Bean
public PlatformTransactionManager
transactionManager(
        EntityManagerFactory emf) {

    return new JpaTransactionManager(emf);
}
```

---

Spring Boot automatically configures this.

---

Usually no manual configuration required.

---

# XML Equivalent

Old Spring applications used:

```xml
<tx:annotation-driven/>
```

---

Equivalent Java configuration:

```java
@EnableTransactionManagement
```

---

# Proxy Mode

Default:

```java
AdviceMode.PROXY
```

---

Example:

```java
@EnableTransactionManagement(
    mode = AdviceMode.PROXY
)
```

---

Uses Spring AOP proxies.

---

Most common.

---

# AspectJ Mode

Alternative:

```java
@EnableTransactionManagement(
    mode = AdviceMode.ASPECTJ
)
```

---

Uses:

```text
AspectJ Weaving
```

---

Supports:

```text
Private Methods
Self Invocation
```

---

Rarely used.

---

# Order Attribute

```java
@EnableTransactionManagement(
    order = 1
)
```

---

Defines execution order among aspects.

---

Useful when combining:

```java
Security
Logging
Transactions
```

---

Rarely configured manually.

---

# Spring Boot

Important.

---

In modern Spring Boot applications:

```java
@SpringBootApplication
```

---

plus:

```text
spring-boot-starter-data-jpa
```

---

automatically enables transaction management.

---

Meaning:

```java
@EnableTransactionManagement
```

is often unnecessary.

---

Spring Boot internally configures it.

---

# Example in Spring Boot

```java
@SpringBootApplication
public class Application {
}
```

---

Service:

```java
@Service
public class EmployeeService {

    @Transactional
    public void saveEmployee() {
    }
}
```

---

Works even without:

```java
@EnableTransactionManagement
```

---

Because Boot enables it automatically.

---

# Internal Workflow

Application Startup:

```text
Read @EnableTransactionManagement
            ↓
Register Transaction Advisor
            ↓
Create Proxies
            ↓
Detect @Transactional
            ↓
Manage Transactions
```

---

Method Call:

```java
saveEmployee()
```

---

Flow:

```text
Proxy
  ↓
Begin Transaction
  ↓
Execute Method
  ↓
Commit/Rollback
```

---

# Common Mistakes

## Mistake 1

Expecting Transactions Without Configuration

Plain Spring:

```java
@Transactional
```

---

Without:

```java
@EnableTransactionManagement
```

---

No transaction support.

---

# Mistake 2

Using Private Methods

```java
@Transactional
private void save() {
}
```

---

Proxy cannot intercept.

---

No transaction.

---

# Mistake 3

Self Invocation

```java
public void methodA() {
    methodB();
}
```

---

```java
@Transactional
public void methodB() {
}
```

---

Transaction ignored.

---

Because proxy is bypassed.

---

# Common Interview Questions

## What is @EnableTransactionManagement?

Enables Spring's annotation-driven transaction management.

---

## Why is it needed?

To activate processing of:

```java
@Transactional
```

---

## What happens internally?

Spring creates AOP proxies and transaction interceptors.

---

## Is it required in Spring Boot?

Usually no.

Spring Boot enables transaction management automatically.

---

## Which transaction manager is used?

Usually:

```java
JpaTransactionManager
```

for JPA applications.

---

## What is the default mode?

```java
AdviceMode.PROXY
```

---

# Enterprise Best Practice

For modern Spring Boot:

```java
@SpringBootApplication
```

is usually sufficient.

---

For plain Spring:

```java
@Configuration

@EnableTransactionManagement
```

is required.

---

Always place:

```java
@Transactional
```

on service layer methods.

---

Keep transaction logic out of controllers.

---

# Key Points To Remember

- `@EnableTransactionManagement` enables annotation-driven transactions.
- Activates processing of `@Transactional`.
- Uses Spring AOP proxies internally.
- Works with `PlatformTransactionManager`.
- Equivalent to XML `<tx:annotation-driven/>`.
- Supports PROXY and ASPECTJ modes.
- Usually unnecessary in Spring Boot because Boot enables it automatically.
- Essential in traditional Spring applications.
- Creates transaction interceptors and advisors.
- Frequently asked Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableTransactionManagement` | Enable Transaction Support |
| `@Transactional` | Define Transaction Boundary |
| `@Rollback` | Rollback Test Transactions |
| `@Commit` | Commit Test Transactions |

---

# Interview Shortcut

```java
@Configuration

@EnableTransactionManagement
public class Config {
}
```

Meaning:

```text
Enable Transaction Infrastructure
            ↓
Detect @Transactional
            ↓
Create AOP Proxies
            ↓
Manage Commit/Rollback Automatically
```

Typical production flow:

```java
@EnableTransactionManagement
        +
@Transactional
```

```text
Method Call
      ↓
Proxy Intercepts
      ↓
Transaction Starts
      ↓
Business Logic Executes
      ↓
Commit / Rollback
```

This is the core Spring mechanism that activates declarative transaction management and allows `@Transactional` methods to work automatically.