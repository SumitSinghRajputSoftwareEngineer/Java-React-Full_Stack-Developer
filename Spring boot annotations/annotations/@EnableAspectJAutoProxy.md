
## Definition

`@EnableAspectJAutoProxy` is a Spring annotation used to enable **Aspect-Oriented Programming (AOP)** support.

It tells Spring:

> Detect classes annotated with `@Aspect` and automatically create AOP proxies for them.

Package:

```java
import org.springframework.context.annotation.EnableAspectJAutoProxy;
```

---

# Why Do We Need @EnableAspectJAutoProxy?

Suppose we create an aspect:

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

Without:

```java
@EnableAspectJAutoProxy
```

Spring sees the bean but does not activate AOP.

---

Result:

```text
Aspect Never Executes
```

---

Solution:

```java
@EnableAspectJAutoProxy
```

---

# Basic Example

```java
@Configuration

@EnableAspectJAutoProxy
public class AppConfig {
}
```

---

Now Spring:

```text
Detects @Aspect Classes
Creates Proxies
Applies Advice
```

---

# What Happens Internally?

When Spring starts:

```java
@EnableAspectJAutoProxy
```

---

Registers:

```text
AnnotationAwareAspectJAutoProxyCreator
```

---

This component:

```text
Finds @Aspect Beans
Creates Proxies
Applies Advice
```

---

Flow:

```text
Application Startup
          ↓
Detect @Aspect
          ↓
Create Proxy
          ↓
Intercept Method Calls
          ↓
Execute Advice
```

---

# Example

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

## Configuration

```java
@Configuration

@EnableAspectJAutoProxy
public class AppConfig {
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

Without:

```java
@EnableAspectJAutoProxy
```

Output:

```text
Saving Employee
```

---

Aspect ignored.

---

# Relationship With @Aspect

Most Important Interview Topic.

---

```java
@Aspect
```

defines:

```text
What To Execute
```

---

```java
@EnableAspectJAutoProxy
```

enables:

```text
How To Execute It
```

---

Flow:

```text
@Aspect
    ↓
Aspect Defined
    ↓
@EnableAspectJAutoProxy
    ↓
Proxy Created
    ↓
Advice Executes
```

---

# How Spring AOP Works

Spring does NOT modify your class.

---

Instead:

```text
Proxy Object
```

is created.

---

Example:

```java
EmployeeService
```

becomes:

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
Aspect Logic
  ↓
Actual Method
```

---

# JDK Dynamic Proxy vs CGLIB

Important Interview Topic.

---

Spring can create proxies using:

```text
JDK Dynamic Proxy
```

or

```text
CGLIB Proxy
```

---

# JDK Dynamic Proxy

Default when interface exists.

---

Example:

```java
public interface EmployeeService {
}
```

---

Spring creates:

```text
Interface-Based Proxy
```

---

# CGLIB Proxy

Used when no interface exists.

---

Spring creates:

```text
Subclass Proxy
```

---

Example:

```java
public class EmployeeService {
}
```

---

Spring generates:

```java
EmployeeService$$EnhancerBySpring
```

---

# proxyTargetClass

Force CGLIB.

---

```java
@EnableAspectJAutoProxy(
    proxyTargetClass = true
)
```

---

Meaning:

```text
Always Use CGLIB
```

---

Even if interface exists.

---

Example:

```java
@Configuration

@EnableAspectJAutoProxy(
 proxyTargetClass = true
)
public class Config {
}
```

---

# exposeProxy

Advanced Feature.

---

```java
@EnableAspectJAutoProxy(
 exposeProxy = true
)
```

---

Allows access to current proxy.

---

Example:

```java
AopContext.currentProxy()
```

---

Used to solve:

```text
Self Invocation Problem
```

---

Rarely needed.

---

# Self Invocation Problem

Interview Favorite.

---

Example:

```java
@Service
public class EmployeeService {

    public void methodA() {

        methodB();
    }

    @Transactional
    public void methodB() {

    }
}
```

---

Problem:

```text
methodB()
```

called directly.

---

Proxy bypassed.

---

Aspect ignored.

---

Solution:

```java
@EnableAspectJAutoProxy(
 exposeProxy = true
)
```

and call through proxy.

---

# Internal Components

Spring registers:

```text
AnnotationAwareAspectJAutoProxyCreator
```

---

Which creates:

```text
Advisors
Pointcuts
Proxies
```

---

These implement:

```text
AOP Infrastructure
```

---

# Spring Boot

Important.

---

If dependency exists:

```xml
spring-boot-starter-aop
```

---

Spring Boot automatically enables:

```java
@EnableAspectJAutoProxy
```

---

Usually no need to write it manually.

---

Example:

```xml
<dependency>
    <groupId>
        org.springframework.boot
    </groupId>

    <artifactId>
        spring-boot-starter-aop
    </artifactId>
</dependency>
```

---

Now:

```java
@Aspect
```

works automatically.

---

# Internal Workflow

Application Startup:

```text
Read @EnableAspectJAutoProxy
            ↓
Register AutoProxyCreator
            ↓
Detect @Aspect Beans
            ↓
Create Proxies
            ↓
Apply Advice
```

---

Method Call:

```java
employeeService.save()
```

---

Flow:

```text
Proxy
  ↓
Before Advice
  ↓
Actual Method
  ↓
After Advice
```

---

# Common Mistakes

## Mistake 1

Missing AOP Dependency

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

Expecting Private Methods To Be Intercepted

```java
private void save() {
}
```

---

Proxy cannot intercept.

---

No AOP.

---

# Common Interview Questions

## What is @EnableAspectJAutoProxy?

Enables Spring AOP and automatic proxy creation for aspects.

---

## Why is it required?

To activate `@Aspect` processing.

---

## What happens internally?

Spring creates AOP proxies.

---

## What proxy mechanisms are supported?

```text
JDK Dynamic Proxy
CGLIB Proxy
```

---

## What does proxyTargetClass=true do?

Forces CGLIB proxy creation.

---

## What does exposeProxy=true do?

Makes the current proxy accessible.

---

## Is it needed in Spring Boot?

Usually no.

Boot enables it automatically when AOP starter is present.

---

# Enterprise Best Practice

Use:

```xml
spring-boot-starter-aop
```

---

and simply define:

```java
@Aspect
@Component
```

---

Avoid manual configuration unless:

```text
Custom Proxy Settings
CGLIB Requirement
Self Invocation Handling
```

---

Prefer:

```java
proxyTargetClass = true
```

only when interface-based proxies are insufficient.

---

# Key Points To Remember

- `@EnableAspectJAutoProxy` enables Spring AOP.
- Activates processing of `@Aspect`.
- Creates proxies around beans.
- Uses JDK Dynamic Proxy or CGLIB.
- Registers `AnnotationAwareAspectJAutoProxyCreator`.
- Supports `proxyTargetClass` and `exposeProxy`.
- Usually auto-configured by Spring Boot.
- Required in traditional Spring configurations.
- Foundation of Spring AOP infrastructure.
- Frequently asked Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Aspect` | Define Aspect |
| `@Pointcut` | Define Join Points |
| `@Before` | Before Advice |
| `@Around` | Around Advice |
| `@EnableAspectJAutoProxy` | Enable AOP |
| `@Transactional` | Transaction Aspect |

---

# Interview Shortcut

```java
@Configuration

@EnableAspectJAutoProxy
public class Config {
}
```

Meaning:

```text
Enable Spring AOP
         ↓
Detect @Aspect Beans
         ↓
Create Proxies
         ↓
Intercept Method Calls
         ↓
Execute Advice
```

Typical production flow:

```java
@EnableAspectJAutoProxy
        +
@Aspect
```

```text
Method Call
      ↓
Proxy Intercepts
      ↓
Execute Advice
      ↓
Execute Business Logic
```

This is the core Spring annotation that activates Aspect-Oriented Programming and allows `@Aspect`-based cross-cutting concerns such as logging, security, auditing, and monitoring to work throughout an application.