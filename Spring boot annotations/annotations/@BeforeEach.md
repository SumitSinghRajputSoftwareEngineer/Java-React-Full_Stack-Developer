
## Definition

`@BeforeEach` is a JUnit 5 lifecycle annotation used to execute a method **before every test method**.

It tells JUnit:

> Run this method before each `@Test` method in the test class.

Package:

```java
import org.junit.jupiter.api.BeforeEach;
```

---

# Why Do We Need @BeforeEach?

Suppose multiple tests need the same setup:

```java
Calculator calculator =
        new Calculator();
```

---

Without `@BeforeEach`:

```java
@Test
void testAdd() {

    Calculator c =
            new Calculator();

}
```

```java
@Test
void testSubtract() {

    Calculator c =
            new Calculator();

}
```

```java
@Test
void testMultiply() {

    Calculator c =
            new Calculator();

}
```

---

Code duplication.

---

Solution:

```java
@BeforeEach
```

---

# Basic Example

```java
class CalculatorTest {

    Calculator calculator;

    @BeforeEach
    void setup() {

        calculator =
            new Calculator();
    }

    @Test
    void testAdd() {

    }

    @Test
    void testSubtract() {

    }
}
```

---

Execution:

```text
setup()
   ↓
testAdd()

setup()
   ↓
testSubtract()
```

---

# Internal Workflow

```text
JUnit Starts
      ↓
@BeforeEach
      ↓
@Test
      ↓
@BeforeEach
      ↓
@Test
      ↓
Repeat
```

---

# Example

```java
class ProductTest {

    String product;

    @BeforeEach
    void setup() {

        product = "Laptop";
    }

    @Test
    void testName() {

        assertEquals(
            "Laptop",
            product
        );
    }

    @Test
    void testNotNull() {

        assertNotNull(
            product
        );
    }
}
```

---

Execution:

### First Test

```text
setup()
product = Laptop
     ↓
testName()
```

---

### Second Test

```text
setup()
product = Laptop
     ↓
testNotNull()
```

---

# Why Does It Run Before Every Test?

JUnit creates:

```text
Fresh Test Instance
```

for every test method.

---

Example:

```java
@Test
void test1() {

}
```

```java
@Test
void test2() {

}
```

---

Flow:

```text
Create Object
     ↓
@BeforeEach
     ↓
test1

Create Object
     ↓
@BeforeEach
     ↓
test2
```

---

This ensures:

```text
Test Isolation
```

---

# Common Use Cases

## Initialize Objects

```java
@BeforeEach
void setup() {

    service =
       new ProductService();
}
```

---

## Prepare Test Data

```java
@BeforeEach
void setup() {

    products =
       List.of(
          "Laptop",
          "Mobile"
       );
}
```

---

## Create Mock Objects

```java
@BeforeEach
void setup() {

    repository =
       mock(
          ProductRepository.class
       );
}
```

---

## Open Database Connections

```java
@BeforeEach
void setup() {

}
```

(Not common in Spring Boot)

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

    String expected;

    @BeforeEach
    void setup() {

        expected = "Laptop";
    }

    @Test
    void testProduct() {

        assertEquals(
            expected,
            service.getProduct()
        );
    }
}
```

---

# Execution Order

Consider:

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

Flow:

```text
setup()
    ↓
test1()
    ↓
cleanup()
```

---

For multiple tests:

```text
setup()
 ↓
test1()
 ↓
cleanup()

setup()
 ↓
test2()
 ↓
cleanup()
```

---

# Multiple @BeforeEach Methods

Possible:

```java
@BeforeEach
void setupDatabase() {

}
```

```java
@BeforeEach
void setupMocks() {

}
```

---

Execution order:

```text
Not Guaranteed
```

unless explicitly controlled.

---

Best practice:

```text
Use One @BeforeEach Method
```

---

# Example With Mockito

```java
class ProductServiceTest {

    ProductRepository repository;

    ProductService service;

    @BeforeEach
    void setup() {

        repository =
            mock(
                ProductRepository.class
            );

        service =
            new ProductService(
                repository
            );
    }
}
```

---

Now every test gets:

```text
Fresh Mocks
Fresh Service
```

---

# Example With Collections

```java
List<String> names;

@BeforeEach
void setup() {

    names =
      new ArrayList<>();
}
```

---

Test 1:

```java
names.add("A");
```

---

Test 2:

```java
assertTrue(
     names.isEmpty()
);
```

---

Works because:

```text
New List Created
Before Every Test
```

---

# @BeforeEach vs Constructor

Constructor:

```java
class ProductTest {

    ProductTest() {

    }
}
```

---

Runs:

```text
When Object Created
```

---

@BeforeEach:

```java
@BeforeEach
void setup() {

}
```

---

Runs:

```text
Before Every Test Method
```

---

JUnit recommends:

```java
@BeforeEach
```

for setup logic.

---

# @BeforeEach vs @BeforeAll

Interview Favorite.

---

## @BeforeEach

Runs:

```text
Before Every Test
```

---

Example:

```text
setup()
test1()

setup()
test2()

setup()
test3()
```

---

## @BeforeAll

Runs:

```text
Only Once
```

---

Example:

```text
setup()

test1()
test2()
test3()
```

---

Comparison:

| Feature | @BeforeEach | @BeforeAll |
|----------|------------|-------------|
| Runs Before Every Test | ✅ | ❌ |
| Runs Once | ❌ | ✅ |
| Instance Method | ✅ | ❌ (usually static) |
| Test Isolation | ✅ | ❌ |

---

# Common Mistakes

## Mistake 1

Using For Heavy Operations

Wrong:

```java
@BeforeEach
void setup() {

    StartServer();
}
```

---

Runs before every test.

---

Slow.

---

Use:

```java
@BeforeAll
```

instead.

---

# Mistake 2

Changing Shared State

Wrong:

```java
@BeforeEach
void setup() {

    count++;
}
```

---

Can make tests confusing.

---

# Mistake 3

Depending On Test Order

Wrong:

```text
test2 depends on test1 setup
```

---

Each test should be independent.

---

# Common Interview Questions

## What is @BeforeEach?

Runs before every test method.

---

## Which Package?

```java
org.junit.jupiter.api.BeforeEach
```

---

## How Many Times Does It Run?

Once for each test.

---

## Can Multiple Methods Be Annotated?

Yes.

---

## Difference Between @BeforeEach and @BeforeAll?

Every test vs once for entire class.

---

## Why Use It?

To initialize common test data and setup logic.

---

# Enterprise Best Practice

Use `@BeforeEach` for:

```text
Mock Creation
Object Initialization
Test Data Preparation
Resetting State
```

---

Avoid:

```text
Heavy Database Setup
Server Startup
Large File Loading
```

---

Use:

```java
@BeforeAll
```

for expensive operations.

---

# Key Points To Remember

- `@BeforeEach` runs before every test method.
- Part of JUnit 5 lifecycle annotations.
- Commonly used for setup logic.
- Helps remove duplicate code.
- Creates clean test state.
- Supports test isolation.
- Works with Spring Boot tests.
- Frequently used with Mockito.
- Different from `@BeforeAll`.
- Frequently asked interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@BeforeEach` | Before Every Test |
| `@AfterEach` | After Every Test |
| `@BeforeAll` | Before All Tests |
| `@AfterAll` | After All Tests |
| `@Test` | Execute Test |

---

# Interview Shortcut

```java
@BeforeEach
void setup() {

    service =
        new ProductService();
}
```

Execution:

```text
Create Test Object
      ↓
Run @BeforeEach
      ↓
Run @Test
      ↓
Repeat For Next Test
```

Think of it as:

```text
"Prepare The Environment
Before Every Test Runs"
```

---

# Real Production Scenario

Service Test:

```java
@SpringBootTest
class OrderServiceTest {

    Order order;

    @BeforeEach
    void setup() {

        order =
            new Order(
                1L,
                "Laptop"
            );
    }

    @Test
    void testOrderCreation() {

    }

    @Test
    void testOrderUpdate() {

    }
}
```

Result:

```text
Fresh Order Object
Before Every Test
No Shared State
Reliable Tests
```

`@BeforeEach` is a JUnit 5 lifecycle annotation that executes setup logic before every test method, ensuring each test starts with a clean and predictable environment.
