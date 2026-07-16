
## Definition

`@AutoConfigureMockMvc` is a Spring Boot test annotation that automatically configures and creates a **MockMvc** bean for testing web endpoints without manually setting up the Spring MVC infrastructure.

It tells Spring Boot:

> Create and inject MockMvc so HTTP requests can be tested against controllers.

Package:

```java
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
```

---

# Why Do We Need It?

Suppose we use:

```java
@SpringBootTest
```

This loads:

```text
Controller
Service
Repository
Database
Security
Cache
Everything
```

---

But:

```java
MockMvc
```

is NOT automatically available.

---

Example:

```java
@SpringBootTest
class ProductTest {

    @Autowired
    MockMvc mockMvc;
}
```

---

Result:

```text
No MockMvc Bean Found
```

---

Solution:

```java
@AutoConfigureMockMvc
```

---

# Basic Example

```java
@SpringBootTest

@AutoConfigureMockMvc
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;
}
```

---

Spring automatically creates:

```java
MockMvc
```

---

Now HTTP requests can be simulated.

---

# What Is MockMvc?

MockMvc is Spring's testing framework that simulates:

```text
HTTP Requests
HTTP Responses
Headers
Parameters
JSON Payloads
```

without starting a real browser.

---

Example:

```java
mockMvc.perform(
      get("/products/1")
);
```

---

Spring internally executes:

```text
DispatcherServlet
Controller
Validation
Exception Handling
```

---

# What Happens Internally?

```text
@SpringBootTest
       ↓
Load Application Context
       ↓
@AutoConfigureMockMvc
       ↓
Create MockMvc Bean
       ↓
Inject Into Test
       ↓
Execute HTTP Tests
```

---

# Complete Example

## Controller

```java
@RestController
@RequestMapping("/products")
public class ProductController {

    @GetMapping("/{id}")
    public String getProduct(
            @PathVariable Long id) {

        return "Laptop";
    }
}
```

---

## Test

```java
@SpringBootTest

@AutoConfigureMockMvc
class ProductControllerTest {

    @Autowired
    MockMvc mockMvc;

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
                content().string("Laptop")
        );
    }
}
```

---

Execution:

```text
Mock HTTP Request
       ↓
Controller
       ↓
Response
       ↓
Assertions
```

---

# Why Not Use Real HTTP Calls?

Without MockMvc:

```java
RestTemplate
```

or

```java
WebClient
```

would require:

```text
Tomcat Startup
Network Layer
Actual Port
```

---

Slower.

---

MockMvc:

```text
No Network
No Browser
No Actual HTTP Traffic
```

---

Fast.

---

# Relationship With @SpringBootTest

Very important interview topic.

---

```java
@SpringBootTest
```

loads:

```text
Entire Application
```

---

```java
@AutoConfigureMockMvc
```

adds:

```text
MockMvc Bean
```

---

Together:

```java
@SpringBootTest

@AutoConfigureMockMvc
```

means:

```text
Full Integration Testing
Using Mock HTTP Requests
```

---

# Example With Service Layer

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

---

Test:

```java
@SpringBootTest

@AutoConfigureMockMvc
```

---

Everything loads.

---

Request:

```java
mockMvc.perform(
      get("/products/1")
);
```

---

Flow:

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

Real execution.

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

Verifies:

```text
JSON Serialization
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
         "name":"Laptop"
      }
      """)
)
.andExpect(
      status().isOk()
);
```

---

# Testing Validation

DTO:

```java
public class User {

    @NotBlank
    private String name;
}
```

---

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
      status()
      .isBadRequest()
);
```

---

Verifies:

```text
Bean Validation
```

---

# Testing Security

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

Very common.

---

# Print Request/Response

Useful for debugging.

---

```java
mockMvc.perform(
      get("/products")
)
.andDo(
      print()
);
```

---

Output:

```text
Request
Headers
Response
Status
Body
```

---

# Disable Filters

By default:

```text
Security Filters
Servlet Filters
```

are applied.

---

Disable:

```java
@AutoConfigureMockMvc(
      addFilters = false
)
```

---

Useful when testing:

```text
Controller Only
```

---

# Internal Components Created

Spring creates:

```text
MockMvc
DispatcherServlet
HandlerMapping
HandlerAdapter
MessageConverters
ExceptionResolvers
Filters
```

---

# @AutoConfigureMockMvc vs @WebMvcTest

Interview Favorite.

---

## @WebMvcTest

Loads:

```text
Controller Only
```

---

Dependencies:

```java
@MockBean
```

required.

---

## @AutoConfigureMockMvc

Usually used with:

```java
@SpringBootTest
```

---

Loads:

```text
Entire Application
```

---

Comparison:

| Feature | @WebMvcTest | @AutoConfigureMockMvc |
|----------|-------------|----------------------|
| Controller | ✅ | ✅ |
| Service | ❌ | ✅ |
| Repository | ❌ | ✅ |
| Database | ❌ | ✅ |
| Security | Limited | Full |
| MockMvc | ✅ | ✅ |
| Fast | ✅ | ❌ |

---

# @AutoConfigureMockMvc vs TestRestTemplate

## MockMvc

```text
No Real Server
```

---

Execution:

```text
In Memory
```

---

Very fast.

---

## TestRestTemplate

Requires:

```java
@SpringBootTest(
 webEnvironment =
 RANDOM_PORT
)
```

---

Uses:

```text
Real HTTP Requests
```

---

Slower.

---

# Common Use Cases

## Integration Testing

```java
@SpringBootTest
@AutoConfigureMockMvc
```

---

## Security Testing

```java
@AutoConfigureMockMvc
```

---

## Validation Testing

```java
@AutoConfigureMockMvc
```

---

## API Testing

```java
@AutoConfigureMockMvc
```

---

## Exception Handling

```java
@AutoConfigureMockMvc
```

---

# Common Mistakes

## Mistake 1

Using Without Spring Context

Wrong:

```java
@AutoConfigureMockMvc
```

alone.

---

Needs:

```java
@SpringBootTest
```

or another test slice.

---

# Mistake 2

Expecting Faster Tests Than @WebMvcTest

Not true.

---

Because:

```text
Entire Application Loads
```

---

# Mistake 3

Not Using MockMvc

```java
@AutoConfigureMockMvc
```

creates MockMvc.

Use it.

---

# Common Interview Questions

## What is @AutoConfigureMockMvc?

Creates MockMvc automatically for tests.

---

## Why Is It Used With @SpringBootTest?

To perform HTTP testing against the full application.

---

## Does It Start Tomcat?

No.

---

## What Is MockMvc?

Spring framework for testing HTTP requests in memory.

---

## Difference Between @WebMvcTest and @AutoConfigureMockMvc?

Controller slice vs full application integration test.

---

# Enterprise Best Practice

For:

```text
Controller Unit Tests
```

use:

```java
@WebMvcTest
```

---

For:

```text
End-To-End Integration Tests
```

use:

```java
@SpringBootTest

@AutoConfigureMockMvc
```

---

Best combination:

```java
@SpringBootTest

@AutoConfigureMockMvc
class ApiIntegrationTest {

}
```

---

# Key Points To Remember

- `@AutoConfigureMockMvc` automatically creates MockMvc.
- Commonly used with `@SpringBootTest`.
- Enables HTTP endpoint testing.
- Does not require a real server.
- Supports JSON validation.
- Supports security testing.
- Supports request/response verification.
- Can disable filters.
- Useful for integration testing.
- Frequently asked Spring Boot testing interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@AutoConfigureMockMvc` | Create MockMvc |
| `@WebMvcTest` | MVC Slice Testing |
| `@SpringBootTest` | Full Application Testing |
| `@MockBean` | Mock Dependencies |
| `@WithMockUser` | Security Testing |

---

# Interview Shortcut

```java
@SpringBootTest

@AutoConfigureMockMvc
class ProductTest {

    @Autowired
    MockMvc mockMvc;
}
```

Execution:

```text
Application Context Loads
      ↓
MockMvc Created
      ↓
Mock HTTP Request Sent
      ↓
Controller Executes
      ↓
Response Verified
```

Think of it as:

```text
Testing APIs
Without Starting A Real Browser
Without Real Network Calls
```

while still using the entire Spring application.

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

@AutoConfigureMockMvc
class OrderApiTest {

}
```

Flow:

```text
Mock HTTP Request
      ↓
Controller
      ↓
Service
      ↓
Repository
      ↓
Database
      ↓
Response
```

Result:

```text
Real Integration Testing
Fast Execution
No Actual Server Needed
```

`@AutoConfigureMockMvc` is Spring Boot's testing annotation that automatically configures a `MockMvc` instance, allowing developers to test HTTP endpoints against the full application context without starting a real web server.