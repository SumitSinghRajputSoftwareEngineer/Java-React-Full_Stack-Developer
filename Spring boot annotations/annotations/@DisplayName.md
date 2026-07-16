
## Definition

`@DisplayName` is a JUnit 5 annotation used to provide a **custom, human-readable name** for a test class or test method.

It tells JUnit:

> Display this text instead of the actual method or class name in test reports and IDEs.

Package:

```java
import org.junit.jupiter.api.DisplayName;
```

---

# Why Do We Need @DisplayName?

Consider a test method:

```java
@Test
void shouldReturnProductWhenIdExists() {

}
```

JUnit report:

```text
shouldReturnProductWhenIdExists()
```

---

Readable, but sometimes we want:

```text
✓ Should return product when valid ID exists
```

---

Solution:

```java
@DisplayName
```

---

# Basic Example

```java
@Test
@DisplayName(
    "Should add two numbers successfully"
)
void testAddition() {

}
```

---

Test Report:

```text
Should add two numbers successfully
```

instead of:

```text
testAddition
```

---

# Internal Workflow

```text
JUnit Finds Test
       ↓
Reads @DisplayName
       ↓
Uses Display Name
In Reports
IDE
CI/CD Results
```

---

# Example Without @DisplayName

```java
@Test
void shouldCalculateTotalPrice() {

}
```

---

Report:

```text
shouldCalculateTotalPrice()
```

---

# Example With @DisplayName

```java
@Test
@DisplayName(
    "Calculate total price of cart"
)
void shouldCalculateTotalPrice() {

}
```

---

Report:

```text
Calculate total price of cart
```

---

Much easier to understand.

---

# Using @DisplayName On Class

```java
@DisplayName(
    "Product Service Tests"
)
class ProductServiceTest {

}
```

---

JUnit Report:

```text
Product Service Tests
```

---

Instead of:

```text
ProductServiceTest
```

---

# Class + Method Example

```java
@DisplayName(
    "Calculator Tests"
)
class CalculatorTest {

    @Test
    @DisplayName(
        "Add two numbers"
    )
    void testAdd() {

    }

    @Test
    @DisplayName(
        "Subtract two numbers"
    )
    void testSubtract() {

    }
}
```

---

Report:

```text
Calculator Tests

  Add two numbers

  Subtract two numbers
```

---

# Using Spaces

Normal method names:

```java
void shouldReturnUserWhenIdExists()
```

---

With:

```java
@DisplayName(
    "Should return user when ID exists"
)
```

---

Report becomes more readable.

---

# Using Special Characters

Allowed:

```java
@Test
@DisplayName(
    "✓ User created successfully"
)
void testUserCreation() {

}
```

---

Report:

```text
✓ User created successfully
```

---

Also valid:

```java
@DisplayName("🚀 Application Starts")
```

---

Though enterprise teams usually avoid emojis.

---

# Spring Boot Example

```java
@SpringBootTest
@DisplayName(
    "Product Service Tests"
)
class ProductServiceTest {

    @Autowired
    ProductService service;

    @Test
    @DisplayName(
        "Should return laptop"
    )
    void testProduct() {

        assertEquals(
            "Laptop",
            service.getProduct()
        );
    }
}
```

---

Report:

```text
Product Service Tests

   Should return laptop
```

---

# Example With Assertions

```java
@Test
@DisplayName(
    "Verify addition operation"
)
void testAddition() {

    assertEquals(
        10,
        5 + 5
    );
}
```

---

Report:

```text
Verify addition operation
```

---

# Example With Exception Testing

```java
@Test
@DisplayName(
    "Should throw ArithmeticException"
)
void testDivision() {

    assertThrows(
        ArithmeticException.class,
        () -> 10 / 0
    );
}
```

---

Report:

```text
Should throw ArithmeticException
```

---

# Nested Tests Example

```java
@Nested
@DisplayName(
    "User Service"
)
class UserTests {

}
```

---

Report:

```text
User Service
```

---

Useful for grouping tests.

---

# @DisplayName vs Method Name

Example:

```java
@Test
void test1() {

}
```

---

Bad report:

```text
test1
```

---

Better:

```java
@Test
@DisplayName(
    "Verify user creation"
)
void test1() {

}
```

---

Report:

```text
Verify user creation
```

---

# Display Name Generation

JUnit also supports:

```java
@DisplayNameGeneration
```

---

Example:

```java
@DisplayNameGeneration(
    ReplaceUnderscores.class
)
```

---

Method:

```java
void should_return_user()
```

---

Report:

```text
should return user
```

---

Interview bonus point.

---

# Common Use Cases

## Better Test Reports

```java
@DisplayName
```

---

## CI/CD Reports

```java
Jenkins
GitHub Actions
Azure DevOps
```

---

## Team Readability

```java
@DisplayName
```

---

## Business-Friendly Reports

```java
Should create customer successfully
```

---

# Common Mistakes

## Mistake 1

Using Meaningless Names

Wrong:

```java
@DisplayName("Test 1")
```

---

Correct:

```java
@DisplayName(
 "Should return active users"
)
```

---

# Mistake 2

Too Long

Wrong:

```java
@DisplayName(
"Verify that the user service returns all active users after performing validation and filtering operations"
)
```

---

Keep concise.

---

# Mistake 3

Contradicting Method Name

Wrong:

```java
@DisplayName("Delete User")
void createUser() {

}
```

---

Confusing.

---

# Common Interview Questions

## What is @DisplayName?

Provides custom names for test classes and methods.

---

## Does It Affect Execution?

No.

Only affects display/reporting.

---

## Can It Be Applied To Class?

Yes.

---

## Can It Be Applied To Method?

Yes.

---

## Is It Mandatory?

No.

Optional.

---

## Does It Change Method Name?

No.

Only changes displayed text.

---

# Enterprise Best Practice

Use:

```java
@DisplayName
```

for:

```text
Readable Test Reports
Business-Friendly Names
CI/CD Visibility
Team Collaboration
```

---

Good Example:

```java
@DisplayName(
    "Should create order successfully"
)
```

---

Avoid:

```java
@DisplayName("Test")
```

---

# Key Points To Remember

- `@DisplayName` provides a custom test name.
- Works on classes and methods.
- Improves readability of reports.
- Does not affect execution.
- Supports spaces and special characters.
- Commonly used with Spring Boot tests.
- Useful in CI/CD pipelines.
- Frequently asked JUnit 5 interview topic.
- Makes test reports business-friendly.
- Purely a presentation feature.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Test` | Marks Test Method |
| `@DisplayName` | Custom Test Name |
| `@BeforeEach` | Setup Before Test |
| `@AfterEach` | Cleanup After Test |
| `@Nested` | Group Related Tests |

---

# Interview Shortcut

```java
@Test
@DisplayName(
    "Should save user successfully"
)
void saveUser() {

}
```

JUnit Report:

```text
Should save user successfully
```

instead of:

```text
saveUser
```

Think of it as:

```text
"Human-Friendly Name
For Tests"
```

---

# Real Production Scenario

Service Test:

```java
@SpringBootTest
@DisplayName(
    "Order Service Tests"
)
class OrderServiceTest {

    @Test
    @DisplayName(
        "Should create order successfully"
    )
    void createOrder() {

    }

    @Test
    @DisplayName(
        "Should reject invalid order"
    )
    void rejectOrder() {

    }
}
```

CI/CD Report:

```text
Order Service Tests

 ✓ Should create order successfully

 ✓ Should reject invalid order
```

Result:

```text
Readable Reports
Easy Debugging
Better Team Collaboration
```

`@DisplayName` is a JUnit 5 annotation that allows custom human-readable names for test classes and methods, improving the clarity and readability of test reports without affecting test execution.