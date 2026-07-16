
## Definition

`@SpringBootTest` is a Spring Boot testing annotation used to **load the complete Spring Application Context for integration testing**.

It tells Spring Boot:

> Start the application context just like a real application and make all Spring beans available for testing.

Package:

```java
import org.springframework.boot.test.context.SpringBootTest;
```

---

# Why Do We Need @SpringBootTest?

Suppose you have:

```java
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

---

You want to verify:

```text
Everything Works Together
```

---

Not just:

```text
Single Class
```

---

This is called:

```text
Integration Testing
```

---

Solution:

```java
@SpringBootTest
```

---

# What Does It Do?

When Spring sees:

```java
@SpringBootTest
```

it:

```text
Starts Spring Container
Creates All Beans
Loads Configurations
Loads Properties
Configures Database
Injects Dependencies
```

---

Exactly like application startup.

---

# Basic Example

```java
@SpringBootTest
class ProductServiceTest {

    @Autowired
    private ProductService service;

    @Test
    void testService() {

        Product product =
             service.getProduct(1L);

        assertNotNull(product);
    }
}
```

---

Execution:

```text
Start Application Context
      ↓
Create Beans
      ↓
Inject ProductService
      ↓
Run Test
```

---

# What Happens Internally?

```text
JUnit Starts
      ↓
SpringBootTestContextBootstrapper
      ↓
ApplicationContext Created
      ↓
All Beans Loaded
      ↓
Dependencies Injected
      ↓
Test Executes
```

---

# Real Startup Example

Application:

```java
@SpringBootApplication
public class App {

}
```

---

Test:

```java
@SpringBootTest
class AppTest {

}
```

---

Equivalent to:

```java
SpringApplication.run(
    App.class
);
```

before test execution.

---

# Verifying Context Loads

Most common beginner test.

---

```java
@SpringBootTest
class ApplicationTests {

    @Test
    void contextLoads() {

    }
}
```

---

Purpose:

```text
Verify Spring Context Starts Successfully
```

---

If any bean fails:

```text
Test Fails
```

---

# Autowiring Beans

```java
@SpringBootTest
class EmployeeTest {

    @Autowired
    EmployeeService service;

    @Test
    void test() {

        assertNotNull(service);
    }
}
```

---

Spring injects:

```java
EmployeeService
```

just like runtime.

---

# Testing Repository

```java
@SpringBootTest
class EmployeeRepositoryTest {

    @Autowired
    EmployeeRepository repository;

    @Test
    void testFindById() {

        Employee emp =
            repository.findById(1L)
                      .orElse(null);

        assertNotNull(emp);
    }
}
```

---

Real repository.

Real database (or test database).

---

# Testing Service Layer

```java
@SpringBootTest
class ProductServiceTest {

    @Autowired
    ProductService service;

    @Test
    void testGetProduct() {

        Product p =
            service.getProduct(1L);

        assertNotNull(p);
    }
}
```

---

Tests:

```text
Service
Repository
Database
```

together.

---

# Testing Controller

```java
@SpringBootTest
class ProductControllerTest {

    @Autowired
    ProductController controller;

    @Test
    void testController() {

        assertNotNull(controller);
    }
}
```

---

Controller bean is created.

---

# Web Environment

Very important interview topic.

---

Default:

```java
@SpringBootTest
```

---

Web server:

```text
NOT Started
```

---

# RANDOM_PORT

```java
@SpringBootTest(
 webEnvironment =
 WebEnvironment.RANDOM_PORT
)
```

---

Starts embedded server.

---

Random port assigned.

---

Example:

```java
@SpringBootTest(
 webEnvironment =
 WebEnvironment.RANDOM_PORT
)
class ApiTest {

}
```

---

Execution:

```text
Application Started
Tomcat Started
Random Port Allocated
```

---

# DEFINED_PORT

```java
@SpringBootTest(
 webEnvironment =
 WebEnvironment.DEFINED_PORT
)
```

---

Uses:

```properties
server.port=8080
```

---

Server starts on:

```text
8080
```

---

# MOCK

```java
@SpringBootTest(
 webEnvironment =
 WebEnvironment.MOCK
)
```

---

Default choice for web tests.

---

Creates:

```text
Mock Servlet Environment
```

---

No real server.

---

Faster execution.

---

# NONE

```java
@SpringBootTest(
 webEnvironment =
 WebEnvironment.NONE
)
```

---

No web environment.

---

Only Spring context.

---

# Example With TestRestTemplate

```java
@SpringBootTest(
 webEnvironment =
 WebEnvironment.RANDOM_PORT
)
class ProductApiTest {

    @Autowired
    private TestRestTemplate rest;

    @Test
    void testApi() {

        String response =
            rest.getForObject(
                "/products/1",
                String.class
            );

        assertNotNull(response);
    }
}
```

---

Real HTTP request.

---

# Properties Override

```java
@SpringBootTest(
 properties = {
   "app.name=TestApp"
 }
)
```

---

During test:

```properties
app.name=TestApp
```

---

Overrides application properties.

---

# Using Test Profile

```java
@SpringBootTest

@ActiveProfiles("test")
class ProductTest {

}
```

---

Loads:

```properties
application-test.properties
```

---

Common in enterprise applications.

---

# MockBean Example

Frequently asked interview question.

---

Suppose:

```java
ProductService
```

depends on:

```java
PaymentService
```

---

Replace real bean:

```java
@MockBean
private PaymentService
        paymentService;
```

---

Example:

```java
@SpringBootTest
class ProductServiceTest {

    @MockBean
    PaymentService paymentService;

    @Autowired
    ProductService service;
}
```

---

Real Spring context.

Mock dependency.

---

# H2 Database Testing

Common setup.

---

Dependency:

```xml
<dependency>
   <groupId>com.h2database</groupId>
   <artifactId>h2</artifactId>
</dependency>
```

---

Test:

```java
@SpringBootTest

@ActiveProfiles("test")
class EmployeeTest {

}
```

---

Uses:

```text
In-Memory Database
```

---

# Internal Components

Uses:

```text
SpringExtension
TestContext Framework
ApplicationContext
Dependency Injection
Bean Factory
```

---

Execution:

```text
JUnit
   ↓
Spring Test Framework
   ↓
Application Context
   ↓
Dependency Injection
   ↓
Test Execution
```

---

# @SpringBootTest vs @WebMvcTest

Interview Favorite.

---

## @SpringBootTest

Loads:

```text
Entire Application
```

---

Beans:

```text
Controller
Service
Repository
Database
Security
Configuration
```

---

# @WebMvcTest

Loads:

```text
Only Web Layer
```

---

Beans:

```text
Controller
MVC Components
```

---

Comparison:

| Feature | @SpringBootTest | @WebMvcTest |
|----------|----------------|-------------|
| Controller | ✅ | ✅ |
| Service | ✅ | ❌ |
| Repository | ✅ | ❌ |
| Database | ✅ | ❌ |
| Full Context | ✅ | ❌ |
| Speed | Slower | Faster |

---

# @SpringBootTest vs @DataJpaTest

## @SpringBootTest

Loads everything.

---

## @DataJpaTest

Loads only:

```text
Repositories
JPA Components
```

---

Faster.

---

# Common Use Cases

## Integration Testing

```java
@SpringBootTest
```

---

## API Testing

```java
@SpringBootTest(
 webEnvironment =
 RANDOM_PORT
)
```

---

## Database Testing

```java
@SpringBootTest
```

---

## Security Testing

```java
@SpringBootTest
```

---

## End-to-End Testing

```java
@SpringBootTest
```

---

# Common Mistakes

## Mistake 1

Using It For Every Test

Wrong:

```java
@SpringBootTest
```

for simple unit tests.

---

Slow.

---

Use:

```java
Mockito
```

for unit tests.

---

# Mistake 2

Loading Full Context Unnecessarily

Need only controller?

Use:

```java
@WebMvcTest
```

---

Need only repository?

Use:

```java
@DataJpaTest
```

---

# Mistake 3

Real Database Access

Can affect production data.

---

Use:

```text
H2
Testcontainers
```

for testing.

---

# Common Interview Questions

## What is @SpringBootTest?

Loads complete Spring Boot application context.

---

## Is It Used For Unit Testing?

No.

Mostly:

```text
Integration Testing
```

---

## Does It Start Spring Container?

Yes.

---

## Does It Create All Beans?

Yes.

---

## Difference Between @SpringBootTest and @WebMvcTest?

Full application vs web layer only.

---

## Why Is It Slow?

Because:

```text
Entire Context Loads
```

---

# Enterprise Best Practice

Use:

```java
@SpringBootTest
```

for:

```text
Integration Tests
```

---

Use:

```java
@WebMvcTest
```

for:

```text
Controller Tests
```

---

Use:

```java
@DataJpaTest
```

for:

```text
Repository Tests
```

---

Use:

```java
@MockBean
```

to isolate external dependencies.

---

# Key Points To Remember

- `@SpringBootTest` loads the entire Spring Boot application.
- Used mainly for integration testing.
- Starts the Spring Application Context.
- Supports dependency injection.
- Can start an embedded web server.
- Supports test-specific properties.
- Works with `@MockBean`.
- Slower than slice tests.
- Frequently asked Spring Boot testing interview topic.
- Verifies all layers work together.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@SpringBootTest` | Full Integration Test |
| `@WebMvcTest` | Controller Test |
| `@DataJpaTest` | Repository Test |
| `@MockBean` | Mock Spring Bean |
| `@ActiveProfiles` | Load Test Profile |

---

# Interview Shortcut

```java
@SpringBootTest
class ProductServiceTest {

    @Autowired
    ProductService service;

    @Test
    void test() {

    }
}
```

Execution:

```text
JUnit Starts
      ↓
Spring Context Starts
      ↓
All Beans Created
      ↓
Dependencies Injected
      ↓
Test Runs
```

Think of it as:

```java
SpringApplication.run(...)
```

executing before your test.

---

# Real Production Scenario

Application:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

Test:

```java
@SpringBootTest
class OrderFlowTest {

}
```

Verifies:

```text
HTTP Request
      ↓
Controller
      ↓
Service
      ↓
Repository
      ↓
Database
```

works correctly together.

Result:

```text
High Confidence
Realistic Testing
Production-Like Behavior
```

`@SpringBootTest` is Spring Boot's primary integration testing annotation that loads the complete application context, allowing tests to run against the application in an environment that closely resembles production.