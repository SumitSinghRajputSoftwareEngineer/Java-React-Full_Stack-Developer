
## Definition

`@AfterEach` is a JUnit 5 lifecycle annotation used to execute a method **after every test method**.

It tells JUnit:

> Run this method after each `@Test` method completes, whether the test passes or fails.

Package:

```java
import org.junit.jupiter.api.AfterEach;
```

---

# Why Do We Need @AfterEach?

During tests, we often create:

```text
Files
Database Connections
Sockets
Streams
Temporary Data
Mocks
```

---

These resources should be cleaned up after each test.

---

Without cleanup:

```text
Memory Leaks
Resource Leaks
Unstable Tests
Shared State Problems
```

---

Solution:

```java
@AfterEach
```

---

# Basic Example

```java
class ProductTest {

    @AfterEach
    void cleanup() {

        System.out.println(
            "Cleanup Executed"
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
   ↓
cleanup()

test2()
   ↓
cleanup()
```

---

Output:

```text
Cleanup Executed
Cleanup Executed
```

---

# Internal Workflow

```text
@BeforeEach
      ↓
@Test
      ↓
@AfterEach

Repeat For Every Test
```

---

# Example

```java
class UserTest {

    List<String> users =
        new ArrayList<>();

    @Test
    void testAddUser() {

        users.add("John");

        assertEquals(
            1,
            users.size()
        );
    }

    @AfterEach
    void cleanup() {

        users.clear();
    }
}
```

---

Execution:

```text
Run Test
     ↓
Add User
     ↓
Assertion
     ↓
Clear List
```

---

# Why Is It Important?

Suppose:

```java
List<String> names =
        new ArrayList<>();
```

---

Test 1:

```java
names.add("John");
```

---

Without cleanup:

Test 2 may see:

```text
John Already Exists
```

---

Leading to:

```text
Unexpected Failures
```

---

# Example With Files

Test:

```java
@Test
void testFile() {

}
```

---

Cleanup:

```java
@AfterEach
void cleanup() {

    Files.deleteIfExists(
        tempFile
    );
}
```

---

Execution:

```text
Create File
     ↓
Run Test
     ↓
Delete File
```

---

# Example With Database Connection

```java
Connection connection;
```

---

Setup:

```java
@BeforeEach
void setup()
throws Exception {

    connection =
       DriverManager
       .getConnection(...);
}
```

---

Cleanup:

```java
@AfterEach
void cleanup()
throws Exception {

    connection.close();
}
```

---

Execution:

```text
Open Connection
      ↓
Run Test
      ↓
Close Connection
```

---

# Spring Boot Example

```java
@SpringBootTest
class ProductServiceTest {

    @AfterEach
    void cleanup() {

        System.out.println(
             "Cleanup"
        );
    }

    @Test
    void testProduct() {

    }
}
```

---

Flow:

```text
Spring Context
     ↓
Run Test
     ↓
Cleanup
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
void test() {

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
test()
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

# Runs Even If Test Fails

Important interview topic.

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
@AfterEach
void cleanup() {

    System.out.println(
        "Executed"
    );
}
```

---

Output:

```text
Executed
```

---

Because:

```text
@AfterEach Always Runs
```

---

# Multiple @AfterEach Methods

Possible:

```java
@AfterEach
void cleanupFiles() {

}
```

```java
@AfterEach
void cleanupDatabase() {

}
```

---

Execution order:

```text
Not Guaranteed
```

---

Best practice:

```text
Use One Cleanup Method
```

---

# Example With Mockito

```java
@AfterEach
void cleanup() {

    clearInvocations(
        repository
    );
}
```

---

Useful when:

```text
Resetting Mock State
```

---

# Example With Temporary Folder

```java
Path tempDir;
```

---

Cleanup:

```java
@AfterEach
void cleanup()
throws Exception {

    Files.deleteIfExists(
        tempDir
    );
}
```

---

Prevents:

```text
Disk Space Issues
```

---

# @AfterEach vs finally Block

Example:

```java
try {

}
finally {

}
```

---

Only works inside one method.

---

`@AfterEach`:

```java
@AfterEach
void cleanup() {

}
```

---

Works for:

```text
Entire Test Lifecycle
```

---

More maintainable.

---

# @AfterEach vs @AfterAll

Interview Favorite.

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
 ↓
cleanup()

test2()
 ↓
cleanup()

test3()
 ↓
cleanup()
```

---

## @AfterAll

Runs:

```text
Only Once
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

Comparison:

| Feature | @AfterEach | @AfterAll |
|----------|-----------|-----------|
| Runs After Every Test | ✅ | ❌ |
| Runs Once | ❌ | ✅ |
| Test Isolation | ✅ | ❌ |
| Instance Method | ✅ | ❌ (usually static) |

---

# Common Use Cases

## Clear Collections

```java
users.clear();
```

---

## Delete Temporary Files

```java
Files.delete(...)
```

---

## Close Database Connections

```java
connection.close();
```

---

## Close Streams

```java
stream.close();
```

---

## Reset Mock State

```java
Mockito.reset(...)
```

---

## Release Resources

```java
cleanup()
```

---

# Common Mistakes

## Mistake 1

Heavy Cleanup

Wrong:

```java
@AfterEach
void cleanup() {

    restartServer();
}
```

---

Runs after every test.

---

Slow.

---

Use:

```java
@AfterAll
```

instead.

---

# Mistake 2

Throwing Unhandled Exceptions

Wrong:

```java
@AfterEach
void cleanup() {

    throw new RuntimeException();
}
```

---

May cause test failures.

---

# Mistake 3

Depending On Cleanup Order

Wrong:

```text
cleanupDatabase()
must run before
cleanupFiles()
```

---

Order isn't guaranteed.

---

# Common Interview Questions

## What is @AfterEach?

Runs after every test method.

---

## Does It Run If Test Fails?

Yes.

---

## Which Package?

```java
org.junit.jupiter.api.AfterEach
```

---

## Difference Between @AfterEach and @AfterAll?

Every test vs once after all tests.

---

## Why Use It?

To release resources and reset test state.

---

# Enterprise Best Practice

Use `@AfterEach` for:

```text
Resource Cleanup
Connection Closing
Mock Resetting
Deleting Temporary Files
State Reset
```

---

Avoid:

```text
Heavy Expensive Operations
```

---

Use:

```java
@AfterAll
```

for costly cleanup.

---

# Key Points To Remember

- `@AfterEach` runs after every test method.
- Executes even when tests fail.
- Used for cleanup logic.
- Helps prevent resource leaks.
- Maintains test isolation.
- Commonly paired with `@BeforeEach`.
- Supports Spring Boot tests.
- Part of JUnit 5 lifecycle annotations.
- Frequently used in enterprise projects.
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
@AfterEach
void cleanup() {

    connection.close();
}
```

Execution:

```text
Run Test
    ↓
Cleanup Resources
    ↓
Prepare For Next Test
```

Think of it as:

```text
"Clean The Environment
After Every Test Runs"
```

---

# Real Production Scenario

Repository Test:

```java
@DataJpaTest
class OrderRepositoryTest {

    File tempFile;

    @AfterEach
    void cleanup() {

        tempFile.delete();
    }
}
```

Flow:

```text
Create Temp File
      ↓
Run Test
      ↓
Delete Temp File
```

Result:

```text
No Leftover Files
No Shared State
Stable Tests
Reliable Execution
```

`@AfterEach` is a JUnit 5 lifecycle annotation that executes cleanup logic after every test method, ensuring resources are released and each test remains isolated from others.