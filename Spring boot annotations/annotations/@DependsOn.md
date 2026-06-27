
## Definition

`@DependsOn` is a Spring annotation used to explicitly specify bean initialization order.

It tells Spring:

> Before creating this bean, make sure the specified bean(s) are created first.

Package:

```java
import org.springframework.context.annotation.DependsOn;
```

---

# Why Do We Need @DependsOn?

Normally Spring creates beans based on dependency requirements.

Example:

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeRepository repository;

}
```

Spring automatically creates:

```text
EmployeeRepository
        ↓
EmployeeService
```

because of dependency injection.

---

However, sometimes beans do not directly depend on each other but still require a specific initialization order.

Example:

```text
DatabaseInitializer
        ↓
CacheManager
        ↓
ApplicationService
```

No direct injection exists, but order matters.

This is where:

```java
@DependsOn
```

becomes useful.

---

# Basic Syntax

```java
@DependsOn("beanName")
```

---

Example:

```java
@Component
@DependsOn("databaseInitializer")
public class EmployeeService {

}
```

---

Meaning:

```text
Create databaseInitializer
        ↓
Then
        ↓
Create EmployeeService
```

---

# What Happens Internally?

Suppose:

```java
@Component
@DependsOn("databaseInitializer")
public class EmployeeService {

}
```

---

Spring Startup:

### Step 1

Find:

```java
@DependsOn
```

---

### Step 2

Locate bean:

```text
databaseInitializer
```

---

### Step 3

Create:

```java
databaseInitializer
```

---

### Step 4

Create:

```java
EmployeeService
```

---

Flow:

```text
Find @DependsOn
        │
        ▼
Create Dependency Bean
        │
        ▼
Create Current Bean
```

---

# Simple Example

## Bean 1

```java
@Component
public class DatabaseInitializer {

    public DatabaseInitializer() {

        System.out.println("Database Initialized");

    }

}
```

---

## Bean 2

```java
@Component
@DependsOn("databaseInitializer")
public class EmployeeService {

    public EmployeeService() {

        System.out.println("Employee Service Created");

    }

}
```

---

Output:

```text
Database Initialized
Employee Service Created
```

---

# Without @DependsOn

Spring may create beans in any order.

Example:

```text
EmployeeService Created
Database Initialized
```

or

```text
Database Initialized
EmployeeService Created
```

---

Order is not guaranteed.

---

# Multiple Dependencies

You can specify multiple beans.

---

```java
@Component
@DependsOn({
    "databaseInitializer",
    "cacheInitializer"
})
public class EmployeeService {

}
```

---

Initialization Order:

```text
databaseInitializer
cacheInitializer
EmployeeService
```

---

# Example with @Bean

---

```java
@Configuration
public class AppConfig {

    @Bean
    public DatabaseInitializer databaseInitializer() {

        return new DatabaseInitializer();

    }

    @Bean
    @DependsOn("databaseInitializer")
    public EmployeeService employeeService() {

        return new EmployeeService();

    }

}
```

---

Spring creates:

```text
databaseInitializer
        ↓
employeeService
```

---

# Bean Name Requirement

Important.

---

```java
@DependsOn("databaseInitializer")
```

refers to:

```text
Bean Name
```

not class name.

---

Example:

```java
@Bean("dbInit")
public DatabaseInitializer databaseInitializer() {

}
```

---

Correct:

```java
@DependsOn("dbInit")
```

---

Wrong:

```java
@DependsOn("databaseInitializer")
```

---

Result:

```text
NoSuchBeanDefinitionException
```

---

# Real-World Example

## Database Initialization

---

```java
@Component
public class DatabaseInitializer {

    @PostConstruct
    public void init() {

        System.out.println("Creating Tables");

    }

}
```

---

```java
@Component
@DependsOn("databaseInitializer")
public class EmployeeService {

}
```

---

Ensures:

```text
Tables Created
        ↓
Service Started
```

---

# Cache Initialization Example

---

```java
@Component
public class CacheLoader {

}
```

---

```java
@Component
@DependsOn("cacheLoader")
public class ProductService {

}
```

---

Ensures:

```text
Cache Loaded
        ↓
ProductService Created
```

---

# Message Queue Example

---

```java
@Component
public class KafkaTopicInitializer {

}
```

---

```java
@Component
@DependsOn("kafkaTopicInitializer")
public class KafkaProducer {

}
```

---

Ensures:

```text
Topic Created
        ↓
Producer Started
```

---

# @DependsOn vs @Autowired

Very Important Interview Question

---

## @Autowired

Represents:

```text
Actual Dependency
```

---

```java
@Autowired
private EmployeeRepository repository;
```

---

Spring automatically determines order.

---

## @DependsOn

Represents:

```text
Initialization Dependency
```

---

No object reference required.

---

Comparison:

| Feature | @Autowired | @DependsOn |
|----------|------------|------------|
| Object Injection | Yes | No |
| Initialization Order | Automatic | Explicit |
| Runtime Dependency | Yes | No |
| Bean Reference | Required | Not Required |

---

# @DependsOn vs @Order

Another interview favorite.

---

## @DependsOn

Controls:

```text
Bean Creation Order
```

---

## @Order

Controls:

```text
Execution Order
```

---

Example:

```java
@Order(1)
```

used for:

```text
Filters
Interceptors
Listeners
Collections
```

---

Not bean creation.

---

# @DependsOn with Singleton Beans

Most common use case.

---

```java
@Component
@DependsOn("cacheManager")
public class ProductService {

}
```

---

Ensures singleton initialization sequence.

---

# @DependsOn with Lazy Beans

Interesting behavior.

---

```java
@Component
@Lazy
@DependsOn("databaseInitializer")
public class EmployeeService {

}
```

---

When bean is requested:

```text
databaseInitializer
        ↓
EmployeeService
```

---

Dependency is still respected.

---

# Circular Dependency Problem

Avoid this.

---

Bean A:

```java
@DependsOn("beanB")
```

---

Bean B:

```java
@DependsOn("beanA")
```

---

Result:

```text
Circular Dependency
```

---

Startup failure.

---

# Destruction Order

Important advanced concept.

---

Suppose:

```java
@DependsOn("databaseInitializer")
```

---

Initialization:

```text
databaseInitializer
        ↓
employeeService
```

---

Destruction:

```text
employeeService
        ↓
databaseInitializer
```

---

Spring destroys dependent beans first.

---

# Internal Working

During container startup:

```text
Read Bean Definitions
        │
        ▼
Find @DependsOn
        │
        ▼
Create Dependency Beans
        │
        ▼
Create Current Bean
```

---

Internally Spring stores dependency metadata in:

```text
BeanDefinition
```

and resolves order before instantiation.

---

# Common Mistakes

## Mistake 1

Using Class Name Instead of Bean Name

---

Wrong:

```java
@DependsOn("DatabaseInitializer")
```

---

Correct:

```java
@DependsOn("databaseInitializer")
```

---

# Mistake 2

Using Non-Existing Bean

---

```java
@DependsOn("unknownBean")
```

---

Result:

```text
NoSuchBeanDefinitionException
```

---

# Mistake 3

Using It Instead of Dependency Injection

Wrong:

```java
@DependsOn("repository")
```

---

If you actually need the object:

```java
@Autowired
private Repository repository;
```

---

# Mistake 4

Creating Circular Dependencies

---

```text
A → B
B → A
```

---

Application startup fails.

---

# Common Interview Questions

## What is @DependsOn?

Used to define explicit bean initialization order.

---

## Does @DependsOn inject dependencies?

No.

It only controls bean creation order.

---

## What does @DependsOn reference?

```text
Bean Name
```

---

## Can multiple beans be specified?

Yes.

---

```java
@DependsOn({"bean1","bean2"})
```

---

## Does it work on @Bean methods?

Yes.

---

## What happens if bean does not exist?

```text
NoSuchBeanDefinitionException
```

---

## Does it affect bean destruction?

Yes.

Destruction order is reversed.

---

## Difference Between @DependsOn and @Autowired?

`@Autowired`

```text
Object Dependency
```

---

`@DependsOn`

```text
Initialization Dependency
```

---

# Real-World Use Cases

## Database Initialization

```text
Schema Creation
        ↓
Services
```

---

## Cache Loading

```text
Cache Population
        ↓
Business Services
```

---

## Kafka Setup

```text
Topic Creation
        ↓
Producers
Consumers
```

---

## Scheduler Initialization

```text
Scheduler Setup
        ↓
Scheduled Jobs
```

---

## Security Initialization

```text
Security Context
        ↓
Application Services
```

---

# Internal Flow Diagram

```text
Application Startup
        │
        ▼
Scan Beans
        │
        ▼
Find @DependsOn
        │
        ▼
Create Dependent Beans
        │
        ▼
Create Current Bean
        │
        ▼
Application Ready
```

---

# Key Points To Remember

- Used to control bean initialization order.
- Package: `org.springframework.context.annotation.DependsOn`.
- References bean names, not class names.
- Does not inject dependencies.
- Supports single and multiple dependencies.
- Works on classes and `@Bean` methods.
- Initialization order is guaranteed.
- Destruction order is reversed.
- Commonly used for startup initialization tasks.
- Useful for cache loading, database setup, Kafka initialization, and schedulers.
- One of the most frequently asked Spring bean lifecycle interview topics.