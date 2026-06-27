
## Definition

`@Lookup` is a Spring annotation used for **Method Injection**.

It tells Spring:

> Override this method at runtime and return a bean from the Spring IoC Container whenever the method is called.

Package:

```java
import org.springframework.beans.factory.annotation.Lookup;
```

---

# Why Do We Need @Lookup?

One of the most important Spring concepts is:

```text
Singleton Bean
        ↓
Needs
        ↓
Prototype Bean
```

Normally:

```java
@Service
public class EmployeeService {

    @Autowired
    private Employee employee;

}
```

If:

```java
EmployeeService → Singleton
Employee → Prototype
```

then:

```java
Employee
```

is injected only once during application startup.

After that:

```java
Same Employee Object
```

is reused forever.

---

But sometimes we need:

```text
New Prototype Bean
For Every Method Call
```

This is where:

```java
@Lookup
```

comes into play.

---

# Problem Without @Lookup

## Prototype Bean

```java
@Component
@Scope("prototype")
public class Employee {

}
```

---

## Singleton Bean

```java
@Service
public class EmployeeService {

    @Autowired
    private Employee employee;

}
```

---

Result:

```text
Only One Employee Object
```

is injected.

Even though Employee is prototype.

---

Why?

Because:

```text
Dependency Injection
Happens Once
During Bean Creation
```

---

# Solution

Use:

```java
@Lookup
```

---

# Basic Example

## Prototype Bean

```java
@Component
@Scope("prototype")
public class Employee {

}
```

---

## Singleton Bean

```java
@Service
public abstract class EmployeeService {

    @Lookup
    protected abstract Employee getEmployee();

}
```

---

Usage:

```java
Employee e1 = getEmployee();

Employee e2 = getEmployee();
```

---

Result:

```java
e1 != e2
```

New object each time.

---

# What Happens Internally?

Spring creates a subclass dynamically.

---

Your code:

```java
@Service
public abstract class EmployeeService {

    @Lookup
    protected abstract Employee getEmployee();

}
```

---

Spring generates something like:

```java
public class EmployeeServiceProxy
        extends EmployeeService {

    @Override
    protected Employee getEmployee() {

        return applicationContext
                .getBean(Employee.class);

    }

}
```

---

Every method call:

```java
getEmployee()
```

becomes:

```java
applicationContext
        .getBean(Employee.class);
```

---

Flow:

```text
Call Method
      │
      ▼
@Lookup Intercepts
      │
      ▼
Container Lookup
      │
      ▼
Return Bean
```

---

# Simple Working Example

## Prototype Bean

```java
@Component
@Scope("prototype")
public class Employee {

    public Employee() {

        System.out.println("Employee Created");

    }

}
```

---

## Service

```java
@Service
public abstract class EmployeeService {

    @Lookup
    protected abstract Employee getEmployee();

    public void test() {

        Employee e1 = getEmployee();
        Employee e2 = getEmployee();

        System.out.println(e1);
        System.out.println(e2);

    }

}
```

---

Output:

```text
Employee Created
Employee Created

Employee@123
Employee@456
```

Different instances.

---

# Using Concrete Method

Method does NOT have to be abstract.

---

```java
@Service
public class EmployeeService {

    @Lookup
    public Employee getEmployee() {

        return null;
    }

}
```

---

Spring overrides method at runtime.

---

# Bean Name Lookup

Instead of type lookup.

---

```java
@Lookup("employeeBean")
public Employee getEmployee() {

    return null;
}
```

---

Equivalent to:

```java
applicationContext
    .getBean("employeeBean");
```

---

# Example with @Bean

---

```java
@Configuration
public class AppConfig {

    @Bean
    @Scope("prototype")
    public Employee employee() {

        return new Employee();

    }

}
```

---

```java
@Service
public abstract class EmployeeService {

    @Lookup
    protected abstract Employee employee();

}
```

---

Works perfectly.

---

# Singleton to Prototype Injection

Most common use case.

---

```text
Singleton
     │
     ▼
Prototype
```

---

Example:

```java
@Service
public abstract class OrderService {

    @Lookup
    protected abstract Order getOrder();

}
```

---

Every order request gets:

```text
New Order Object
```

---

# Without @Lookup

```java
@Autowired
private Order order;
```

Only one instance.

---

# With @Lookup

```java
@Lookup
protected abstract Order getOrder();
```

New instance every call.

---

# Alternative: ApplicationContext

Without @Lookup:

---

```java
@Autowired
private ApplicationContext context;
```

---

```java
public Employee getEmployee() {

    return context.getBean(Employee.class);

}
```

---

Works.

But:

```text
Business Logic Depends On Spring
```

---

Not ideal.

---

# Alternative: ObjectProvider

Modern preferred approach.

---

```java
@Autowired
private ObjectProvider<Employee> provider;
```

---

```java
Employee employee =
        provider.getObject();
```

---

Advantages:

- Cleaner
- More Explicit
- Better Testing

---

# Alternative: Provider

Jakarta version.

---

```java
@Inject
private Provider<Employee> provider;
```

---

```java
Employee employee =
        provider.get();
```

---

# @Lookup vs ObjectProvider

Important interview question.

---

## @Lookup

```java
@Lookup
protected abstract Employee getEmployee();
```

---

Pros:

- Clean API
- No Spring Code

---

Cons:

- Runtime subclass generation
- Harder to understand

---

## ObjectProvider

```java
provider.getObject();
```

---

Pros:

- Explicit
- Easier Debugging

---

Cons:

- Spring API dependency

---

Modern Spring projects often prefer:

```java
ObjectProvider
```

---

# Internal Implementation

Spring uses:

```text
CGLIB Proxy
```

to override the method.

---

Generated code:

```java
@Override
public Employee getEmployee() {

    return beanFactory
            .getBean(Employee.class);

}
```

---

# Requirements of @Lookup

Important.

---

Method must be:

```java
non-final
```

---

Because Spring needs to override it.

---

Wrong:

```java
@Lookup
public final Employee getEmployee() {

}
```

---

Fails.

---

# Abstract Class Example

Most common.

---

```java
@Service
public abstract class EmployeeService {

    @Lookup
    protected abstract Employee getEmployee();

}
```

---

Spring generates implementation.

---

# Concrete Class Example

Also valid.

---

```java
@Service
public class EmployeeService {

    @Lookup
    public Employee getEmployee() {

        return null;
    }

}
```

---

Spring overrides method.

---

# Common Mistakes

## Mistake 1

Using Final Method

---

```java
@Lookup
public final Employee getEmployee() {

}
```

---

Spring cannot override.

---

Fails.

---

# Mistake 2

Using Final Class

---

```java
public final class EmployeeService {

}
```

---

Spring cannot create subclass.

---

Fails.

---

# Mistake 3

Expecting Singleton Behavior

---

```java
@Lookup
```

always performs lookup.

---

May return:

```text
New Bean
```

for prototype scope.

---

# Mistake 4

Using It Unnecessarily

For singleton beans:

```java
@Lookup
```

usually adds no value.

---

# Common Interview Questions

## What is @Lookup?

Method Injection annotation used to obtain beans from the container dynamically.

---

## Why is @Lookup used?

Mainly for:

```text
Singleton → Prototype
```

dependency scenarios.

---

## What happens internally?

Spring generates a subclass using:

```text
CGLIB
```

and overrides the method.

---

## Is @Lookup runtime or compile-time?

```text
Runtime
```

---

## Can it return singleton beans?

Yes.

But it's primarily used with:

```text
Prototype Beans
```

---

## Can @Lookup be used with bean names?

Yes.

---

```java
@Lookup("employeeBean")
```

---

## Alternative to @Lookup?

- ObjectProvider
- Provider
- ApplicationContext.getBean()

---

# Real-World Use Cases

Common scenarios:

### Order Processing

```text
OrderService (Singleton)
        ↓
Order Object (Prototype)
```

---

### Report Generation

```text
ReportService (Singleton)
        ↓
ReportContext (Prototype)
```

---

### Workflow Execution

```text
WorkflowEngine (Singleton)
        ↓
WorkflowInstance (Prototype)
```

---

### Batch Processing

```text
BatchService (Singleton)
        ↓
JobExecutionContext (Prototype)
```

---

# Internal Flow

```text
Application Startup
        │
        ▼
Find @Lookup Method
        │
        ▼
Create CGLIB Subclass
        │
        ▼
Override Method
        │
        ▼
Call BeanFactory.getBean()
        │
        ▼
Return Bean
```

---

# @Lookup vs @Autowired

| Feature | @Autowired | @Lookup |
|----------|-----------|----------|
| Injection Time | Startup | Runtime |
| Bean Resolution | Once | Every Method Call |
| Prototype Support | Limited | Excellent |
| Dynamic Retrieval | No | Yes |
| Uses CGLIB | No | Yes |

---

# Key Points To Remember

- Used for Method Injection.
- Performs runtime bean lookup.
- Mainly solves Singleton → Prototype dependency problems.
- Spring overrides method using CGLIB.
- Method must not be final.
- Class should not be final.
- Can lookup by type or bean name.
- Equivalent to calling `ApplicationContext.getBean()`.
- Common alternative is `ObjectProvider`.
- Frequently asked advanced Spring interview topic.