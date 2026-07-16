
## Definition

`@Aspect` is a Spring AOP annotation used to define an **Aspect**.

An Aspect contains logic that should be applied across multiple classes, such as:

```text
Logging
Security
Transactions
Auditing
Performance Monitoring
Exception Handling
```

It tells Spring:

> This class contains cross-cutting concerns and advice that should be executed at specific points during application execution.

Package:

```java
import org.aspectj.lang.annotation.Aspect;
```

---

# What Problem Does @Aspect Solve?

Imagine 100 service methods:

```java
createEmployee()
updateEmployee()
deleteEmployee()
getEmployee()
saveOrder()
cancelOrder()
...
```

---

Now management asks:

```text
Log every method call.
```

Without AOP:

```java
public void saveEmployee() {

    System.out.println("Method Started");

    // business logic

    System.out.println("Method Ended");
}
```

---

Need to repeat in:

```text
100 Methods
```

---

Problems:

```text
Code Duplication
Hard Maintenance
Messy Business Logic
```

---

Solution:

```java
@Aspect
```

---

# What is an Aspect?

An Aspect is a module containing:

```text
Advice
+
Pointcut
```

---

Example:

```java
@Aspect
@Component
public class LoggingAspect {
}
```

---

Meaning:

```text
Apply Logging Logic
Across Multiple Classes
```

---

# Real-Life Analogy

Think of:

```text
Airport Security
```

---

Every passenger passes through:

```text
Security Check
```

---

Regardless of:

```text
Destination
Airline
Seat Class
```

---

Security is a:

```text
Cross-Cutting Concern
```

---

Similarly:

```text
Logging
Security
Transactions
```

apply across many methods.

---

# Basic Example

```java
@Aspect
@Component
public class LoggingAspect {

}
```

---

This tells Spring:

```text
This Class Contains AOP Logic
```

---

# AOP Terminology

Very Important Interview Topic.

---

## Aspect

The class containing cross-cutting logic.

```java
@Aspect
public class LoggingAspect {
}
```

---

## Advice

Action executed at a join point.

Example:

```java
@Before
```

---

## Join Point

A specific method execution.

Example:

```java
employeeService.saveEmployee()
```

---

## Pointcut

Expression identifying join points.

Example:

```java
execution(* com.demo.service.*.*(..))
```

---

## Weaving

Process of applying aspect to target object.

---

# Before Advice

Execute before method.

---

```java
@Aspect
@Component
public class LoggingAspect {

    @Before(
      "execution(* com.demo.service.*.*(..))"
    )
    public void log() {

        System.out.println(
            "Method Started"
        );
    }
}
```

---

Service:

```java
employeeService.save();
```

---

Output:

```text
Method Started
```

---

Then method executes.

---

# After Advice

Runs after method finishes.

---

```java
@After(
 "execution(* com.demo.service.*.*(..))"
)
public void after() {

    System.out.println(
      "Method Finished"
    );
}
```

---

Output:

```text
Method Started
Business Logic
Method Finished
```

---

# After Returning

Runs only when method succeeds.

---

```java
@AfterReturning(
 "execution(* com.demo.service.*.*(..))"
)
public void success() {

    System.out.println(
      "Success"
    );
}
```

---

Output:

```text
Success
```

---

Only if no exception.

---

# After Throwing

Runs only when exception occurs.

---

```java
@AfterThrowing(
 "execution(* com.demo.service.*.*(..))"
)
public void error() {

    System.out.println(
      "Exception Occurred"
    );
}
```

---

Output:

```text
Exception Occurred
```

---

# Around Advice

Most powerful advice.

---

Can:

```text
Before
After
Modify Result
Handle Exception
Measure Time
```

---

Example:

```java
@Around(
 "execution(* com.demo.service.*.*(..))"
)
public Object around(
       ProceedingJoinPoint pjp
) throws Throwable {

    System.out.println("Before");

    Object result =
        pjp.proceed();

    System.out.println("After");

    return result;
}
```

---

Output:

```text
Before
Business Logic
After
```

---

# Complete Example

## Service

```java
@Service
public class EmployeeService {

    public void saveEmployee() {

        System.out.println(
            "Saving Employee"
        );
    }
}
```

---

## Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @Before(
      "execution(* com.demo.service.*.*(..))"
    )
    public void before() {

        System.out.println(
            "Method Started"
        );
    }
}
```

---

Execution:

```java
employeeService.saveEmployee();
```

---

Output:

```text
Method Started
Saving Employee
```

---

# Pointcut Expressions

Most Asked Interview Topic.

---

## All Methods

```java
execution(* *(..))
```

---

## All Methods In Package

```java
execution(
 * com.demo.service.*.*(..)
)
```

---

## Specific Method

```java
execution(
 * saveEmployee(..)
)
```

---

## Specific Return Type

```java
execution(
 void saveEmployee(..)
)
```

---

# Reusable Pointcut

```java
@Pointcut(
 "execution(* com.demo.service.*.*(..))"
)
public void serviceMethods() {
}
```

---

Use:

```java
@Before(
 "serviceMethods()"
)
```

---

Cleaner.

---

# Internal Workflow

Method Call:

```java
employeeService.save()
```

---

Spring AOP:

```text
Proxy Created
      ↓
Intercept Method
      ↓
Execute Advice
      ↓
Execute Method
      ↓
Execute Remaining Advice
```

---

# How Spring AOP Works

Spring creates:

```text
Proxy Object
```

---

Instead of:

```java
EmployeeService
```

Spring provides:

```java
EmployeeServiceProxy
```

---

Flow:

```text
Client
  ↓
Proxy
  ↓
Aspect
  ↓
Target Method
```

---

# Common Use Cases

---

## Logging

```java
@Before
```

Log method calls.

---

## Security

```java
@Before
```

Check user permissions.

---

## Performance Monitoring

```java
@Around
```

Measure execution time.

---

## Auditing

```java
@AfterReturning
```

Store activity logs.

---

## Exception Tracking

```java
@AfterThrowing
```

Log errors.

---

# @Aspect vs @Transactional

Interview Favorite.

---

## @Aspect

Generic AOP functionality.

---

Example:

```text
Logging
Security
Auditing
```

---

## @Transactional

Built using Spring AOP internally.

---

Example:

```java
@Transactional
```

creates an internal aspect.

---

Comparison:

| Feature | @Aspect | @Transactional |
|----------|----------|----------------|
| Logging | ✅ | ❌ |
| Security | ✅ | ❌ |
| Auditing | ✅ | ❌ |
| Transactions | Possible | ✅ |
| Generic AOP | ✅ | ❌ |

---

# Common Mistakes

## Mistake 1

Missing @Component

Wrong:

```java
@Aspect
public class LoggingAspect {
}
```

---

Spring won't create bean.

---

Need:

```java
@Component
```

---

## Mistake 2

Forgetting AOP Dependency

Need:

```xml
spring-boot-starter-aop
```

---

Without it:

```java
@Aspect
```

won't work.

---

## Mistake 3

Wrong Pointcut

```java
execution(...)
```

must match method signatures correctly.

---

# Common Interview Questions

## What is @Aspect?

Defines an Aspect containing cross-cutting concerns.

---

## What is AOP?

Aspect-Oriented Programming.

---

## What Problems Does It Solve?

```text
Logging
Security
Auditing
Transactions
Monitoring
```

---

## What is a Pointcut?

Expression identifying where advice should execute.

---

## What is Advice?

Code executed before/after a method.

---

## Most Powerful Advice?

```java
@Around
```

---

## How Does Spring AOP Work?

Using proxies.

---

## Is @Transactional Based On AOP?

Yes.

---

# Enterprise Best Practice

Use AOP only for:

```text
Cross-Cutting Concerns
```

---

Avoid placing:

```text
Business Logic
```

inside aspects.

---

Keep aspects:

```text
Small
Reusable
Focused
```

---

Prefer:

```java
@Around
```

for:

```text
Performance Monitoring
Tracing
Metrics
```

---

# Key Points To Remember

- `@Aspect` defines an AOP aspect.
- Used for cross-cutting concerns.
- Requires `spring-boot-starter-aop`.
- Usually combined with `@Component`.
- Uses pointcuts to identify methods.
- Supports Before, After, Around, AfterReturning, and AfterThrowing advice.
- Implemented using Spring AOP proxies.
- Reduces code duplication.
- Used heavily in enterprise applications.
- Frequently asked in Spring interviews.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Aspect` | Define Aspect |
| `@Pointcut` | Reusable Pointcut |
| `@Before` | Before Method |
| `@After` | After Method |
| `@AfterReturning` | After Success |
| `@AfterThrowing` | After Exception |
| `@Around` | Around Method |

---

# Interview Shortcut

```java
@Aspect
@Component
public class LoggingAspect {

    @Before(
      "execution(* com.demo.service.*.*(..))"
    )
    public void log() {
        System.out.println(
            "Method Started"
        );
    }
}
```

Meaning:

```text
Method Called
      ↓
Spring Proxy Intercepts
      ↓
Execute Aspect Logic
      ↓
Execute Actual Method
```

Most common production usage:

```java
Logging
Security
Performance Monitoring
Auditing
Exception Tracking
```

This is the core Spring AOP annotation used to separate cross-cutting concerns from business logic and apply them across an application in a clean, reusable way.