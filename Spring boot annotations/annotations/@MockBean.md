
## Definition

`@MockBean` is a Spring Boot testing annotation used to create and register a **Mockito mock object as a Spring Bean** inside the Application Context.

It tells Spring:

> Replace the real bean with a Mockito mock during test execution.

Package:

```java
import org.springframework.boot.test.mock.mockito.MockBean;
```

---

# Why Do We Need @MockBean?

Suppose we have:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

---

Controller:

```java
@RestController
public class ProductController {

    private final ProductService service;

    public ProductController(
            ProductService service) {

        this.service = service;
    }

    @GetMapping("/product")
    public String getProduct() {

        return service.getProduct();
    }
}
```

---

During testing:

```text
We Don't Want
Database Calls
```

---

We only want:

```text
Controller Logic
```

---

Solution:

```java
@MockBean
```

---

# What Does @MockBean Do?

Spring normally creates:

```java
ProductService
```

---

With:

```java
@MockBean
ProductService service;
```

Spring creates:

```java
Mockito Mock
```

instead.

---

Flow:

```text
Real Bean
    ↓
Removed
    ↓
Mockito Mock Added
```

---

# Basic Example

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @MockBean
    private ProductService service;

    @Autowired
    private MockMvc mockMvc;
}
```

---

Spring creates:

```text
Mock ProductService
```

---

Controller receives:

```text
Mock Instead Of Real Bean
```

---

# Internal Workflow

```text
Test Starts
      ↓
Spring Context Created
      ↓
Find @MockBean
      ↓
Create Mockito Mock
      ↓
Replace Existing Bean
      ↓
Inject Into Dependencies
      ↓
Run Test
```

---

# Complete Example

Controller:

```java
@RestController
public class ProductController {

    private final ProductService service;

    public ProductController(
            ProductService service) {

        this.service = service;
    }

    @GetMapping("/product")
    public String getProduct() {

        return service.getName();
    }
}
```

---

Test:

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @MockBean
    ProductService service;

    @Autowired
    MockMvc mockMvc;

    @Test
    void test()
    throws Exception {

        when(service.getName())
            .thenReturn("Laptop");

        mockMvc.perform(
            get("/product")
        )
        .andExpect(
            status().isOk()
        )
        .andExpect(
            content().string(
                "Laptop"
            )
        );
    }
}
```

---

Execution:

```text
Controller Calls Service
       ↓
Mock Service Called
       ↓
Returns "Laptop"
       ↓
Response Verified
```

---

# Why Not Use @Mock?

Mockito provides:

```java
@Mock
```

---

Example:

```java
@Mock
ProductService service;
```

---

Problem:

```text
Spring Doesn't Know About It
```

---

It is NOT registered as a bean.

---

# Difference Between @Mock And @MockBean

## @Mock

```java
@Mock
ProductService service;
```

---

Creates:

```text
Mockito Mock Only
```

---

Not managed by Spring.

---

## @MockBean

```java
@MockBean
ProductService service;
```

---

Creates:

```text
Mockito Mock
+
Spring Bean
```

---

Comparison:

| Feature | @Mock | @MockBean |
|----------|--------|-----------|
| Mockito Mock | ✅ | ✅ |
| Spring Bean | ❌ | ✅ |
| Replaces Existing Bean | ❌ | ✅ |
| Works In Spring Context | ❌ | ✅ |

---

# Example With @SpringBootTest

```java
@SpringBootTest
class ProductServiceTest {

    @MockBean
    ProductRepository repository;

    @Autowired
    ProductService service;
}
```

---

Application loads:

```text
All Beans
```

---

But:

```java
ProductRepository
```

becomes:

```text
Mockito Mock
```

---

# Stubbing Behavior

Mockito:

```java
when(
    repository.findById(1L)
)
.thenReturn(
    Optional.of(product)
);
```

---

Meaning:

```text
If Called
Return Product
```

---

# Verifying Method Calls

```java
verify(service)
      .getName();
```

---

Checks:

```text
Method Executed
```

---

Example:

```java
verify(repository)
      .save(any());
```

---

# Mocking Exceptions

```java
when(service.getName())
      .thenThrow(
           new RuntimeException()
      );
```

---

Useful for:

```text
Exception Testing
```

---

# Multiple MockBeans

```java
@MockBean
ProductService service;

@MockBean
EmailService emailService;

@MockBean
NotificationService notificationService;
```

---

Spring registers all mocks.

---

# MockBean In Integration Tests

```java
@SpringBootTest
```

---

Real:

```text
Controller
Service
```

---

Mock:

```text
Repository
```

---

Example:

```java
@SpringBootTest
class UserServiceTest {

    @MockBean
    UserRepository repository;

    @Autowired
    UserService service;
}
```

---

Useful when:

```text
Database Not Required
```

---

# MockBean With Security

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @MockBean
    UserService service;
}
```

---

Security loads.

---

Business logic mocked.

---

# Internal Components Used

```text
Mockito
Spring TestContext
BeanFactory
ApplicationContext
Dependency Injection
```

---

# @MockBean vs @TestConfiguration

Interview Favorite.

---

## @MockBean

Creates:

```text
Mockito Mock
```

---

Example:

```java
@MockBean
PaymentService service;
```

---

## @TestConfiguration

Creates:

```text
Custom Bean
```

---

Example:

```java
@Bean
PaymentService service() {

}
```

---

Comparison:

| Feature | @MockBean | @TestConfiguration |
|----------|-----------|-------------------|
| Mockito Mock | ✅ | ❌ |
| Custom Logic | ❌ | ✅ |
| Bean Replacement | ✅ | ✅ |
| Fast | ✅ | ❌ |

---

# @MockBean vs SpyBean

## @MockBean

Everything mocked.

---

Example:

```java
@MockBean
ProductService service;
```

---

Method:

```java
service.getName();
```

returns:

```text
Null
```

until stubbed.

---

## @SpyBean

Real bean.

Partial mocking.

---

Example:

```java
@SpyBean
ProductService service;
```

---

Real methods execute.

---

Can selectively mock.

---

# Common Use Cases

## Controller Testing

```java
@WebMvcTest
```

with

```java
@MockBean
```

---

## Service Testing

```java
@SpringBootTest
```

with mocked repository.

---

## Database Isolation

```java
@MockBean
Repository
```

---

## External API Isolation

```java
@MockBean
RestClient
```

---

## Error Simulation

```java
thenThrow(...)
```

---

# Common Mistakes

## Mistake 1

Using @Mock Instead Of @MockBean

Wrong:

```java
@Mock
ProductService service;
```

---

Controller won't receive it.

---

# Mistake 2

Not Stubbing Behavior

```java
@MockBean
ProductService service;
```

---

Returns:

```text
null
```

by default.

---

Need:

```java
when(...)
```

---

# Mistake 3

Overusing MockBean

Too many mocks:

```text
Unrealistic Tests
```

---

Use only when necessary.

---

# Common Interview Questions

## What is @MockBean?

Creates a Mockito mock and registers it as a Spring bean.

---

## Does It Replace Existing Bean?

Yes.

---

## Difference Between @Mock and @MockBean?

Spring-managed bean vs plain Mockito mock.

---

## Can It Be Used With @SpringBootTest?

Yes.

---

## Can It Be Used With @WebMvcTest?

Yes.

Very common.

---

## What Happens If No Stubbing Is Done?

Default Mockito values returned:

```text
null
0
false
empty collections
```

---

# Enterprise Best Practice

Use:

```java
@WebMvcTest
```

+

```java
@MockBean
```

for controller tests.

---

Use:

```java
@SpringBootTest
```

+

```java
@MockBean
```

for integration tests where some dependencies should be isolated.

---

Avoid mocking:

```text
Everything
```

---

Mock only:

```text
External Systems
Database
Remote APIs
Expensive Operations
```

---

# Key Points To Remember

- `@MockBean` creates a Mockito mock.
- Registers the mock inside Spring Context.
- Replaces existing bean of the same type.
- Commonly used with `@WebMvcTest`.
- Can be used with `@SpringBootTest`.
- Supports stubbing and verification.
- Useful for isolating dependencies.
- Faster than using real dependencies.
- Built on top of Mockito.
- Frequently asked Spring Boot testing interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@MockBean` | Mock Spring Bean |
| `@Mock` | Plain Mockito Mock |
| `@SpyBean` | Partial Mock |
| `@TestConfiguration` | Custom Test Bean |
| `@SpringBootTest` | Full Integration Test |

---

# Interview Shortcut

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @MockBean
    ProductService service;
}
```

Execution:

```text
Create Controller
      ↓
Create Mockito Mock
      ↓
Inject Mock Into Controller
      ↓
Stub Behavior
      ↓
Verify Response
```

Think of it as:

```text
Mockito Mock
+
Spring Bean
```

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
@WebMvcTest(OrderController.class)
```

Replace:

```java
@MockBean
OrderService service;
```

Flow:

```text
HTTP Request
      ↓
Controller
      ↓
Mock Service
      ↓
Fake Response
      ↓
Assertions
```

Result:

```text
Fast Tests
No Database Calls
Isolated Controller Validation
```

`@MockBean` is Spring Boot's testing annotation that creates a Mockito mock and registers it as a Spring-managed bean, allowing dependencies to be replaced and isolated during tests.