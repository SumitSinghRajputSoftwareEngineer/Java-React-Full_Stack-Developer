
## Definition

`@ComponentScan` tells Spring Framework:

> Scan the specified packages for Spring-managed components (beans) and register them in the Spring IoC Container.

Spring automatically discovers classes annotated with:

```java
@Component
@Service
@Repository
@Controller
@RestController
@Configuration
```

and creates beans for them.

Without `@ComponentScan`, Spring would not know where to find your application classes.

---

# Syntax

## Default Scan

```java
@ComponentScan
public class AppConfig {
}
```

---

## Scan Specific Packages

```java
@ComponentScan("com.company")
public class AppConfig {
}
```

---

## Scan Multiple Packages

```java
@ComponentScan({
    "com.company.service",
    "com.company.repository"
})
public class AppConfig {
}
```

---

## Using basePackages

```java
@ComponentScan(
    basePackages = {
        "com.company.service",
        "com.company.repository"
    }
)
```

---

## Using basePackageClasses (Type Safe)

```java
@ComponentScan(
    basePackageClasses = {
        EmployeeService.class,
        EmployeeRepository.class
    }
)
```

Recommended because package names are validated at compile time.

---

# Why Do We Need @ComponentScan?

Suppose we have:

```java
@Service
public class EmployeeService {
}
```

and

```java
@RestController
public class EmployeeController {
}
```

How does Spring know these classes exist?

Answer:

```java
@ComponentScan
```

scans packages, finds these annotations, and creates beans.

---

# What Happens Internally?

When Spring starts:

```java
ApplicationContext context =
        new AnnotationConfigApplicationContext();
```

Spring encounters:

```java
@ComponentScan
```

Then:

### Step 1

Identify package(s) to scan.

---

### Step 2

Scan all classes inside packages.

---

### Step 3

Check for stereotype annotations.

```java
@Component
@Service
@Repository
@Controller
@RestController
@Configuration
```

---

### Step 4

Create Bean Definitions.

---

### Step 5

Register Bean Definitions in IoC Container.

---

### Step 6

Instantiate beans when required.

---

# Real Example

## Service

```java
@Service
public class EmployeeService {

    public String getEmployee() {
        return "John";
    }

}
```

---

## Controller

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

---

## Main Class

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }

}
```

Because:

```java
@SpringBootApplication
```

contains:

```java
@ComponentScan
```

both beans are discovered automatically.

---

# Package Scanning Rules

Suppose:

```text
com.company

 ├── Application
 ├── controller
 ├── service
 └── repository
```

Application class:

```java
@SpringBootApplication
public class Application {
}
```

Spring scans:

```text
com.company
com.company.controller
com.company.service
com.company.repository
```

All beans discovered.

---

# Why Main Class Should Be At Root Package?

Because scanning starts from the package where:

```java
@SpringBootApplication
```

exists.

---

## Recommended Structure

```text
com.company

 ├── Application
 ├── controller
 ├── service
 ├── repository
 └── config
```

Everything gets scanned.

---

## Bad Structure

```text
com.company

 ├── controller
 │      └── Application
 ├── service
 └── repository
```

Now Spring scans:

```text
com.company.controller
```

only.

Beans inside:

```text
service
repository
```

may not be discovered.

---

Result:

```text
NoSuchBeanDefinitionException
```

---

# Stereotype Annotations Discovered

## @Component

```java
@Component
public class Utility {
}
```

Bean created.

---

## @Service

```java
@Service
public class EmployeeService {
}
```

Bean created.

---

## @Repository

```java
@Repository
public class EmployeeRepository {
}
```

Bean created.

---

## @Controller

```java
@Controller
public class EmployeeController {
}
```

Bean created.

---

## @RestController

```java
@RestController
public class EmployeeController {
}
```

Bean created.

---

## @Configuration

```java
@Configuration
public class AppConfig {
}
```

Bean created.

---

# What Classes Are NOT Scanned?

Normal class:

```java
public class Employee {
}
```

No stereotype annotation.

Result:

```java
Not registered as Bean.
```

---

# Example

```java
public class EmployeeService {
}
```

Injection:

```java
@Autowired
private EmployeeService service;
```

Error:

```text
No qualifying bean found
```

because:

```java
EmployeeService
```

is not a Spring bean.

---

# @ComponentScan Filters

Allows controlling what gets scanned.

---

## Include Filter

```java
@ComponentScan(
    includeFilters = @Filter(
        type = FilterType.ANNOTATION,
        classes = Service.class
    )
)
```

Only services scanned.

---

## Exclude Filter

```java
@ComponentScan(
    excludeFilters = @Filter(
        type = FilterType.ANNOTATION,
        classes = Controller.class
    )
)
```

Controllers ignored.

---

# Example

```java
@ComponentScan(
    excludeFilters =
        @Filter(
            type = FilterType.ASSIGNABLE_TYPE,
            classes = EmployeeService.class
        )
)
```

EmployeeService bean not created.

---

# Bean Naming During Component Scan

Default Bean Name:

```java
@Service
public class EmployeeService {
}
```

Bean name:

```java
employeeService
```

(first letter lowercase)

---

Custom Bean Name:

```java
@Service("employeeBean")
public class EmployeeService {
}
```

Bean name:

```java
employeeBean
```

---

# Component Scan vs Bean Annotation

## Using Component Scan

```java
@Service
public class EmployeeService {
}
```

Bean automatically created.

---

## Using @Bean

```java
@Configuration
public class Config {

    @Bean
    public EmployeeService employeeService() {
        return new EmployeeService();
    }

}
```

Bean manually registered.

---

# Component Scan vs Auto Configuration

This is a favorite interview question.

## @ComponentScan

Discovers application beans.

Example:

```java
EmployeeService
EmployeeController
EmployeeRepository
```

---

## @EnableAutoConfiguration

Creates framework beans.

Example:

```java
DispatcherServlet
Tomcat
DataSource
ObjectMapper
Hibernate
```

---

| @ComponentScan | @EnableAutoConfiguration |
|---------------|--------------------------|
| Finds application classes | Creates framework classes |
| Scans packages | Reads auto-configurations |
| Discovers @Service | Creates Tomcat |
| Discovers @Controller | Creates DataSource |
| Discovers @Repository | Creates DispatcherServlet |

---

# Common Errors

## Error 1

```text
No qualifying bean found
```

Reason:

Package not scanned.

---

## Error 2

```text
UnsatisfiedDependencyException
```

Reason:

Required bean not discovered.

---

## Error 3

```text
404 Not Found
```

Reason:

Controller package not scanned.

---

# Debugging Component Scan

Enable:

```properties
logging.level.org.springframework=DEBUG
```

or

```properties
debug=true
```

Startup logs show discovered beans.

---

# Interview Questions

## What is @ComponentScan?

Scans packages for Spring-managed components and registers them as beans.

---

## Which annotations are discovered?

```java
@Component
@Service
@Repository
@Controller
@RestController
@Configuration
```

---

## What package does Spring scan by default?

The package containing:

```java
@SpringBootApplication
```

and all its sub-packages.

---

## What happens if a bean is outside the scanned package?

Spring won't discover it.

Result:

```text
NoSuchBeanDefinitionException
```

---

## Difference Between @Component and @ComponentScan?

### @Component

Marks a class as a bean candidate.

```java
@Component
public class EmployeeService {
}
```

---

### @ComponentScan

Searches for those bean candidates.

```java
@ComponentScan
```

Without scanning:

```java
@Component
```

alone is useless.

---

## Which is better?

```java
@ComponentScan(basePackageClasses = ...)
```

because it is type-safe and refactoring-friendly.

---

# Internal Flow

```text
Application Start
        │
        ▼
@ComponentScan
        │
        ▼
Find Packages
        │
        ▼
Scan Classes
        │
        ▼
Find Stereotype Annotations
        │
        ▼
Create Bean Definitions
        │
        ▼
Register Beans
        │
        ▼
Application Ready
```

---

# Real-World Significance

In a typical Spring Boot application:

```text
Controller Layer
Service Layer
Repository Layer
Configuration Layer
```

contain hundreds of classes.

Manually registering every bean would be impossible.

`@ComponentScan` automates bean discovery and registration, making dependency injection and application wiring possible.

Without `@ComponentScan`, most Spring Boot applications would not function.

---

# Key Points To Remember

- Used to discover Spring-managed components.
- Registers beans automatically in the IoC container.
- Scans current package and sub-packages by default.
- Included inside `@SpringBootApplication`.
- Detects stereotype annotations.
- Supports package filtering.
- Main class should be placed in root package.
- Different from `@EnableAutoConfiguration`.
- Essential for Dependency Injection.
- Foundation of Spring's bean discovery mechanism.