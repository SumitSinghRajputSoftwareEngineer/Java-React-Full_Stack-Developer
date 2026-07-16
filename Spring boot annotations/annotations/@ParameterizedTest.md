
## Definition

`@ParameterizedTest` is a JUnit 5 annotation that allows the **same test method to run multiple times with different input values**.

It tells JUnit:

> Execute this test repeatedly using different parameters.

Package:

```java
import org.junit.jupiter.params.ParameterizedTest;
```

---

# Why Do We Need @ParameterizedTest?

Without it:

```java
@Test
void test1() {

    assertTrue(10 > 0);
}

@Test
void test2() {

    assertTrue(20 > 0);
}

@Test
void test3() {

    assertTrue(30 > 0);
}
```

---

Problem:

```text
Duplicate Code
```

---

Solution:

```java
@ParameterizedTest
```

---

One test:

```java
@ParameterizedTest
@ValueSource(ints = {10,20,30})
void test(int value) {

    assertTrue(value > 0);
}
```

---

Runs:

```text
test(10)
test(20)
test(30)
```

---

# Internal Workflow

```text
JUnit Finds Parameters
        ↓
Creates Test Invocation #1
        ↓
Runs Test

Creates Test Invocation #2
        ↓
Runs Test

Creates Test Invocation #3
        ↓
Runs Test
```

---

# Basic Example

```java
@ParameterizedTest
@ValueSource(
    ints = {1,2,3,4,5}
)
void testPositiveNumbers(
        int number) {

    assertTrue(number > 0);
}
```

---

Execution:

```text
1
2
3
4
5
```

---

Five separate test executions.

---

# Required Dependency

Spring Boot already includes it through:

```xml
spring-boot-starter-test
```

---

Otherwise:

```xml
<dependency>
    <groupId>
        org.junit.jupiter
    </groupId>
    <artifactId>
        junit-jupiter-params
    </artifactId>
</dependency>
```

---

# @ValueSource

Most common source.

---

Example:

```java
@ParameterizedTest
@ValueSource(
    strings = {
      "Java",
      "Spring",
      "React"
    }
)
void testStrings(
        String value) {

    assertNotNull(value);
}
```

---

Runs:

```text
Java
Spring
React
```

---

Supported Types

```java
ints
longs
doubles
strings
booleans
chars
classes
```

---

Example:

```java
@ValueSource(
    booleans = {
       true,
       false
    }
)
```

---

# @NullSource

Provides:

```java
null
```

---

Example:

```java
@ParameterizedTest
@NullSource
void testNull(
        String input) {

    assertNull(input);
}
```

---

Execution:

```text
null
```

---

# @EmptySource

Provides:

```text
Empty Value
```

---

Example:

```java
@ParameterizedTest
@EmptySource
void testEmpty(
        String input) {

    assertTrue(
        input.isEmpty()
    );
}
```

---

Execution:

```text
""
```

---

# @NullAndEmptySource

Combination.

---

Example:

```java
@ParameterizedTest
@NullAndEmptySource
void test(
        String input) {

}
```

---

Execution:

```text
null
""
```

---

# @CsvSource

Most used in interviews.

---

Example:

```java
@ParameterizedTest
@CsvSource({
   "10,20,30",
   "5,5,10",
   "7,8,15"
})
void testAddition(
        int a,
        int b,
        int result) {

    assertEquals(
        result,
        a + b
    );
}
```

---

Execution:

```text
10,20,30
5,5,10
7,8,15
```

---

# @CsvFileSource

Reads values from CSV file.

---

CSV:

```csv
10,20,30
5,5,10
```

---

Test:

```java
@ParameterizedTest
@CsvFileSource(
    resources =
      "/data.csv"
)
void test(
      int a,
      int b,
      int result) {

}
```

---

Useful for:

```text
Large Test Datasets
```

---

# @MethodSource

Most powerful source.

---

Example:

```java
static Stream<Integer>
numbers() {

    return Stream.of(
        1,2,3,4
    );
}
```

---

Test:

```java
@ParameterizedTest
@MethodSource(
    "numbers"
)
void test(int number) {

    assertTrue(
        number > 0
    );
}
```

---

Execution:

```text
1
2
3
4
```

---

# Multiple Arguments Using MethodSource

```java
static Stream<Arguments>
data() {

    return Stream.of(

       Arguments.of(
           10,20,30
       ),

       Arguments.of(
           5,5,10
       )
    );
}
```

---

Test:

```java
@ParameterizedTest
@MethodSource("data")
void test(
    int a,
    int b,
    int result) {

    assertEquals(
       result,
       a+b
    );
}
```

---

# @EnumSource

Used for enums.

---

Enum:

```java
enum Status {

    ACTIVE,
    INACTIVE
}
```

---

Test:

```java
@ParameterizedTest
@EnumSource(Status.class)
void test(Status status) {

}
```

---

Execution:

```text
ACTIVE
INACTIVE
```

---

# Example With Spring Boot

Service:

```java
@Service
public class CalculatorService {

    public int add(
            int a,
            int b) {

        return a+b;
    }
}
```

---

Test:

```java
@SpringBootTest
class CalculatorTest {

    @Autowired
    CalculatorService service;

    @ParameterizedTest
    @CsvSource({
        "1,2,3",
        "5,5,10",
        "10,20,30"
    })
    void testAdd(
        int a,
        int b,
        int expected) {

        assertEquals(
            expected,
            service.add(a,b)
        );
    }
}
```

---

Execution:

```text
3 Test Runs
```

---

# Display Names

```java
@ParameterizedTest(
   name =
   "Input={0}"
)
```

---

Example:

```java
@ParameterizedTest(
   name =
   "Testing value {0}"
)
@ValueSource(
   ints={1,2,3}
)
void test(int value) {

}
```

---

Report:

```text
Testing value 1
Testing value 2
Testing value 3
```

---

# @ParameterizedTest vs @Test

## @Test

```java
@Test
void test() {

}
```

Runs:

```text
Once
```

---

## @ParameterizedTest

```java
@ParameterizedTest
```

Runs:

```text
Multiple Times
```

---

Comparison:

| Feature | @Test | @ParameterizedTest |
|----------|--------|-------------------|
| Single Execution | ✅ | ❌ |
| Multiple Inputs | ❌ | ✅ |
| Reduces Duplication | ❌ | ✅ |
| Data Driven Testing | ❌ | ✅ |

---

# Common Use Cases

## Validation Testing

```java
Emails
Passwords
Phone Numbers
```

---

## Mathematical Calculations

```java
Addition
Interest
Tax
Discount
```

---

## API Input Testing

```java
Different Requests
```

---

## Edge Cases

```java
null
empty
invalid
valid
```

---

# Common Mistakes

## Mistake 1

Missing Parameter

Wrong:

```java
@ValueSource(ints={1,2})
void test() {

}
```

---

Must accept parameter:

```java
void test(int value)
```

---

# Mistake 2

Wrong Data Type

```java
@ValueSource(
   ints={1,2}
)
void test(String value)
```

---

Type mismatch.

---

# Mistake 3

Using Too Much Logic

Keep parameterized tests:

```text
Simple
Focused
Readable
```

---

# Common Interview Questions

## What is @ParameterizedTest?

Runs the same test multiple times with different data.

---

## Which Package?

```java
org.junit.jupiter.params.ParameterizedTest
```

---

## Difference Between @Test and @ParameterizedTest?

Single execution vs multiple executions.

---

## Most Common Sources?

```java
@ValueSource
@CsvSource
@MethodSource
```

---

## Why Use It?

To avoid duplicate test methods.

---

# Enterprise Best Practice

Use:

```java
@CsvSource
```

for small datasets.

---

Use:

```java
@MethodSource
```

for complex datasets.

---

Use:

```java
@CsvFileSource
```

for large datasets.

---

Keep tests:

```text
Readable
Data Driven
Maintainable
```

---

# Key Points To Remember

- `@ParameterizedTest` executes the same test multiple times.
- Part of JUnit 5.
- Reduces duplicate test methods.
- Supports many data sources.
- Common sources are `@ValueSource`, `@CsvSource`, and `@MethodSource`.
- Excellent for validation testing.
- Supports display names.
- Frequently used in enterprise projects.
- Frequently asked interview topic.
- Core concept of data-driven testing.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Test` | Single Test Execution |
| `@ParameterizedTest` | Multiple Executions |
| `@ValueSource` | Single Value Inputs |
| `@CsvSource` | Multiple Columns |
| `@MethodSource` | Dynamic Data |
| `@EnumSource` | Enum Values |

---

# Interview Shortcut

```java
@ParameterizedTest
@ValueSource(
    ints = {1,2,3}
)
void test(int n) {

}
```

Execution:

```text
test(1)
test(2)
test(3)
```

Think of it as:

```text
"One Test Method
Multiple Input Values"
```

---

# Real Production Scenario

Password Validation:

```java
@ParameterizedTest
@ValueSource(strings = {
   "123",
   "abc",
   "",
   "password"
})
void testInvalidPasswords(
        String password) {

    assertThrows(
        ValidationException.class,
        () -> service.validate(
               password
        )
    );
}
```

Result:

```text
Less Duplicate Code
More Coverage
Cleaner Tests
Data-Driven Validation
```

`@ParameterizedTest` is a JUnit 5 annotation that enables a single test method to run repeatedly with different input values, making tests more concise, maintainable, and data-driven.