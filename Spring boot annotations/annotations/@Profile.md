
## Definition

`@Profile` is used to activate or deactivate Spring Beans, Configurations, Components, and Services based on the currently active environment profile.

It tells Spring:

> Register this bean only when specific profile(s) are active.

Profiles allow applications to have different configurations for different environments such as:

```text
Development (dev)
Testing (test)
Staging (stage)
Production (prod)
Local (local)
UAT
```

---

# Why Do We Need @Profile?

Consider a database configuration.

### Development Database

```properties
jdbc:mysql://localhost:3306/devdb
```

---

### Production Database

```properties
jdbc:mysql://prod-server:3306/proddb
```

We don't want:

```java
if(environment.equals("dev"))
```

checks everywhere.

Instead:

```java
@Profile("dev")
```

and

```java
@Profile("prod")
```

allow Spring to load the correct beans automatically.

---

# Basic Syntax

```java
@Profile("dev")
@Component
public class DevService {

}
```

This bean is created only when:

```text
dev
```

profile is active.

---

# Activating Profiles

## application.properties

```properties
spring.profiles.active=dev
```

---

Spring loads:

```java
@Profile("dev")
```

beans.

---

# Example

## Development Service

```java
@Service
@Profile("dev")
public class DevNotificationService
        implements NotificationService {

    @Override
    public void send() {
        System.out.println("Dev Notification");
    }
}
```

---

## Production Service

```java
@Service
@Profile("prod")
public class ProdNotificationService
        implements NotificationService {

    @Override
    public void send() {
        System.out.println("Production Notification");
    }
}
```

---

## application.properties

```properties
spring.profiles.active=prod
```

Output:

```text
Production Notification
```

Only:

```java
ProdNotificationService
```

is registered.

---

# What Happens Internally?

Suppose:

```java
@Service
@Profile("dev")
public class DevService {
}
```

and

```properties
spring.profiles.active=prod
```

Spring:

### Step 1

Scans bean definition.

---

### Step 2

Finds:

```java
@Profile("dev")
```

---

### Step 3

Checks active profiles.

```text
prod
```

---

### Step 4

Profile mismatch.

---

### Step 5

Bean not registered.

---

# @Profile on @Bean Methods

Very common.

---

```java
@Configuration
public class AppConfig {

    @Bean
    @Profile("dev")
    public DataSource devDataSource() {

        return new HikariDataSource();

    }

}
```

Bean created only in:

```text
dev
```

environment.

---

# Real Example

## Development Database

```java
@Bean
@Profile("dev")
public DataSource devDataSource() {

    HikariDataSource ds =
            new HikariDataSource();

    ds.setJdbcUrl(
        "jdbc:mysql://localhost/devdb"
    );

    return ds;
}
```

---

## Production Database

```java
@Bean
@Profile("prod")
public DataSource prodDataSource() {

    HikariDataSource ds =
            new HikariDataSource();

    ds.setJdbcUrl(
        "jdbc:mysql://prod-server/proddb"
    );

    return ds;
}
```

Spring loads the appropriate bean automatically.

---

# @Profile on Configuration Classes

Very common.

---

```java
@Configuration
@Profile("dev")
public class DevConfig {

}
```

Entire configuration activates only for:

```text
dev
```

profile.

---

Example:

```java
@Configuration
@Profile("prod")
public class ProductionConfig {

    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }

}
```

Loaded only in production.

---

# Multiple Profiles

## OR Condition

```java
@Profile({"dev","test"})
```

Means:

```text
dev OR test
```

---

Example

```java
@Service
@Profile({"dev","test"})
public class MockEmailService {

}
```

Active if:

```text
dev
```

or

```text
test
```

profile is active.

---

# Profile Expressions

Introduced in Spring 5.

Very important interview topic.

---

## AND

```java
@Profile("dev & cloud")
```

Both profiles must be active.

---

Example

```java
@Profile("dev & docker")
```

Requires:

```text
dev
docker
```

both active.

---

# OR

```java
@Profile("dev | test")
```

Either profile works.

---

# NOT

```java
@Profile("!prod")
```

Active in every environment except:

```text
prod
```

---

Example

```java
@Service
@Profile("!prod")
public class MockService {

}
```

Loaded everywhere except production.

---

# Activating Profiles

---

## application.properties

```properties
spring.profiles.active=dev
```

---

## application.yml

```yaml
spring:
  profiles:
    active: dev
```

---

## JVM Argument

```bash
-Dspring.profiles.active=prod
```

---

## Command Line

```bash
java -jar app.jar \
--spring.profiles.active=prod
```

---

## Environment Variable

```bash
SPRING_PROFILES_ACTIVE=prod
```

---

# Profile-Specific Property Files

One of the most important Spring Boot features.

---

## application.properties

Common properties.

---

## application-dev.properties

```properties
server.port=8081
```

---

## application-prod.properties

```properties
server.port=9090
```

---

Activate:

```properties
spring.profiles.active=dev
```

Spring loads:

```text
application.properties
application-dev.properties
```

---

Activate:

```properties
spring.profiles.active=prod
```

Spring loads:

```text
application.properties
application-prod.properties
```

---

# Real Enterprise Example

## Development

```properties
spring.datasource.url=
jdbc:mysql://localhost/devdb
```

---

## Production

```properties
spring.datasource.url=
jdbc:mysql://prod-server/proddb
```

---

Spring automatically selects proper file.

---

# Default Profile

If no profile is active:

```text
default
```

profile is used.

---

Example

```java
@Profile("default")
@Service
public class DefaultService {

}
```

Loaded when:

```properties
spring.profiles.active
```

is not specified.

---

# How Spring Boot Uses Profiles

Spring Boot internally uses profiles for:

- Security Configurations
- Cloud Configurations
- Actuator Configurations
- Database Configurations
- Environment-specific beans

---

# Internal Flow

```text
Application Start
        │
        ▼
Read Active Profiles
        │
        ▼
Scan Bean Definitions
        │
        ▼
Check @Profile Conditions
        │
        ▼
Match Profile?
      /   \
    YES    NO
     │      │
     ▼      ▼
Register   Ignore
 Bean       Bean
```

---

# @Profile vs @Conditional

Very common interview question.

---

## @Profile

Specialized conditional annotation.

```java
@Profile("prod")
```

Checks profile only.

---

## @Conditional

Generic condition mechanism.

```java
@Conditional(...)
```

Can check:

- OS
- Properties
- Classes
- Custom Logic

---

Example:

```java
@ConditionalOnProperty
```

is built on top of:

```java
@Conditional
```

---

# Common Mistakes

## Mistake 1

Forgetting to activate profile.

```java
@Profile("prod")
```

but:

```properties
spring.profiles.active=
```

No bean created.

---

## Mistake 2

Two implementations.

```java
@Service
@Profile("dev")
class DevService
```

---

```java
@Service
@Profile("prod")
class ProdService
```

---

No active profile.

Result:

```text
No qualifying bean found
```

---

## Mistake 3

Wrong profile name.

```java
@Profile("production")
```

Active:

```properties
spring.profiles.active=prod
```

No match.

---

# Common Interview Questions

## What is @Profile?

Used to load beans conditionally based on active Spring profiles.

---

## Why use profiles?

To support different environments:

```text
dev
test
stage
prod
```

---

## Can @Profile be used on methods?

Yes.

```java
@Bean
@Profile("dev")
```

---

## Can @Profile be used on configuration classes?

Yes.

```java
@Configuration
@Profile("prod")
```

---

## What is the default profile?

```text
default
```

---

## How do you activate a profile?

```properties
spring.profiles.active=prod
```

or JVM arguments, environment variables, command-line parameters.

---

## What does @Profile("!prod") mean?

Bean is loaded in all environments except production.

---

## What does @Profile({"dev","test"}) mean?

```text
dev OR test
```

---

# Real-World Significance

Almost every enterprise Spring Boot application uses profiles for:

- Database Configuration
- External APIs
- Security Settings
- Logging Configuration
- Cloud Deployments
- Docker Environments
- Kubernetes Environments
- Feature Toggles
- Mock vs Real Services

Profiles allow the same application codebase to run in multiple environments without changing source code.

---

# Key Points To Remember

- Enables environment-specific bean registration.
- Used with `dev`, `test`, `stage`, `prod`, etc.
- Can be applied to Classes.
- Can be applied to `@Bean` methods.
- Can be applied to `@Configuration` classes.
- Supports OR, AND, and NOT expressions.
- Works with profile-specific property files.
- Active profile is set using `spring.profiles.active`.
- Default profile is `default`.
- Prevents environment-specific code branching.
- Widely used in enterprise applications.
- One of the most important configuration annotations in Spring Boot.