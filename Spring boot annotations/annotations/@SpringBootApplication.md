
## Definition

`@SpringBootApplication` is the main annotation used to bootstrap a Spring Boot application.

It is a convenience annotation that combines three important Spring annotations:

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
```

Instead of writing all three annotations separately, Spring Boot provides `@SpringBootApplication`.

---

# Syntax

```java
@SpringBootApplication
public class EmployeeApplication {

    public static void main(String[] args) {
        SpringApplication.run(EmployeeApplication.class, args);
    }

}
```

---

# What Happens Internally?

When Spring Boot starts:

1. Creates the Spring IoC Container (Application Context)
2. Performs Component Scanning
3. Registers Beans
4. Applies Auto Configuration
5. Loads Properties Files
6. Starts Embedded Server (Tomcat/Jetty/Undertow)
7. Makes the application ready to accept requests

---

# Meta Annotations Present

## 1. @SpringBootConfiguration

```java
@SpringBootConfiguration
```

Specialized version of:

```java
@Configuration
```

Indicates that this class contains bean definitions.

Example:

```java
@Bean
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

---

## 2. @EnableAutoConfiguration

```java
@EnableAutoConfiguration
```

Tells Spring Boot:

> Configure beans automatically based on dependencies available in the classpath.

Example:

If dependency exists:

```xml
spring-boot-starter-web
```

Spring Boot automatically configures:

```java
DispatcherServlet
Tomcat
Jackson ObjectMapper
Request Mapping Infrastructure
Error Controller
```

No manual configuration required.

---

## 3. @ComponentScan

```java
@ComponentScan
```

Scans packages for Spring-managed beans.

Example:

```java
com.company
    ├── EmployeeApplication
    ├── controller
    ├── service
    ├── repository
```

Spring automatically detects:

```java
@Controller
@Service
@Repository
@Component
@Configuration
@RestController
```

and creates beans for them.

---

# Internal Structure

Simplified source:

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited

@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
public @interface SpringBootApplication {
}
```

---

# Real Example

```java
@SpringBootApplication
public class EmployeeApplication {

    public static void main(String[] args) {
        SpringApplication.run(EmployeeApplication.class, args);
    }
}
```

Controller:

```java
@RestController
public class EmployeeController {

    @GetMapping("/employees")
    public String getEmployees() {
        return "Employees";
    }
}
```

No XML configuration.

No DispatcherServlet configuration.

No ComponentScan configuration.

Everything works automatically.

---

# Why Should Main Class Be At Root Package?

Recommended structure:

```java
com.company

    EmployeeApplication

    controller
    service
    repository
    config
```

Because:

```java
@ComponentScan
```

starts scanning from the package where the main class exists.

---

## Correct

```java
com.company.EmployeeApplication
```

Scans:

```java
com.company.*
```

---

## Wrong

```java
com.company.controller.EmployeeApplication
```

Will only scan:

```java
com.company.controller.*
```

and may miss:

```java
service
repository
config
```

Result:

```java
NoSuchBeanDefinitionException
```

---

# Common Interview Question

## Can We Use @SpringBootApplication Multiple Times?

Technically:

```java
Yes
```

Recommended:

```java
No
```

There should be only one entry point for the application.

---

# Can We Replace It?

Yes.

Instead of:

```java
@SpringBootApplication
```

You can write:

```java
@Configuration
@EnableAutoConfiguration
@ComponentScan
```

Example:

```java
@Configuration
@EnableAutoConfiguration
@ComponentScan
public class EmployeeApplication {
}
```

Behavior remains the same.

---

# Excluding Auto Configuration

Sometimes Spring Boot configures unwanted beans.

Example:

```java
@SpringBootApplication(
    exclude = {
        DataSourceAutoConfiguration.class
    }
)
```

Used when:

```java
Database dependency exists
```

but application doesn't require database.

---

# Excluding Multiple Configurations

```java
@SpringBootApplication(
    exclude = {
        DataSourceAutoConfiguration.class,
        SecurityAutoConfiguration.class
    }
)
```

---

# Auto Configuration Mechanism

Spring Boot checks:

```java
classpath
```

and decides configuration.

Example:

Dependency:

```xml
spring-boot-starter-data-jpa
```

Auto Configures:

```java
DataSource
EntityManager
TransactionManager
Hibernate
```

Dependency:

```xml
spring-boot-starter-security
```

Auto Configures:

```java
SecurityFilterChain
AuthenticationManager
PasswordEncoder
```

This behavior comes from:

```java
@EnableAutoConfiguration
```

---

# How Auto Configuration Works Internally?

Spring Boot reads:

```text
META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
```

Contains hundreds of auto-configuration classes.

Example:

```java
WebMvcAutoConfiguration
DataSourceAutoConfiguration
SecurityAutoConfiguration
JpaRepositoriesAutoConfiguration
```

Spring loads only those whose conditions match.

Example:

```java
@ConditionalOnClass
@ConditionalOnBean
@ConditionalOnProperty
```

---

# Common Errors

## Error 1

```text
No qualifying bean found
```

Reason:

Bean package outside component scan.

---

## Error 2

```text
Failed to configure DataSource
```

Reason:

JPA dependency exists but datasource properties missing.

Solution:

```java
@SpringBootApplication(
    exclude = DataSourceAutoConfiguration.class
)
```

or configure datasource properly.

---

## Error 3

```text
404 Not Found
```

Reason:

Controller package not scanned.

---

# Best Practices

## Keep Main Class At Root Package

```java
com.company.Application
```

---

## Keep Only One Main Class

Avoid:

```java
@SpringBootApplication
public class App1 {}

@SpringBootApplication
public class App2 {}
```

unless intentionally creating multiple applications.

---

## Use Exclude Sparingly

Avoid:

```java
exclude = { ... }
```

unless necessary.

---

## Prefer Convention Over Configuration

Allow Spring Boot to auto-configure whenever possible.

---

# Interview Questions

## What is @SpringBootApplication?

Combination of:

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
```

---

## Why is it used?

To bootstrap and configure a Spring Boot application automatically.

---

## What is the most important annotation inside it?

```java
@EnableAutoConfiguration
```

because it provides Spring Boot's automatic configuration capability.

---

## What happens if we remove @ComponentScan?

Spring will not discover:

```java
@Controller
@Service
@Repository
@Component
```

beans automatically.

---

## What happens if we remove @EnableAutoConfiguration?

Auto-configured beans such as:

```java
Tomcat
DataSource
Jackson
Spring MVC
```

will not be configured automatically.

---

## What happens if main class is not in root package?

Some beans may not be discovered during component scanning, causing:

```java
NoSuchBeanDefinitionException
```

---

# Key Points To Remember

- Most important Spring Boot annotation.
- Entry point of Spring Boot application.
- Combines 3 annotations:
  - `@SpringBootConfiguration`
  - `@EnableAutoConfiguration`
  - `@ComponentScan`
- Creates and bootstraps Application Context.
- Enables component scanning.
- Enables auto-configuration.
- Starts embedded server.
- Should be placed in root package.
- Supports excluding unwanted auto-configurations.
- Usually appears only once in an application.
- Foundation of every Spring Boot application.