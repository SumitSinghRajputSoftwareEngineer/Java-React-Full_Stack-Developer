
## Definition

`@Around` is the most powerful Spring AOP advice.

It allows you to execute code:

```text
Before Method Execution
After Method Execution
On Success
On Exception
Modify Arguments
Modify Return Values
Control Whether Method Executes
```

It tells Spring:

> Intercept the method call and give me complete control over its execution.

Package:

```java
import org.aspectj.lang.annotation.Around;
```

---

# Why Do We Need @Around?

Suppose we want to:

```text
Log Request
Measure Execution Time
Handle Exceptions
Modify Response
Validate Inputs
Cache Results
```

Using:

```java
@Before
@After
@AfterReturning
@AfterThrowing
```

requires multiple advices.

---

With:

```java
@Around
```

everything can be done in one place.

---

# Real Meaning

Think of:

```java
@Before
```

as:

```text
Before Method
```

---

Think of:

```java
@After
```

as:

```text
After Method
```

---

Think of:

```java
@Around
```

as:

```text
Wrap Entire Method Execution
```

---

# Basic Example

## Service

```java
@Service
public class EmployeeService {

    public String saveEmployee() {

        System.out.println(
            "Saving Employee"
        );

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

    @Around(
      "execution(* com.demo.service.*.*(..))"
    )
    public Object around(
       ProceedingJoinPoint pjp
    ) throws Throwable {

        System.out.println(
            "Before Method"
        );

        Object result =
            pjp.proceed();

        System.out.println(
            "After Method"
        );

        return result;
    }
}
```

---

Output:

```text
Before Method
Saving Employee
After Method
```

---

# What Is ProceedingJoinPoint?

Special version of:

```java
JoinPoint
```

---

Used only with:

```java
@Around
```

---

Provides:

```java
proceed()
```

method.

---

Without:

```java
proceed()
```

target method never executes.

---

# What Happens Internally?

Flow:

```text
Method Called
      ↓
@Around Starts
      ↓
proceed()
      ↓
Actual Method Executes
      ↓
Returns Result
      ↓
@Around Finishes
```

---

Proxy Flow:

```text
Client
  ↓
Proxy
  ↓
@Around
  ↓
Target Method
  ↓
Return Result
```

---

# Most Important Line

```java
Object result =
    pjp.proceed();
```

---

This line means:

```text
Execute Actual Method
```

---

Without it:

```java
@Around(...)
public Object test(
 ProceedingJoinPoint pjp
) {

    System.out.println(
      "Blocked"
    );

    return null;
}
```

---

Output:

```text
Blocked
```

---

Method never runs.

---

# Measuring Execution Time

Very common production use case.

---

```java
@Around(
 "execution(* *(..))"
)
public Object measure(
       ProceedingJoinPoint pjp
) throws Throwable {

    long start =
        System.currentTimeMillis();

    Object result =
        pjp.proceed();

    long end =
        System.currentTimeMillis();

    System.out.println(
       "Time: "
       + (end - start)
    );

    return result;
}
```

---

Output:

```text
Time: 25 ms
```

---

# Logging Example

```java
@Around(
 "execution(* *(..))"
)
public Object log(
      ProceedingJoinPoint pjp
) throws Throwable {

    System.out.println(
      "Method Started"
    );

    Object result =
        pjp.proceed();

    System.out.println(
      "Method Finished"
    );

    return result;
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

# Exception Handling Example

```java
@Around(
 "execution(* *(..))"
)
public Object handle(
      ProceedingJoinPoint pjp
) {

    try {

        return pjp.proceed();

    } catch(Throwable ex) {

        System.out.println(
          ex.getMessage()
        );

        return null;
    }
}
```

---

Can intercept exceptions.

---

Unlike:

```java
@AfterThrowing
```

---

# Access Method Name

```java
@Around(
 "execution(* *(..))"
)
public Object log(
 ProceedingJoinPoint pjp
) throws Throwable {

    System.out.println(
      pjp.getSignature()
         .getName()
    );

    return pjp.proceed();
}
```

---

Output:

```text
saveEmployee
```

---

# Access Arguments

```java
Object[] args =
    pjp.getArgs();
```

---

Example:

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

# Modify Arguments

Possible only with:

```java
@Around
```

---

```java
Object[] args =
    pjp.getArgs();

args[0] = "Modified";
```

---

Then:

```java
pjp.proceed(args);
```

---

Method receives:

```text
Modified
```

---

# Modify Return Value

Example:

```java
@Around(
 "execution(* *(..))"
)
public Object modify(
 ProceedingJoinPoint pjp
) throws Throwable {

    Object result =
        pjp.proceed();

    return result +
           " Modified";
}
```

---

Method returns:

```text
Original Modified
```

---

# Caching Example

Production-level use case.

---

```java
@Around(
 "execution(* getEmployee(..))"
)
public Object cache(
 ProceedingJoinPoint pjp
) throws Throwable {

    if(cacheExists()) {

        return cacheValue;
    }

    return pjp.proceed();
}
```

---

Avoids database calls.

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
@Around(
 "serviceMethods()"
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
Detect @Around
      ↓
Create Proxy
```

---

Method Call:

```text
Proxy Intercepts
       ↓
@Before Logic
       ↓
proceed()
       ↓
Method Executes
       ↓
@Post Logic
       ↓
Return Result
```

---

# @Around vs Other Advice

Interview Favorite.

---

## @Before

Before only.

---

## @After

After only.

---

## @AfterReturning

Success only.

---

## @AfterThrowing

Exception only.

---

## @Around

Everything.

---

Comparison:

| Advice | Before | After | Success | Exception | Modify Result |
|----------|---------|--------|----------|------------|---------------|
| `@Before` | ✅ | ❌ | ❌ | ❌ | ❌ |
| `@After` | ❌ | ✅ | ❌ | ❌ | ❌ |
| `@AfterReturning` | ❌ | ❌ | ✅ | ❌ | ❌ |
| `@AfterThrowing` | ❌ | ❌ | ❌ | ✅ | ❌ |
| `@Around` | ✅ | ✅ | ✅ | ✅ | ✅ |

---

# Common Use Cases

---

## Performance Monitoring

```java
@Around
```

---

## Logging

```java
@Around
```

---

## Security Checks

```java
@Around
```

---

## Caching

```java
@Around
```

---

## Retry Logic

```java
@Around
```

---

## Rate Limiting

```java
@Around
```

---

## Transaction-Like Behavior

```java
@Around
```

---

# Common Mistakes

## Mistake 1

Forgetting proceed()

Wrong:

```java
@Around(...)
public Object test(
 ProceedingJoinPoint pjp
) {

    return null;
}
```

---

Method never executes.

---

# Mistake 2

Returning Wrong Type

Method returns:

```java
Employee
```

---

Advice returns:

```java
String
```

---

Causes errors.

---

# Mistake 3

Catching Exception Without Re-throwing

Can hide failures unintentionally.

---

# Common Interview Questions

## What is @Around?

Most powerful AOP advice that surrounds method execution.

---

## Why Use ProceedingJoinPoint?

To execute the actual method.

---

## What Happens If proceed() Is Not Called?

Method never runs.

---

## Can It Modify Return Values?

Yes.

---

## Can It Modify Arguments?

Yes.

---

## Can It Handle Exceptions?

Yes.

---

## Most Powerful Advice?

```java
@Around
```

---

# Enterprise Best Practice

Use:

```java
@Around
```

for:

```text
Performance Monitoring
Logging
Caching
Retry Logic
Rate Limiting
Tracing
```

---

Avoid placing:

```text
Business Logic
Heavy Database Work
```

inside advice.

---

Keep aspects focused.

---

# Key Points To Remember

- `@Around` surrounds entire method execution.
- Uses `ProceedingJoinPoint`.
- Must call `proceed()` to execute target method.
- Can modify arguments.
- Can modify return values.
- Can handle exceptions.
- Combines functionality of all other advice types.
- Most powerful Spring AOP advice.
- Implemented using Spring proxies.
- Frequently asked Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Before` | Before Method |
| `@After` | After Method |
| `@AfterReturning` | After Success |
| `@AfterThrowing` | After Exception |
| `@Around` | Full Control |

---

# Interview Shortcut

```java
@Around(
 "execution(* com.demo.service.*.*(..))"
)
public Object log(
 ProceedingJoinPoint pjp
) throws Throwable {

    System.out.println(
      "Before"
    );

    Object result =
        pjp.proceed();

    System.out.println(
      "After"
    );

    return result;
}
```

Meaning:

```text
Method Called
      ↓
@Around Starts
      ↓
proceed()
      ↓
Method Executes
      ↓
Result Returned
      ↓
@Around Ends
```

Think of `@Around` as:

```java
before();

Object result = method();

after();

return result;
```

Most common production usage:

```text
Performance Monitoring
Logging
Caching
Security
Retry Mechanisms
Rate Limiting
```

This is the most flexible and powerful Spring AOP advice because it gives complete control over the execution of a target method.