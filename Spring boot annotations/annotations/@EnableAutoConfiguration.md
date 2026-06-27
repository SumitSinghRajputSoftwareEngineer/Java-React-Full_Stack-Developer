# @EnableAutoConfiguration

## Definition

`@EnableAutoConfiguration` is one of the most powerful features of Spring Boot.

It tells Spring Boot:

> Automatically configure the application based on the dependencies available in the classpath, existing beans, application properties, and environment settings.

Before Spring Boot, developers had to manually configure:

- DataSource
- Hibernate
- DispatcherServlet
- Jackson
- TransactionManager
- Security
- Embedded Server

With `@EnableAutoConfiguration`, Spring Boot does this automatically.

---

# Syntax

```java
@EnableAutoConfiguration
public class Application {
}
```

Usually, we don't use it directly because it is already included inside:

```java
@SpringBootApplication
```

```java
@SpringBootApplication
=
@SpringBootConfiguration
+ @EnableAutoConfiguration
+ @ComponentScan
```

---

# Why Was It Introduced?

## Traditional Spring

To configure Spring MVC:

```xml
<bean class="DispatcherServlet"/>
<bean class="ViewResolver"/>
<bean class="HandlerMapping"/>
<bean class="ObjectMapper"/>
```

To configure Hibernate:

```xml
<bean class="DataSource"/>
<bean class="EntityManagerFactory"/>
<bean class="TransactionManager"/>
```

Hundreds of lines of configuration.

---

## Spring Boot

Add dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

And:

```java
@SpringBootApplication
public class Application {
}
```

Everything is configured automatically.

---

# What Does It Actually Do?

When Spring Boot starts:

```java
SpringApplication.run(...)
```

it checks:

### Step 1

Available dependencies

Example:

```xml
spring-boot-starter-web
```

means:

```java
Spring MVC needed
Tomcat needed
Jackson needed
```

---

### Step 2

Application properties

Example:

```properties
server.port=9090
```

Spring configures Tomcat on port 9090.

---

### Step 3

Existing beans

Example:

```java
@Bean
public ObjectMapper objectMapper() {
    return new ObjectMapper();
}
```

Spring Boot sees custom bean already exists.

Therefore:

```java
Does NOT create default ObjectMapper.
```

---

### Step 4

Creates required beans automatically.

---

# Real Example

Dependency:

```xml
spring-boot-starter-web
```

Application:

```java
@SpringBootApplication
public class Application {
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

Without writing:

```java
DispatcherServlet
Tomcat
ObjectMapper
RequestMappingHandlerAdapter
ViewResolver
```

Application works.

Reason:

```java
@EnableAutoConfiguration
```

---

# How Auto Configuration Works Internally

This is one of the most important interview topics.

---

## Step 1

Spring Boot starts.

```java
SpringApplication.run()
```

---

## Step 2

Encounters:

```java
@EnableAutoConfiguration
```

---

## Step 3

Loads:

```text
META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
```

Present inside Spring Boot JARs.

---

## Step 4

File contains hundreds of auto-configuration classes.

Example:

```java
WebMvcAutoConfiguration
DataSourceAutoConfiguration
SecurityAutoConfiguration
JpaRepositoriesAutoConfiguration
JacksonAutoConfiguration
RedisAutoConfiguration
KafkaAutoConfiguration
```

---

## Step 5

Spring evaluates conditions.

Example:

```java
@ConditionalOnClass
@ConditionalOnBean
@ConditionalOnProperty
@ConditionalOnMissingBean
```

Only matching configurations are loaded.

---

# Example: Web Auto Configuration

Dependency:

```xml
spring-boot-starter-web
```

Spring finds:

```java
DispatcherServlet.class
```

Condition satisfied.

Loads:

```java
WebMvcAutoConfiguration
```

Automatically creates:

```java
DispatcherServlet
HandlerMapping
HandlerAdapter
MessageConverters
```

---

# Example: JPA Auto Configuration

Dependency:

```xml
spring-boot-starter-data-jpa
```

Spring loads:

```java
DataSourceAutoConfiguration
HibernateJpaAutoConfiguration
JpaRepositoriesAutoConfiguration
```

Creates:

```java
DataSource
EntityManagerFactory
TransactionManager
```

---

# Example: Security Auto Configuration

Dependency:

```xml
spring-boot-starter-security
```

Spring loads:

```java
SecurityAutoConfiguration
```

Automatically creates:

```java
SecurityFilterChain
AuthenticationManager
Default Login Page
```

Result:

```text
All endpoints become secured
```

without writing any security code.

---

# Important Conditional Annotations Used

## @ConditionalOnClass

Load configuration only if class exists.

Example:

```java
@ConditionalOnClass(DataSource.class)
```

If DataSource exists:

```java
Load DataSource configuration.
```

---

## @ConditionalOnMissingBean

Load bean only if bean does not exist.

Example:

```java
@ConditionalOnMissingBean(ObjectMapper.class)
```

Meaning:

```java
Create default ObjectMapper
only when user hasn't created one.
```

---

## @ConditionalOnBean

Load configuration only when another bean exists.

Example:

```java
@ConditionalOnBean(DataSource.class)
```

---

## @ConditionalOnProperty

Load configuration based on property.

Example:

```properties
feature.enabled=true
```

```java
@ConditionalOnProperty(
        name="feature.enabled",
        havingValue="true")
```

---

# Auto Configuration vs Component Scan

Many developers confuse these.

## @ComponentScan

Discovers:

```java
@Component
@Service
@Repository
@Controller
```

your application classes.

---

## @EnableAutoConfiguration

Creates Spring Boot infrastructure beans.

Example:

```java
Tomcat
DispatcherServlet
DataSource
Hibernate
Jackson
```

---

# Example

Application:

```java
@SpringBootApplication
public class Application {
}
```

Controller:

```java
@RestController
public class EmployeeController {
}
```

Controller discovered by:

```java
@ComponentScan
```

---

Tomcat created by:

```java
@EnableAutoConfiguration
```

---

# Excluding Auto Configuration

Sometimes auto configuration is unwanted.

---

## Example 1

Dependency exists:

```xml
spring-boot-starter-data-jpa
```

But application doesn't use database.

Error:

```text
Failed to configure DataSource
```

---

Solution:

```java
@EnableAutoConfiguration(
    exclude = DataSourceAutoConfiguration.class
)
```

or

```java
@SpringBootApplication(
    exclude = DataSourceAutoConfiguration.class
)
```

---

## Example 2

Disable Security Auto Configuration

```java
@SpringBootApplication(
    exclude = SecurityAutoConfiguration.class
)
```

---

# Exclude Multiple Configurations

```java
@SpringBootApplication(
    exclude = {
        DataSourceAutoConfiguration.class,
        SecurityAutoConfiguration.class
    }
)
```

---

# Auto Configuration Report

Useful for debugging.

Enable:

```properties
debug=true
```

Application startup logs show:

```text
Positive Matches
Negative Matches
Auto Configurations Applied
Auto Configurations Skipped
```

---

# Common Interview Questions

## How does Spring Boot know what to configure?

By checking:

```java
Dependencies
Classpath
Properties
Existing Beans
Environment
```

and evaluating conditional annotations.

---

## Which file contains auto configurations?

Spring Boot 3:

```text
META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
```

---

## What is the purpose of @ConditionalOnMissingBean?

Allows user-defined bean to override Spring Boot default bean.

---

## What happens if spring-boot-starter-web is added?

Spring Boot auto-configures:

```java
DispatcherServlet
Embedded Tomcat
Jackson
MVC Infrastructure
```

---

## What happens if @EnableAutoConfiguration is removed?

Application loses automatic configuration.

You must manually configure:

```java
Tomcat
DispatcherServlet
Jackson
DataSource
Hibernate
Security
```

---

## Difference Between @ComponentScan and @EnableAutoConfiguration?

| @ComponentScan | @EnableAutoConfiguration |
|---------------|--------------------------|
| Scans application beans | Creates framework beans |
| Finds @Service | Creates DataSource |
| Finds @Controller | Creates Tomcat |
| Finds @Repository | Creates DispatcherServlet |

---

# Internal Flow Diagram

```text
Application Start
        │
        ▼
@EnableAutoConfiguration
        │
        ▼
Read AutoConfiguration.imports
        │
        ▼
Load Auto Configuration Classes
        │
        ▼
Evaluate Conditions
        │
        ▼
Conditions Match?
        │
    Yes ▼
Create Beans
        │
        ▼
Application Ready
```

---

# Real-World Significance

Without Spring Boot:

```java
Configure Tomcat
Configure MVC
Configure Hibernate
Configure Security
Configure Jackson
Configure Transactions
Configure Validation
```

Manually.

With:

```java
@EnableAutoConfiguration
```

Spring Boot automatically configures everything required based on project dependencies.

This is the primary reason why Spring Boot applications can be built with very little configuration.

---

# Key Points To Remember

- Core annotation of Spring Boot.
- Included inside `@SpringBootApplication`.
- Enables automatic bean configuration.
- Works based on dependencies, properties, and environment.
- Reads auto-configuration classes from `AutoConfiguration.imports`.
- Uses conditional annotations extensively.
- Creates infrastructure beans automatically.
- Can be customized using `exclude`.
- User-defined beans override default auto-configured beans.
- One of the biggest reasons Spring Boot drastically reduces configuration effort.