
## Definition

`@AfterAll` is a JUnit 5 lifecycle annotation used to execute a method **once after all test methods** in a test class have finished.

It tells JUnit:

> Run this method one time after every `@Test` method has completed.

Package:

```java
import org.junit.jupiter.api.AfterAll;
```

---

# Why Do We Need @AfterAll?

Some resources are created once and shared by all tests:

```text
Database Containers
Embedded Servers
Message Brokers
Caches
Large Files
External Connections
```

---

After all tests finish, these resources should be released.

---

Without cleanup:

```text
Memory Leaks
Port Conflicts
Container Leaks
Resource Wastage
Slow CI/CD Pipelines
```

---

Solution:

```java
@AfterAll
```

---

# Basic Example

```java
class ProductTest {

    @AfterAll
    static void cleanup() {

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
test1()
test2()

cleanup()
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

Most asked interview question.

---

JUnit creates:

```text
Separate Test Instance
For Every Test Method
```

Example:

```text
Create Instance #1
Run test1()

Create Instance #2
Run test2()
```

---

When all tests finish:

```text
No Specific Instance Exists
```

---

Therefore:

```java
@AfterAll
static void cleanup() {

}
```

must usually be:

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
    }

    @AfterAll
    static void cleanup() {

        users.clear();
    }
}
```

---

Execution:

```text
Create List
     ↓
Run Tests
     ↓
Clear List
```

---

# Common Use Cases

## Stop Database Container

```java
@AfterAll
static void stopContainer() {

}
```

---

## Close Embedded Server

```java
@AfterAll
static void stopServer() {

}
```

---

## Shutdown ExecutorService

```java
@AfterAll
static void shutdown() {

    executor.shutdown();
}
```

---

## Release Cache

```java
@AfterAll
static void cleanupCache() {

}
```

---

## Delete Large Temporary Files

```java
@AfterAll
static void cleanupFiles() {

}
```

---

# Spring Boot Example

```java
@SpringBootTest
class ProductServiceTest {

    @AfterAll
    static void cleanup() {

        System.out.println(
            "Cleanup Once"
        );
    }
}
```

---

Execution:

```text
All Tests
     ↓
Cleanup Once
```

---

# Example With Testcontainers

Very common in enterprise projects.

---

Setup:

```java
@BeforeAll
static void start() {

    postgres.start();
}
```

---

Cleanup:

```java
@AfterAll
static void stop() {

    postgres.stop();
}
```

---

Execution:

```text
Start Container
      ↓
Run Tests
      ↓
Stop Container
```

---

Without:

```java
@AfterAll
```

container may remain running.

---

# Example With ExecutorService

```java
static ExecutorService executor;
```

---

Setup:

```java
@BeforeAll
static void setup() {

    executor =
        Executors
        .newFixedThreadPool(10);
}
```

---

Cleanup:

```java
@AfterAll
static void cleanup() {

    executor.shutdown();
}
```

---

Prevents:

```text
Thread Leaks
```

---

# Execution Order

Example:

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

Repeat

      ↓
@AfterAll
```

---

Detailed:

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

# Runs Even If Tests Fail

Important interview point.

---

Example:

```java
@Test
void test() {

    throw new RuntimeException();
}
```

---

Cleanup:

```java
@AfterAll
static void cleanup() {

}
```

---

JUnit still attempts:

```text
Final Cleanup
```

---

# Non-Static @AfterAll

Possible with:

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

    @AfterAll
    void cleanup() {

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

# @AfterAll vs @AfterEach

Most Important Interview Question.

---

## @AfterAll

Runs:

```text
Once
```

---

Example:

```text
test1()
test2()
test3()

cleanup()
```

---

## @AfterEach

Runs:

```text
After Every Test
```

---

Example:

```text
test1()
cleanup()

test2()
cleanup()

test3()
cleanup()
```

---

Comparison:

| Feature | @AfterAll | @AfterEach |
|----------|-----------|------------|
| Runs Once | ✅ | ❌ |
| Runs After Every Test | ❌ | ✅ |
| Performance | Better | Lower |
| Static Required | Usually Yes | No |

---

# @BeforeAll + @AfterAll Together

Most common usage.

---

Example:

```java
@BeforeAll
static void setup() {

    startServer();
}
```

```java
@AfterAll
static void cleanup() {

    stopServer();
}
```

---

Execution:

```text
Start Server
     ↓
Run Tests
     ↓
Stop Server
```

---

# Common Mistakes

## Mistake 1

Forgetting static

Wrong:

```java
@AfterAll
void cleanup() {

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

Using For Per-Test Cleanup

Wrong:

```java
@AfterAll
```

for:

```text
Resetting Data After Every Test
```

---

Use:

```java
@AfterEach
```

instead.

---

# Mistake 3

Not Releasing Resources

Wrong:

```java
executor = ...
```

without:

```java
executor.shutdown();
```

---

Leads to:

```text
Thread Leaks
```

---

# Common Interview Questions

## What is @AfterAll?

Runs once after all tests complete.

---

## Why Is It Static?

No single test instance exists after all tests finish.

---

## Difference Between @AfterAll and @AfterEach?

Once vs after every test.

---

## Can It Be Non-Static?

Yes.

Using:

```java
@TestInstance(
    Lifecycle.PER_CLASS
)
```

---

## Common Use Case?

Stopping shared resources.

---

# Enterprise Best Practice

Use `@AfterAll` for:

```text
Stopping Testcontainers
Closing Shared Connections
Stopping Embedded Servers
Executor Shutdown
Cache Cleanup
```

---

Avoid:

```text
Per-Test Cleanup
```

---

Use:

```java
@AfterEach
```

instead.

---

# Key Points To Remember

- `@AfterAll` runs once after all tests.
- Usually requires a static method.
- Used for final cleanup.
- Often paired with `@BeforeAll`.
- Commonly used with Testcontainers.
- Helps prevent resource leaks.
- Can be non-static using `@TestInstance(PER_CLASS)`.
- Different from `@AfterEach`.
- Frequently asked interview topic.
- Improves test resource management.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@BeforeAll` | Setup Once Before All Tests |
| `@BeforeEach` | Setup Before Every Test |
| `@AfterEach` | Cleanup After Every Test |
| `@AfterAll` | Cleanup Once After All Tests |
| `@Test` | Execute Test |

---

# Interview Shortcut

```java
@AfterAll
static void cleanup() {

    stopDatabase();
}
```

Execution:

```text
Run All Tests
      ↓
Cleanup Shared Resources
      ↓
Finish
```

Think of it as:

```text
"Release Expensive Shared Resources
One Time At The End"
```

---

# Real Production Scenario

Integration Test:

```java
@SpringBootTest
class OrderServiceTest {

    static PostgreSQLContainer<?> postgres;

    @BeforeAll
    static void start() {

        postgres.start();
    }

    @AfterAll
    static void stop() {

        postgres.stop();
    }
}
```

Flow:

```text
Start PostgreSQL
        ↓
Run 100 Tests
        ↓
Stop PostgreSQL
```

Result:

```text
No Container Leaks
Efficient Resource Usage
Reliable CI/CD Execution
```

`@AfterAll` is a JUnit 5 lifecycle annotation that executes cleanup logic once after all test methods have finished, making it ideal for releasing shared resources and performing final test teardown.