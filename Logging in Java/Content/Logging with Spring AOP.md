# Logging with Spring AOP

> **Goal:** Understand Aspect-Oriented Logging using Spring AOP, logging Method Entry, Method Exit, Execution Time, Exceptions, using @Before, @After, @Around annotations, production use cases, best practices, and interview concepts.

---

# 1. Introduction to Logging with Spring AOP

In enterprise applications, logging is required in many places.

Examples:

```text
Controllers

Services

Repositories

Utility Classes
```

---

Traditional logging approach:

```java
public Employee getEmployee(Long id) {

    log.info("Entering getEmployee()");

    Employee employee =
            employeeRepository.findById(id);

    log.info("Exiting getEmployee()");

    return employee;
}
```

---

Problem:

```text
Repeated Logging Code

Code Duplication

Poor Maintainability
```

---

Imagine:

```text
100 Services

500 Methods
```

---

Adding logging manually becomes difficult.

---

# Solution

Use:

```text
Spring AOP
```

---

# What is AOP?

AOP stands for:

```text
Aspect-Oriented Programming
```

---

## Definition

> AOP is a programming paradigm that allows cross-cutting concerns such as logging, security, auditing, and transaction management to be separated from business logic.

---

# Cross-Cutting Concerns

Features used across multiple classes.

Examples:

```text
Logging

Security

Transactions

Monitoring

Auditing
```

---

Instead of writing logging code everywhere:

```java
log.info(...)
```

AOP automatically handles logging.

---

# AOP Logging Flow

```text
Method Call

    ↓

Aspect

    ↓

Log Entry

    ↓

Business Method

    ↓

Log Exit

    ↓

Return Result
```

---

# Benefits

```text
Centralized Logging

Reusable Code

Cleaner Business Logic

Easy Maintenance
```

---

# 2. Key AOP Concepts

Before learning logging, understand the AOP terminology.

---

# Aspect

A class containing logging logic.

---

Example:

```java
@Aspect
@Component
public class LoggingAspect {

}
```

---

Aspect contains:

```text
Logging Logic

Security Logic

Audit Logic
```

---

# Join Point

A point during application execution.

Examples:

```text
Method Call

Method Execution

Exception Throwing
```

---

# Pointcut

Defines:

```text
Which Methods
Should Be Intercepted
```

---

Example:

```java
execution(
    * com.company.service.*.*(..)
)
```

---

Meaning:

```text
Intercept All Methods
Inside Service Package
```

---

# Advice

Action executed at a Join Point.

Examples:

```text
@Before

@After

@Around
```

---

# AOP Architecture

```text
Application Method

      ↓

Pointcut Match

      ↓

Advice Executes

      ↓

Business Logic
```

---

# 3. Aspect-Oriented Logging

The most common AOP use case.

---

# Why Use AOP for Logging?

Without AOP:

```java
log.info("Entering");

method();

log.info("Exiting");
```

---

Repeated everywhere.

---

With AOP:

```java
@Around(...)
```

---

Logging happens automatically.

---

# Typical Logging Activities

```text
Method Entry

Method Exit

Execution Time

Exceptions

Arguments

Return Values
```

---

# Logging Flow

```text
Method Call

     ↓

Aspect

     ↓

Entry Log

     ↓

Method Execution

     ↓

Exit Log

     ↓

Execution Time
```

---

# 4. Method Entry Logging

One of the most common requirements.

---

## What is Method Entry Logging?

Logging when a method starts execution.

---

# Example

Method:

```java
public Employee getEmployee(Long id)
```

---

Entry Log:

```text
Entering Method:
getEmployee()
```

---

# Why Log Method Entry?

Helps identify:

```text
Method Invoked

Execution Path

Input Flow
```

---

# Using @Before

```java
@Before(
    "execution(* com.company.service.*.*(..))"
)
public void logMethodEntry(
        JoinPoint joinPoint) {

    log.info(
        "Entering Method: {}",
        joinPoint.getSignature()
    );
}
```

---

# Output

```text
Entering Method:
EmployeeService.getEmployee()
```

---

# Flow

```text
Method Call

      ↓

@Before

      ↓

Entry Log

      ↓

Method Execution
```

---

# Benefits

```text
Traceability

Debugging

Monitoring
```

---

# 5. Method Exit Logging

Logs when method execution completes.

---

## Why Method Exit Logging?

Helps determine:

```text
Method Completed

Execution Successful

Return Flow
```

---

# Using @After

```java
@After(
    "execution(* com.company.service.*.*(..))"
)
public void logMethodExit(
        JoinPoint joinPoint) {

    log.info(
        "Exiting Method: {}",
        joinPoint.getSignature()
    );
}
```

---

# Output

```text
Exiting Method:
EmployeeService.getEmployee()
```

---

# Flow

```text
Method Starts

     ↓

Business Logic

     ↓

@After

     ↓

Exit Log
```

---

# Difference

Method Entry:

```text
Before Execution
```

---

Method Exit:

```text
After Execution
```

---

# Benefits

```text
Execution Tracking

Monitoring

Flow Analysis
```

---

# 6. Execution Time Logging

Extremely important in production systems.

---

## Why Measure Execution Time?

Helps identify:

```text
Slow APIs

Performance Bottlenecks

Database Delays
```

---

# Example

Method:

```java
getEmployee()
```

---

Start Time:

```text
10:00:00
```

---

End Time:

```text
10:00:02
```

---

Execution Time:

```text
2000 ms
```

---

# Using @Around

Most common approach.

---

# Example

```java
@Around(
    "execution(* com.company.service.*.*(..))"
)
public Object logExecutionTime(
        ProceedingJoinPoint joinPoint)
        throws Throwable {

    long start =
            System.currentTimeMillis();

    Object result =
            joinPoint.proceed();

    long end =
            System.currentTimeMillis();

    log.info(
        "{} executed in {} ms",
        joinPoint.getSignature(),
        (end - start)
    );

    return result;
}
```

---

# Output

```text
EmployeeService.getEmployee()

Executed In

2000 ms
```

---

# Flow

```text
Method Called

      ↓

Capture Start Time

      ↓

Execute Method

      ↓

Capture End Time

      ↓

Calculate Duration

      ↓

Log Time
```

---

# Benefits

```text
Performance Monitoring

Bottleneck Detection

Optimization Analysis
```

---

# 7. Exception Logging

Very important production requirement.

---

## Why Log Exceptions?

To identify:

```text
What Failed?

Where Failed?

Why Failed?
```

---

# Example

```java
@Around(
    "execution(* com.company.service.*.*(..))"
)
public Object logExceptions(
        ProceedingJoinPoint joinPoint)
        throws Throwable {

    try {

        return joinPoint.proceed();

    } catch (Exception ex) {

        log.error(
            "Exception in {}",
            joinPoint.getSignature(),
            ex
        );

        throw ex;
    }
}
```

---

# Output

```text
Exception In

EmployeeService.getEmployee()

java.lang.NullPointerException
```

---

# Benefits

```text
Root Cause Analysis

Production Support

Debugging
```

---

# Exception Logging Flow

```text
Method Called

      ↓

Exception Occurs

      ↓

Aspect Captures

      ↓

Log Exception

      ↓

Re-Throw Exception
```

---

# 8. @Before Annotation

Most basic AOP advice.

---

## Purpose

Executes:

```text
Before Method Execution
```

---

# Syntax

```java
@Before(
    "execution(* com.company.*.*(..))"
)
```

---

# Use Cases

```text
Method Entry Logging

Security Checks

Validation
```

---

# Flow

```text
@Before

     ↓

Method Executes
```

---

# Example

```java
@Before(
    "execution(* com.company.service.*.*(..))"
)
public void beforeMethod(
        JoinPoint joinPoint) {

    log.info(
        "Entering {}",
        joinPoint.getSignature()
    );
}
```

---

# Characteristics

```text
Runs Before Method

Cannot Control Return Value

Cannot Measure Full Execution Time
```

---

# 9. @After Annotation

Runs after method execution.

---

## Purpose

Executes:

```text
After Method Completion
```

---

# Syntax

```java
@After(
    "execution(* com.company.*.*(..))"
)
```

---

# Example

```java
@After(
    "execution(* com.company.service.*.*(..))"
)
public void afterMethod(
        JoinPoint joinPoint) {

    log.info(
        "Exiting {}",
        joinPoint.getSignature()
    );
}
```

---

# Output

```text
Exiting Method
```

---

# Characteristics

```text
Runs After Method

Runs Even If Exception Occurs

Cannot Access Return Value
```

---

# Use Cases

```text
Method Exit Logging

Cleanup Activities
```

---

# 10. @Around Annotation

Most powerful advice type.

---

## Purpose

Executes:

```text
Before Method

After Method
```

---

and controls:

```text
Method Execution
```

---

# Syntax

```java
@Around(
    "execution(* com.company.*.*(..))"
)
```

---

# Example

```java
@Around(
    "execution(* com.company.service.*.*(..))"
)
public Object logMethod(
        ProceedingJoinPoint joinPoint)
        throws Throwable {

    log.info(
        "Entering {}",
        joinPoint.getSignature()
    );

    Object result =
            joinPoint.proceed();

    log.info(
        "Exiting {}",
        joinPoint.getSignature()
    );

    return result;
}
```

---

# Flow

```text
Before Log

     ↓

Method Executes

     ↓

After Log
```

---

# Advantages

```text
Method Entry Logging

Method Exit Logging

Execution Time Logging

Exception Logging

Return Value Logging
```

---

# Why Is @Around Preferred?

Provides complete control.

---

Can:

```text
Execute Method

Skip Method

Modify Result

Measure Time

Handle Exceptions
```

---

# 11. Real Production Logging Aspect

```java
@Aspect
@Component
@Slf4j
public class LoggingAspect {

    @Around(
      "execution(* com.company.service.*.*(..))"
    )
    public Object logMethod(
            ProceedingJoinPoint joinPoint)
            throws Throwable {

        long start =
            System.currentTimeMillis();

        try {

            log.info(
                "Entering {}",
                joinPoint.getSignature()
            );

            Object result =
                    joinPoint.proceed();

            return result;

        } catch (Exception ex) {

            log.error(
                "Exception in {}",
                joinPoint.getSignature(),
                ex
            );

            throw ex;

        } finally {

            long end =
                System.currentTimeMillis();

            log.info(
                "Exiting {} in {} ms",
                joinPoint.getSignature(),
                (end - start)
            );
        }
    }
}
```

---

# Output

```text
Entering EmployeeService.getEmployee()

Exiting EmployeeService.getEmployee()

Execution Time: 120 ms
```

---

# 12. Best Practices

---

## Use @Around for Logging

Most flexible advice.

---

## Log Execution Time

Useful for:

```text
Performance Monitoring
```

---

## Log Exceptions

Always include:

```java
log.error(
    "Message",
    ex
);
```

---

## Avoid Excessive Logging

Too many logs:

```text
Increase Noise

Reduce Readability
```

---

## Restrict Pointcuts

Avoid:

```java
execution(* *(..))
```

---

Use:

```java
execution(
    * com.company.service.*.*(..)
)
```

---

# 13. Common Mistakes

---

## Logging Every Method

Problem:

```text
Huge Log Volume
```

---

## Ignoring Exceptions

Bad:

```java
catch(Exception ex) {

}
```

---

## Using Broad Pointcuts

Intercepting:

```text
Entire Application
```

unnecessarily.

---

## Logging Sensitive Data

Avoid:

```text
Passwords

Tokens

Credit Cards
```

---

# 14. Interview Questions

### Q1. What is AOP?

Aspect-Oriented Programming used to separate cross-cutting concerns from business logic.

---

### Q2. What are common cross-cutting concerns?

```text
Logging

Security

Auditing

Transactions
```

---

### Q3. What is an Aspect?

A class containing AOP logic.

---

### Q4. What is a Join Point?

A point during program execution where advice can be applied.

---

### Q5. What is a Pointcut?

An expression defining which methods should be intercepted.

---

### Q6. Which annotation is used for Method Entry Logging?

```java
@Before
```

---

### Q7. Which annotation is used for Method Exit Logging?

```java
@After
```

---

### Q8. Which annotation is best for Execution Time Logging?

```java
@Around
```

---

### Q9. Why is @Around considered the most powerful advice?

Because it can:

```text
Run Before Method

Run After Method

Control Execution

Measure Time

Handle Exceptions
```

---

### Q10. What object is used with @Around?

```java
ProceedingJoinPoint
```

---

# Quick Revision

```text
Logging with Spring AOP
-----------------------

Purpose

✔ Centralized Logging
✔ Cleaner Code
✔ Reusable Logic

--------------------------------

AOP Components

Aspect
Join Point
Pointcut
Advice

--------------------------------

Method Entry Logging

@Before

--------------------------------

Method Exit Logging

@After

--------------------------------

Execution Time Logging

@Around

--------------------------------

Exception Logging

@Around

try-catch

--------------------------------

Most Powerful Advice

@Around

Reason

✔ Before Method
✔ After Method
✔ Time Calculation
✔ Exception Handling
✔ Return Value Access

--------------------------------

Typical Logging

Entering Method

Business Logic

Exiting Method

Execution Time

--------------------------------

Benefits

✔ Less Boilerplate
✔ Better Monitoring
✔ Easier Maintenance
✔ Consistent Logging

--------------------------------

Best Practice

Use @Around

For

✔ Entry Logs
✔ Exit Logs
✔ Execution Time
✔ Exception Logging

--------------------------------

Remember

Filter
      ↓
HTTP Logging

Interceptor
      ↓
MVC Logging

AOP
      ↓
Method-Level Logging
```