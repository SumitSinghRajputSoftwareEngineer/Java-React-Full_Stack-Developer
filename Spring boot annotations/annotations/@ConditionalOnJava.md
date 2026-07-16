
## Definition

`@ConditionalOnJava` is a Spring Boot annotation used to **create or enable beans/configurations only if the application is running on a specific Java version (or range of versions)**.

It tells Spring:

> “Activate this configuration only if the required Java version matches.”

Package:

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnJava;
import org.springframework.boot.system.JavaVersion;
```

---

# Simple Understanding

Normally Spring does:

```text id="a1"
Run same configuration on all Java versions
```

---

With `@ConditionalOnJava`:

```text id="b2"
Enable bean only if Java version matches condition
```

---

So:

```text id="c3"
@ConditionalOnJava = Java version-based feature toggle
```

---

# Real-Life Analogy

Think of software features:

```text id="d4"
Advanced encryption → only available in newer OS versions
New UI features → only supported in latest app versions
Performance optimizations → only for newer hardware
```

---

That version-based control is:

```text id="e5"
@ConditionalOnJava
```

---

# Why Do We Need @ConditionalOnJava?

We use it for:

```text id="f6"
Supporting multiple Java versions
Avoiding runtime incompatibility
Enabling modern features conditionally
Maintaining backward compatibility
```

---

# Basic Example

```java id="g7"
@Configuration
@ConditionalOnJava(JavaVersion.EIGHT)
public class LegacyConfig {

    @Bean
    public LegacyService legacyService() {
        return new LegacyService();
    }
}
```

---

# What Happens Here?

```text id="h8"
If Java 8 → bean is created
If Java 11/17/21 → bean is skipped
```

---

# Supported JavaVersion Enum

Spring Boot provides:

```text id="i9"
JavaVersion.EIGHT
JavaVersion.NINE
JavaVersion.TEN
JavaVersion.ELEVEN
JavaVersion.TWELVE
JavaVersion.THIRTEEN
JavaVersion.FOURTEEN
JavaVersion.FIFTEEN
JavaVersion.SIXTEEN
JavaVersion.SEVENTEEN
JavaVersion.EIGHTEEN+
```

---

# Advanced Usage

## 1. Equal or Greater Than

```java id="j10"
@ConditionalOnJava(JavaVersion.SEVENTEEN)
```

Meaning:

```text id="k11"
Java 17 or higher
```

---

## 2. Range-Based Conditions

```java id="l12"
@ConditionalOnJava(
    range = ConditionalOnJava.Range.OLDER_THAN,
    value = JavaVersion.ELEVEN
)
```

---

```java id="m13"
@ConditionalOnJava(
    range = ConditionalOnJava.Range.EQUAL_OR_NEWER,
    value = JavaVersion.SEVENTEEN
)
```

---

# How It Works Internally

```text id="n14"
Spring Boot starts
   ↓
Detects Java runtime version
   ↓
Compares with @ConditionalOnJava rule
   ↓
If match → register bean
If not match → skip bean
```

---

# Real Production Example: Legacy vs Modern API

## Legacy Support (Java 8)

```java id="o15"
@Configuration
@ConditionalOnJava(JavaVersion.EIGHT)
public class LegacyDateConfig {

    @Bean
    public DateFormatter formatter() {
        return new LegacyDateFormatter();
    }
}
```

---

## Modern Support (Java 17+)

```java id="p16"
@Configuration
@ConditionalOnJava(JavaVersion.SEVENTEEN)
public class ModernDateConfig {

    @Bean
    public DateFormatter formatter() {
        return new ModernDateFormatter();
    }
}
```

---

# Real Use Cases

## 1. Backward Compatibility

```text id="q17"
Support Java 8 and Java 17 in same codebase
```

---

## 2. Feature Optimization

```text id="r18"
Enable better APIs only in newer Java versions
```

---

## 3. Library Compatibility

```text id="s19"
Different implementations for different Java versions
```

---

## 4. Framework Auto-Configuration

```text id="t20"
Spring Boot enables features based on runtime Java version
```

---

# Real Architecture View

```text id="u21"
Application Starts
     ↓
Detect Java version
     ↓
Evaluate @ConditionalOnJava
     ↓
Enable compatible beans only
     ↓
Application runs safely across versions
```

---

# @ConditionalOnJava vs @ConditionalOnClass

| Feature | OnJava | OnClass |
|----------|--------|--------|
| Checks | Java runtime version | Class presence |
| Level | JVM environment | Dependency/library |
| Use Case | Version compatibility | Optional features |

---

# @ConditionalOnJava vs @ConditionalOnProperty

| Feature | OnJava | OnProperty |
|----------|--------|-------------|
| Based on | Java version | Config value |
| Control type | Runtime environment | External configuration |

---

# Common Mistakes

## Mistake 1: Assuming backward compatibility automatically works

```text id="v22"
You must still provide fallback beans
```

---

## Mistake 2: Wrong Java version assumption

```text id="w23"
App runs on Java 17 but condition set for Java 11
```

---

## Mistake 3: Overusing version conditions

```text id="x24"
Too many version-specific beans → hard maintenance
```

---

# Common Interview Questions

## What is @ConditionalOnJava?

Creates beans based on Java runtime version.

---

## Why is it used?

To maintain compatibility across Java versions.

---

## Which class is used?

JavaVersion enum.

---

## Can it support version ranges?

Yes, using range attribute.

---

## When is it evaluated?

At application startup.

---

# Enterprise Best Practice

Use `@ConditionalOnJava` for:

```text id="y25"
Backward compatibility
Version-specific optimizations
Gradual migration to newer Java versions
Framework-level configuration
```

---

Avoid for:

```text id="z26"
Business logic
Feature toggles
Environment configs
```

---

# Key Points To Remember

- `@ConditionalOnJava` activates beans based on Java version.
- Helps support multiple JVM versions.
- Uses JavaVersion enum for control.
- Supports range-based conditions.
- Ensures backward compatibility.
- Used in Spring Boot auto-configuration.
- Evaluated at startup time.
- Enables version-specific optimizations.
- Prevents runtime incompatibility issues.
- Important advanced Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| @ConditionalOnJava | Java version-based control |
| @ConditionalOnClass | Dependency-based control |
| @ConditionalOnProperty | Config-based control |
| @ConditionalOnBean | Bean-based control |

---

# Interview Shortcut

```java id="a27"
@Configuration
@ConditionalOnJava(JavaVersion.SEVENTEEN)
public class ModernConfig {

    @Bean
    public Service service() {
        return new ModernService();
    }
}
```

Meaning:

```text id="b28"
Enable configuration only if running on specific Java version

Supports version-based feature control

Ensures compatibility and safe execution
```

---

# Real Production Example

```text id="c29"
Enterprise Microservice System
```

Flow:

```text id="d30"
Application Starts
     ↓
Detect Java version
     ↓
If Java 8 → legacy config loaded
If Java 17 → modern config loaded
     ↓
System adapts automatically
```

Result:

```text id="e31"
Version-aware architecture
Smooth migration path
Safe multi-Java support
```

---

# Final Summary

`@ConditionalOnJava` is a Spring Boot annotation used to conditionally enable beans or configurations based on the Java runtime version, enabling backward compatibility, version-specific features, and safe multi-Java support in enterprise applications.
```