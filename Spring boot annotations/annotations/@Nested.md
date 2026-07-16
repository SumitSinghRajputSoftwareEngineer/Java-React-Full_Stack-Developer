
## Definition

`@Nested` is a JUnit 5 annotation used to create **hierarchical and logically grouped test classes** inside a parent test class.

It tells JUnit:

> Treat this inner class as a separate group of related test cases.

Package:

```java
import org.junit.jupiter.api.Nested;
```

---

# Why Do We Need @Nested?

As applications grow, test classes become large.

Example:

```java
class UserServiceTest {

    @Test
    void createUser() {}

    @Test
    void updateUser() {}

    @Test
    void deleteUser() {}

    @Test
    void loginUser() {}

    @Test
    void logoutUser() {}
}
```

---

Problem:

```text
Difficult To Organize
```

---

Solution:

```java
@Nested
```

---

Group related tests:

```text
User Creation Tests

User Login Tests

User Deletion Tests
```

---

# Basic Example

```java
class UserServiceTest {

    @Nested
    class CreateUserTests {

        @Test
        void createUser() {

        }
    }

    @Nested
    class LoginTests {

        @Test
        void loginUser() {

        }
    }
}
```

---

Execution:

```text
UserServiceTest

 ├── CreateUserTests
 │     └── createUser()

 └── LoginTests
       └── loginUser()
```

---

# Internal Workflow

JUnit sees:

```java
@Nested
class LoginTests {

}
```

as:

```text
Separate Test Group
Inside Parent Test Class
```

---

# Real Example

```java
class CalculatorTest {

    @Nested
    class AdditionTests {

        @Test
        void addPositiveNumbers() {

        }

        @Test
        void addNegativeNumbers() {

        }
    }

    @Nested
    class DivisionTests {

        @Test
        void divideNumbers() {

        }
    }
}
```

---

Report:

```text
CalculatorTest

 AdditionTests
   addPositiveNumbers
   addNegativeNumbers

 DivisionTests
   divideNumbers
```

---

# Using @DisplayName With @Nested

Very common.

```java
@Nested
@DisplayName(
    "User Creation Tests"
)
class CreateUserTests {

}
```

---

Report:

```text
User Creation Tests
```

instead of:

```text
CreateUserTests
```

---

# Complete Example

```java
@DisplayName(
    "User Service"
)
class UserServiceTest {

    @Nested
    @DisplayName(
        "User Creation"
    )
    class CreateUser {

        @Test
        @DisplayName(
            "Should create valid user"
        )
        void createUser() {

        }
    }

    @Nested
    @DisplayName(
        "User Login"
    )
    class Login {

        @Test
        @DisplayName(
            "Should login successfully"
        )
        void login() {

        }
    }
}
```

---

Report:

```text
User Service

  User Creation
      Should create valid user

  User Login
      Should login successfully
```

---

# Nested Lifecycle

Parent lifecycle methods execute.

---

Example:

```java
@BeforeEach
void setup() {

}
```

```java
@Nested
class LoginTests {

    @Test
    void login() {

    }
}
```

---

Execution:

```text
Parent Setup
      ↓
Nested Test
```

---

# Parent + Nested Lifecycle Example

```java
class UserTest {

    @BeforeEach
    void setup() {

        System.out.println(
            "Parent Setup"
        );
    }

    @Nested
    class LoginTests {

        @Test
        void login() {

        }
    }
}
```

---

Output:

```text
Parent Setup
```

before login test.

---

# Nested Class Can Have Its Own Lifecycle

```java
@Nested
class LoginTests {

    @BeforeEach
    void loginSetup() {

    }

    @Test
    void login() {

    }
}
```

---

Execution:

```text
Parent Setup
       ↓
Nested Setup
       ↓
Test
```

---

# Multiple Levels Of Nesting

Possible.

```java
class UserTest {

    @Nested
    class LoginTests {

        @Nested
        class AdminLoginTests {

        }
    }
}
```

---

Structure:

```text
UserTest
   ↓
LoginTests
   ↓
AdminLoginTests
```

---

Not recommended beyond 2-3 levels.

---

# Spring Boot Example

```java
@SpringBootTest
class ProductServiceTest {

    @Nested
    class ProductCreationTests {

        @Test
        void createProduct() {

        }
    }

    @Nested
    class ProductDeletionTests {

        @Test
        void deleteProduct() {

        }
    }
}
```

---

Helps organize:

```text
Creation
Update
Delete
Search
```

tests.

---

# Common Use Cases

## Grouping Related Tests

```java
@Nested
```

---

## Organizing Large Test Classes

```java
@Nested
```

---

## Scenario-Based Testing

```java
Success Cases
Failure Cases
Validation Cases
```

---

## Domain-Based Testing

```java
User Tests
Order Tests
Payment Tests
```

---

# Example: Success vs Failure Tests

```java
class PaymentTest {

    @Nested
    class SuccessCases {

        @Test
        void paymentSuccess() {

        }
    }

    @Nested
    class FailureCases {

        @Test
        void paymentFailure() {

        }
    }
}
```

---

Report:

```text
PaymentTest

 SuccessCases
   paymentSuccess

 FailureCases
   paymentFailure
```

---

# @Nested vs Separate Test Classes

Without @Nested:

```java
CreateUserTest
LoginTest
DeleteUserTest
```

---

With @Nested:

```java
UserServiceTest

 ├ CreateUserTests
 ├ LoginTests
 └ DeleteUserTests
```

---

Both are valid.

---

Use `@Nested` when tests share context.

---

# Common Mistakes

## Mistake 1

Making Nested Class Static

Wrong:

```java
@Nested
static class LoginTests {

}
```

---

Error:

```text
Nested Class Must Be Non-Static
```

---

Correct:

```java
@Nested
class LoginTests {

}
```

---

# Mistake 2

Too Many Levels

Wrong:

```text
5-6 Levels Deep
```

---

Hard to read.

---

# Mistake 3

Using For Unrelated Tests

Wrong:

```text
User Tests
Order Tests
Payment Tests
```

inside same file.

---

Keep related tests together.

---

# Common Interview Questions

## What is @Nested?

Groups related tests into inner classes.

---

## Which JUnit Version Supports It?

JUnit 5.

---

## Can Nested Classes Be Static?

No.

Must be non-static.

---

## Can Nested Classes Have @BeforeEach?

Yes.

---

## Does Parent @BeforeEach Execute?

Yes.

---

## Can @DisplayName Be Used?

Yes.

Very common.

---

# Enterprise Best Practice

Use `@Nested` for:

```text
Success Scenarios
Failure Scenarios
Validation Tests
Feature-Based Grouping
```

---

Example:

```java
UserServiceTest

 ├ CreationTests
 ├ UpdateTests
 ├ DeleteTests
 └ ValidationTests
```

---

Improves:

```text
Readability
Maintainability
Test Reports
```

---

# Key Points To Remember

- `@Nested` creates grouped test classes.
- Available in JUnit 5.
- Helps organize large test suites.
- Nested classes must be non-static.
- Supports lifecycle annotations.
- Works with `@DisplayName`.
- Parent lifecycle methods execute first.
- Useful for scenario-based testing.
- Improves test reports.
- Frequently asked interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Test` | Test Method |
| `@DisplayName` | Custom Name |
| `@Nested` | Group Related Tests |
| `@BeforeEach` | Setup Before Test |
| `@AfterEach` | Cleanup After Test |

---

# Interview Shortcut

```java
@Nested
class LoginTests {

    @Test
    void loginSuccess() {

    }

    @Test
    void loginFailure() {

    }
}
```

Report:

```text
LoginTests
   loginSuccess
   loginFailure
```

Think of it as:

```text
"Folder Structure
For Test Cases"
```

---

# Real Production Scenario

```java
@SpringBootTest
class OrderServiceTest {

    @Nested
    class CreateOrderTests {

    }

    @Nested
    class UpdateOrderTests {

    }

    @Nested
    class CancelOrderTests {

    }

    @Nested
    class ValidationTests {

    }
}
```

CI/CD Report:

```text
OrderServiceTest

 CreateOrderTests
 UpdateOrderTests
 CancelOrderTests
 ValidationTests
```

Result:

```text
Clean Test Organization
Better Readability
Easier Maintenance
Professional Test Structure
```

`@Nested` is a JUnit 5 annotation that allows grouping related test cases into inner classes, creating a hierarchical and organized test structure that improves readability and maintainability.