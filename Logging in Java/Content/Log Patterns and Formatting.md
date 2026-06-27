# Log Patterns and Formatting

> **Goal:** Understand Spring Boot log patterns, how log formatting works, how to customize log output, pattern syntax, commonly used pattern components (`%d`, `%level`, `%logger`, `%thread`, `%msg`, `%n`), real-world examples, production best practices, and interview concepts.

---

# 1. What is Log Formatting?

By default, Spring Boot automatically formats logs.

Example:

```text
2026-06-15 10:30:25 INFO 1234 --- [main]
com.company.Application : Application Started
```

This format is generated using a:

```text
Log Pattern
```

---

## Definition

> A Log Pattern is a template that determines how log messages are displayed in the console or log files.

---

# Why Log Formatting Matters?

Without formatting:

```text
Application Started

Database Connected

Order Created
```

Problems:

```text
No Timestamp
No Log Level
No Thread Information
No Source Class
```

---

With formatting:

```text
2026-06-15 10:30:25 INFO [main]
EmployeeService : Employee Created
```

Provides:

```text
When Event Occurred
Severity
Thread Information
Source Class
Actual Message
```

---

# 2. Spring Boot Default Log Pattern

Spring Boot uses Logback internally and automatically applies a default pattern.

---

## Example

```text
2026-06-15 10:30:25 INFO 1234 --- [main]
com.company.Application : Application Started
```

---

Contains:

```text
Timestamp
Log Level
Process ID
Thread Name
Logger Name
Message
```

---

# Default Structure

```text
Timestamp
     ↓
Level
     ↓
PID
     ↓
Thread
     ↓
Logger
     ↓
Message
```

---

# 3. What is a Log Pattern?

A log pattern defines:

```text
Which Components To Display
Order Of Components
Formatting Style
```

---

## Example Pattern

```text
%d %-5level [%thread] %logger - %msg%n
```

---

Generated Output

```text
2026-06-15 10:30:25 INFO [main]
com.company.Application - Application Started
```

---

# Pattern Flow

```text
Pattern
     ↓
Logback
     ↓
Formatted Output
```

---

# 4. Custom Log Pattern

Spring Boot allows customizing log patterns.

---

## Console Pattern

```properties
logging.pattern.console=
```

---

## Syntax

```properties
logging.pattern.console=<pattern>
```

---

Example:

```properties
logging.pattern.console=
%d [%thread] %-5level %logger - %msg%n
```

---

Generated Output

```text
2026-06-15 10:30:25 [main]
INFO com.company.Application -
Application Started
```

---

# Why Customize Log Patterns?

Different organizations have different requirements.

Examples:

```text
More Readable Logs
Shorter Logs
Additional Information
Monitoring Compatibility
Centralized Logging Standards
```

---

# 5. Pattern Components

Spring Boot and Logback provide various placeholders.

---

# Component 1: %d

Represents:

```text
Date and Time
```

---

## Example

Pattern:

```text
%d
```

---

Output:

```text
2026-06-15 10:30:25
```

---

# Why Use %d?

Helps identify:

```text
When Event Occurred
Execution Timeline
Request Duration
```

---

# Custom Date Format

```text
%d{yyyy-MM-dd HH:mm:ss}
```

---

Output:

```text
2026-06-15 10:30:25
```

---

# Another Example

Pattern:

```text
%d{HH:mm:ss}
```

---

Output:

```text
10:30:25
```

---

# Component 2: %level

Represents:

```text
Logging Level
```

---

Possible Values

```text
TRACE
DEBUG
INFO
WARN
ERROR
```

---

Example Pattern

```text
%level
```

---

Output

```text
INFO
```

---

# Why Use %level?

Helps quickly identify:

```text
Normal Events
Warnings
Errors
```

---

# Example

```text
INFO Application Started

WARN Slow Database Query

ERROR Database Connection Failed
```

---

# Component 3: %logger

Represents:

```text
Logger Name
```

usually:

```text
Class Name
```

---

Example

Pattern:

```text
%logger
```

---

Output

```text
com.company.EmployeeService
```

---

# Why Use %logger?

Helps identify:

```text
Which Class Generated Log
```

---

# Short Logger Name

Pattern:

```text
%logger{20}
```

---

Output:

```text
EmployeeService
```

---

Reduces log size.

---

# Component 4: %thread

Represents:

```text
Current Thread Name
```

---

Example

Pattern:

```text
%thread
```

---

Output:

```text
main
```

---

Other Examples

```text
http-nio-8080-exec-1

task-1

scheduling-1
```

---

# Why Use %thread?

Helps debug:

```text
Concurrency Issues
Async Processing
Request Execution
Background Jobs
```

---

# Component 5: %msg

Represents:

```text
Actual Log Message
```

---

Example

Code:

```java
log.info("Employee Created");
```

---

Pattern:

```text
%msg
```

---

Output:

```text
Employee Created
```

---

# Why Use %msg?

This is the:

```text
Developer Written Information
```

---

Without `%msg`:

```text
Logs Become Useless
```

---

# Component 6: %n

Represents:

```text
New Line
```

---

Example

Pattern:

```text
%msg%n
```

---

Output

```text
Application Started

Next Log Appears Here
```

---

Without `%n`

Logs may appear on the same line.

---

# Why Use %n?

Provides:

```text
Proper Formatting
Better Readability
Cross-Platform Compatibility
```

---

# 6. Commonly Used Log Pattern

Very popular in Spring Boot projects.

---

Pattern

```properties
logging.pattern.console=
%d{yyyy-MM-dd HH:mm:ss}
[%thread]
%-5level
%logger{36}
-
%msg%n
```

---

Output

```text
2026-06-15 10:30:25
[main]
INFO
EmployeeService
-
Employee Created Successfully
```

---

# Understanding %-5level

Example:

```text
%-5level
```

---

Output:

```text
INFO
WARN
ERROR
```

aligned neatly.

---

Without Formatting:

```text
INFO

ERROR

WARN
```

may not align properly.

---

# 7. Pattern Customization Examples

---

# Example 1: Simple Pattern

Configuration:

```properties
logging.pattern.console=
%msg%n
```

---

Output:

```text
Application Started

Employee Created

Payment Successful
```

---

Only messages appear.

---

# Example 2: Timestamp + Message

Configuration:

```properties
logging.pattern.console=
%d %msg%n
```

---

Output:

```text
2026-06-15 10:30:25
Application Started
```

---

# Example 3: Timestamp + Level + Message

Configuration:

```properties
logging.pattern.console=
%d %level %msg%n
```

---

Output:

```text
2026-06-15 10:30:25
INFO
Application Started
```

---

# Example 4: Thread Information

Configuration:

```properties
logging.pattern.console=
%d [%thread] %msg%n
```

---

Output:

```text
2026-06-15 10:30:25
[main]
Application Started
```

---

# Example 5: Logger Name

Configuration:

```properties
logging.pattern.console=
%d %level %logger - %msg%n
```

---

Output:

```text
2026-06-15 10:30:25
INFO
EmployeeService
-
Employee Created
```

---

# Example 6: Production Pattern

Configuration:

```properties
logging.pattern.console=
%d{yyyy-MM-dd HH:mm:ss}
[%thread]
%-5level
%logger{36}
-
%msg%n
```

---

Output

```text
2026-06-15 10:30:25
[http-nio-8080-exec-1]
INFO
EmployeeController
-
Employee Created Successfully
```

---

# 8. Console Pattern vs File Pattern

Spring Boot supports:

```text
Console Logs
File Logs
```

---

Console Pattern

```properties
logging.pattern.console=
%d %level %msg%n
```

---

File Pattern

```properties
logging.pattern.file=
%d %level %msg%n
```

---

# Difference

```text
Console Pattern
     ↓
Terminal Output

File Pattern
     ↓
Log Files
```

---

# 9. Real Production Example

Configuration:

```properties
logging.pattern.console=
%d{yyyy-MM-dd HH:mm:ss}
[%thread]
%-5level
%logger{30}
-
%msg%n
```

---

Code:

```java
log.info(
    "Order Created Successfully"
);
```

---

Output:

```text
2026-06-15 10:30:25
[http-nio-8080-exec-1]
INFO
OrderService
-
Order Created Successfully
```

---

Provides:

```text
When?
Which Thread?
Which Class?
What Happened?
```

---

# 10. Best Practices

---

## Always Include Timestamp

Good:

```text
%d
```

---

Reason:

```text
Track Events Chronologically
```

---

## Include Log Level

Good:

```text
%level
```

---

Reason:

```text
Identify Severity
```

---

## Include Logger Name

Good:

```text
%logger
```

---

Reason:

```text
Identify Source Class
```

---

## Include Thread Name

Good:

```text
%thread
```

---

Reason:

```text
Debug Concurrent Requests
```

---

## Always End With %n

Good:

```text
%msg%n
```

---

Reason:

```text
Proper Line Separation
```

---

# 11. Common Mistakes

---

## Missing Timestamp

Bad Pattern:

```text
%level %msg
```

---

Problem:

```text
No Timing Information
```

---

## Missing Logger Name

Bad Pattern:

```text
%d %level %msg%n
```

---

Problem:

```text
Cannot Identify Source Class
```

---

## Missing Thread Information

Bad Pattern:

```text
%d %level %logger %msg%n
```

---

Problem:

```text
Hard To Debug Multi-threaded Applications
```

---

## Missing New Line

Bad Pattern:

```text
%d %msg
```

---

Problem:

```text
Logs May Merge Together
```

---

# 12. Interview Questions

### Q1. What is a Log Pattern?

A template that defines how log messages are formatted.

---

### Q2. How do you customize console logs?

```properties
logging.pattern.console=
```

---

### Q3. What does `%d` represent?

```text
Timestamp
```

---

### Q4. What does `%level` represent?

```text
Logging Level
```

---

### Q5. What does `%logger` represent?

```text
Logger/Class Name
```

---

### Q6. What does `%thread` represent?

```text
Current Thread Name
```

---

### Q7. What does `%msg` represent?

```text
Actual Log Message
```

---

### Q8. What does `%n` represent?

```text
New Line
```

---

### Q9. Why is `%thread` useful?

For debugging:

```text
Concurrent Requests
Async Processing
Background Tasks
```

---

# Quick Revision

```text
Log Patterns and Formatting
---------------------------

Custom Pattern

logging.pattern.console=

Common Components

%d
    ↓
Timestamp

%level
    ↓
Log Level

%logger
    ↓
Class Name

%thread
    ↓
Thread Name

%msg
    ↓
Actual Message

%n
    ↓
New Line

--------------------------------

Common Pattern

%d [%thread] %-5level
%logger - %msg%n

--------------------------------

Best Practices

✔ Include Timestamp
✔ Include Log Level
✔ Include Logger Name
✔ Include Thread Name
✔ Include New Line

Remember

%d       → When

%level   → Severity

%thread  → Thread

%logger  → Source Class

%msg     → Message

%n       → New Line
```