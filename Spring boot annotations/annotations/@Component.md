
## Definition

`@Component` is the most fundamental stereotype annotation in Spring.

It tells Spring:

> This class is a Spring-managed bean and should be automatically detected during component scanning.

When Spring encounters a class annotated with:

```java
@Component
```

it:

1. Creates a Bean Definition.
2. Registers it in the IoC Container.
3. Manages its lifecycle.
4. Makes it available for Dependency Injection.

---

# Why Do We Need @Component?

Suppose we have:

```java
public class EmployeeService {

}
```

Spring does not know about this class.

Trying to inject it:

```java
@Autowired
private EmployeeService service;
```

Results in:

```text
NoSuchBeanDefinitionException
```

because Spring never created a bean.

---

Solution:

```java
@Component
public class EmployeeService {

}
```

Now Spring automatically creates and manages the bean.

---

# Basic Syntax

```java
@Component
public class EmployeeService {

}
```

---

# What Happens Internally?

During startup:

```java
@ComponentScan
```

scans packages.

---

Spring finds:

```java
@Component
public class EmployeeService {
}
```

---

Then:

### Step 1

Create Bean Definition.

---

### Step 2

Register Bean Definition.

---

### Step 3

Instantiate Bean.

---

### Step 4

Perform Dependency Injection.

---

### Step 5

Store Bean in IoC Container.

---

Flow:

```text
@Component
      │
      ▼
Component Scan
      │
      ▼
Bean Definition
      │
      ▼
Bean Creation
      │
      ▼
Dependency Injection
      │
      ▼
Bean Ready
```

---

# Basic Example

```java
@Component
public class EmployeeService {

    public String getEmployee() {
        return "John";
    }

}
```

---

```java
@RestController
public class EmployeeController {

    @Autowired
    private EmployeeService service;

    @GetMapping("/employee")
    public String getEmployee() {
        return service.getEmployee();
    }

}
```

Spring injects:

```java
EmployeeService
```

automatically.

---

# Bean Naming

Default bean name:

```java
@Component
public class EmployeeService {
}
```

Bean Name:

```text
employeeService
```

Spring converts first character to lowercase.

---

# Custom Bean Name

```java
@Component("empService")
public class EmployeeService {

}
```

Bean Name:

```text
empService
```

---

# How Spring Finds @Component?

Through:

```java
@ComponentScan
```

---

Example:

```java
@SpringBootApplication
```

contains:

```java
@ComponentScan
```

internally.

---

Therefore:

```java
@Component
```

classes are automatically discovered.

---

# Real Example

Project Structure:

```text
com.company

 ├── Application
 ├── controller
 ├── service
 └── repository
```

---

Application:

```java
@SpringBootApplication
public class Application {

}
```

---

Service:

```java
@Component
public class EmployeeService {

}
```

Spring scans:

```text
com.company
```

and subpackages.

Bean gets registered.

---

# Component Lifecycle

```java
@Component
public class EmployeeService {

}
```

Lifecycle:

```text
Bean Definition Created
         │
         ▼
Constructor Called
         │
         ▼
Dependency Injection
         │
         ▼
@PostConstruct
         │
         ▼
Bean Ready
         │
         ▼
@PreDestroy
         │
         ▼
Bean Destroyed
```

---

# Constructor Injection Example

Recommended approach.

---

```java
@Component
public class EmployeeRepository {

}
```

---

```java
@Component
public class EmployeeService {

    private final EmployeeRepository repository;

    public EmployeeService(
            EmployeeRepository repository) {

        this.repository = repository;
    }

}
```

Spring injects dependency automatically.

---

# Field Injection Example

```java
@Component
public class EmployeeService {

    @Autowired
    private EmployeeRepository repository;

}
```

Works but not recommended.

---

# Setter Injection Example

```java
@Component
public class EmployeeService {

    private EmployeeRepository repository;

    @Autowired
    public void setRepository(
            EmployeeRepository repository) {

        this.repository = repository;
    }

}
```

---

# Scope of @Component

Default scope:

```java
Singleton
```

---

Example:

```java
@Component
public class EmployeeService {

}
```

Only one instance exists.

---

# Prototype Scope

```java
@Component
@Scope("prototype")
public class EmployeeService {

}
```

New bean every request.

---

# Lazy Component

```java
@Component
@Lazy
public class EmployeeService {

}
```

Created only when first needed.

---

# Conditional Component

```java
@Component
@Profile("dev")
public class DevService {

}
```

Bean created only for:

```text
dev
```

profile.

---

# @Component vs @Bean

Very important interview question.

---

## @Component

Used on class.

```java
@Component
public class EmployeeService {

}
```

Spring creates bean automatically.

---

## @Bean

Used on method.

```java
@Bean
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

Manual bean creation.

---

| @Component | @Bean |
|------------|--------|
| Class Level | Method Level |
| Auto Detection | Manual Registration |
| Component Scan Required | Configuration Class Required |
| Best for Own Classes | Best for Third-Party Classes |

---

# @Component vs @Service

Another favorite interview question.

---

## @Component

Generic stereotype.

```java
@Component
public class EmployeeService {

}
```

---

## @Service

Specialized stereotype.

```java
@Service
public class EmployeeService {

}
```

---

Internally:

```java
@Service
```

contains:

```java
@Component
```

---

Difference:

```text
@Service gives semantic meaning.
```

Used for business layer.

---

# @Component vs @Repository

```java
@Repository
```

also contains:

```java
@Component
```

---

Additional feature:

```java
Exception Translation
```

Database exceptions converted into:

```java
DataAccessException
```

---

# @Component vs @Controller

```java
@Controller
```

also contains:

```java
@Component
```

---

Purpose:

```text
Web MVC Controllers
```

---

# Stereotype Hierarchy

```text
@Component
    │
    ├── @Service
    ├── @Repository
    ├── @Controller
    └── @RestController
```

---

# Meta-Annotation

Very important concept.

---

Definition of:

```java
@Service
```

looks similar to:

```java
@Target(TYPE)
@Retention(RUNTIME)
@Component
public @interface Service {
}
```

Meaning:

```java
@Service
```

is actually a specialized:

```java
@Component
```

---

# Can We Create Custom Annotations?

Yes.

---

Example:

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface BusinessService {

}
```

---

Usage:

```java
@BusinessService
public class EmployeeService {

}
```

Spring treats it as:

```java
@Component
```

---

# Internal Working

```text
Application Start
        │
        ▼
@ComponentScan
        │
        ▼
Find @Component
        │
        ▼
Create Bean Definition
        │
        ▼
Instantiate Bean
        │
        ▼
Inject Dependencies
        │
        ▼
Store In IoC Container
        │
        ▼
Application Ready
```

---

# Common Mistakes

## Mistake 1

Missing Component Scan.

```java
@Component
public class EmployeeService {
}
```

Outside scanned package.

Result:

```text
NoSuchBeanDefinitionException
```

---

## Mistake 2

Using @Component everywhere.

Wrong:

```java
@Component
public class EmployeeController {
}
```

Better:

```java
@Controller
```

or

```java
@RestController
```

---

## Mistake 3

Manual object creation.

```java
EmployeeService service =
        new EmployeeService();
```

This bypasses Spring.

Dependencies won't be injected.

---

# Common Interview Questions

## What is @Component?

Marks a class as a Spring-managed bean.

---

## How is it discovered?

Through:

```java
@ComponentScan
```

---

## What is default scope?

```java
Singleton
```

---

## Can @Component have a custom bean name?

Yes.

```java
@Component("empService")
```

---

## Difference Between @Component and @Bean?

`@Component` = automatic bean registration.

`@Bean` = manual bean registration.

---

## Difference Between @Component and @Service?

`@Service` is a specialized form of `@Component` intended for business logic.

---

## Is @Service internally a Component?

Yes.

```java
@Service
```

is meta-annotated with:

```java
@Component
```

---

## Can @Component be Lazy?

Yes.

```java
@Component
@Lazy
```

---

## Can @Component be Profile-specific?

Yes.

```java
@Component
@Profile("prod")
```

---

# Real-World Significance

`@Component` is the foundation of Spring's Dependency Injection mechanism.

Every major stereotype annotation is built upon it:

```java
@Service
@Repository
@Controller
@RestController
```

Without `@Component`, Spring would not be able to:

- Discover classes
- Create beans
- Inject dependencies
- Manage object lifecycle

It is one of the most fundamental annotations in the entire Spring Framework.

---

# Key Points To Remember

- Marks a class as a Spring Bean.
- Discovered through `@ComponentScan`.
- Managed by Spring IoC Container.
- Supports Dependency Injection.
- Default scope is Singleton.
- Can have custom bean names.
- Can be combined with `@Lazy`, `@Profile`, and `@Scope`.
- Parent annotation of `@Service`, `@Repository`, and `@Controller`.
- Forms the foundation of Spring's bean management system.
- One of the most important annotations in Spring Core and Spring Boot.