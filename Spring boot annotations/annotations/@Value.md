
## Definition

`@Value` is a Spring annotation used to inject values into Spring-managed beans.

It tells Spring:

> Read a value from a property file, environment variable, system property, SpEL expression, or literal value and inject it into a field, constructor parameter, or method parameter.

Package:

```java
import org.springframework.beans.factory.annotation.Value;
```

---

# Why Do We Need @Value?

Applications often contain configurable values:

```properties
server.port=8080
app.name=Employee Management System
database.url=jdbc:mysql://localhost:3306/employees
```

Instead of hardcoding:

```java
private String appName = "Employee Management System";
```

we can externalize configuration:

```java
@Value("${app.name}")
private String appName;
```

This makes applications:

- Configurable
- Environment Independent
- Easier to Maintain
- Production Ready

---

# Basic Syntax

```java
@Value("${property.name}")
private String value;
```

---

Example:

```properties
app.name=Spring Boot Demo
```

---

```java
@Component
public class AppInfo {

    @Value("${app.name}")
    private String appName;

}
```

---

Injected Value:

```java
Spring Boot Demo
```

---

# What Happens Internally?

Suppose:

```java
@Value("${app.name}")
private String appName;
```

Spring:

### Step 1

Loads:

```properties
application.properties
```

---

### Step 2

Finds property:

```properties
app.name=Spring Boot Demo
```

---

### Step 3

Resolves placeholder.

---

### Step 4

Injects value into field.

---

Flow:

```text
application.properties
        │
        ▼
Read Property
        │
        ▼
Resolve Placeholder
        │
        ▼
Inject Value
```

---

# Reading From application.properties

## application.properties

```properties
app.name=Employee Portal
```

---

## Bean

```java
@Component
public class AppInfo {

    @Value("${app.name}")
    private String appName;

}
```

---

Result:

```java
appName = "Employee Portal"
```

---

# Reading From application.yml

## application.yml

```yaml
app:
  name: Employee Portal
```

---

## Bean

```java
@Value("${app.name}")
private String appName;
```

---

Result:

```java
Employee Portal
```

---

# Injecting Primitive Types

---

## Integer

```properties
app.timeout=30
```

---

```java
@Value("${app.timeout}")
private int timeout;
```

---

Result:

```java
30
```

---

## Boolean

```properties
feature.enabled=true
```

---

```java
@Value("${feature.enabled}")
private boolean enabled;
```

---

Result:

```java
true
```

---

## Double

```properties
tax.rate=18.5
```

---

```java
@Value("${tax.rate}")
private double taxRate;
```

---

Result:

```java
18.5
```

---

# Default Values

Very important.

---

If property doesn't exist:

```java
@Value("${app.name}")
```

application startup fails.

---

Solution:

```java
@Value("${app.name:DefaultApp}")
```

---

Meaning:

```text
Use property value
OR
DefaultApp if missing
```

---

Example:

```java
@Value("${server.port:8080}")
private int port;
```

---

# Literal Values

You can inject direct values.

---

```java
@Value("Hello Spring")
private String message;
```

---

Result:

```java
Hello Spring
```

---

# Constructor Injection

Recommended approach.

---

```java
@Component
public class AppInfo {

    private final String appName;

    public AppInfo(
        @Value("${app.name}")
        String appName) {

        this.appName = appName;
    }

}
```

---

Benefits:

- Immutable fields
- Easier testing
- Better design

---

# Setter Injection

---

```java
private String appName;

@Value("${app.name}")
public void setAppName(
        String appName) {

    this.appName = appName;
}
```

---

Works perfectly.

---

# Environment Variables

Very important for production deployments.

---

Operating System:

```bash
export DB_HOST=localhost
```

---

Spring:

```java
@Value("${DB_HOST}")
private String host;
```

---

Injected:

```java
localhost
```

---

# System Properties

---

Run Application:

```bash
java -Dapp.name=SpringBootDemo app.jar
```

---

Read:

```java
@Value("${app.name}")
private String appName;
```

---

Result:

```java
SpringBootDemo
```

---

# Spring Expression Language (SpEL)

One of the most powerful features.

---

```java
@Value("#{10 + 20}")
private int result;
```

---

Result:

```java
30
```

---

# Calling Methods

```java
@Value("#{T(java.lang.Math).sqrt(16)}")
private double value;
```

---

Result:

```java
4.0
```

---

# Reading Bean Properties

Suppose:

```java
@Component
public class AppInfo {

    public String getName() {

        return "Spring";

    }

}
```

---

```java
@Value("#{appInfo.name}")
private String name;
```

---

Result:

```java
Spring
```

---

# Injecting Collections

---

Properties:

```properties
roles=ADMIN,USER,MANAGER
```

---

Bean:

```java
@Value("#{'${roles}'.split(',')}")
private List<String> roles;
```

---

Result:

```java
[ADMIN, USER, MANAGER]
```

---

# Injecting Arrays

---

```java
@Value("${roles}".split(","))
```

❌ Invalid

---

Correct:

```java
@Value("#{'${roles}'.split(',')}")
private String[] roles;
```

---

Result:

```java
["ADMIN","USER","MANAGER"]
```

---

# Reading Map Values

---

Property:

```properties
country=India
```

---

```java
@Value("${country}")
private String country;
```

---

Simple key-value injection.

---

# @Value with Bean Methods

---

```java
@Bean
public EmployeeService service(
        @Value("${app.name}")
        String appName) {

    return new EmployeeService(appName);

}
```

---

Spring injects property before bean creation.

---

# Using @Value in Configuration Classes

---

```java
@Configuration
public class AppConfig {

    @Value("${app.name}")
    private String appName;

}
```

---

Works normally.

---

# @Value vs @ConfigurationProperties

Most important interview question.

---

## @Value

Good for:

```text
Few Properties
```

---

Example:

```java
@Value("${app.name}")
private String appName;
```

---

## @ConfigurationProperties

Good for:

```text
Large Groups of Properties
```

---

Properties:

```properties
app.name=Demo
app.version=1.0
app.author=Sumit
```

---

```java
@ConfigurationProperties(prefix="app")
public class AppProperties {

    private String name;
    private String version;
    private String author;

}
```

---

Much cleaner.

---

Comparison:

| Feature | @Value | @ConfigurationProperties |
|----------|---------|--------------------------|
| Single Property | Excellent | Overkill |
| Many Properties | Poor | Excellent |
| Type Safety | Limited | Strong |
| Validation | Difficult | Easy |
| Maintainability | Lower | Higher |

---

# @Value vs Environment

---

## @Value

```java
@Value("${app.name}")
```

---

## Environment

```java
@Autowired
private Environment env;
```

---

```java
env.getProperty("app.name");
```

---

Comparison:

| @Value | Environment |
|----------|------------|
| Declarative | Programmatic |
| Cleaner | More Flexible |
| Fixed Value | Dynamic Lookup |

---

# Common Mistakes

## Mistake 1

Missing Property

---

```java
@Value("${unknown}")
```

No property exists.

---

Result:

```text
BeanCreationException
```

or

```text
Could not resolve placeholder
```

---

# Mistake 2

Forgetting Default Value

---

```java
@Value("${app.name}")
```

Startup fails if missing.

---

Better:

```java
@Value("${app.name:DefaultApp}")
```

---

# Mistake 3

Using @Value for Large Configurations

Wrong:

```java
@Value("${db.url}")
@Value("${db.username}")
@Value("${db.password}")
@Value("${db.driver}")
@Value("${db.poolsize}")
```

---

Use:

```java
@ConfigurationProperties
```

instead.

---

# Mistake 4

Trying to Use @Value in Non-Spring Objects

---

```java
public class Test {

    @Value("${app.name}")
    private String name;

}
```

---

Not managed by Spring.

Injection fails.

---

Must be inside:

```java
@Component
@Service
@Repository
@Controller
@Configuration
@Bean
```

managed objects.

---

# Common Interview Questions

## What is @Value?

Used to inject configuration values into Spring beans.

---

## Which property sources can it read from?

- application.properties
- application.yml
- Environment Variables
- System Properties
- SpEL Expressions

---

## Can @Value inject primitive types?

Yes.

Examples:

```java
int
long
double
boolean
String
```

---

## What happens if property is missing?

Application startup fails unless a default value is provided.

---

## How do you provide a default value?

```java
@Value("${app.name:DefaultApp}")
```

---

## Difference Between @Value and @ConfigurationProperties?

`@Value`

```text
Single Property Injection
```

---

`@ConfigurationProperties`

```text
Bulk Configuration Binding
```

---

## Can @Value use SpEL?

Yes.

Example:

```java
@Value("#{10 + 20}")
```

---

# Real-World Significance

Almost every Spring Boot application uses:

```java
@Value
```

for:

- Feature Flags
- API Keys
- Server Configuration
- Database Settings
- Application Metadata
- Timeouts
- Retry Counts
- External Service URLs

Examples:

```java
@Value("${jwt.secret}")
```

```java
@Value("${payment.api.url}")
```

```java
@Value("${cache.timeout}")
```

---

# Internal Working

```text
Application Starts
        │
        ▼
Load Property Sources
        │
        ▼
Find @Value
        │
        ▼
Resolve Placeholder
        │
        ▼
Convert Data Type
        │
        ▼
Inject Value
```

---

# Key Points To Remember

- Used to inject configuration values.
- Reads from properties, YAML, environment variables, system properties, and SpEL.
- Supports primitive types and objects.
- Supports default values using `:`.
- Supports constructor, field, and setter injection.
- Can evaluate SpEL expressions.
- Requires Spring-managed beans.
- Best for a small number of properties.
- For large configurations prefer `@ConfigurationProperties`.
- One of the most frequently used annotations in Spring Boot applications.