
## Definition

`@PropertySource` is used to load properties files into Spring's Environment.

It tells Spring:

> Load properties from the specified file and make them available for dependency injection, property resolution, and configuration binding.

By default, Spring Boot automatically loads:

```properties
application.properties
application.yml
```

However, if you want to load:

```properties
database.properties
mail.properties
jwt.properties
custom.properties
```

you can use:

```java
@PropertySource
```

---

# Syntax

```java
@Configuration
@PropertySource("classpath:custom.properties")
public class AppConfig {
}
```

---

# Why Do We Need @PropertySource?

Suppose we have:

```properties
application.properties
```

and

```properties
database.properties
```

Spring Boot automatically loads:

```properties
application.properties
```

but NOT:

```properties
database.properties
```

unless explicitly configured.

Solution:

```java
@PropertySource("classpath:database.properties")
```

---

# Basic Example

## database.properties

```properties
db.url=jdbc:mysql://localhost:3306/test
db.username=root
db.password=root
```

---

## Configuration Class

```java
@Configuration
@PropertySource("classpath:database.properties")
public class DatabaseConfig {

}
```

Now Spring can access:

```properties
db.url
db.username
db.password
```

---

# Accessing Properties Using @Value

## database.properties

```properties
db.url=jdbc:mysql://localhost:3306/test
```

---

## Configuration

```java
@Configuration
@PropertySource("classpath:database.properties")
public class DatabaseConfig {

    @Value("${db.url}")
    private String url;

}
```

Spring injects:

```java
jdbc:mysql://localhost:3306/test
```

into:

```java
url
```

---

# Complete Example

## custom.properties

```properties
app.name=Employee System
app.version=1.0
```

---

## Configuration

```java
@Configuration
@PropertySource("classpath:custom.properties")
public class AppConfig {

    @Value("${app.name}")
    private String appName;

    @Bean
    public String appName() {
        return appName;
    }

}
```

Output:

```java
Employee System
```

---

# Loading Multiple Property Files

```java
@Configuration
@PropertySource({
    "classpath:database.properties",
    "classpath:mail.properties",
    "classpath:jwt.properties"
})
public class AppConfig {

}
```

Spring loads all files.

---

# Using @ConfigurationProperties with @PropertySource

Very common usage.

---

## application.properties

Not used.

---

## custom.properties

```properties
app.name=Employee Management System
app.version=1.0
```

---

## Configuration Class

```java
@Configuration
@PropertySource("classpath:custom.properties")
@EnableConfigurationProperties(AppProperties.class)
public class AppConfig {

}
```

---

## Properties Class

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String name;
    private String version;

}
```

Spring binds values from:

```properties
custom.properties
```

---

# Property File Locations

## Classpath

Most common.

```java
@PropertySource("classpath:custom.properties")
```

File:

```text
src/main/resources/custom.properties
```

---

## Relative Path

```java
@PropertySource("file:config/custom.properties")
```

---

## Absolute Path

```java
@PropertySource(
    "file:/opt/application/config.properties"
)
```

---

## Environment Variables

Can also reference:

```java
@PropertySource(
    "file:${CONFIG_DIR}/config.properties"
)
```

Spring resolves:

```java
CONFIG_DIR
```

at runtime.

---

# Placeholder Resolution

Property file:

```properties
app.name=Employee System
app.admin=Sumit
```

---

Java:

```java
@Value("${app.name}")
private String appName;
```

Spring resolves:

```java
Employee System
```

---

# Default Values

Very important.

---

Property may not exist.

Example:

```java
@Value("${app.timeout:30}")
private Integer timeout;
```

If:

```properties
app.timeout
```

exists:

```java
Uses actual value
```

---

Otherwise:

```java
Uses 30
```

---

# Ignoring Missing Files

By default:

```java
@PropertySource("classpath:test.properties")
```

If file doesn't exist:

```java
Application Startup Fails
```

---

To avoid failure:

```java
@PropertySource(
    value = "classpath:test.properties",
    ignoreResourceNotFound = true
)
```

Application starts normally.

---

# PropertySource Order and Precedence

Very important interview topic.

Suppose:

## application.properties

```properties
app.name=Application File
```

---

## custom.properties

```properties
app.name=Custom File
```

---

Configuration:

```java
@PropertySource("classpath:custom.properties")
```

Which value wins?

Depends on property source order.

Spring follows precedence rules.

Generally:

```text
Command Line
↓
Environment Variables
↓
application.properties
↓
@PropertySource
↓
Default Values
```

Always remember:

```java
Higher precedence overrides lower precedence.
```

---

# @PropertySource vs application.properties

One of the most common interview questions.

---

## application.properties

Automatically loaded by Spring Boot.

No annotation required.

Example:

```properties
server.port=8080
```

Works automatically.

---

## @PropertySource

Used for custom property files.

Example:

```java
@PropertySource(
    "classpath:database.properties"
)
```

---

| Feature | application.properties | @PropertySource |
|----------|-----------------------|-----------------|
| Auto Loaded | Yes | No |
| Annotation Needed | No | Yes |
| Custom Files | No | Yes |
| Spring Boot Preferred | Yes | Sometimes |

---

# @PropertySource and YAML

Important limitation.

---

Works:

```java
@PropertySource("classpath:test.properties")
```

---

Does NOT directly support:

```java
@PropertySource("classpath:test.yml")
```

or

```java
@PropertySource("classpath:test.yaml")
```

---

Reason:

```java
@PropertySource
```

only supports standard `.properties` files by default.

---

For YAML:

Use:

```yaml
application.yml
```

or create a custom:

```java
PropertySourceFactory
```

---

# Real Enterprise Example

## jwt.properties

```properties
jwt.secret=my-secret-key
jwt.expiration=3600
```

---

Configuration

```java
@Configuration
@PropertySource("classpath:jwt.properties")
public class JwtConfig {

    @Value("${jwt.secret}")
    private String secret;

}
```

---

# Internal Working

Startup Flow:

```text
Application Start
        │
        ▼
@Configuration Class Loaded
        │
        ▼
Find @PropertySource
        │
        ▼
Load Properties File
        │
        ▼
Add To Environment
        │
        ▼
Resolve Placeholders
        │
        ▼
Inject Values
```

---

# Common Mistakes

## Mistake 1

Wrong path.

```java
@PropertySource("custom.properties")
```

Wrong.

---

Correct:

```java
@PropertySource(
    "classpath:custom.properties"
)
```

---

## Mistake 2

File not found.

```java
@PropertySource(
    "classpath:test.properties"
)
```

Missing file causes:

```java
FileNotFoundException
```

unless:

```java
ignoreResourceNotFound=true
```

---

## Mistake 3

Trying to load YAML.

```java
@PropertySource(
    "classpath:test.yml"
)
```

Does not work by default.

---

## Mistake 4

Using it for application.properties.

Unnecessary.

Spring Boot already loads:

```properties
application.properties
application.yml
```

automatically.

---

# Common Interview Questions

## What is @PropertySource?

Used to load external property files into Spring Environment.

---

## Why is it needed?

To load custom `.properties` files that Spring Boot does not automatically load.

---

## Can it load multiple files?

Yes.

```java
@PropertySource({
    "classpath:db.properties",
    "classpath:mail.properties"
})
```

---

## Can it load YAML files?

Not directly.

Only `.properties` files are supported by default.

---

## What happens if the file is missing?

Application startup fails unless:

```java
ignoreResourceNotFound=true
```

is used.

---

## Difference Between @PropertySource and @ConfigurationProperties?

| @PropertySource | @ConfigurationProperties |
|----------------|-------------------------|
| Loads property file | Binds values to object |
| Source provider | Binding mechanism |
| File level | Object level |

Often used together.

---

## Difference Between @PropertySource and @Value?

| @PropertySource | @Value |
|----------------|---------|
| Loads file | Reads individual value |
| Source configuration | Property injection |

---

# Real-World Significance

Although Spring Boot mostly relies on:

```properties
application.properties
application.yml
```

`@PropertySource` is still useful for:

- Legacy property files
- Module-specific configuration
- Third-party integrations
- Security/JWT settings
- Database-specific settings
- External configuration files

It provides fine-grained control over loading custom property sources into the Spring Environment.

---

# Key Points To Remember

- Loads custom `.properties` files.
- Adds properties to Spring Environment.
- Commonly used with `@Value`.
- Can be used with `@ConfigurationProperties`.
- Supports multiple files.
- Supports classpath and file system locations.
- Supports placeholder resolution.
- Supports optional files using `ignoreResourceNotFound`.
- Does NOT directly support YAML files.
- Not needed for `application.properties` or `application.yml`.
- Useful for custom and legacy configuration management.