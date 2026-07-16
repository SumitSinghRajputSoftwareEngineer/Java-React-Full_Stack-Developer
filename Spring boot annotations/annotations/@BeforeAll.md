
## Definition

`@BeforeAll` is a JUnit 5 lifecycle annotation used to execute a method **once before all test methods** in a test class.

It tells JUnit:

> Run this method one time before any `@Test` method is executed.

Package:

```java
import org.junit.jupiter.api.BeforeAll;
```

---

# Why Do We Need @BeforeAll?

Some operations are expensive:

```text
Starting Database
Creating Test Containers
Loading Large Files
Initializing Cache
Creating Shared Resources
Starting Embedded Servers
```

---

Running them before every test using:

```java
@BeforeEach
```

would be slow.

---

Solution:

```java
@BeforeAll
```

---

# Basic Example

```java
class ProductTest {

    @BeforeAll
    static void setup() {

        System.out.println(
            "Executed Once"
        );
    }

    @Test
    void test1() {

    }

    @Test
    void test2() {

    }
}
```

---

Execution:

```text
setup()

test1()
test2()
```

---

Output:

```text
Executed Once
```

Only once.

---

# Internal Workflow

```text
JUnit Starts
      ↓
@BeforeAll
      ↓
@Test 1
      ↓
@Test 2
      ↓
@Test 3
      ↓
@AfterAll
```

---

# Why Must It Be Static?

Most important interview question.

---

JUnit creates a new instance of the test class for each test.

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

Internally:

```text
Create Instance #1
Run test1()

Create Instance #2
Run test2()
```

---

At the time `@BeforeAll` runs:

```text
No Test Instance Exists Yet
```

---

Therefore:

```java
@BeforeAll
static void setup() {

}
```

must be:

```text
Static
```

---

# Example

```java
class UserTest {

    static List<String> users;

    @BeforeAll
    static void setup() {

        users =
            new ArrayList<>();

        users.add("John");
        users.add("Mike");
    }

    @Test
    void testSize() {

        assertEquals(
            2,
            users.size()
        );
    }

    @Test
    void testContains() {

        assertTrue(
            users.contains("John")
        );
    }
}
```

---

Execution:

```text
Create List Once
       ↓
Run Multiple Tests
```

---

# Common Use Cases

## Database Initialization

```java
@BeforeAll
static void setupDatabase() {

}
```

---

## Starting TestContainers

```java
@BeforeAll
static void startContainer() {

}
```

---

## Creating Shared Cache

```java
@BeforeAll
static void initCache() {

}
```

---

## Loading Large Files

```java
@BeforeAll
static void loadFile() {

}
```

---

## Starting Embedded Server

```java
@BeforeAll
static void startServer() {

}
```

---

# Spring Boot Example

```java
@SpringBootTest
class ProductServiceTest {

    @BeforeAll
    static void setup() {

        System.out.println(
            "Executed Once"
        );
    }

    @Test
    void test1() {

    }

    @Test
    void test2() {

    }
}
```

---

Execution:

```text
setup()

test1()
test2()
```

---

# Example With Testcontainers

Very common in enterprise projects.

---

```java
@BeforeAll
static void startContainer() {

    postgres.start();
}
```

---

Without:

```java
@BeforeEach
```

container starts before every test.

---

Very slow.

---

With:

```java
@BeforeAll
```

container starts:

```text
Only Once
```

---

# Execution Order

Consider:

```java
@BeforeAll
```

```java
@BeforeEach
```

```java
@Test
```

```java
@AfterEach
```

```java
@AfterAll
```

---

Flow:

```text
@BeforeAll
      ↓
@BeforeEach
      ↓
@Test
      ↓
@AfterEach

Repeat Tests

      ↓
@AfterAll
```

---

Example:

```text
setupOnce()

setup()
test1()
cleanup()

setup()
test2()
cleanup()

finalCleanup()
```

---

# Shared State Example

```java
class CacheTest {

    static Map<Long,String> cache;

    @BeforeAll
    static void setup() {

        cache =
            new HashMap<>();

        cache.put(
            1L,
            "Laptop"
        );
    }
}
```

---

Loaded:

```text
One Time
```

---

Available:

```text
For All Tests
```

---

# Non-Static @BeforeAll

Possible using:

```java
@TestInstance(
    TestInstance.Lifecycle.PER_CLASS
)
```

---

Example:

```java
@TestInstance(
    Lifecycle.PER_CLASS
)
class ProductTest {

    @BeforeAll
    void setup() {

    }
}
```

---

Now:

```text
Static Not Required
```

---

Interview Bonus Point.

---

# @BeforeAll vs @BeforeEach

Most Important Interview Question.

---

## @BeforeAll

Runs:

```text
Once
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

Comparison:

| Feature | @BeforeAll | @BeforeEach |
|----------|-----------|-------------|
| Runs Once | ✅ | ❌ |
| Runs Before Every Test | ❌ | ✅ |
| Performance | Faster | Slower |
| Static Required | Usually Yes | No |

---

# @BeforeAll vs Constructor

Constructor:

```java
ProductTest() {

}
```

---

Runs:

```text
Every Time Instance Created
```

---

@BeforeAll:

```java
@BeforeAll
```

---

Runs:

```text
Only Once
```

---

# Common Mistakes

## Mistake 1

Forgetting static

Wrong:

```java
@BeforeAll
void setup() {

}
```

---

Error:

```text
Method Must Be Static
```

(unless PER_CLASS)

---

# Mistake 2

Modifying Shared State

Wrong:

```java
@BeforeAll
static void setup() {

    sharedCounter++;
}
```

---

Can make tests dependent.

---

# Mistake 3

Using For Test-Specific Data

Wrong:

```java
@BeforeAll
```

for:

```text
Fresh Object Per Test
```

---

Use:

```java
@BeforeEach
```

instead.

---

# Common Interview Questions

## What is @BeforeAll?

Runs once before all tests.

---

## Why Is It Static?

No test instance exists yet.

---

## How Many Times Does It Run?

Exactly once per test class.

---

## Difference Between @BeforeAll and @BeforeEach?

Once vs before every test.

---

## Can It Be Non-Static?

Yes, with:

```java
@TestInstance(
    Lifecycle.PER_CLASS
)
```

---

# Enterprise Best Practice

Use `@BeforeAll` for:

```text
Starting Containers
Database Setup
Loading Huge Data
Starting Embedded Servers
Shared Resource Initialization
```

---

Avoid using it for:

```text
Test-Specific Objects
```

---

Use:

```java
@BeforeEach
```

instead for isolated test data.

---

# Key Points To Remember

- `@BeforeAll` runs once before all tests.
- Part of JUnit 5 lifecycle annotations.
- Usually requires a static method.
- Improves performance for expensive setup operations.
- Commonly used with Testcontainers.
- Executes before any `@Test`.
- Can be non-static with `@TestInstance(PER_CLASS)`.
- Frequently paired with `@AfterAll`.
- Different from `@BeforeEach`.
- Frequently asked interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@BeforeAll` | Before All Tests Once |
| `@BeforeEach` | Before Every Test |
| `@AfterEach` | After Every Test |
| `@AfterAll` | After All Tests Once |
| `@Test` | Execute Test |

---

# Interview Shortcut

```java
@BeforeAll
static void setup() {

    startDatabase();
}
```

Execution:

```text
Run Once
     ↓
All Tests Execute
```

Think of it as:

```text
"Initialize Expensive Shared Resources
Only One Time"
```

---

# Real Production Scenario

Integration Test:

```java
@SpringBootTest
class OrderServiceTest {

    @BeforeAll
    static void setup() {

        postgresContainer.start();
    }
}
```

Flow:

```text
Start PostgreSQL Container
          ↓
Run 100 Tests
          ↓
Container Remains Running
```

Result:

```text
Fast Execution
Reduced Startup Cost
Efficient Resource Usage
```

`@BeforeAll` is a JUnit 5 lifecycle annotation that executes setup logic once before all test methods, making it ideal for expensive initialization tasks and shared resources.