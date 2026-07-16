
## Definition

`@TestConfiguration` is a Spring Boot testing annotation used to define **test-specific beans and configurations** that are available only during testing.

It tells Spring:

> Use these bean definitions only for tests and do not include them in the main application configuration.

Package:

```java
import org.springframework.boot.test.context.TestConfiguration;
```

---

# Why Do We Need @TestConfiguration?

Suppose your application uses:

```java
@Service
public class PaymentService {

    public String processPayment() {

        return "Real Payment Gateway";
    }
}
```

---

During testing:

```text
We Don't Want
Real Payment Calls
```

---

Instead:

```text
Use Fake/Test Implementation
```

---

Solution:

```java
@TestConfiguration
```

---

# What Problem Does It Solve?

Production:

```text
Real Database
Real APIs
Real Payment Gateway
Real External Systems
```

---

Testing:

```text
Fake Database
Mock Services
Test Implementations
```

---

Need a way to replace beans.

---

`@TestConfiguration` provides that.

---

# Basic Example

## Production Bean

```java
@Service
public class PaymentService {

    public String process() {

        return "REAL PAYMENT";
    }
}
```

---

## Test Configuration

```java
@TestConfiguration
public class TestConfig {

    @Bean
    public PaymentService paymentService() {

        return new PaymentService() {

            @Override
            public String process() {

                return "FAKE PAYMENT";
            }
        };
    }
}
```

---

## Test Class

```java
@SpringBootTest

@Import(TestConfig.class)
class PaymentTest {

    @Autowired
    private PaymentService service;

    @Test
    void test() {

        System.out.println(
           service.process()
        );
    }
}
```

---

Output:

```text
FAKE PAYMENT
```

---

# What Happens Internally?

```text
Spring Test Starts
       ↓
Read TestConfiguration
       ↓
Create Test Beans
       ↓
Override Existing Beans
       ↓
Run Test
```

---

# Difference Between @Configuration And @TestConfiguration

## @Configuration

```java
@Configuration
public class AppConfig {

}
```

---

Available:

```text
Production
Testing
Everywhere
```

---

## @TestConfiguration

```java
@TestConfiguration
public class TestConfig {

}
```

---

Available:

```text
Testing Only
```

---

Comparison:

| Feature | @Configuration | @TestConfiguration |
|----------|---------------|-------------------|
| Production | ✅ | ❌ |
| Testing | ✅ | ✅ |
| Test Only | ❌ | ✅ |
| Bean Override | ❌ | ✅ Commonly Used |

---

# Defining Test Beans

```java
@TestConfiguration
public class TestConfig {

    @Bean
    public Clock clock() {

        return Clock.fixed(
            Instant.now(),
            ZoneId.systemDefault()
        );
    }
}
```

---

Used for:

```text
Fixed Time
Deterministic Tests
```

---

# Importing Test Configuration

Most common usage.

---

```java
@SpringBootTest

@Import(TestConfig.class)
class ProductTest {

}
```

---

Meaning:

```text
Load TestConfig
Along With Test Context
```

---

# Nested Test Configuration

Very common interview topic.

---

```java
@SpringBootTest
class ProductTest {

    @TestConfiguration
    static class Config {

        @Bean
        ProductService productService() {

            return new ProductService();
        }
    }
}
```

---

No need for:

```java
@Import
```

---

Spring automatically loads it.

---

# Example With Repository

Production:

```java
@Repository
public class ProductRepository {

}
```

---

Testing:

```java
@TestConfiguration
class TestConfig {

    @Bean
    ProductRepository repository() {

        return Mockito.mock(
            ProductRepository.class
        );
    }
}
```

---

Result:

```text
Mock Repository
Used During Test
```

---

# Example With External API

Production:

```java
@Service
public class EmailService {

    public void sendEmail() {

        callExternalProvider();
    }
}
```

---

Testing:

```java
@TestConfiguration
class TestConfig {

    @Bean
    EmailService emailService() {

        return new FakeEmailService();
    }
}
```

---

Result:

```text
No Real Emails Sent
```

---

# Bean Override Example

Production:

```java
@Bean
public PaymentGateway gateway() {

    return new StripeGateway();
}
```

---

Test:

```java
@TestConfiguration
class TestConfig {

    @Bean
    public PaymentGateway gateway() {

        return new FakeGateway();
    }
}
```

---

Result:

```text
Fake Gateway Used
```

---

# @TestConfiguration With @SpringBootTest

Most common combination.

---

```java
@SpringBootTest

@Import(TestConfig.class)
class UserServiceTest {

}
```

---

Flow:

```text
Load Application Context
      ↓
Load Test Configuration
      ↓
Create Test Beans
      ↓
Run Tests
```

---

# @TestConfiguration vs @MockBean

Interview Favorite.

---

## @MockBean

```java
@MockBean
private PaymentService service;
```

---

Creates:

```text
Mockito Mock
```

---

## @TestConfiguration

```java
@Bean
public PaymentService service() {

}
```

---

Creates:

```text
Custom Implementation
```

---

Comparison:

| Feature | @MockBean | @TestConfiguration |
|----------|------------|-------------------|
| Mockito Mock | ✅ | ❌ |
| Real Custom Bean | ❌ | ✅ |
| Override Bean | ✅ | ✅ |
| Complex Logic | ❌ | ✅ |

---

# Example

```java
@TestConfiguration
class Config {

    @Bean
    NotificationService service() {

        return new NotificationService() {

            @Override
            public void send() {

                System.out.println(
                    "TEST MESSAGE"
                );
            }
        };
    }
}
```

---

Useful when:

```text
MockBean Is Too Simple
```

---

# Internal Components

Uses:

```text
Spring TestContext Framework
BeanFactory
ApplicationContext
Bean Definition Registry
```

---

Execution:

```text
Test Starts
      ↓
Find TestConfiguration
      ↓
Register Test Beans
      ↓
Initialize Context
      ↓
Inject Dependencies
```

---

# Real-World Use Cases

## Fake Payment Gateway

```java
@TestConfiguration
```

---

## Fake Email Service

```java
@TestConfiguration
```

---

## Fixed Clock

```java
@TestConfiguration
```

---

## Test Database

```java
@TestConfiguration
```

---

## Fake Kafka Producer

```java
@TestConfiguration
```

---

## Fake Redis Client

```java
@TestConfiguration
```

---

# Common Mistakes

## Mistake 1

Expecting It In Production

Wrong:

```java
@TestConfiguration
```

---

Not loaded in production.

---

# Mistake 2

Forgetting @Import

If configuration is external:

```java
@Import(TestConfig.class)
```

required.

---

# Mistake 3

Using For Simple Mocks

Better:

```java
@MockBean
```

---

Use:

```java
@TestConfiguration
```

when custom behavior is needed.

---

# Common Interview Questions

## What is @TestConfiguration?

Defines test-only Spring beans.

---

## Is It Loaded In Production?

No.

---

## Can It Override Existing Beans?

Yes.

Very commonly.

---

## Difference Between @Configuration And @TestConfiguration?

`@Configuration`

works everywhere.

---

`@TestConfiguration`

works only in tests.

---

## Difference Between @MockBean And @TestConfiguration?

Mock vs custom bean implementation.

---

# Enterprise Best Practice

Use:

```java
@MockBean
```

for:

```text
Simple Mocking
```

---

Use:

```java
@TestConfiguration
```

for:

```text
Custom Test Implementations
```

---

Examples:

```text
Fake Email Service
Fake Kafka Producer
Fake Payment Gateway
Fixed Clock
```

---

# Key Points To Remember

- `@TestConfiguration` creates test-only beans.
- Not loaded in production.
- Can override existing beans.
- Works with `@SpringBootTest`.
- Commonly used with `@Import`.
- Supports nested static configurations.
- Useful for fake implementations.
- Alternative to `@MockBean` when custom behavior is needed.
- Part of Spring Boot Test framework.
- Frequently asked Spring Boot testing interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@TestConfiguration` | Test-Only Beans |
| `@Configuration` | Production/Test Beans |
| `@MockBean` | Mockito Mock Bean |
| `@SpringBootTest` | Full Integration Test |
| `@Import` | Import Configuration |

---

# Interview Shortcut

```java
@TestConfiguration
public class TestConfig {

    @Bean
    public PaymentService paymentService() {

        return new FakePaymentService();
    }
}
```

Used in:

```java
@SpringBootTest

@Import(TestConfig.class)
class PaymentTest {

}
```

Execution:

```text
Load Spring Context
      ↓
Load TestConfig
      ↓
Create Fake Bean
      ↓
Inject Into Test
      ↓
Run Test
```

Think of it as:

```java
@Configuration
```

but:

```text
Testing Only
```

---

# Real Production Scenario

Production:

```java
PaymentService
    ↓
Stripe API
```

---

Testing:

```java
@TestConfiguration
```

provides:

```java
FakePaymentService
```

---

Flow:

```text
Test Starts
      ↓
Fake Service Loaded
      ↓
No External API Calls
      ↓
Fast Reliable Test
```

Result:

```text
Isolated Tests
No Side Effects
Predictable Behavior
```

`@TestConfiguration` is Spring Boot's test-specific configuration annotation that allows developers to define custom beans, override production beans, and create isolated testing environments without affecting the actual application configuration.