# Proxy Design Pattern

> **Goal:** Understand Proxy Design Pattern from scratch, why it exists, how it works internally, Static Proxy, Dynamic Proxy, JDK Dynamic Proxy, CGLIB Proxy, Spring AOP, Lazy Loading, Security Proxy, Caching Proxy, Remote Proxy, advantages, disadvantages, interview questions, and how it differs from Decorator, Facade, and Adapter.

---

# 1. What Problem Does Proxy Pattern Solve?

Suppose you have a very large image.

```text
10 MB Image
```

---

Loading the image takes:

```text
5 seconds
```

---

If your application creates the image immediately:

```java
Image image =
        new RealImage("photo.jpg");
```

---

The image loads even if the user never opens it.

---

Waste of:

```text
Memory
CPU
Time
```

---

Need a mechanism that says:

```text
Don't load now.
Load only when needed.
```

---

This is one use case of Proxy Pattern.

---

# 2. Definition

> Proxy Pattern provides a placeholder or surrogate object that controls access to another object.

---

# 3. Simple Meaning

Instead of:

```text
Client
   ↓
Real Object
```

Use:

```text
Client
   ↓
Proxy
   ↓
Real Object
```

---

Proxy sits in the middle.

---

It can:

```text
Control Access
Check Security
Cache Results
Lazy Load
Log Calls
Call Remote Systems
```

---

# 4. Real-Life Examples

---

## Example 1: ATM Card

You don't directly access:

```text
Bank Account
```

---

You use:

```text
ATM Card
```

---

ATM card acts as:

```text
Proxy
```

---

# Example 2: Security Guard

Visitor wants to enter:

```text
Office Building
```

---

Security Guard checks:

```text
Identity
Permission
```

---

Then allows entry.

---

Security Guard is:

```text
Proxy
```

---

# Example 3: Credit Card

You don't directly handle:

```text
Bank Balance
```

---

You use:

```text
Credit Card
```

---

Credit Card acts as proxy.

---

# 5. Core Idea

Proxy object and Real object share:

```text
Same Interface
```

---

Client cannot distinguish between them.

---

Very important interview point.

---

# 6. Structure

```text
Client
   ↓
Proxy
   ↓
Real Subject
```

---

# 7. Components

---

## 1. Subject

Common interface.

---

Example:

```java
Image
```

---

## 2. Real Subject

Actual implementation.

---

Example:

```java
RealImage
```

---

## 3. Proxy

Controls access.

---

Example:

```java
ProxyImage
```

---

## 4. Client

Uses Subject interface.

---

# 8. UML Structure

```text
             Subject
                ↑
        ----------------
        ↑              ↑

     Proxy       RealSubject
```

---

# 9. Real-World Example

# Lazy Loading Image

---

## Step 1: Create Subject

```java
public interface Image {

    void display();
}
```

---

# Step 2: Create Real Subject

```java
public class RealImage
        implements Image {

    private String fileName;

    public RealImage(
            String fileName) {

        this.fileName = fileName;

        loadImage();
    }

    private void loadImage() {

        System.out.println(
                "Loading Image...");
    }

    @Override
    public void display() {

        System.out.println(
                "Displaying "
                        + fileName);
    }
}
```

---

Image loads immediately.

---

# Step 3: Create Proxy

```java
public class ProxyImage
        implements Image {

    private RealImage realImage;

    private String fileName;

    public ProxyImage(
            String fileName) {

        this.fileName = fileName;
    }

    @Override
    public void display() {

        if(realImage == null) {

            realImage =
                new RealImage(
                        fileName);
        }

        realImage.display();
    }
}
```

---

Important:

```java
if(realImage == null)
```

---

This is:

```text
Lazy Initialization
```

---

# Step 4: Client

```java
Image image =
        new ProxyImage(
                "photo.jpg");

System.out.println(
        "Image Created");

image.display();
```

---

Output:

```text
Image Created

Loading Image...

Displaying photo.jpg
```

---

Notice:

Image loaded only when:

```java
display()
```

was called.

---

# 10. Internal Working

Memory:

```text
ProxyImage
      ↓
RealImage (null)
```

---

After first call:

```text
ProxyImage
      ↓
RealImage
```

---

Subsequent calls:

```text
No Reload
```

---

# 11. Types of Proxy Pattern

Most important interview topic.

---

# Type 1: Virtual Proxy

Most common.

---

Purpose:

```text
Lazy Loading
```

---

Example:

```text
Large Images
PDF Files
Videos
```

---

Real object created only when required.

---

# Type 2: Protection Proxy

Purpose:

```text
Access Control
```

---

Example:

```java
if(user.isAdmin())
{
    service.execute();
}
```

---

Only authorized users allowed.

---

# 12. Protection Proxy Example

```java
public class SecurityProxy
        implements Service {

    private RealService service =
            new RealService();

    @Override
    public void execute() {

        if(isAdmin()) {

            service.execute();
        }
        else {

            System.out.println(
                    "Access Denied");
        }
    }
}
```

---

Very common in enterprise applications.

---

# 13. Type 3: Remote Proxy

Purpose:

```text
Represent Remote Object
```

---

Example:

```text
Client
 ↓
Proxy
 ↓
Remote Server
```

---

Examples:

```text
RMI
Web Services
gRPC
REST Clients
```

---

Proxy hides network complexity.

---

# 14. Type 4: Caching Proxy

Purpose:

```text
Store Results
```

---

Example:

```java
cache.get(productId);
```

---

If data exists:

```text
Return Cached Data
```

---

Else:

```text
Call Database
```

---

Huge performance improvement.

---

# 15. Type 5: Logging Proxy

Purpose:

```text
Track Method Calls
```

---

Example:

```java
log("Before");

service.execute();

log("After");
```

---

Common in AOP.

---

# 16. Type 6: Smart Reference Proxy

Adds extra behavior.

---

Examples:

```text
Reference Counting
Transaction Management
Monitoring
```

---

Advanced usage.

---

# 17. Static Proxy

Proxy class written manually.

---

Example:

```java
class ServiceProxy
        implements Service
```

---

Developer writes code.

---

Advantages:

```text
Simple
```

---

Disadvantages:

```text
Many Proxy Classes
```

---

# 18. Dynamic Proxy

Generated automatically at runtime.

---

No manual proxy class needed.

---

Java provides:

```java
Proxy.newProxyInstance()
```

---

Very important interview topic.

---

# 19. JDK Dynamic Proxy

Works only for:

```text
Interfaces
```

---

Example:

```java
UserService
```

interface.

---

Runtime generates:

```text
Proxy Class
```

automatically.

---

# Example

```java
UserService proxy =
    (UserService)
    Proxy.newProxyInstance(
        ...
    );
```

---

# 20. InvocationHandler

Heart of JDK Dynamic Proxy.

---

```java
public class LoggingHandler
        implements InvocationHandler {

    @Override
    public Object invoke(
            Object proxy,
            Method method,
            Object[] args)
            throws Throwable {

        System.out.println(
                "Before");

        Object result =
                method.invoke(
                        target,
                        args);

        System.out.println(
                "After");

        return result;
    }
}
```

---

Every method call passes through:

```java
invoke()
```

---

# 21. CGLIB Proxy

Problem:

```text
JDK Proxy Needs Interface
```

---

Solution:

```text
CGLIB
```

---

Creates proxy using:

```text
Inheritance
```

---

Example:

```java
class UserService
```

without interface.

---

Still proxied.

---

Very important in Spring.

---

# 22. Spring Framework and Proxy

One of the most important real-world usages.

---

Spring AOP works using:

```text
Proxy Pattern
```

---

When you write:

```java
@Transactional
```

Spring creates proxy.

---

Flow:

```text
Client
  ↓
Spring Proxy
  ↓
Real Service
```

---

# 23. @Transactional Example

```java
@Transactional
public void transferMoney()
{
    ...
}
```

---

Spring internally creates:

```text
Transaction Proxy
```

---

Flow:

```text
Start Transaction
       ↓
Call Method
       ↓
Commit/Rollback
```

---

Classic Proxy Pattern.

---

# 24. Spring Security Example

:contentReference[oaicite:0]{index=0}

When endpoint is secured:

```java
@PreAuthorize("hasRole('ADMIN')")
```

---

Proxy checks:

```text
Authorization
```

before executing method.

---

Protection Proxy.

---

# 25. Lazy Loading in JPA

Most important real-world example.

---

Suppose:

```java
User
```

contains:

```java
List<Order>
```

---

Orders may not be loaded immediately.

---

Hibernate creates:

```text
Proxy Object
```

---

When:

```java
user.getOrders()
```

is called,

Hibernate fetches data.

---

This is:

```text
Virtual Proxy
```

---

# 26. Java Examples

---

## Dynamic Proxy API

Package:

```java
java.lang.reflect.Proxy
```

---

## InvocationHandler

```java
java.lang.reflect.InvocationHandler
```

---

Used heavily in frameworks.

---

# 27. Advantages

---

## 1. Access Control

Protection proxy.

---

## 2. Lazy Loading

Virtual proxy.

---

## 3. Caching

Caching proxy.

---

## 4. Logging

Logging proxy.

---

## 5. Separation Of Concerns

Business code stays clean.

---

# 28. Disadvantages

---

## 1. More Classes

Additional proxy layer.

---

## 2. More Complexity

Debugging harder.

---

## 3. Slight Performance Cost

Extra method invocation.

---

# 29. Proxy vs Decorator

Most asked interview question.

---

Structure looks almost identical.

---

## Proxy

Purpose:

```text
Control Access
```

---

Examples:

```text
Security

Caching

Lazy Loading
```

---

## Decorator

Purpose:

```text
Add Behavior
```

---

Examples:

```text
Milk Coffee

Sugar Coffee
```

---

### Memory Trick

```text
Decorator
      ↓
Enhance

Proxy
      ↓
Control
```

---

# 30. Proxy vs Facade

---

## Facade

Simplifies system.

---

Example:

```java
checkoutFacade.placeOrder();
```

---

## Proxy

Controls access.

---

Example:

```java
securityProxy.execute();
```

---

### Memory Trick

```text
Facade
      ↓
Simplify

Proxy
      ↓
Control
```

---

# 31. Proxy vs Adapter

---

## Adapter

Converts interface.

---

Example:

```text
USB Adapter
```

---

## Proxy

Keeps same interface.

---

Example:

```text
Security Proxy
```

---

### Memory Trick

```text
Adapter
      ↓
Convert

Proxy
      ↓
Control
```

---

# 32. Static Proxy vs Dynamic Proxy

| Static Proxy | Dynamic Proxy |
|-------------|-------------|
| Manual Class | Runtime Generated |
| More Code | Less Code |
| Easy To Understand | More Powerful |
| Compile Time | Runtime |

---

Important interview topic.

---

# 33. JDK Proxy vs CGLIB Proxy

| JDK Proxy | CGLIB |
|------------|--------|
| Interface Required | No Interface Required |
| Uses Proxy API | Uses Inheritance |
| Faster Creation | More Flexible |

---

Spring chooses:

```text
JDK Proxy
```

if interface exists.

---

Otherwise:

```text
CGLIB
```

---

# 34. When To Use Proxy

Use when:

- Security required
- Lazy loading needed
- Caching needed
- Logging needed
- Remote communication involved

Examples:

```text
Spring AOP
Hibernate Lazy Loading
Security
Caching
Remote APIs
```

---

# 35. When NOT To Use

Avoid when:

- Direct access is fine
- No additional control needed

---

# 36. Interview Questions

### Q1. What problem does Proxy solve?

Controls access to an object.

---

### Q2. What are the types of Proxy?

```text
Virtual Proxy

Protection Proxy

Remote Proxy

Caching Proxy

Logging Proxy

Smart Proxy
```

---

### Q3. Difference Between Proxy and Decorator?

Decorator:

```text
Add Behavior
```

Proxy:

```text
Control Access
```

---

### Q4. What is Dynamic Proxy?

Runtime-generated proxy.

---

### Q5. What is InvocationHandler?

Handles method calls in JDK Dynamic Proxy.

---

### Q6. JDK Proxy vs CGLIB?

JDK:

```text
Interface Required
```

CGLIB:

```text
Inheritance Based
```

---

### Q7. Give Spring examples.

```text
@Transactional

@PreAuthorize

Spring AOP

Hibernate Lazy Loading
```

---

### Q8. Which design pattern is used by Spring AOP?

```text
Proxy Pattern
```

---

# Real-Life Memory Trick

```text
Singleton
     ↓
One Object

Factory
     ↓
Create Object

Builder
     ↓
Build Object

Prototype
     ↓
Clone Object

Adapter
     ↓
Convert Interface

Bridge
     ↓
Separate Abstraction

Composite
     ↓
Tree Structure

Facade
     ↓
Hide Complexity

Decorator
     ↓
Add Behavior

Flyweight
     ↓
Share State

Chain Of Responsibility
     ↓
Pass Request

Proxy
     ↓
Control Access
```

---

# Quick Revision

```text
Proxy Design Pattern
--------------------

Purpose:
Control access to an object.

Structure:

Client
  ↓
Proxy
  ↓
Real Object

Types:

1. Virtual Proxy
2. Protection Proxy
3. Remote Proxy
4. Caching Proxy
5. Logging Proxy
6. Smart Proxy

Spring Examples:

✔ @Transactional
✔ @PreAuthorize
✔ Spring AOP
✔ Hibernate Lazy Loading

Advantages:

✔ Security
✔ Lazy Loading
✔ Caching
✔ Logging

Disadvantages:

✘ Extra Layer
✘ More Complexity

Remember:

Adapter
     ↓
Convert

Facade
     ↓
Simplify

Decorator
     ↓
Enhance

Proxy
     ↓
Control
```