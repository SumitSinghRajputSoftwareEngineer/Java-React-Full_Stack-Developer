
## Definition

`@ConfigurationProperties` is used to bind external configuration properties from:

- `application.properties`
- `application.yml`
- Environment Variables
- Command Line Arguments
- Kubernetes ConfigMaps
- Cloud Config Servers

directly to a Java POJO.

It tells Spring Boot:

> Read configuration values and map them to fields of this class automatically.

Instead of reading properties one by one using `@Value`, Spring Boot can bind an entire group of related properties into a single object.

---

# Why Do We Need @ConfigurationProperties?

Suppose we have:

```properties
app.name=Employee Management System
app.version=1.0
app.admin=Sumit
app.max-users=1000
```

Without `@ConfigurationProperties`:

```java
@Value("${app.name}")
private String name;

@Value("${app.version}")
private String version;

@Value("${app.admin}")
private String admin;

@Value("${app.max-users}")
private Integer maxUsers;
```

Imagine 50 properties.

Code becomes messy.

---

Using:

```java
@ConfigurationProperties(prefix = "app")
```

Spring automatically binds all properties into a single object.

---

# Basic Syntax

## application.properties

```properties
app.name=Employee Management System
app.version=1.0
app.admin=Sumit
```

---

## Configuration Class

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String name;
    private String version;
    private String admin;

    // getters and setters
}
```

---

Spring automatically maps:

```properties
app.name
```

to:

```java
private String name;
```

---

# Registering ConfigurationProperties Bean

There are multiple ways.

---

## Approach 1 (Most Common)

```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String name;
    private String version;

    // getters and setters
}
```

Spring discovers it through component scanning.

---

## Approach 2

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {

}
```

Enable:

```java
@Configuration
@EnableConfigurationProperties(AppProperties.class)
public class AppConfig {
}
```

---

## Approach 3 (Spring Boot Preferred)

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {

}
```

---

Main class:

```java
@ConfigurationPropertiesScan
@SpringBootApplication
public class Application {
}
```

Spring scans all `@ConfigurationProperties` classes automatically.

---

# Complete Example

## application.properties

```properties
app.name=Employee Management System
app.version=1.0
app.admin=Sumit
```

---

## Properties Class

```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String name;
    private String version;
    private String admin;

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

    public String getAdmin() {
        return admin;
    }

    public void setAdmin(String admin) {
        this.admin = admin;
    }
}
```

---

## Service

```java
@Service
public class AppService {

    @Autowired
    private AppProperties properties;

    public void print() {

        System.out.println(properties.getName());
        System.out.println(properties.getVersion());

    }

}
```

Output:

```text
Employee Management System
1.0
```

---

# Relaxed Binding

One of the biggest advantages.

Spring Boot supports multiple naming styles.

---

Property:

```properties
app.max-users=100
```

Java:

```java
private Integer maxUsers;
```

Works.

---

Property:

```properties
app.maxUsers=100
```

Works.

---

Property:

```properties
app.max_users=100
```

Works.

---

Property:

```properties
APP_MAX_USERS=100
```

Environment Variable.

Works.

---

Spring automatically converts them.

---

# Nested Properties

Very important interview topic.

---

## application.properties

```properties
app.database.url=jdbc:mysql://localhost:3306/test
app.database.username=root
app.database.password=root
```

---

## Java Class

```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private Database database;

    public static class Database {

        private String url;
        private String username;
        private String password;

        // getters setters
    }

    // getters setters
}
```

Spring automatically creates nested objects.

---

# YAML Example

## application.yml

```yaml
app:
  name: Employee System
  version: 1.0

  database:
    url: jdbc:mysql://localhost/test
    username: root
    password: root
```

---

Java:

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
}
```

Automatically mapped.

---

# List Binding

## application.yml

```yaml
app:
  roles:
    - ADMIN
    - USER
    - MANAGER
```

---

Java

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private List<String> roles;

}
```

Result:

```java
[ADMIN, USER, MANAGER]
```

---

# Map Binding

## application.yml

```yaml
app:
  departments:
    hr: Human Resource
    dev: Development
    qa: Testing
```

---

Java

```java
private Map<String,String> departments;
```

Result:

```java
{
 hr=Human Resource,
 dev=Development,
 qa=Testing
}
```

---

# Constructor Binding (Immutable Objects)

Very important in Spring Boot 3.

---

## application.properties

```properties
app.name=Employee System
app.version=1.0
```

---

## Immutable Class

```java
@ConfigurationProperties(prefix = "app")
public record AppProperties(
        String name,
        String version) {
}
```

Spring Boot automatically binds values.

---

Older versions used:

```java
@ConstructorBinding
```

Spring Boot 3 automatically supports records.

---

# Type Conversion

Spring automatically converts.

---

Property

```properties
app.timeout=30
```

Java

```java
private Integer timeout;
```

Automatically converted.

---

Property

```properties
app.enabled=true
```

Java

```java
private Boolean enabled;
```

Automatically converted.

---

Property

```properties
app.start-date=2025-01-01
```

Java

```java
private LocalDate startDate;
```

Automatically converted.

---

Property

```properties
app.salary=100000
```

Java

```java
private BigDecimal salary;
```

Automatically converted.

---

# Validation Support

Very important in production.

---

## Dependency

```xml
spring-boot-starter-validation
```

---

## Properties Class

```java
@Component
@ConfigurationProperties(prefix = "app")
@Validated
public class AppProperties {

    @NotBlank
    private String name;

    @Min(1)
    private Integer maxUsers;

}
```

---

Properties:

```properties
app.name=
app.max-users=0
```

Application startup fails.

---

Error:

```text
Binding validation errors
```

This is desirable because invalid configuration is detected early.

---

# @ConfigurationProperties vs @Value

One of the most common interview questions.

---

## @Value

```java
@Value("${app.name}")
private String name;
```

Good for:

```java
1 or 2 properties
```

---

## @ConfigurationProperties

```java
@ConfigurationProperties(prefix="app")
```

Good for:

```java
Large groups of properties
```

---

| Feature | @Value | @ConfigurationProperties |
|----------|---------|-------------------------|
| Single Property | Excellent | Overkill |
| Multiple Properties | Poor | Excellent |
| Type Safe | Limited | Yes |
| Nested Objects | No | Yes |
| List Binding | Difficult | Easy |
| Map Binding | Difficult | Easy |
| Validation | Limited | Excellent |
| Maintainability | Low | High |

---

# Example: Database Configuration

## application.properties

```properties
db.url=jdbc:mysql://localhost/test
db.username=root
db.password=root
db.pool-size=20
```

---

## Java

```java
@ConfigurationProperties(prefix = "db")
public class DatabaseProperties {

    private String url;
    private String username;
    private String password;
    private Integer poolSize;

}
```

All database settings available in one object.

---

# How Spring Boot Processes It Internally

Startup Flow:

```text
Application Start
        │
        ▼
Load application.properties
        │
        ▼
Find @ConfigurationProperties
        │
        ▼
Read Prefix
        │
        ▼
Match Properties
        │
        ▼
Convert Types
        │
        ▼
Bind Values
        │
        ▼
Validate
        │
        ▼
Create Bean
```

---

# Common Mistakes

## Mistake 1

```java
@ConfigurationProperties(prefix="app")
public class AppProperties {
}
```

Not registered as Spring Bean.

Result:

```java
No Bean Created
```

Need:

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

Missing getters/setters.

```java
private String name;
```

No getter/setter.

Binding may fail (except records).

---

## Mistake 3

Wrong Prefix

Properties:

```properties
app.name=Test
```

Class:

```java
@ConfigurationProperties(prefix="application")
```

Result:

```java
No Binding
```

---

# Common Interview Questions

## What is @ConfigurationProperties?

Used to bind external configuration properties to a Java object.

---

## Why is it preferred over @Value?

Because it is:

- Type-safe
- Supports nested objects
- Supports lists
- Supports maps
- Supports validation
- Easier to maintain

---

## How do we register a ConfigurationProperties class?

1. `@Component`
2. `@EnableConfigurationProperties`
3. `@ConfigurationPropertiesScan`

---

## What is relaxed binding?

Allows:

```properties
app.max-users
app.maxUsers
app_max_users
APP_MAX_USERS
```

to map to:

```java
maxUsers
```

---

## Can it bind Lists?

Yes.

```java
List<String>
```

---

## Can it bind Maps?

Yes.

```java
Map<String,String>
```

---

## Can it validate configuration?

Yes.

Using:

```java
@Validated
```

and Bean Validation annotations.

---

## Is it type-safe?

Yes.

Spring automatically converts property values into Java types.

---

# Real-World Significance

Almost every enterprise Spring Boot application uses `@ConfigurationProperties` for:

- Database settings
- Kafka settings
- Redis settings
- JWT settings
- Security settings
- Cloud settings
- External API settings
- Feature flags
- Application-level configuration

It centralizes configuration into strongly typed Java objects, making applications cleaner, safer, and easier to maintain.

---

# Key Points To Remember

- Binds external configuration to Java objects.
- Uses a prefix to group related properties.
- Supports properties and YAML files.
- Supports nested objects.
- Supports Lists and Maps.
- Supports relaxed binding.
- Supports automatic type conversion.
- Supports validation with `@Validated`.
- Preferred over `@Value` for large configurations.
- Must be registered as a Spring bean.
- Widely used in enterprise Spring Boot applications.
- One of the most important Spring Boot configuration annotations.