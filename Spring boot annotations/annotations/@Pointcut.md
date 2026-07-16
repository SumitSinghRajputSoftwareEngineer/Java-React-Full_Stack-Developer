
## Definition

`@Pointcut` is a Spring AOP annotation used to define a **reusable pointcut expression**.

It tells Spring:

> These are the methods where my AOP advice should be applied.

Package:

```java
import org.aspectj.lang.annotation.Pointcut;
```

---

# Why Do We Need @Pointcut?

Suppose you have multiple advices:

```java
@Before(...)
@After(...)
@AfterReturning(...)
@AfterThrowing(...)
```

and all use the same expression:

```java
execution(* com.demo.service.*.*(..))
```

---

Without `@Pointcut`:

```java
@Before(
 "execution(* com.demo.service.*.*(..))"
)
public void before() {
}

@After(
 "execution(* com.demo.service.*.*(..))"
)
public void after() {
}

@AfterReturning(
 "execution(* com.demo.service.*.*(..))"
)
public void success() {
}
```

---

Problems:

```text
Code Duplication
Hard Maintenance
Error-Prone
```

---

Solution:

```java
@Pointcut
```

---

# Basic Example

```java
@Aspect
@Component
public class LoggingAspect {

    @Pointcut(
      "execution(* com.demo.service.*.*(..))"
    )
    public void serviceMethods() {
    }

}
```

---

Meaning:

```text
All Methods
Inside Service Package
```

---

Now reuse it.

---

```java
@Before(
 "serviceMethods()"
)
public void before() {
}
```

---

```java
@After(
 "serviceMethods()"
)
public void after() {
}
```

---

```java
@AfterReturning(
 "serviceMethods()"
)
public void success() {
}
```

---

# Real-Life Analogy

Think of:

```text
Employee Department
```

---

Instead of writing:

```text
Employee1
Employee2
Employee3
Employee4
```

everywhere,

---

Create:

```text
ITDepartment
```

---

Then reuse.

---

`@Pointcut` works the same way.

---

# Syntax

```java
@Pointcut(
 "pointcut-expression"
)
public void methodName() {
}
```

---

Example:

```java
@Pointcut(
 "execution(* *(..))"
)
public void allMethods() {
}
```

---

Method body:

```java
Empty
```

---

Because it is only a reference.

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

    @Pointcut(
      "execution(* com.demo.service.*.*(..))"
    )
    public void serviceMethods() {
    }

    @Before(
      "serviceMethods()"
    )
    public void before() {

        System.out.println(
           "Before Method"
        );
    }

    @After(
      "serviceMethods()"
    )
    public void after() {

        System.out.println(
           "After Method"
        );
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

# Most Common Pointcut Expressions

---

## All Methods

```java
@Pointcut(
 "execution(* *(..))"
)
```

---

Meaning:

```text
Every Method
```

---

# All Methods In Package

```java
@Pointcut(
 "execution(* com.demo.service.*.*(..))"
)
```

---

Meaning:

```text
All Service Methods
```

---

# Specific Method

```java
@Pointcut(
 "execution(* saveEmployee(..))"
)
```

---

Meaning:

```text
Only saveEmployee()
```

---

# Specific Return Type

```java
@Pointcut(
 "execution(void *.*(..))"
)
```

---

Meaning:

```text
All Void Methods
```

---

# Methods Starting With save

```java
@Pointcut(
 "execution(* save*(..))"
)
```

---

Matches:

```java
save()
saveEmployee()
saveOrder()
```

---

# Methods Starting With get

```java
@Pointcut(
 "execution(* get*(..))"
)
```

---

Matches:

```java
getEmployee()
getOrder()
```

---

# Package Matching

---

## Single Package

```java
execution(
 * com.demo.service.*.*(..)
)
```

---

Only:

```text
com.demo.service
```

---

# All Subpackages

```java
execution(
 * com.demo.service..*(..)
)
```

---

Matches:

```text
com.demo.service
com.demo.service.impl
com.demo.service.user
com.demo.service.admin
```

---

# Annotation-Based Pointcuts

Very common in enterprise applications.

---

## Methods With @Transactional

```java
@Pointcut(
 "@annotation(
  org.springframework.transaction.annotation.Transactional
 )"
)
public void transactionalMethods() {
}
```

---

Matches:

```java
@Transactional
public void save() {
}
```

---

# Class-Based Pointcuts

```java
@Pointcut(
 "within(com.demo.service..*)"
)
```

---

Meaning:

```text
All Classes
Inside Package
```

---

# Bean-Based Pointcuts

```java
@Pointcut(
 "bean(employeeService)"
)
```

---

Matches only:

```java
employeeService bean
```

---

# Combining Pointcuts

Very Important Interview Topic.

---

## AND

```java
@Pointcut(
 "serviceMethods() && saveMethods()"
)
```

---

Both must match.

---

## OR

```java
@Pointcut(
 "serviceMethods() || repoMethods()"
)
```

---

Either can match.

---

## NOT

```java
@Pointcut(
 "serviceMethods() && !adminMethods()"
)
```

---

Exclude matches.

---

# Reusing Across Aspects

Example:

```java
@Aspect
public class CommonPointcuts {

    @Pointcut(
      "execution(* com.demo.service.*.*(..))"
    )
    public void serviceMethods() {
    }
}
```

---

Another Aspect:

```java
@Before(
 "com.demo.CommonPointcuts.serviceMethods()"
)
```

---

Reusable enterprise design.

---

# Internal Workflow

Application Startup:

```text
Detect @Pointcut
         ↓
Store Expression
         ↓
Advice References It
         ↓
Create Proxy
```

---

Method Execution:

```text
Method Called
       ↓
Check Pointcut Match
       ↓
Execute Advice
```

---

# @Pointcut vs Advice

Interview Favorite.

---

## @Pointcut

Defines:

```text
Where To Apply Advice
```

---

## @Before

Defines:

```text
What To Do Before
```

---

## @After

Defines:

```text
What To Do After
```

---

Example:

```java
@Pointcut(...)
```

Where?

---

```java
@Before(...)
```

What?

---

# Common Mistakes

## Mistake 1

Adding Logic Inside Pointcut Method

Wrong:

```java
@Pointcut(...)
public void test() {

    System.out.println(
      "Wrong"
    );
}
```

---

Pointcut method should be:

```java
Empty
```

---

# Mistake 2

Forgetting Parentheses

Wrong:

```java
@Before("serviceMethods")
```

---

Correct:

```java
@Before("serviceMethods()")
```

---

# Mistake 3

Using Wrong Package Pattern

```java
service.*
```

vs

```java
service..*
```

---

Can change matching behavior.

---

# Common Interview Questions

## What is @Pointcut?

Reusable definition of join points.

---

## Why Use It?

Avoid duplicate expressions.

---

## Does It Contain Logic?

No.

Only expression definition.

---

## Can Multiple Advices Use Same Pointcut?

Yes.

That is its main purpose.

---

## Can Pointcuts Be Combined?

Yes.

Using:

```text
&&
||
!
```

---

## Can It Match Annotations?

Yes.

Using:

```java
@annotation(...)
```

---

# Enterprise Best Practice

Create:

```java
CommonPointcuts
```

class.

---

Keep all reusable pointcuts there.

---

Example:

```java
serviceMethods()
repositoryMethods()
controllerMethods()
transactionalMethods()
```

---

Reuse throughout application.

---

This improves:

```text
Readability
Maintainability
Scalability
```

---

# Key Points To Remember

- `@Pointcut` defines reusable join point expressions.
- Contains no business logic.
- Used by advice annotations.
- Reduces duplication.
- Supports execution expressions.
- Supports annotation-based matching.
- Supports package-based matching.
- Supports bean-based matching.
- Supports AND, OR, NOT combinations.
- Frequently asked Spring AOP interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Pointcut` | Define Join Points |
| `@Before` | Execute Before |
| `@After` | Execute After |
| `@AfterReturning` | Execute After Success |
| `@AfterThrowing` | Execute After Exception |
| `@Around` | Full Control |

---

# Interview Shortcut

```java
@Pointcut(
 "execution(* com.demo.service.*.*(..))"
)
public void serviceMethods() {
}
```

Meaning:

```text
Create Reusable Name
          ↓
serviceMethods()
          ↓
Represents
          ↓
All Service Methods
```

Usage:

```java
@Before("serviceMethods()")
@After("serviceMethods()")
@Around("serviceMethods()")
```

Think of `@Pointcut` as:

```text
A Variable For A Complex AOP Expression
```

It doesn't execute anything itself—it simply defines **where** AOP advice should run, making Spring AOP code cleaner, reusable, and easier to maintain.