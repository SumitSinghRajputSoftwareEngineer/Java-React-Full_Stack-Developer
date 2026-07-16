
## Definition

`@SpyBean` is a Spring Boot testing annotation used to create a **Mockito Spy** for an existing Spring Bean.

It tells Spring:

> Wrap the real bean with a Mockito spy so that real methods execute by default, while still allowing specific methods to be mocked or verified.

Package:

```java
import org.springframework.boot.test.mock.mockito.SpyBean;
```

---

# Why Do We Need @SpyBean?

Suppose we have:

```java
@Service
public class PaymentService {

    public String processPayment() {

        return "PAYMENT SUCCESS";
    }

    public String generateReceipt() {

        return "RECEIPT GENERATED";
    }
}
```

---

During testing:

We want:

```text
Real Business Logic
```

but also want to:

```text
Verify Method Calls
Mock Specific Methods
```

---

Solution:

```java
@SpyBean
```

---

# What Is A Spy?

A spy is:

```text
Real Object
+
Mockito Monitoring
```

---

Real methods execute.

---

Unlike:

```java
@MockBean
```

which replaces everything with a mock.

---

# Internal Workflow

```text
Spring Creates Real Bean
          ↓
@SpyBean Wraps Bean
          ↓
Mockito Spy Created
          ↓
Real Methods Execute
          ↓
Calls Can Be Verified
```

---

# Basic Example

Service:

```java
@Service
public class ProductService {

    public String getProduct() {

        return "Laptop";
    }
}
```

---

Test:

```java
@SpringBootTest
class ProductTest {

    @SpyBean
    private ProductService service;

    @Test
    void test() {

        String result =
                service.getProduct();

        verify(service)
                .getProduct();
    }
}
```

---

Execution:

```text
Real Method Executes
       ↓
Returns "Laptop"
       ↓
Mockito Tracks Call
```

---

# What Happens Internally?

```java
@SpyBean
ProductService service;
```

Spring creates:

```java
ProductService realObject
```

then:

```java
Mockito.spy(realObject)
```

---

Result:

```text
Real Bean + Spy Wrapper
```

---

# Real Method Execution

Service:

```java
@Service
public class UserService {

    public String getName() {

        return "John";
    }
}
```

---

Test:

```java
@SpyBean
UserService service;

@Test
void test() {

    String result =
        service.getName();

    System.out.println(result);
}
```

---

Output:

```text
John
```

---

Because:

```text
Real Method Executes
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
Method Was Called
```

---

Useful for:

```text
Auditing
Logging
Notifications
Event Publishing
```

---

# Mocking Specific Methods

Service:

```java
@Service
public class PaymentService {

    public String process() {

        return "SUCCESS";
    }

    public String receipt() {

        return "RECEIPT";
    }
}
```

---

Spy:

```java
@SpyBean
PaymentService service;
```

---

Mock One Method:

```java
doReturn("FAILED")
.when(service)
.process();
```

---

Now:

```java
service.process();
```

returns:

```text
FAILED
```

---

But:

```java
service.receipt();
```

still returns:

```text
RECEIPT
```

---

# Why Use doReturn() Instead Of when()?

Interview Favorite.

---

Wrong:

```java
when(service.process())
      .thenReturn("FAILED");
```

---

Problem:

```text
Real Method Executes First
```

---

Correct:

```java
doReturn("FAILED")
.when(service)
.process();
```

---

No real method execution.

---

# Example With Controller

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

    @SpyBean
    ProductService service;

    @Autowired
    MockMvc mockMvc;
}
```

---

Controller gets:

```text
Spy Bean
```

instead of plain bean.

---

# Example With Repository

```java
@SpringBootTest
class ProductServiceTest {

    @SpyBean
    ProductRepository repository;
}
```

---

Allows:

```text
Real Queries
Verification
Partial Mocking
```

---

# Partial Mocking Example

Service:

```java
@Service
public class OrderService {

    public String validate() {

        return "VALID";
    }

    public String process() {

        return validate();
    }
}
```

---

Spy:

```java
@SpyBean
OrderService service;
```

---

Mock:

```java
doReturn("INVALID")
.when(service)
.validate();
```

---

Now:

```java
service.process();
```

returns:

```text
INVALID
```

---

Useful for testing edge cases.

---

# @SpyBean vs @MockBean

Most Important Interview Question.

---

## @MockBean

```java
@MockBean
ProductService service;
```

---

Creates:

```text
Pure Mockito Mock
```

---

Methods return:

```text
null
0
false
```

until stubbed.

---

## @SpyBean

```java
@SpyBean
ProductService service;
```

---

Creates:

```text
Real Bean
+
Mockito Spy
```

---

Methods execute normally.

---

Comparison:

| Feature | @MockBean | @SpyBean |
|----------|------------|----------|
| Real Methods Execute | ❌ | ✅ |
| Mockito Verification | ✅ | ✅ |
| Spring Bean | ✅ | ✅ |
| Partial Mocking | ❌ | ✅ |
| Full Mocking | ✅ | ❌ |

---

# Example

Service:

```java
public String getName() {

    return "Laptop";
}
```

---

MockBean:

```java
@MockBean
ProductService service;
```

Result:

```java
service.getName();
```

returns:

```text
null
```

---

SpyBean:

```java
@SpyBean
ProductService service;
```

Result:

```java
service.getName();
```

returns:

```text
Laptop
```

---

# @SpyBean vs @Spy

Mockito provides:

```java
@Spy
```

---

Example:

```java
@Spy
ProductService service;
```

---

Problem:

```text
Not Managed By Spring
```

---

`@SpyBean`

creates:

```text
Spring Managed Spy
```

---

Comparison:

| Feature | @Spy | @SpyBean |
|----------|-------|----------|
| Mockito Spy | ✅ | ✅ |
| Spring Bean | ❌ | ✅ |
| Dependency Injection | ❌ | ✅ |
| Bean Replacement | ❌ | ✅ |

---

# Multiple SpyBeans

```java
@SpyBean
ProductService productService;

@SpyBean
EmailService emailService;

@SpyBean
NotificationService notificationService;
```

---

Spring wraps all.

---

# Common Use Cases

## Verify Method Calls

```java
@SpyBean
```

---

## Partial Mocking

```java
@SpyBean
```

---

## Audit Verification

```java
verify(...)
```

---

## Event Publishing

```java
verify(...)
```

---

## Logging Validation

```java
verify(...)
```

---

## Complex Business Logic

```java
@SpyBean
```

---

# Common Mistakes

## Mistake 1

Using when() On Spy

Wrong:

```java
when(service.process())
    .thenReturn("FAILED");
```

---

May call real method.

---

Correct:

```java
doReturn("FAILED")
.when(service)
.process();
```

---

# Mistake 2

Using Spy When Mock Is Enough

If all methods are fake:

```java
@MockBean
```

is better.

---

# Mistake 3

Verifying Methods Never Called

```java
verify(service)
     .method();
```

fails if method wasn't executed.

---

# Common Interview Questions

## What is @SpyBean?

Creates a Mockito spy around a Spring bean.

---

## Difference Between @SpyBean And @MockBean?

Spy executes real methods.

Mock does not.

---

## Does @SpyBean Replace The Bean?

Yes.

It wraps the bean with a spy.

---

## Can We Verify Calls?

Yes.

```java
verify(...)
```

---

## Can We Mock Specific Methods?

Yes.

```java
doReturn(...)
```

---

## Why Use doReturn() Instead Of when()?

Avoids executing real methods during stubbing.

---

# Enterprise Best Practice

Use:

```java
@MockBean
```

when:

```text
Entire Dependency Should Be Mocked
```

---

Use:

```java
@SpyBean
```

when:

```text
Real Logic Should Execute
But Some Methods Need Monitoring
Or Partial Mocking
```

---

# Key Points To Remember

- `@SpyBean` creates a Mockito spy around a Spring bean.
- Real methods execute by default.
- Supports verification.
- Supports partial mocking.
- Uses Spring Dependency Injection.
- Frequently used with `@SpringBootTest`.
- Frequently asked in Spring Boot interviews.
- Prefer `doReturn()` while stubbing spy methods.
- Different from `@MockBean`.
- Useful for testing complex business logic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@MockBean` | Full Mock Bean |
| `@SpyBean` | Partial Mock Bean |
| `@Mock` | Mockito Mock |
| `@Spy` | Mockito Spy |
| `@SpringBootTest` | Full Context Test |

---

# Interview Shortcut

```java
@SpyBean
ProductService service;
```

Spring does:

```java
Mockito.spy(
    realProductService
);
```

Execution:

```text
Real Bean Created
      ↓
Spy Wrapper Added
      ↓
Real Methods Execute
      ↓
Mockito Tracks Calls
```

Think of it as:

```text
Real Spring Bean
+
Mockito Monitoring
```

---

# Real Production Scenario

Application:

```text
Order Service
      ↓
Audit Service
```

Test:

```java
@SpringBootTest
```

Spy:

```java
@SpyBean
AuditService auditService;
```

Verify:

```java
verify(auditService)
       .writeAudit();
```

Result:

```text
Real Business Logic Runs
Audit Execution Verified
No Need To Mock Entire Service
```

`@SpyBean` is Spring Boot's testing annotation that wraps an existing Spring bean with a Mockito spy, allowing real methods to execute while still supporting verification and selective method stubbing.