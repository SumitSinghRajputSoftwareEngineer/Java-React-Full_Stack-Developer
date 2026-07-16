
## Definition

`@After` is a Spring AOP advice annotation used to execute code **after a target method finishes execution**, regardless of whether the method succeeds or throws an exception.

It tells Spring:

> Run this advice after the target method completes.

Package:

```java
import org.aspectj.lang.annotation.After;
```

---

# Why Do We Need @After?

Sometimes we need to perform tasks after a method finishes:

```text
Logging
Resource Cleanup
Audit Tracking
Performance Monitoring
Final Notifications
```

Without AOP:

```java
public void saveEmployee() {

    try {

        // Business Logic

    } finally {

        System.out.println(
            "Method Finished"
        );
    }
}
```

---

Need to repeat in many methods.

---

Solution:

```java
@After
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

    @After(
      "execution(* com.demo.service.*.*(..))"
    )
    public void after() {

        System.out.println(
            "Method Finished"
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
Saving Employee
Method Finished
```

---

# What Happens Internally?

Flow:

```text
Method Starts
      ↓
Business Logic Executes
      ↓
Method Ends
      ↓
@After Executes
```

---

Proxy Flow:

```text
Client
  ↓
Proxy
  ↓
Target Method
  ↓
@After Advice
```

---

# Important Behavior

`@After` behaves like:

```java
finally {
}
```

---

Meaning:

```text
Runs Always
```

---

Whether:

```text
Success
Failure
Exception
```

---

# Success Scenario

```java
public void saveEmployee() {

    System.out.println(
      "Employee Saved"
    );
}
```

---

Output:

```text
Employee Saved
Method Finished
```

---

# Exception Scenario

```java
public void saveEmployee() {

    int x = 10 / 0;
}
```

---

Exception:

```java
ArithmeticException
```

---

Output:

```text
Method Finished
Exception Thrown
```

---

Notice:

```text
@After Still Executes
```

---

# Syntax

```java
@After(
 "pointcut-expression"
)
```

Example:

```java
@After(
 "execution(* *(..))"
)
```

---

Meaning:

```text
Run After Every Method
```

---

# Pointcut Examples

## All Methods

```java
@After(
 "execution(* *(..))"
)
```

---

## All Service Methods

```java
@After(
 "execution(* com.demo.service.*.*(..))"
)
```

---

## Specific Method

```java
@After(
 "execution(* saveEmployee(..))"
)
```

---

## All Void Methods

```java
@After(
 "execution(void *.*(..))"
)
```

---

# Using JoinPoint

Allows access to method information.

---

```java
@After(
 "execution(* *(..))"
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
void saveEmployee()
```

---

# Access Method Name

```java
@After(
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

# Audit Example

```java
@Aspect
@Component
public class AuditAspect {

    @After(
      "execution(* com.demo.service.*.*(..))"
    )
    public void audit(
           JoinPoint jp) {

        System.out.println(
          "Completed: "
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
Completed: saveEmployee
```

---

# Resource Cleanup Example

```java
@After(
 "execution(* *(..))"
)
public void cleanup() {

    System.out.println(
      "Resources Released"
    );
}
```

---

Useful for:

```text
Closing Files
Cleaning ThreadLocal
Removing Context
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
@After(
 "serviceMethods()"
)
public void after() {
}
```

---

Cleaner code.

---

# Internal Workflow

Application Startup:

```text
Detect @Aspect
        ↓
Detect @After
        ↓
Create Proxy
```

---

Method Call:

```text
Execute Method
       ↓
Method Completes
       ↓
Execute @After Advice
```

---

# @After vs Other Advice

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

Runs only if method succeeds.

---

```text
Success Only
```

---

## @AfterThrowing

Runs only if exception occurs.

---

```text
Failure Only
```

---

## @Around

Runs before and after.

---

```text
Full Control
```

---

Comparison:

| Advice | Executes When |
|----------|--------------|
| `@Before` | Before Method |
| `@After` | Always After |
| `@AfterReturning` | After Success |
| `@AfterThrowing` | After Exception |
| `@Around` | Before & After |

---

# @After vs @AfterReturning

Important Interview Question.

---

## @After

Runs:

```text
Success
Exception
```

---

Example:

```java
@After(...)
```

---

Always executes.

---

## @AfterReturning

Runs only:

```text
Success
```

---

Example:

```java
@AfterReturning(...)
```

---

Does NOT run on exception.

---

# Example Comparison

Method:

```java
public void test() {

    int x = 10 / 0;
}
```

---

Aspects:

```java
@After
```

and

```java
@AfterReturning
```

---

Output:

```text
@After Executed
Exception Thrown
```

---

`@AfterReturning`

```text
Not Executed
```

---

# Common Use Cases

---

## Logging

```java
@After
```

Log completion.

---

## Audit

```java
@After
```

Track user actions.

---

## Cleanup

```java
@After
```

Release resources.

---

## Monitoring

```java
@After
```

Track method completion.

---

# Common Mistakes

## Mistake 1

Expecting Return Value

`@After` does not directly provide return value.

---

Use:

```java
@AfterReturning
```

instead.

---

# Mistake 2

Missing @Aspect

Wrong:

```java
@After(...)
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

# Mistake 3

Wrong Pointcut

If pointcut doesn't match:

```text
Advice Never Executes
```

---

# Common Interview Questions

## What is @After?

An advice executed after method completion.

---

## Does It Run On Exception?

Yes.

---

## Does It Run On Success?

Yes.

---

## Equivalent Java Construct?

```java
finally
```

block.

---

## Can It Access Method Details?

Yes.

Using:

```java
JoinPoint
```

---

## Can It Access Return Value?

No.

Use:

```java
@AfterReturning
```

---

# Enterprise Best Practice

Use:

```java
@After
```

for:

```text
Audit Logs
Cleanup
Completion Logging
Resource Release
```

---

Avoid:

```text
Heavy Processing
Database Transactions
Business Logic
```

inside advice.

---

Keep it lightweight.

---

# Key Points To Remember

- `@After` executes after method completion.
- Runs whether method succeeds or fails.
- Similar to Java `finally`.
- Part of Spring AOP.
- Must be inside an `@Aspect`.
- Can access method details using `JoinPoint`.
- Cannot access return value directly.
- Commonly used for cleanup and auditing.
- Implemented through Spring proxies.
- Frequently asked Spring AOP interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Before` | Before Method |
| `@After` | Always After |
| `@AfterReturning` | After Success |
| `@AfterThrowing` | After Exception |
| `@Around` | Before & After |

---

# Interview Shortcut

```java
@After(
 "execution(* com.demo.service.*.*(..))"
)
public void log() {

    System.out.println(
      "Method Finished"
    );
}
```

Meaning:

```text
Method Executes
       ↓
Success OR Exception
       ↓
@After Executes
```

Think of `@After` as:

```java
try {

   // method

} finally {

   // @After advice
}
```

Most common production usage:

```java
@After
```

for:

```text
Audit Logging
Cleanup
Completion Tracking
Monitoring
```

This is the Spring AOP advice used when you need logic to run after a method finishes, regardless of the outcome.