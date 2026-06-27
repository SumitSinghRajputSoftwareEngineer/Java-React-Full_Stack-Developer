
## Definition

`@EnableConfigurationProperties` is used to register one or more `@ConfigurationProperties` classes as Spring Beans.

It tells Spring Boot:

> Create and manage the specified `@ConfigurationProperties` classes and bind external configuration values to them.

Without this annotation, a class annotated with:

```java
@ConfigurationProperties
```

is just a normal Java class unless it is registered as a Spring bean through some mechanism.

---

# Why Was It Introduced?

Suppose we have:

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String name;
    private String version;

}
```

Spring knows how to bind properties.

But:

```java
Who will create the bean?
```

Answer:

One option is:

```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {
}
```

Another option is:

```java
@EnableConfigurationProperties(AppProperties.class)
```

---

# Basic Syntax

```java
@Configuration
@EnableConfigurationProperties(AppProperties.class)
public class AppConfig {
}
```

---

# What Happens Internally?

When Spring Boot starts:

### Step 1

Finds:

```java
@EnableConfigurationProperties
```

---

### Step 2

Registers:

```java
AppProperties
```

as a Spring Bean.

---

### Step 3

Loads:

```properties
application.properties
```

or

```yaml
application.yml
```

---

### Step 4

Binds matching properties.

---

### Step 5

Stores the populated object in IoC Container.

---

# Complete Example

## application.properties

```properties
app.name=Employee Management System
app.version=1.0
```

---

## Properties Class

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String name;
    private String version;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getVersion() {
        return version;
    }

    public void setVersion(String version) {
        this.version = version;
    }
}
```

---

## Configuration Class

```java
@Configuration
@EnableConfigurationProperties(AppProperties.class)
public class AppConfig {

}
```

---

## Service

```java
@Service
public class EmployeeService {

    private final AppProperties properties;

    public EmployeeService(AppProperties properties) {
        this.properties = properties;
    }

}
```

Spring automatically injects:

```java
AppProperties
```

bean.

---

# Why Not Just Use @Component?

Many developers ask this.

---

## Approach 1

```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {
}
```

Works perfectly.

---

## Approach 2

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
}
```

and

```java
@EnableConfigurationProperties(AppProperties.class)
```

Also works.

---

# Difference

### Using @Component

```java
@Component
@ConfigurationProperties
```

Properties class becomes part of component scanning.

---

### Using @EnableConfigurationProperties

```java
@ConfigurationProperties
```

class remains a pure configuration model.

No component scanning required.

Many teams prefer this approach.

---

# Multiple Configuration Classes

You can register multiple classes.

---

## application.properties

```properties
db.url=jdbc:mysql://localhost/test

kafka.bootstrap-servers=localhost:9092
```

---

## Database Properties

```java
@ConfigurationProperties(prefix = "db")
public class DatabaseProperties {
}
```

---

## Kafka Properties

```java
@ConfigurationProperties(prefix = "kafka")
public class KafkaProperties {
}
```

---

## Register Both

```java
@Configuration
@EnableConfigurationProperties({
        DatabaseProperties.class,
        KafkaProperties.class
})
public class AppConfig {
}
```

Spring registers both beans.

---

# Real Enterprise Example

## JWT Configuration

```properties
jwt.secret=my-secret-key
jwt.expiry=3600
```

---

```java
@ConfigurationProperties(prefix = "jwt")
public class JwtProperties {

    private String secret;
    private Long expiry;

}
```

---

```java
@Configuration
@EnableConfigurationProperties(JwtProperties.class)
public class SecurityConfig {
}
```

---

Inject anywhere:

```java
@Service
public class JwtService {

    private final JwtProperties jwtProperties;

    public JwtService(JwtProperties jwtProperties) {
        this.jwtProperties = jwtProperties;
    }

}
```

---

# Validation Support

Works with:

```java
@Validated
```

---

Example

```java
@ConfigurationProperties(prefix = "app")
@Validated
public class AppProperties {

    @NotBlank
    private String name;

}
```

---

Properties

```properties
app.name=
```

Startup fails.

---

Reason:

```java
@EnableConfigurationProperties
```

supports validation processing.

---

# Configuration Metadata Generation

Very important in enterprise projects.

When used with:

```xml
spring-boot-configuration-processor
```

Spring Boot generates metadata.

Benefits:

- IDE Auto-completion
- Property Suggestions
- Validation Hints
- Documentation Support

---

Example:

Typing:

```properties
app.
```

IDE suggests:

```properties
app.name
app.version
```

---

# Spring Boot 2.2+ Alternative

Instead of:

```java
@EnableConfigurationProperties
```

Spring Boot introduced:

```java
@ConfigurationPropertiesScan
```

---

Example

```java
@ConfigurationPropertiesScan
@SpringBootApplication
public class Application {
}
```

Spring automatically finds all:

```java
@ConfigurationProperties
```

classes.

No need to register individually.

---

# When Should We Use It?

Useful when:

```java
@ConfigurationProperties
```

class should NOT be a component.

---

Example:

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
}
```

No:

```java
@Component
```

required.

Cleaner design.

---

# Internal Working

Startup Flow:

```text
Application Start
        │
        ▼
@EnableConfigurationProperties
        │
        ▼
Register ConfigurationProperties Classes
        │
        ▼
Create Bean Definitions
        │
        ▼
Load application.properties
        │
        ▼
Bind Values
        │
        ▼
Validate
        │
        ▼
Store Bean In IoC Container
```

---

# Relationship With @ConfigurationProperties

Think of it this way:

```java
@ConfigurationProperties
```

says:

> How to bind properties.

---

```java
@EnableConfigurationProperties
```

says:

> Create and register the bean.

---

Both work together.

---

# Example

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
}
```

Only tells Spring:

```java
How to bind.
```

---

```java
@EnableConfigurationProperties(AppProperties.class)
```

Tells Spring:

```java
Create bean and perform binding.
```

---

# Common Mistakes

## Mistake 1

```java
@ConfigurationProperties(prefix="app")
public class AppProperties {
}
```

No registration mechanism.

Result:

```java
No Bean Created
```

---

Need one of:

```java
@Component
```

or

```java
@EnableConfigurationProperties
```

or

```java
@ConfigurationPropertiesScan
```

---

## Mistake 2

Using both unnecessarily.

```java
@Component
@ConfigurationProperties(prefix="app")
```

and

```java
@EnableConfigurationProperties(AppProperties.class)
```

Redundant.

Choose one approach.

---

## Mistake 3

Wrong Prefix

```properties
app.name=Test
```

```java
@ConfigurationProperties(prefix="application")
```

No binding occurs.

---

# @EnableConfigurationProperties vs @ConfigurationPropertiesScan

Very common interview question.

---

## @EnableConfigurationProperties

Explicit registration.

```java
@EnableConfigurationProperties(
        AppProperties.class)
```

---

Pros:

```java
Explicit
Easy to understand
Controlled registration
```

---

Cons:

```java
Need to register each class manually
```

---

## @ConfigurationPropertiesScan

Automatic scanning.

```java
@ConfigurationPropertiesScan
```

---

Pros:

```java
Automatic discovery
Less boilerplate
```

---

Cons:

```java
Less explicit
```

---

# Interview Questions

## What is @EnableConfigurationProperties?

Registers and enables `@ConfigurationProperties` classes as Spring Beans.

---

## Is it mandatory?

No.

Alternative options:

```java
@Component
```

or

```java
@ConfigurationPropertiesScan
```

---

## Why use it?

Keeps configuration classes free from component annotations and allows explicit registration.

---

## Can multiple classes be registered?

Yes.

```java
@EnableConfigurationProperties({
    DbProperties.class,
    KafkaProperties.class
})
```

---

## Difference Between @ConfigurationProperties and @EnableConfigurationProperties?

| @ConfigurationProperties | @EnableConfigurationProperties |
|-------------------------|--------------------------------|
| Defines binding rules | Registers bean |
| Maps properties | Creates bean |
| Specifies prefix | Enables processing |

---

## Which is preferred in Spring Boot 3?

Most projects use:

```java
@ConfigurationPropertiesScan
```

for automatic discovery.

However, `@EnableConfigurationProperties` is still heavily used in:

- Auto-configuration modules
- Spring Boot starters
- Library development

---

# Real-World Significance

Spring Boot itself uses `@EnableConfigurationProperties` extensively.

Examples include:

- DataSource configuration
- Kafka configuration
- Redis configuration
- RabbitMQ configuration
- Mail configuration
- Security configuration
- Actuator configuration

Almost every Spring Boot starter internally registers configuration classes using:

```java
@EnableConfigurationProperties
```

---

# Key Points To Remember

- Registers `@ConfigurationProperties` classes as Spring Beans.
- Works together with `@ConfigurationProperties`.
- Creates and binds configuration objects.
- Alternative to using `@Component`.
- Supports multiple configuration classes.
- Supports validation.
- Used heavily in Spring Boot auto-configuration.
- Common in custom starters and libraries.
- Can be replaced by `@ConfigurationPropertiesScan` in many applications.
- One of the most important configuration annotations in Spring Boot.