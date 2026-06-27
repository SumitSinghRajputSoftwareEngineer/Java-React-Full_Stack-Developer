
## Definition

`@Import` is used to import one or more Spring configuration classes, component classes, or custom import selectors into the current Spring configuration.

It tells Spring:

> Load and register beans from another configuration or component class as part of the current Application Context.

Think of it as the Java Configuration equivalent of:

```xml
<import resource="applicationContext.xml"/>
```

from traditional Spring XML configuration.

---

# Syntax

```java
@Configuration
@Import(DatabaseConfig.class)
public class AppConfig {

}
```

Spring loads:

```java
DatabaseConfig
```

along with:

```java
AppConfig
```

---

# Why Do We Need @Import?

Suppose we have multiple configurations:

```text
DatabaseConfig
SecurityConfig
CacheConfig
KafkaConfig
```

Instead of putting everything in one huge configuration class:

```java
@Configuration
public class AppConfig {
}
```

we can modularize the application.

```java
@Configuration
@Import({
    DatabaseConfig.class,
    SecurityConfig.class,
    CacheConfig.class
})
public class AppConfig {
}
```

This keeps configuration clean and maintainable.

---

# Basic Example

## DatabaseConfig

```java
@Configuration
public class DatabaseConfig {

    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }

}
```

---

## Main Configuration

```java
@Configuration
@Import(DatabaseConfig.class)
public class AppConfig {

}
```

Spring registers:

```java
DataSource
```

bean from:

```java
DatabaseConfig
```

---

# Multiple Configuration Classes

```java
@Configuration
@Import({
    DatabaseConfig.class,
    SecurityConfig.class,
    CacheConfig.class
})
public class AppConfig {

}
```

All beans from imported configurations become available.

---

# Real Example

## DatabaseConfig

```java
@Configuration
public class DatabaseConfig {

    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }

}
```

---

## SecurityConfig

```java
@Configuration
public class SecurityConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

}
```

---

## Main Configuration

```java
@Configuration
@Import({
    DatabaseConfig.class,
    SecurityConfig.class
})
public class AppConfig {

}
```

Spring registers:

```java
DataSource
PasswordEncoder
```

---

# What Happens Internally?

When Spring starts:

```java
@Configuration
@Import(DatabaseConfig.class)
public class AppConfig {
}
```

Spring:

### Step 1

Processes:

```java
AppConfig
```

---

### Step 2

Finds:

```java
@Import
```

---

### Step 3

Loads:

```java
DatabaseConfig
```

---

### Step 4

Processes its:

```java
@Bean
```

methods.

---

### Step 5

Registers all beans.

---

# @Import with Regular Classes

Many developers think it only works for configuration classes.

Not true.

---

Example:

```java
public class EmployeeService {

}
```

---

Import:

```java
@Configuration
@Import(EmployeeService.class)
public class AppConfig {

}
```

Spring registers:

```java
EmployeeService
```

as a bean.

---

Equivalent to:

```java
@Bean
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

---

# Example

```java
public class EmailService {

}
```

---

```java
@Configuration
@Import(EmailService.class)
public class AppConfig {

}
```

Now:

```java
@Autowired
private EmailService emailService;
```

works successfully.

---

# @Import vs @ComponentScan

One of the most important interview questions.

---

## @Import

Explicit registration.

```java
@Import(EmployeeService.class)
```

Spring loads exactly:

```java
EmployeeService
```

---

## @ComponentScan

Automatic discovery.

```java
@ComponentScan("com.company")
```

Spring scans all classes in package.

---

| @Import | @ComponentScan |
|----------|---------------|
| Explicit | Automatic |
| Specific classes | Entire packages |
| Faster | Broader |
| Controlled registration | Discovery based |

---

# Example

## Using @Import

```java
@Import(EmployeeService.class)
```

Only:

```java
EmployeeService
```

registered.

---

## Using Component Scan

```java
@ComponentScan("com.company")
```

Registers:

```java
EmployeeService
EmployeeRepository
EmployeeController
EmployeeUtil
```

and many more.

---

# @Import vs @Bean

Another common interview question.

---

## Using @Bean

```java
@Bean
public EmployeeService employeeService() {
    return new EmployeeService();
}
```

---

## Using @Import

```java
@Import(EmployeeService.class)
```

---

Difference:

### @Bean

Allows custom construction.

```java
return new EmployeeService(
        repository,
        cacheManager);
```

---

### @Import

Uses default constructor.

```java
new EmployeeService()
```

unless dependency injection occurs.

---

# Importing ConfigurationProperties Classes

Common in Spring Boot.

---

## Properties Class

```java
@ConfigurationProperties(prefix="app")
public class AppProperties {

}
```

---

## Configuration

```java
@Configuration
@Import(AppProperties.class)
public class AppConfig {

}
```

Spring registers:

```java
AppProperties
```

as a bean.

---

However, preferred approach is:

```java
@EnableConfigurationProperties
```

---

# @Import with ImportSelector

Advanced Spring topic.

---

Example:

```java
@Import(MyImportSelector.class)
```

---

Custom Selector

```java
public class MyImportSelector
        implements ImportSelector {

    @Override
    public String[] selectImports(
            AnnotationMetadata metadata) {

        return new String[]{
                DatabaseConfig.class.getName()
        };
    }

}
```

Spring dynamically decides what to import.

---

# Why Spring Boot Uses It?

Spring Boot auto-configuration heavily relies on:

```java
@Import
```

internally.

---

Example:

```java
@EnableAutoConfiguration
```

eventually imports hundreds of configuration classes.

Such as:

```java
WebMvcAutoConfiguration
DataSourceAutoConfiguration
SecurityAutoConfiguration
KafkaAutoConfiguration
```

---

# @Import with ImportBeanDefinitionRegistrar

Advanced topic.

---

Example

```java
@Import(MyRegistrar.class)
```

---

```java
public class MyRegistrar
        implements ImportBeanDefinitionRegistrar {

}
```

Allows programmatic bean registration.

Used in:

- Spring Data
- Spring Security
- Spring Cloud

---

# Real Enterprise Example

## DatabaseConfig

```java
@Configuration
public class DatabaseConfig {

}
```

---

## KafkaConfig

```java
@Configuration
public class KafkaConfig {

}
```

---

## SecurityConfig

```java
@Configuration
public class SecurityConfig {

}
```

---

## Main AppConfig

```java
@Configuration
@Import({
        DatabaseConfig.class,
        KafkaConfig.class,
        SecurityConfig.class
})
public class AppConfig {

}
```

All configurations become part of one application context.

---

# Internal Flow

```text
Application Start
        │
        ▼
Process Configuration Class
        │
        ▼
Find @Import
        │
        ▼
Load Imported Classes
        │
        ▼
Process Bean Definitions
        │
        ▼
Register Beans
        │
        ▼
Application Ready
```

---

# Common Mistakes

## Mistake 1

Using @Import when Component Scan already handles it.

Example:

```java
@Service
public class EmployeeService {
}
```

and

```java
@ComponentScan
```

already exists.

No need:

```java
@Import(EmployeeService.class)
```

---

## Mistake 2

Expecting package scanning.

```java
@Import("com.company.service")
```

Invalid.

`@Import` accepts:

```java
Class objects
```

not package names.

---

## Mistake 3

Importing duplicate configurations.

```java
@Import(DatabaseConfig.class)
```

and

```java
@ComponentScan
```

both discovering same beans.

May cause bean conflicts.

---

# Common Interview Questions

## What is @Import?

Used to import and register configuration classes or component classes into the Spring Application Context.

---

## Is it similar to XML import?

Yes.

Equivalent of:

```xml
<import resource="config.xml"/>
```

---

## Can it import multiple classes?

Yes.

```java
@Import({
    Config1.class,
    Config2.class
})
```

---

## Can it import non-configuration classes?

Yes.

Example:

```java
@Import(EmployeeService.class)
```

Registers bean.

---

## Difference Between @Import and @ComponentScan?

| @Import | @ComponentScan |
|----------|---------------|
| Explicit registration | Package scanning |
| Specific classes | All discovered classes |
| Controlled | Automatic |

---

## Why is it important in Spring Boot?

Because Spring Boot auto-configuration is largely built on top of:

```java
@Import
```

to bring configuration classes into the Application Context.

---

## Can @Import be used with ImportSelector?

Yes.

Allows dynamic configuration loading.

---

## Can @Import be used with ImportBeanDefinitionRegistrar?

Yes.

Allows programmatic bean registration.

---

# Real-World Significance

`@Import` is heavily used in:

- Modular applications
- Spring Boot auto-configuration
- Spring Security
- Spring Data JPA
- Spring Cloud
- Custom Framework Development
- Library/Starter Development

It allows developers to split configurations into independent modules and compose them together when needed.

---

# Key Points To Remember

- Used to import classes into Spring Context.
- Can import configuration classes.
- Can import regular component classes.
- Supports multiple imports.
- Alternative to XML `<import>`.
- Provides explicit bean registration.
- Different from package scanning.
- Supports `ImportSelector`.
- Supports `ImportBeanDefinitionRegistrar`.
- Used extensively by Spring Boot auto-configuration.
- Useful for modular and enterprise applications.
- One of the foundational annotations behind Spring Boot's internal architecture.