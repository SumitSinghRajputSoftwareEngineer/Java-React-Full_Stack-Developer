
## Definition

`@WebMvcTest` is a Spring Boot test annotation used to test only the **Spring MVC (Web Layer)** components such as Controllers, Controller Advice, Filters, and MVC configuration without loading the entire application.

It tells Spring Boot:

> Load only the web layer and ignore Service, Repository, Database, Cache, Messaging, and other application components.

Package:

```java
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
```

---

# Why Do We Need @WebMvcTest?

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

If we use:

```java
@SpringBootTest
```

Spring loads:

```text
Controller
Service
Repository
Database
Security
Cache
Messaging
Configurations
Everything
```

---

Problem:

```text
Slow Test Execution
High Memory Usage
```

---

For controller testing we only need:

```text
Controller
Request Mapping
JSON Conversion
Validation
HTTP Status
Response Body
```

---

Solution:

```java
@WebMvcTest
```

---

# What Does @WebMvcTest Load?

Loaded:

```text
@Controller
@RestController
@ControllerAdvice
Json Components
Converters
Filters
Spring MVC Configuration
Handler Mapping
MockMvc
```

---

Not Loaded:

```text
@Service
@Repository
Database
Cache
Kafka
RabbitMQ
Redis
```

---

# Internal Workflow

```text
JUnit
   ↓
@WebMvcTest
   ↓
Create MVC Context
   ↓
Create Controller
   ↓
Inject Mock Beans
   ↓
Execute HTTP Tests
```

---

# Basic Example

## Controller

```java
@RestController
@RequestMapping("/products")
public class ProductController {

    @GetMapping("/{id}")
    public String getProduct(
            @PathVariable Long id) {

        return "Mobile";
    }
}
```

---

## Test

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testProduct()
    throws Exception {

        mockMvc.perform(
                get("/products/1")
        )
        .andExpect(
            status().isOk()
        )
        .andExpect(
            content().string("Mobile")
        );
    }
}
```

---

Execution:

```text
HTTP Request
      ↓
Controller
      ↓
Response
```

---

No database.

No service.

No repository.

---

# Why MockMvc?

`MockMvc` simulates HTTP requests.

---

Without starting:

```text
Tomcat
Jetty
Undertow
```

---

Example:

```java
@Autowired
MockMvc mockMvc;
```

---

Request:

```java
mockMvc.perform(
    get("/products/1")
);
```

---

Response verified.

---

# Controller With Service

## Controller

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

Problem:

```text
ProductService
Not Loaded
```

---

Test fails.

---

# Solution: @MockBean

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

Spring injects:

```text
Mock ProductService
```

---

# Complete Example

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
                content().string("Laptop")
        );
    }
}
```

---

# Testing JSON Response

Controller:

```java
@GetMapping("/product")
public Product getProduct() {

    return new Product(
        1L,
        "Laptop"
    );
}
```

---

Test:

```java
mockMvc.perform(
      get("/product")
)
.andExpect(
      status().isOk()
)
.andExpect(
      jsonPath("$.id")
      .value(1)
)
.andExpect(
      jsonPath("$.name")
      .value("Laptop")
);
```

---

# Testing POST API

Controller:

```java
@PostMapping("/products")
public Product save(
      @RequestBody Product p) {

    return p;
}
```

---

Test:

```java
mockMvc.perform(

      post("/products")
      .contentType(
          MediaType.APPLICATION_JSON
      )
      .content("""
      {
        "id":1,
        "name":"Mobile"
      }
      """)
)
.andExpect(
      status().isOk()
);
```

---

# Testing Validation

Controller:

```java
@PostMapping("/users")
public User save(
      @Valid
      @RequestBody User user) {

    return user;
}
```

---

DTO:

```java
public class User {

    @NotBlank
    private String name;
}
```

---

Test:

```java
mockMvc.perform(

    post("/users")
    .contentType(
        APPLICATION_JSON
    )
    .content("{}")
)
.andExpect(
    status().isBadRequest()
);
```

---

Verifies:

```text
Validation Works
```

---

# Testing Exception Handling

Controller Advice:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
       RuntimeException.class
    )
    public ResponseEntity<String>
    handle(RuntimeException ex) {

        return ResponseEntity
                .badRequest()
                .body("ERROR");
    }
}
```

---

Test:

```java
@WebMvcTest(ProductController.class)
```

---

Controller advice loaded automatically.

---

Can verify:

```text
Error Responses
```

---

# Security Testing

Controller:

```java
@GetMapping("/admin")
public String admin() {

    return "ADMIN";
}
```

---

Test:

```java
@WithMockUser(
    roles = "ADMIN"
)
@Test
void testAdmin()
throws Exception {

    mockMvc.perform(
        get("/admin")
    )
    .andExpect(
        status().isOk()
    );
}
```

---

Useful when:

```text
Spring Security Enabled
```

---

# Internal Components Loaded

```text
DispatcherServlet
HandlerMapping
HandlerAdapter
ViewResolver
MessageConverters
Validation Framework
MockMvc
```

---

Not loaded:

```text
Repositories
Services
Database
Kafka
Redis
```

---

# @WebMvcTest vs @SpringBootTest

Interview Favorite.

---

## @WebMvcTest

Loads:

```text
Controller Layer Only
```

---

Speed:

```text
Fast
```

---

## @SpringBootTest

Loads:

```text
Entire Application
```

---

Speed:

```text
Slow
```

---

Comparison:

| Feature | @WebMvcTest | @SpringBootTest |
|----------|------------|----------------|
| Controller | ✅ | ✅ |
| Service | ❌ | ✅ |
| Repository | ❌ | ✅ |
| Database | ❌ | ✅ |
| MVC | ✅ | ✅ |
| MockMvc | ✅ | Optional |
| Fast | ✅ | ❌ |

---

# @WebMvcTest vs @DataJpaTest

## @WebMvcTest

Tests:

```text
Controller Layer
```

---

## @DataJpaTest

Tests:

```text
Repository Layer
```

---

# Common Use Cases

## REST API Testing

```java
@WebMvcTest
```

---

## Request Validation

```java
@WebMvcTest
```

---

## JSON Response Testing

```java
@WebMvcTest
```

---

## Exception Handling

```java
@WebMvcTest
```

---

## Security Rules

```java
@WebMvcTest
```

---

# Common Mistakes

## Mistake 1

Expecting Service Bean

```java
@WebMvcTest
```

does NOT load:

```java
@Service
```

---

Need:

```java
@MockBean
```

---

# Mistake 2

Expecting Repository

Not loaded.

---

Need:

```java
@SpringBootTest
```

for full integration tests.

---

# Mistake 3

Using @WebMvcTest For Integration Tests

Wrong choice.

---

Use:

```java
@SpringBootTest
```

instead.

---

# Common Interview Questions

## What is @WebMvcTest?

Loads only Spring MVC components for controller testing.

---

## Does It Load Service Layer?

No.

---

## Why Use MockBean?

To mock missing service dependencies.

---

## What Is MockMvc?

Framework for testing HTTP requests without starting a server.

---

## Difference Between @WebMvcTest and @SpringBootTest?

Web layer only vs full application.

---

## Is Database Loaded?

No.

---

# Enterprise Best Practice

Use:

```java
@WebMvcTest
```

for:

```text
Controller Tests
Request Validation
Security Testing
Exception Handling
JSON Testing
```

---

Use:

```java
@MockBean
```

for service dependencies.

---

Reserve:

```java
@SpringBootTest
```

for integration testing.

---

# Key Points To Remember

- `@WebMvcTest` loads only the web layer.
- Used for controller testing.
- Automatically configures `MockMvc`.
- Does not load services or repositories.
- Requires `@MockBean` for controller dependencies.
- Supports validation testing.
- Supports security testing.
- Faster than `@SpringBootTest`.
- Frequently asked Spring Boot testing interview topic.
- Ideal for REST API unit testing.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@WebMvcTest` | Controller Testing |
| `@SpringBootTest` | Full Integration Testing |
| `@DataJpaTest` | Repository Testing |
| `@MockBean` | Mock Spring Dependency |
| `@WithMockUser` | Security Testing |

---

# Interview Shortcut

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @Autowired
    MockMvc mockMvc;

    @MockBean
    ProductService service;
}
```

Execution:

```text
HTTP Request
      ↓
MockMvc
      ↓
Controller
      ↓
Mock Service
      ↓
Response Verification
```

Think of it as:

```text
Controller Unit Testing
Using HTTP Requests
Without Starting Server
```

---

# Real Production Scenario

Application:

```text
REST Controller
      ↓
Service
      ↓
Repository
```

---

Test:

```java
@WebMvcTest(OrderController.class)
```

Loads:

```text
OrderController
Spring MVC
MockMvc
```

Mocks:

```java
@MockBean
OrderService
```

Flow:

```text
HTTP Request
      ↓
Controller
      ↓
Mock Service
      ↓
JSON Response
```

Result:

```text
Fast Tests
Isolated Controller Validation
Reliable API Verification
```

`@WebMvcTest` is Spring Boot's web-layer testing annotation that loads only MVC components, allowing developers to efficiently test controllers, request mappings, validation, security, and JSON responses without loading the entire application context.