
## Definition

`@Bean` is used to declare a method that produces a Spring-managed bean.

It tells Spring:

> The object returned by this method should be registered in the Spring IoC Container and managed by Spring.

Unlike:

```java
@Component
@Service
@Repository
@Controller
```

which are used on classes, `@Bean` is used on methods.

---

# Syntax

```java
@Bean
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

Spring executes this method and registers the returned object as a bean.

---

# Why Do We Need @Bean?

Suppose we have a class from a third-party library:

```java
public class RestTemplate {
}
```

We cannot modify it and add:

```java
@Component
```

because source code is not ours.

Solution:

```java
@Configuration
public class AppConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

}
```

Now Spring manages the object.

---

# What Happens Internally?

When Spring starts:

```java
ApplicationContext context =
        new AnnotationConfigApplicationContext(AppConfig.class);
```

Spring:

### Step 1

Finds:

```java
@Configuration
```

class.

---

### Step 2

Scans for:

```java
@Bean
```

methods.

---

### Step 3

Invokes method.

```java
employeeService()
```

---

### Step 4

Receives returned object.

```java
new EmployeeService()
```

---

### Step 5

Creates Bean Definition.

---

### Step 6

Registers bean in IoC Container.

---

# Basic Example

```java
@Configuration
public class AppConfig {

    @Bean
    public EmployeeService employeeService() {
        return new EmployeeService();
    }

}
```

Retrieve bean:

```java
ApplicationContext context =
        new AnnotationConfigApplicationContext(AppConfig.class);

EmployeeService service =
        context.getBean(EmployeeService.class);
```

---

# Bean Name

By default:

```java
@Bean
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

Bean name:

```java
employeeService
```

Method name becomes bean name.

---

# Custom Bean Name

```java
@Bean("empService")
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

Bean name:

```java
empService
```

---

# Multiple Bean Names

```java
@Bean({"empService","employeeBean"})
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

Both names refer to same bean.

---

# Real-World Example

## RestTemplate Bean

```java
@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

}
```

Usage:

```java
@Service
public class EmployeeService {

    @Autowired
    private RestTemplate restTemplate;

}
```

---

# Another Example

## WebClient

```java
@Bean
public WebClient webClient() {
    return WebClient.builder().build();
}
```

---

# Another Example

## ObjectMapper

```java
@Bean
public ObjectMapper objectMapper() {
    return new ObjectMapper();
}
```

---

# Dependency Injection Between Beans

Spring automatically injects dependencies.

---

Example:

```java
@Bean
public EmployeeRepository repository() {
    return new EmployeeRepository();
}
```

---

```java
@Bean
public EmployeeService service() {
    return new EmployeeService(repository());
}
```

Spring manages dependency relationship.

---

# Preferred Way

Method parameter injection.

```java
@Bean
public EmployeeService service(
        EmployeeRepository repository) {

    return new EmployeeService(repository);
}
```

Spring automatically injects:

```java
EmployeeRepository
```

bean.

---

# Bean Lifecycle

```java
@Bean
public EmployeeService service() {
    return new EmployeeService();
}
```

Lifecycle:

```text
Create Bean
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
Application Running
      │
      ▼
@PreDestroy
      │
      ▼
Bean Destroyed
```

---

# Bean Scope

Default scope:

```java
Singleton
```

One bean instance per Spring Container.

---

Example:

```java
@Bean
@Scope("singleton")
public EmployeeService service() {
    return new EmployeeService();
}
```

---

# Prototype Scope

```java
@Bean
@Scope("prototype")
public EmployeeService service() {
    return new EmployeeService();
}
```

Every request:

```java
getBean()
```

creates new object.

---

# Example

```java
EmployeeService s1 =
        context.getBean(EmployeeService.class);

EmployeeService s2 =
        context.getBean(EmployeeService.class);
```

---

Singleton:

```java
s1 == s2
```

Output:

```java
true
```

---

Prototype:

```java
s1 == s2
```

Output:

```java
false
```

---

# Bean Initialization Method

Spring can invoke custom initialization.

---

Example

```java
@Bean(initMethod = "init")
public EmployeeService service() {
    return new EmployeeService();
}
```

---

Class

```java
public class EmployeeService {

    public void init() {
        System.out.println("Initialized");
    }

}
```

Spring calls:

```java
init()
```

after bean creation.

---

# Bean Destruction Method

```java
@Bean(destroyMethod = "cleanup")
public EmployeeService service() {
    return new EmployeeService();
}
```

---

Class

```java
public class EmployeeService {

    public void cleanup() {
        System.out.println("Destroyed");
    }

}
```

Spring calls:

```java
cleanup()
```

during shutdown.

---

# Lazy Initialization

Default:

```java
Eager
```

Bean created during startup.

---

Example

```java
@Bean
@Lazy
public EmployeeService service() {
    return new EmployeeService();
}
```

Created only when first used.

---

# @Bean vs @Component

This is one of the most important interview questions.

---

## @Component

```java
@Component
public class EmployeeService {
}
```

Spring discovers bean automatically.

---

## @Bean

```java
@Bean
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

Spring creates bean through configuration method.

---

| @Component | @Bean |
|------------|--------|
| Class level | Method level |
| Automatic detection | Manual registration |
| Requires component scan | Requires configuration class |
| Best for own classes | Best for third-party classes |

---

# Real Example

Your own class:

```java
@Service
public class EmployeeService {
}
```

Use:

```java
@Service
```

---

Third-party class:

```java
RestTemplate
ObjectMapper
ExecutorService
KafkaTemplate
WebClient
```

Use:

```java
@Bean
```

---

# @Bean vs XML Bean

Old Spring:

```xml
<bean id="employeeService"
      class="com.company.EmployeeService"/>
```

Modern Spring:

```java
@Bean
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

Purpose is identical.

---

# @Bean With Conditional Creation

Very common in Spring Boot.

---

Example

```java
@Bean
@ConditionalOnMissingBean
public ObjectMapper objectMapper() {
    return new ObjectMapper();
}
```

Create bean only if another ObjectMapper does not already exist.

---

# @Bean and Spring Boot Auto Configuration

Most auto-configurations create beans using:

```java
@Bean
```

Example:

```java
@Configuration
public class JacksonAutoConfiguration {

    @Bean
    public ObjectMapper objectMapper() {
        return new ObjectMapper();
    }

}
```

Spring Boot heavily relies on:

```java
@Bean
```

internally.

---

# Can @Bean Return Interface Type?

Yes.

Recommended.

---

Example

```java
@Bean
public List<String> names() {
    return new ArrayList<>();
}
```

---

Example

```java
@Bean
public DataSource dataSource() {
    return new HikariDataSource();
}
```

Program to interface, not implementation.

---

# Can One Method Create Multiple Beans?

No.

One `@Bean` method returns one bean object.

---

Wrong:

```java
@Bean
public void createBeans() {
}
```

No bean created because return type is:

```java
void
```

---

Correct:

```java
@Bean
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

---

# Common Mistakes

## Mistake 1

```java
@Bean
private EmployeeService service() {
    return new EmployeeService();
}
```

Avoid private methods.

Use:

```java
public
```

---

## Mistake 2

```java
@Bean
void service() {
}
```

No bean created.

Must return object.

---

## Mistake 3

Forgetting:

```java
@Configuration
```

class.

---

## Mistake 4

Using `new` directly everywhere.

```java
EmployeeService service =
        new EmployeeService();
```

Instead let Spring manage dependencies.

---

# Common Interview Questions

## What is @Bean?

Used to register objects as Spring-managed beans.

---

## Where can @Bean be used?

On methods.

---

## What becomes the bean name?

By default:

```java
Method Name
```

---

Example:

```java
@Bean
public EmployeeService employeeService()
```

Bean name:

```java
employeeService
```

---

## Why use @Bean instead of @Component?

Useful when:

- Source code cannot be modified.
- Third-party libraries.
- Complex object creation logic.

---

## Can @Bean be used without @Configuration?

Yes.

Example:

```java
@Component
public class Config {

    @Bean
    public EmployeeService service() {
        return new EmployeeService();
    }

}
```

Bean is created.

However:

```java
No full CGLIB enhancement.
```

This is called Lite Configuration.

---

## Can @Bean return interface types?

Yes.

Recommended practice.

---

## What is default scope of @Bean?

```java
Singleton
```

---

## Can Spring inject parameters into @Bean methods?

Yes.

Example:

```java
@Bean
public EmployeeService service(
        EmployeeRepository repository) {

    return new EmployeeService(repository);
}
```

Spring resolves dependency automatically.

---

# Internal Flow

```text
@Configuration
       │
       ▼
Find @Bean Methods
       │
       ▼
Invoke Method
       │
       ▼
Receive Returned Object
       │
       ▼
Create Bean Definition
       │
       ▼
Register In IoC Container
       │
       ▼
Manage Lifecycle
```

---

# Real-World Significance

`@Bean` is one of the most heavily used annotations in enterprise Spring applications.

Common examples:

```java
RestTemplate
WebClient
ObjectMapper
ExecutorService
DataSource
KafkaTemplate
RabbitTemplate
SecurityFilterChain
PasswordEncoder
CacheManager
TaskExecutor
```

Most Spring Boot auto-configuration classes internally create objects using:

```java
@Bean
```

Whenever an object needs custom construction logic or belongs to a third-party library, `@Bean` is usually the preferred approach.

---

# Key Points To Remember

- Used to register Spring-managed beans.
- Applied on methods, not classes.
- Returned object becomes a bean.
- Default bean name is method name.
- Supports custom bean names.
- Supports dependency injection through method parameters.
- Default scope is Singleton.
- Supports init and destroy methods.
- Commonly used with `@Configuration`.
- Ideal for third-party library classes.
- Heavily used in Spring Boot auto-configuration.
- Modern replacement for XML `<bean>` definitions.