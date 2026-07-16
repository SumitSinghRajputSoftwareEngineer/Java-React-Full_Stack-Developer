
## Definition

`@AfterReturning` is a Spring AOP advice annotation used to execute code **after a target method successfully completes and returns a value**.

It tells Spring:

> Run this advice only when the method finishes successfully (without throwing an exception).

Package:

```java
import org.aspectj.lang.annotation.AfterReturning;
```

---

# Why Do We Need @AfterReturning?

Sometimes we need to perform actions only when a method succeeds:

```text
Success Logging
Audit Records
Notifications
Metrics Collection
Response Tracking
```

Without AOP:

```java
public Employee saveEmployee() {

    Employee emp = repository.save(employee);

    System.out.println(
        "Employee Saved Successfully"
    );

    return emp;
}
```

---

Need to repeat this logic in many methods.

---

Solution:

```java
@AfterReturning
```

---

# Basic Example

## Service

```java
@Service
public class EmployeeService {

    public String saveEmployee() {

        return "Employee Saved";
    }
}
```

---

## Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @AfterReturning(
      "execution(* com.demo.service.*.*(..))"
    )
    public void success() {

        System.out.println(
          "Method Completed Successfully"
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
Method Completed Successfully
```

---

# Important Behavior

`@AfterReturning` runs only when:

```text
Method Completes Successfully
```

---

It does NOT run when:

```text
Exception Occurs
```

---

# Success Example

Method:

```java
public String saveEmployee() {

    return "Success";
}
```

---

Output:

```text
Method Completed Successfully
```

---

Advice executes.

---

# Exception Example

Method:

```java
public String saveEmployee() {

    int x = 10 / 0;

    return "Success";
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
Exception Thrown
```

---

`@AfterReturning`

```text
NOT Executed
```

---

# What Happens Internally?

Flow:

```text
Method Starts
      ↓
Business Logic Executes
      ↓
Returns Successfully
      ↓
@AfterReturning Executes
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
Return Value
  ↓
@AfterReturning
```

---

# Syntax

```java
@AfterReturning(
    value = "pointcut",
    returning = "result"
)
```

---

Example:

```java
@AfterReturning(
    value =
    "execution(* *(..))",
    returning = "result"
)
```

---

# Accessing Return Value

One of the biggest advantages.

---

```java
@AfterReturning(
    value =
    "execution(* *(..))",

    returning = "result"
)
public void log(
      Object result) {

    System.out.println(
        result
    );
}
```

---

Method:

```java
public String saveEmployee() {

    return "Employee Saved";
}
```

---

Output:

```text
Employee Saved
```

---

# Complete Example

## Service

```java
@Service
public class EmployeeService {

    public String saveEmployee() {

        return "Saved";
    }
}
```

---

## Aspect

```java
@Aspect
@Component
public class LoggingAspect {

    @AfterReturning(
       value =
       "execution(* com.demo.service.*.*(..))",

       returning = "result"
    )
    public void afterReturn(
            Object result) {

        System.out.println(
            "Returned: "
            + result
        );
    }
}
```

---

Output:

```text
Returned: Saved
```

---

# Strongly Typed Return Values

Instead of:

```java
Object
```

use actual type.

---

```java
@AfterReturning(
    value =
    "execution(* *(..))",

    returning = "employee"
)
public void log(
      Employee employee) {

    System.out.println(
       employee.getName()
    );
}
```

---

# Using JoinPoint

Can access method details.

---

```java
@AfterReturning(
    value =
    "execution(* *(..))",

    returning = "result"
)
public void log(
        JoinPoint jp,
        Object result) {

    System.out.println(
       jp.getSignature()
         .getName()
    );

    System.out.println(
       result
    );
}
```

---

Output:

```text
saveEmployee
Saved
```

---

# Audit Example

```java
@Aspect
@Component
public class AuditAspect {

    @AfterReturning(
      value =
      "execution(* com.demo.service.*.*(..))",

      returning = "response"
    )
    public void audit(
          Object response) {

        System.out.println(
          "Response: "
          + response
        );
    }
}
```

---

Useful for:

```text
Audit Logs
Tracking
Monitoring
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
@AfterReturning(
 value = "serviceMethods()",
 returning = "result"
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
Detect @AfterReturning
       ↓
Create Proxy
```

---

Method Call:

```text
Execute Method
       ↓
Method Returns
       ↓
Execute @AfterReturning
```

---

# @AfterReturning vs Other Advice

Interview Favorite.

---

## @Before

Runs before method.

---

## @After

Runs always after method.

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

# @After vs @AfterReturning

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

## @AfterReturning

Equivalent to:

```java
Method Returned Successfully
```

---

Runs:

```text
Success Only
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
| @AfterReturning | No |

---

# Common Use Cases

---

## Success Logging

```java
@AfterReturning
```

---

## Audit Tracking

```java
@AfterReturning
```

---

## Response Logging

```java
@AfterReturning
```

---

## Metrics Collection

```java
@AfterReturning
```

---

## Notification Triggers

```java
@AfterReturning
```

---

# Common Mistakes

## Mistake 1

Wrong Returning Variable Name

Wrong:

```java
@AfterReturning(
 returning="result"
)
```

---

Method:

```java
public void log(
    Object data
)
```

---

Must match:

```java
returning="data"
```

---

# Mistake 2

Expecting It To Run On Exception

It doesn't.

---

Use:

```java
@AfterThrowing
```

---

# Mistake 3

Wrong Return Type

Method returns:

```java
Employee
```

---

Advice expects:

```java
String
```

---

Binding error.

---

# Common Interview Questions

## What is @AfterReturning?

Advice executed after successful method completion.

---

## Does It Run On Exception?

No.

---

## Can It Access Return Value?

Yes.

Using:

```java
returning
```

attribute.

---

## Difference Between @After and @AfterReturning?

`@After`

```text
Always Executes
```

---

`@AfterReturning`

```text
Success Only
```

---

## Can It Modify Return Value?

No.

---

Use:

```java
@Around
```

for that.

---

# Enterprise Best Practice

Use:

```java
@AfterReturning
```

for:

```text
Audit Logs
Success Logs
Metrics
Notifications
Response Tracking
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

- `@AfterReturning` executes after successful method completion.
- Does not execute when exceptions occur.
- Can access the returned value.
- Must be inside an `@Aspect`.
- Uses the `returning` attribute.
- Part of Spring AOP.
- Commonly used for logging and auditing.
- Implemented through Spring proxies.
- Cannot modify return value directly.
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
@AfterReturning(
 value =
 "execution(* com.demo.service.*.*(..))",

 returning = "result"
)
public void log(
       Object result) {

    System.out.println(result);
}
```

Meaning:

```text
Method Executes
       ↓
Returns Successfully
       ↓
Return Value Captured
       ↓
@AfterReturning Executes
```

Think of it as:

```java
Object result = method();

log(result);
```

Most common production usage:

```java
Success Logging
Audit Tracking
Response Monitoring
Metrics Collection
```

This is the Spring AOP advice used when you want logic to run only after a method successfully returns and optionally inspect the returned value.