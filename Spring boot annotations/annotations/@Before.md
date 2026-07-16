
## Definition

`@Before` is a Spring AOP advice annotation used to execute code **before a target method is invoked**.

It tells Spring:

> Run this advice before the matched method execution starts.

Package:

```java
import org.aspectj.lang.annotation.Before;
```

---

# Why Do We Need @Before?

Suppose every service method needs:

```text
Logging
Authentication
Authorization
Validation
Auditing
```

Without AOP:

```java
public void saveEmployee() {

    System.out.println("Method Started");

    // Business Logic
}
```

---

Need to repeat this in:

```text
100+ Methods
```

---

Problems:

```text
Code Duplication
Maintenance Issues
Messy Business Logic
```

---

Solution:

```java
@Before
```

---

# Basic Example

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

# What Happens Internally?

Method Call:

```java
saveEmployee()
```

---

Spring Proxy:

```text
Intercept Method
        ↓
Execute @Before Advice
        ↓
Execute Actual Method
```

---

Flow:

```text
Client
  ↓
Proxy
  ↓
@Before
  ↓
Target Method
```

---

# Syntax

```java
@Before(
 "pointcut-expression"
)
```

Example:

```java
@Before(
 "execution(* *(..))"
)
```

---

Meaning:

```text
Run Before Every Method
```

---

# Pointcut Expressions

Most Important Interview Topic.

---

## All Methods

```java
@Before(
 "execution(* *(..))"
)
```

---

Matches:

```text
Every Method
```

---

# All Methods In Package

```java
@Before(
 "execution(* com.demo.service.*.*(..))"
)
```

---

Matches:

```text
All Service Methods
```

---

# Specific Method

```java
@Before(
 "execution(* saveEmployee(..))"
)
```

---

Matches:

```java
saveEmployee()
```

only.

---

# Methods Returning void

```java
@Before(
 "execution(void *.*(..))"
)
```

---

Matches:

```text
All Void Methods
```

---

# Using JoinPoint

Allows access to method details.

---

```java
@Before(
 "execution(* com.demo.service.*.*(..))"
)
public void log(
        JoinPoint jp) {

    System.out.println(
       jp.getSignature()
    );
}
```

---

Output:

```text
void EmployeeService.saveEmployee()
```

---

# Access Method Name

```java
@Before(
 "execution(* *(..))"
)
public void log(
        JoinPoint jp) {

    System.out.println(
      jp.getSignature()
        .getName()
    );
}
```

---

Output:

```text
saveEmployee
```

---

# Access Method Arguments

```java
@Before(
 "execution(* *(..))"
)
public void log(
        JoinPoint jp) {

    Object[] args =
        jp.getArgs();

    for(Object arg : args) {

        System.out.println(arg);
    }
}
```

---

Method:

```java
saveEmployee(
   "Sumit",
   50000
);
```

---

Output:

```text
Sumit
50000
```

---

# Logging Example

```java
@Aspect
@Component
public class LoggingAspect {

    @Before(
      "execution(* com.demo.service.*.*(..))"
    )
    public void log(
       JoinPoint jp) {

        System.out.println(
          "Executing: "
          +
          jp.getSignature()
            .getName()
        );
    }
}
```

---

Output:

```text
Executing: saveEmployee
```

---

# Security Example

```java
@Before(
 "execution(* com.demo.service.*.*(..))"
)
public void authorize() {

    System.out.println(
      "Checking Permissions"
    );
}
```

---

Output:

```text
Checking Permissions
```

---

Then method executes.

---

# Validation Example

```java
@Before(
 "execution(* com.demo.service.*.*(..))"
)
public void validate() {

    System.out.println(
      "Validating Request"
    );
}
```

---

Output:

```text
Validating Request
```

---

Before actual business logic.

---

# Reusable Pointcut

Instead of repeating expressions:

---

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
public void before() {
}
```

---

Cleaner and reusable.

---

# Real Production Example

Audit Logging.

---

```java
@Aspect
@Component
public class AuditAspect {

    @Before(
      "execution(* com.demo.service.*.*(..))"
    )
    public void audit(
            JoinPoint jp) {

        System.out.println(
          "User Called: "
          +
          jp.getSignature()
           .getName()
        );
    }
}
```

---

Useful for:

```text
Auditing
Tracing
Monitoring
```

---

# Internal Workflow

Application Startup:

```text
Detect @Aspect
        ↓
Detect @Before
        ↓
Create Proxy
```

---

Method Execution:

```text
Call Method
     ↓
Proxy Intercepts
     ↓
Execute @Before Advice
     ↓
Execute Actual Method
```

---

# @Before vs Other Advice Types

Interview Favorite.

---

## @Before

Runs before method.

---

```text
Before Execution
```

---

## @After

Runs after method.

---

```text
Always Executes
```

---

## @AfterReturning

Runs only when successful.

---

```text
After Success
```

---

## @AfterThrowing

Runs only when exception occurs.

---

```text
After Failure
```

---

## @Around

Runs before and after.

---

```text
Most Powerful
```

---

Comparison:

| Advice | When Executes |
|----------|---------------|
| `@Before` | Before Method |
| `@After` | After Method |
| `@AfterReturning` | After Success |
| `@AfterThrowing` | After Exception |
| `@Around` | Before & After |

---

# Common Mistakes

## Mistake 1

Missing @Aspect

Wrong:

```java
@Before(...)
public void log() {
}
```

---

Must be inside:

```java
@Aspect
```

class.

---

# Mistake 2

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

# Mistake 3

Wrong Pointcut

```java
execution(...)
```

must match actual method signatures.

---

Otherwise advice never runs.

---

# Common Interview Questions

## What is @Before?

An AOP advice executed before a target method.

---

## Can It Stop Method Execution?

No.

Only runs before method.

---

For full control use:

```java
@Around
```

---

## Can It Access Method Arguments?

Yes.

Using:

```java
JoinPoint
```

---

## Can It Access Method Name?

Yes.

```java
joinPoint
    .getSignature()
    .getName()
```

---

## Most Common Use Cases?

```text
Logging
Security
Auditing
Validation
Monitoring
```

---

# Enterprise Best Practice

Use:

```java
@Before
```

for:

```text
Logging
Permission Checks
Request Validation
Audit Tracking
```

---

Avoid:

```text
Business Logic
Database Updates
Heavy Processing
```

inside advice.

---

Keep advice lightweight.

---

# Key Points To Remember

- `@Before` executes before a matched method.
- Part of Spring AOP.
- Must be inside an `@Aspect` class.
- Uses pointcut expressions to select methods.
- Can access method details using `JoinPoint`.
- Commonly used for logging and security.
- Cannot prevent execution directly.
- Implemented through Spring proxies.
- Executes before the target method starts.
- Frequently asked Spring AOP interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Before` | Before Method Execution |
| `@After` | After Method Execution |
| `@AfterReturning` | After Successful Execution |
| `@AfterThrowing` | After Exception |
| `@Around` | Around Entire Execution |

---

# Interview Shortcut

```java
@Before(
 "execution(* com.demo.service.*.*(..))"
)
public void log() {

    System.out.println(
      "Method Started"
    );
}
```

Meaning:

```text
Method Called
      ↓
Proxy Intercepts
      ↓
Execute @Before Advice
      ↓
Execute Actual Method
```

Typical production usage:

```java
@Before
```

for:

```text
Logging
Authentication
Authorization
Validation
Auditing
```

This is the simplest and most commonly used Spring AOP advice for executing logic immediately before a method begins execution.