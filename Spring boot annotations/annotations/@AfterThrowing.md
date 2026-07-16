
## Definition

`@AfterThrowing` is a Spring AOP advice annotation used to execute code **only when a target method throws an exception**.

It tells Spring:

> Run this advice when the matched method fails with an exception.

Package:

```java
import org.aspectj.lang.annotation.AfterThrowing;
```

---

# Why Do We Need @AfterThrowing?

In enterprise applications, when exceptions occur we often need to:

```text
Log Errors
Send Alerts
Audit Failures
Track Exceptions
Generate Incident Tickets
Notify Monitoring Systems
```

Without AOP:

```java
public void saveEmployee() {

    try {

        // Business Logic

    } catch(Exception e) {

        log.error(e.getMessage());

        throw e;
    }
}
```

---

Need to repeat this logic everywhere.

---

Solution:

```java
@AfterThrowing
```

---

# Basic Example

## Service

```java
@Service
public class EmployeeService {

    public void saveEmployee() {

        int x = 10 / 0;
    }
}
```

---

## Aspect

```java
@Aspect
@Component
public class ExceptionAspect {

    @AfterThrowing(
      "execution(* com.demo.service.*.*(..))"
    )
    public void handleException() {

        System.out.println(
          "Exception Occurred"
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
Exception Occurred
ArithmeticException
```

---

# Important Behavior

`@AfterThrowing` executes only when:

```text
Exception Is Thrown
```

---

It does NOT execute when:

```text
Method Completes Successfully
```

---

# Success Scenario

Method:

```java
public void saveEmployee() {

    System.out.println(
      "Saved"
    );
}
```

---

Output:

```text
Saved
```

---

`@AfterThrowing`

```text
Not Executed
```

---

# Failure Scenario

Method:

```java
public void saveEmployee() {

    int x = 10 / 0;
}
```

---

Output:

```text
Exception Occurred
ArithmeticException
```

---

Advice executes.

---

# What Happens Internally?

Flow:

```text
Method Starts
      ↓
Business Logic Executes
      ↓
Exception Thrown
      ↓
@AfterThrowing Executes
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
Exception
  ↓
@AfterThrowing
```

---

# Syntax

```java
@AfterThrowing(
    value = "pointcut",
    throwing = "ex"
)
```

---

Example:

```java
@AfterThrowing(
    value =
    "execution(* *(..))",

    throwing = "ex"
)
```

---

# Accessing Exception

Most common usage.

---

```java
@AfterThrowing(
    value =
    "execution(* *(..))",

    throwing = "ex"
)
public void log(
       Exception ex) {

    System.out.println(
       ex.getMessage()
    );
}
```

---

Method:

```java
int x = 10 / 0;
```

---

Output:

```text
/ by zero
```

---

# Complete Example

## Aspect

```java
@Aspect
@Component
public class ExceptionAspect {

    @AfterThrowing(
        value =
        "execution(* com.demo.service.*.*(..))",

        throwing = "exception"
    )
    public void handle(
          Exception exception) {

        System.out.println(
            "Error: "
            +
            exception.getMessage()
        );
    }
}
```

---

Output:

```text
Error: / by zero
```

---

# Using Specific Exception Types

Instead of:

```java
Exception
```

---

Use:

```java
ArithmeticException
```

---

Example:

```java
@AfterThrowing(
    value =
    "execution(* *(..))",

    throwing = "ex"
)
public void handle(
     ArithmeticException ex) {

    System.out.println(
      "Math Error"
    );
}
```

---

Only handles:

```java
ArithmeticException
```

---

# Using JoinPoint

Access method information.

---

```java
@AfterThrowing(
    value =
    "execution(* *(..))",

    throwing = "ex"
)
public void handle(
      JoinPoint jp,
      Exception ex) {

    System.out.println(
      jp.getSignature()
        .getName()
    );

    System.out.println(
      ex.getMessage()
    );
}
```

---

Output:

```text
saveEmployee
/ by zero
```

---

# Audit Failure Example

```java
@Aspect
@Component
public class AuditAspect {

    @AfterThrowing(
       value =
       "execution(* com.demo.service.*.*(..))",

       throwing = "ex"
    )
    public void audit(
         Exception ex) {

        System.out.println(
          "Failure Logged"
        );
    }
}
```

---

Useful for:

```text
Audit Logs
Security Tracking
Incident Monitoring
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
@AfterThrowing(
 value = "serviceMethods()",
 throwing = "ex"
)
```

---

Cleaner code.

---

# Internal Workflow

Application Startup:

```text
Detect @Aspect
       ↓
Detect @AfterThrowing
       ↓
Create Proxy
```

---

Method Call:

```text
Execute Method
       ↓
Exception Occurs
       ↓
Execute @AfterThrowing
```

---

# @AfterThrowing vs Other Advice

Interview Favorite.

---

## @Before

Runs before method.

---

## @After

Runs always.

---

## @AfterReturning

Runs only on success.

---

## @AfterThrowing

Runs only on exception.

---

## @Around

Runs before and after.

---

Comparison:

| Advice | Success | Exception |
|----------|----------|-----------|
| `@Before` | ✅ | ✅ |
| `@After` | ✅ | ✅ |
| `@AfterReturning` | ✅ | ❌ |
| `@AfterThrowing` | ❌ | ✅ |
| `@Around` | ✅ | ✅ |

---

# @AfterThrowing vs @After

Interview Question.

---

## @After

Equivalent to:

```java
finally
```

---

Runs:

```text
Always
```

---

## @AfterThrowing

Equivalent to:

```java
catch
```

---

Runs:

```text
Exception Only
```

---

Example:

```java
int x = 10 / 0;
```

---

Result:

| Advice | Executes |
|----------|----------|
| @After | Yes |
| @AfterThrowing | Yes |

---

Example:

```java
return "Success";
```

---

Result:

| Advice | Executes |
|----------|----------|
| @After | Yes |
| @AfterThrowing | No |

---

# Common Use Cases

---

## Error Logging

```java
@AfterThrowing
```

---

## Monitoring

```java
@AfterThrowing
```

---

## Alert Systems

```java
@AfterThrowing
```

---

## Audit Failures

```java
@AfterThrowing
```

---

## Incident Tracking

```java
@AfterThrowing
```

---

# Common Mistakes

## Mistake 1

Wrong Exception Variable Name

Wrong:

```java
@AfterThrowing(
 throwing = "ex"
)
```

Method:

```java
public void log(
    Exception error
)
```

---

Names must match:

```java
throwing = "error"
```

---

# Mistake 2

Expecting It To Run On Success

It doesn't.

---

Use:

```java
@AfterReturning
```

---

# Mistake 3

Swallowing Exceptions

`@AfterThrowing`

```java
cannot
```

prevent exception propagation.

---

Exception continues upward.

---

# Can @AfterThrowing Handle Exception?

No.

It can:

```text
Observe
Log
Track
Audit
```

---

But cannot:

```text
Recover
Suppress
Replace
```

the exception.

---

For that use:

```java
@Around
```

---

# Common Interview Questions

## What is @AfterThrowing?

Advice executed when a method throws an exception.

---

## Does It Run On Success?

No.

---

## Can It Access Exception Details?

Yes.

Using:

```java
throwing
```

attribute.

---

## Can It Stop Exception Propagation?

No.

---

## Difference Between @After and @AfterThrowing?

`@After`

```text
Always Executes
```

---

`@AfterThrowing`

```text
Exception Only
```

---

## Can It Access Method Information?

Yes.

Using:

```java
JoinPoint
```

---

# Enterprise Best Practice

Use:

```java
@AfterThrowing
```

for:

```text
Error Logging
Audit Tracking
Monitoring
Alerting
Failure Analytics
```

---

Avoid:

```text
Business Logic
Database Updates
Complex Recovery Logic
```

inside advice.

---

Keep it lightweight.

---

# Key Points To Remember

- `@AfterThrowing` executes only when an exception occurs.
- Does not run on successful execution.
- Can access the thrown exception.
- Uses the `throwing` attribute.
- Must be inside an `@Aspect`.
- Part of Spring AOP.
- Commonly used for logging and monitoring.
- Cannot suppress or replace exceptions.
- Implemented through Spring proxies.
- Frequently asked Spring AOP interview topic.

---

# Quick Comparison

| Annotation | Executes When |
|------------|---------------|
| `@Before` | Before Method |
| `@After` | Always After |
| `@AfterReturning` | After Success |
| `@AfterThrowing` | After Exception |
| `@Around` | Before & After |

---

# Interview Shortcut

```java
@AfterThrowing(
 value =
 "execution(* com.demo.service.*.*(..))",

 throwing = "ex"
)
public void log(
      Exception ex) {

    System.out.println(
      ex.getMessage()
    );
}
```

Meaning:

```text
Method Executes
       ↓
Exception Occurs
       ↓
Exception Captured
       ↓
@AfterThrowing Executes
```

Think of it as:

```java
try {

   method();

} catch(Exception ex) {

   log(ex);
}
```

Most common production usage:

```text
Error Logging
Monitoring
Alerting
Audit Failure Tracking
```

This is the Spring AOP advice used when you want logic to execute only when a method fails with an exception and you need access to exception details.