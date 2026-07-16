
## Definition

`@Test` is the fundamental JUnit annotation used to mark a method as a **test case**.

It tells the testing framework:

> Execute this method as a test during test execution.

For modern Spring Boot applications, it comes from JUnit 5.

Package:

```java
import org.junit.jupiter.api.Test;
```

---

# Why Do We Need @Test?

Without:

```java
@Test
public void testAdd() {

}
```

JUnit treats it as:

```text
Normal Java Method
```

and ignores it.

---

With:

```java
@Test
public void testAdd() {

}
```

JUnit treats it as:

```text
Executable Test Case
```

---

# Basic Example

```java
import org.junit.jupiter.api.Test;

class CalculatorTest {

    @Test
    void testAddition() {

        int result = 5 + 5;

        assertEquals(10, result);
    }
}
```

---

Execution:

```text
JUnit Runner
      ↓
Find @Test
      ↓
Execute Method
      ↓
Verify Assertions
      ↓
Pass / Fail
```

---

# Internal Workflow

```text
JUnit Starts
      ↓
Scan Classes
      ↓
Find @Test Methods
      ↓
Invoke Methods
      ↓
Evaluate Assertions
      ↓
Generate Report
```

---

# Example

```java
@Test
void testName() {

    String name = "Sumit";

    assertEquals(
        "Sumit",
        name
    );
}
```

---

Result:

```text
PASSED
```

---

# Failed Test Example

```java
@Test
void testName() {

    assertEquals(
        "John",
        "Mike"
    );
}
```

---

Output:

```text
Expected : John
Actual   : Mike
```

---

Result:

```text
FAILED
```

---

# Assertions With @Test

Most tests contain assertions.

---

Example:

```java
@Test
void testSum() {

    int result = 10 + 20;

    assertEquals(
        30,
        result
    );
}
```

---

Without assertions:

```java
@Test
void test() {

}
```

---

Test executes but verifies nothing.

---

# Multiple Test Methods

```java
class CalculatorTest {

    @Test
    void testAdd() {

    }

    @Test
    void testSubtract() {

    }

    @Test
    void testMultiply() {

    }
}
```

---

JUnit executes:

```text
testAdd()
testSubtract()
testMultiply()
```

independently.

---

# Exception Testing

JUnit 5:

```java
@Test
void testException() {

    assertThrows(
        ArithmeticException.class,
        () -> {
            int x = 10 / 0;
        }
    );
}
```

---

Result:

```text
PASS
```

because exception occurred.

---

# Testing Null Values

```java
@Test
void testNull() {

    String name = null;

    assertNull(name);
}
```

---

# Testing Not Null

```java
@Test
void testNotNull() {

    String name = "Laptop";

    assertNotNull(name);
}
```

---

# Testing Boolean Conditions

```java
@Test
void testCondition() {

    assertTrue(
        10 > 5
    );
}
```

---

```java
@Test
void testFalse() {

    assertFalse(
        10 < 5
    );
}
```

---

# Testing Collections

```java
@Test
void testList() {

    List<String> list =
            List.of("A","B");

    assertEquals(
        2,
        list.size()
    );
}
```

---

# Testing Arrays

```java
@Test
void testArray() {

    int[] expected =
            {1,2,3};

    int[] actual =
            {1,2,3};

    assertArrayEquals(
            expected,
            actual
    );
}
```

---

# Timeout Testing

```java
@Test
void testPerformance() {

    assertTimeout(
        Duration.ofSeconds(2),
        () -> {
            Thread.sleep(1000);
        }
    );
}
```

---

Passes because:

```text
1 second < 2 seconds
```

---

# Spring Boot Example

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
class ProductServiceTest {

    @Autowired
    ProductService service;

    @Test
    void testProduct() {

        assertEquals(
            "Laptop",
            service.getProduct()
        );
    }
}
```

---

# JUnit 4 vs JUnit 5

## JUnit 4

Package:

```java
import org.junit.Test;
```

---

## JUnit 5

Package:

```java
import org.junit.jupiter.api.Test;
```

---

Modern Spring Boot:

```text
Uses JUnit 5
```

---

# Common Assertions

## assertEquals

```java
assertEquals(10, result);
```

---

## assertNotEquals

```java
assertNotEquals(5, result);
```

---

## assertNull

```java
assertNull(value);
```

---

## assertNotNull

```java
assertNotNull(value);
```

---

## assertTrue

```java
assertTrue(condition);
```

---

## assertFalse

```java
assertFalse(condition);
```

---

## assertThrows

```java
assertThrows(
    RuntimeException.class,
    () -> method()
);
```

---

## assertAll

```java
assertAll(
    () -> assertEquals(1,a),
    () -> assertEquals(2,b)
);
```

---

# Test Naming Best Practices

Bad:

```java
@Test
void test1() {

}
```

---

Good:

```java
@Test
void shouldReturnProductWhenIdExists() {

}
```

---

Better readability.

---

# Lifecycle Around @Test

```java
@BeforeEach
```

runs before every test.

---

```java
@AfterEach
```

runs after every test.

---

Example:

```java
@BeforeEach
void setup() {

}
```

```java
@Test
void test1() {

}
```

```java
@AfterEach
void cleanup() {

}
```

---

Execution:

```text
setup()
   ↓
test1()
   ↓
cleanup()
```

---

# Common Mistakes

## Mistake 1

Wrong Import

Wrong:

```java
import org.junit.Test;
```

for JUnit 5 projects.

---

Correct:

```java
import org.junit.jupiter.api.Test;
```

---

# Mistake 2

No Assertions

```java
@Test
void test() {

}
```

---

Test passes but verifies nothing.

---

# Mistake 3

Dependent Tests

Wrong:

```text
Test 2 Depends On Test 1
```

---

Each test should be independent.

---

# Common Interview Questions

## What is @Test?

Marks a method as a JUnit test case.

---

## Which Package Is Used In Spring Boot?

```java
org.junit.jupiter.api.Test
```

---

## Can Private Methods Be Annotated?

No.

---

Wrong:

```java
@Test
private void test() {

}
```

---

JUnit won't execute it properly.

---

## Can We Have Multiple @Test Methods?

Yes.

---

## Does Test Order Matter?

No.

Tests should be independent.

---

# Enterprise Best Practice

Use:

```java
@Test
```

for:

```text
Unit Tests
Integration Tests
Repository Tests
Controller Tests
Service Tests
```

---

Keep tests:

```text
Independent
Fast
Readable
Repeatable
```

---

# Key Points To Remember

- `@Test` marks a method as a test case.
- Comes from JUnit 5 in modern Spring Boot projects.
- JUnit discovers and executes test methods automatically.
- Usually contains assertions.
- Can be combined with Spring testing annotations.
- Supports exception testing.
- Supports timeout testing.
- Supports collection and array testing.
- Most important testing annotation.
- Frequently asked interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Test` | Execute Test Method |
| `@BeforeEach` | Run Before Each Test |
| `@AfterEach` | Run After Each Test |
| `@SpringBootTest` | Load Full Spring Context |
| `@WebMvcTest` | Controller Testing |
| `@DataJpaTest` | Repository Testing |

---

# Interview Shortcut

```java
@Test
void shouldAddNumbers() {

    assertEquals(
        10,
        5 + 5
    );
}
```

Execution:

```text
JUnit Finds @Test
      ↓
Runs Method
      ↓
Checks Assertions
      ↓
PASS / FAIL
```

Think of it as:

```text
"This Method Is A Test Case"
```

---

# Real Production Scenario

Service:

```java
public String getOrderStatus() {

    return "SUCCESS";
}
```

Test:

```java
@Test
void shouldReturnSuccess() {

    assertEquals(
        "SUCCESS",
        service.getOrderStatus()
    );
}
```

Result:

```text
Business Logic Verified
Automatic Regression Detection
Higher Code Quality
```

`@Test` is the core JUnit annotation that identifies a method as a test case, allowing automated execution and verification of application behavior through assertions.