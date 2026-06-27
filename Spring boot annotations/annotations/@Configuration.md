
## Definition

`@Configuration` is used to declare a class as a source of Spring Bean definitions.

It tells Spring:

> This class contains bean configuration methods that should be processed by the Spring IoC Container.

Before Spring Boot, XML files were used for configuration.

Example:

```xml
<bean id="employeeService"
      class="com.company.EmployeeService"/>
```

With Java-based configuration:

```java
@Configuration
public class AppConfig {
}
```

Spring now uses Java code instead of XML to define beans.

---

# Syntax

```java
@Configuration
public class AppConfig {

}
```

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

Spring creates:

```java
EmployeeService
```

as a bean and stores it inside the IoC Container.

---

# Why Do We Need @Configuration?

Suppose a class is not annotated:

```java
public class AppConfig {

    @Bean
    public EmployeeService employeeService() {
        return new EmployeeService();
    }

}
```

Spring will NOT process:

```java
@Bean
```

methods.

Result:

```java
No Bean Created
```

To tell Spring:

> Process this class and create beans from its @Bean methods.

we use:

```java
@Configuration
```

---

# What Happens Internally?

When Spring starts:

```java
ApplicationContext context =
    new AnnotationConfigApplicationContext(AppConfig.class);
```

Spring sees:

```java
@Configuration
```

and performs:

### Step 1

Registers configuration class.

---

### Step 2

Scans all methods.

---

### Step 3

Finds:

```java
@Bean
```

methods.

---

### Step 4

Creates Bean Definitions.

---

### Step 5

Stores them in IoC Container.

---

### Step 6

Manages lifecycle and dependency injection.

---

# Example

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

Output:

```java
Bean Retrieved Successfully
```

---

# @Configuration + @Bean

Most common usage.

```java
@Configuration
public class AppConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

}
```

Spring registers:

```java
RestTemplate
```

as a bean.

Usage:

```java
@Autowired
private RestTemplate restTemplate;
```

---

# Real-World Example

Database Configuration

```java
@Configuration
public class DatabaseConfig {

    @Bean
    public DataSource dataSource() {

        DriverManagerDataSource ds =
                new DriverManagerDataSource();

        ds.setUrl("jdbc:mysql://localhost:3306/test");
        ds.setUsername("root");
        ds.setPassword("root");

        return ds;
    }

}
```

Spring manages the DataSource bean.

---

# Bean Creation Flow

```java
@Configuration
public class AppConfig {

    @Bean
    public EmployeeService employeeService() {
        return new EmployeeService();
    }

}
```

Flow:

```text
@Configuration
       │
       ▼
Process Configuration Class
       │
       ▼
Find @Bean Methods
       │
       ▼
Invoke Methods
       │
       ▼
Create Objects
       │
       ▼
Register As Beans
```

---

# @Configuration vs @Component

This is a very important interview question.

---

## @Component

```java
@Component
public class AppConfig {
}
```

Creates:

```java
AppConfig
```

as a bean.

---

## @Configuration

```java
@Configuration
public class AppConfig {
}
```

Creates:

```java
AppConfig
```

as a bean AND enables special bean-processing behavior.

---

# Example Showing Difference

```java
@Configuration
public class AppConfig {

    @Bean
    public Employee employee() {
        return new Employee();
    }

    @Bean
    public EmployeeManager manager() {
        return new EmployeeManager(employee());
    }

}
```

---

Spring guarantees:

```java
employee()
```

returns SAME singleton bean every time.

---

# Internal Enhancement

Spring creates a proxy subclass using:

```java
CGLIB
```

Example:

```java
AppConfig$$SpringCGLIB
```

instead of:

```java
new AppConfig()
```

This proxy intercepts:

```java
employee()
```

calls.

---

Result:

Only one singleton bean created.

---

# Example

```java
@Bean
public Employee employee() {
    return new Employee();
}
```

---

```java
@Bean
public EmployeeManager manager() {
    return new EmployeeManager(employee());
}
```

Without proxy:

```java
employee()
```

would create a new object every time.

---

With proxy:

```java
employee()
```

returns existing singleton bean.

---

# Full Mode vs Lite Mode

Spring supports two modes.

---

## Full Mode

```java
@Configuration
public class AppConfig {
}
```

Uses:

```java
CGLIB Proxy
```

Benefits:

```java
Singleton guarantee
Bean method interception
Dependency management
```

---

## Lite Mode

```java
@Component
public class AppConfig {
}
```

with:

```java
@Bean
```

methods.

Example:

```java
@Component
public class AppConfig {

    @Bean
    public Employee employee() {
        return new Employee();
    }

}
```

Bean created successfully.

BUT:

```java
No CGLIB enhancement.
```

No singleton method interception.

---

# proxyBeanMethods Attribute

Introduced for optimization.

Default:

```java
@Configuration(proxyBeanMethods = true)
```

Uses CGLIB proxy.

---

Example:

```java
@Configuration(proxyBeanMethods = false)
public class AppConfig {
}
```

No proxy created.

Faster startup.

Lower memory usage.

---

# When Should We Use proxyBeanMethods = false?

Use when:

```java
@Bean methods do not call each other.
```

Example:

```java
@Configuration(proxyBeanMethods = false)
public class AppConfig {

    @Bean
    public Employee employee() {
        return new Employee();
    }

    @Bean
    public Department department() {
        return new Department();
    }

}
```

Safe because:

```java
employee()
```

is never called from:

```java
department()
```

---

# Real Spring Boot Example

```java
@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

}
```

Injection:

```java
@Service
public class EmployeeService {

    @Autowired
    private RestTemplate restTemplate;

}
```

Spring injects managed bean.

---

# Can We Inject Dependencies Into Configuration Class?

Yes.

Example:

```java
@Configuration
public class AppConfig {

    @Autowired
    private Environment env;

}
```

or constructor injection:

```java
@Configuration
public class AppConfig {

    private final Environment env;

    public AppConfig(Environment env) {
        this.env = env;
    }

}
```

---

# Configuration Class Importing

Useful for modular applications.

---

## Config 1

```java
@Configuration
public class DatabaseConfig {
}
```

---

## Config 2

```java
@Configuration
@Import(DatabaseConfig.class)
public class AppConfig {
}
```

Spring loads both configurations.

---

# Common Mistakes

## Mistake 1

```java
public class AppConfig {

    @Bean
    public Employee employee() {
        return new Employee();
    }

}
```

Missing:

```java
@Configuration
```

Result:

```java
@Bean may not be processed.
```

---

## Mistake 2

Calling @Bean methods manually.

```java
Employee employee =
        appConfig.employee();
```

outside Spring context.

This bypasses container management.

---

## Mistake 3

Using @Configuration for every class.

Wrong:

```java
@Configuration
public class EmployeeService {
}
```

Use:

```java
@Service
```

instead.

---

# Interview Questions

## What is @Configuration?

Marks a class as a source of bean definitions for Spring IoC Container.

---

## Is @Configuration a bean itself?

Yes.

Configuration classes themselves are registered as Spring beans.

---

## Difference Between @Configuration and @Component?

| @Configuration | @Component |
|---------------|------------|
| Specialized component | Generic component |
| Supports CGLIB enhancement | No enhancement |
| Singleton bean guarantee | No guarantee |
| Intended for bean definitions | Intended for business classes |

---

## Can We Use @Bean Without @Configuration?

Yes.

Example:

```java
@Component
public class AppConfig {

    @Bean
    public Employee employee() {
        return new Employee();
    }

}
```

Bean gets created.

But:

```java
No CGLIB proxy enhancement.
```

This is called Lite Configuration.

---

## Why Does Spring Use CGLIB?

To ensure:

```java
@Bean
```

methods return managed singleton beans rather than creating new objects repeatedly.

---

## What Is proxyBeanMethods?

Controls whether Spring creates CGLIB proxies for configuration classes.

```java
true
```

Default.

```java
false
```

Optimized startup.

---

# Real-World Significance

`@Configuration` is heavily used for:

- Database configuration
- Security configuration
- Cache configuration
- Messaging configuration
- RestTemplate/WebClient configuration
- Third-party library integration
- Custom bean creation

Whenever you need to create and manage objects that are not directly annotated with:

```java
@Component
@Service
@Repository
```

you typically use:

```java
@Configuration + @Bean
```

---

# Key Points To Remember

- Marks a class as a configuration source.
- Replacement for XML-based configuration.
- Usually used with `@Bean`.
- Processed by Spring IoC Container.
- Creates bean definitions.
- Supports CGLIB proxy enhancement.
- Guarantees singleton behavior for inter-bean method calls.
- Is itself a Spring bean.
- Supports modular configuration via `@Import`.
- `proxyBeanMethods=false` improves startup performance when inter-bean calls are not required.
- One of the most important annotations in Spring Core and Spring Boot.