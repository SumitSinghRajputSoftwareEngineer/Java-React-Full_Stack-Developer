
## Definition

`@SneakyThrows` is a Lombok annotation that allows you to **avoid explicitly handling checked exceptions** (like `IOException`, `SQLException`) by “sneaking” them past the compiler.

Package:

```java
import lombok.SneakyThrows;
```

---

# Simple Understanding

Normally Java requires:

```text id="a1"
Either handle exception using try-catch
OR declare throws in method signature
```

---

With Lombok:

```text id="b2"
@SneakyThrows removes the need to declare or catch checked exceptions
```

---

So:

```text id="c3"
@SneakyThrows = bypass checked exception handling
```

---

# Real-Life Analogy

Think of airport security:

```text id="d4"
Normally:
- You show passport at every checkpoint

With @SneakyThrows:
- You pass through without showing it every time (internally still validated)
```

---

# Basic Example

## Without Lombok

```java id="f6"
import java.io.*;

public class FileService {

    public void readFile() throws IOException {
        FileReader fr = new FileReader("test.txt");
        fr.read();
    }
}
```

---

## With @SneakyThrows

```java id="g7"
import lombok.SneakyThrows;

public class FileService {

    @SneakyThrows
    public void readFile() {
        FileReader fr = new FileReader("test.txt");
        fr.read();
    }
}
```

---

# What Lombok Generates Internally

```java id="h8"
public void readFile() {
    try {
        FileReader fr = new FileReader("test.txt");
        fr.read();
    } catch (Throwable t) {
        throw lombok.Lombok.sneakyThrow(t);
    }
}
```

---

# Why @SneakyThrows is Controversial

```text id="i9"
It hides checked exceptions from method signature
→ reduces clarity
→ can surprise callers
```

---

# When It Is Useful

## 1. Lambda Expressions

```java id="j10"
list.forEach(item -> {
    processFile(item); // throws checked exception
});
```

With Lombok:

```java id="k11"
list.forEach(item -> {
    @SneakyThrows
    Runnable r = () -> processFile(item);
});
```

---

## 2. Test Code

```java id="l12"
@SneakyThrows
@Test
public void testFileRead() {
    Files.readString(Path.of("test.txt"));
}
```

---

## 3. Prototype / Quick Code

```text id="m13"
Used for fast development or debugging
```

---

# How It Works Internally

```text id="n14"
You write @SneakyThrows
   ↓
Lombok wraps code in try-catch
   ↓
Checked exception is rethrown as unchecked
   ↓
Compiler does NOT force handling
```

---

# Important Behavior

## 1. Converts checked → unchecked

```text id="o15"
IOException becomes RuntimeException-like behavior
```

---

## 2. Does NOT remove exceptions

```text id="p16"
Exception still exists, just hidden from compiler rules
```

---

## 3. Can be dangerous

```text id="q17"
Callers may not know method can throw exceptions
```

---

# Real Production Example

```java id="r18"
import lombok.SneakyThrows;
import java.nio.file.Files;
import java.nio.file.Path;

public class ConfigLoader {

    @SneakyThrows
    public String loadConfig() {
        return Files.readString(Path.of("config.json"));
    }
}
```

---

# @SneakyThrows vs Normal Exception Handling

| Feature | try-catch | throws | @SneakyThrows |
|----------|-----------|--------|---------------|
| Visibility | High | High | Low |
| Boilerplate | High | Medium | None |
| Safety | High | High | Risky |
| Clean code | Medium | Medium | High |

---

# Common Mistakes

## Mistake 1: Overusing in business logic

```text id="s19"
Can hide critical failures
```

---

## Mistake 2: Using in public APIs

```text id="t20"
Callers won't know exceptions exist
```

---

## Mistake 3: Misunderstanding it removes exceptions

```text id="u21"
It only hides handling, not the exception itself
```

---

# Best Practices

## Use for:

```text id="v22"
Test cases
Lambda expressions
Internal utilities
Quick prototyping
```

---

## Avoid for:

```text id="w23"
Public APIs
Critical business services
Production error-sensitive logic
```

---

# Common Interview Questions

## What is @SneakyThrows?

Lombok annotation that bypasses checked exception handling.

---

## Does it remove exceptions?

No, it only hides them from compiler checks.

---

## Is it safe?

Not always; can reduce code clarity.

---

## When should we use it?

Mostly in tests or internal helper code.

---

## What does compiler see?

No throws declaration or try-catch needed.

---

# Enterprise Perspective

```text id="x24"
Use carefully; many teams discourage it in production services
```

---

# Key Points To Remember

- `@SneakyThrows` hides checked exceptions.
- Removes need for try-catch or throws declaration.
- Internally rethrows exceptions using Lombok helper.
- Useful in tests and lambdas.
- Can reduce code clarity and safety.
- Does NOT remove exceptions, only bypasses checks.
- Should NOT be used in public APIs.
- Works at compile time via Lombok.
- Popular but controversial annotation.
- Important advanced Java interview topic.

---

# Quick Example

```java id="y25"
@SneakyThrows
public void process() {
    Thread.sleep(1000);
}
```

---

# Final Summary

`@SneakyThrows` is a Lombok annotation that allows developers to bypass Java’s checked exception handling mechanism, making code cleaner but potentially less explicit and safer, so it should be used carefully in production systems.
```