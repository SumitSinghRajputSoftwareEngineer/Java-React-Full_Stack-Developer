# Console Logging

> **Goal:** Understand Console Logging in Spring Boot, how logs are displayed in the console, default console logging behavior, colored console logs, ANSI color support, custom console patterns, production considerations, best practices, and interview concepts.

---

# 1. What is Console Logging?

Console Logging is the process of displaying log messages directly in the terminal, command prompt, IDE console, or application output window.

---

## Definition

> Console Logging is the default mechanism in Spring Boot that writes log messages to the application's console output.

---

# Example

Code:

```java
log.info("Application Started");
```

Output:

```text
2026-06-15 10:30:25 INFO 1234 --- [main]
com.company.Application :
Application Started
```

---

# Where Are Console Logs Displayed?

Depending on how the application is executed:

---

## IntelliJ IDEA

```text
Run Console Window
```

---

## Eclipse

```text
Console View
```

---

## Command Prompt

```text
Terminal Output
```

---

## Linux Server

```text
Terminal Session
```

---

# Console Logging Flow

```text
Application
      ↓
Logger
      ↓
SLF4J
      ↓
Logback
      ↓
Console
```

---

# Why Console Logging is Important?

During development developers need immediate feedback.

Examples:

```text
Application Startup
API Requests
Database Operations
Errors
Warnings
```

---

Without Console Logging:

```text
Difficult To Debug
Difficult To Monitor
```

---

With Console Logging:

```text
Real-Time Monitoring
Immediate Feedback
Easy Troubleshooting
```

---

# 2. Default Console Logging

Spring Boot automatically enables console logging.

No configuration required.

---

## Example

Application:

```java
@SpringBootApplication
public class Application {
}
```

---

Even without:

```properties
logging.pattern.console
```

Spring Boot logs automatically.

---

# Default Behavior

```text
Logs Displayed In Console
INFO Level Enabled
Color Support Enabled (Most IDEs)
Default Pattern Applied
```

---

# Example Startup Log

```text
2026-06-15 10:30:25 INFO 1234 --- [main]
com.company.Application :
Starting Application
```

---

```text
2026-06-15 10:30:27 INFO 1234 --- [main]
com.company.Application :
Started Application in 2.4 seconds
```

---

# Default Console Output Components

```text
Timestamp

Log Level

Process ID

Thread Name

Logger Name

Message
```

---

# Visual Representation

```text
2026-06-15 10:30:25 INFO 1234 --- [main]
com.company.Application :
Application Started

│
├─ Timestamp
├─ Log Level
├─ PID
├─ Thread Name
├─ Logger Name
└─ Message
```

---

# Default Log Levels Displayed

If root level is:

```properties
logging.level.root=INFO
```

Visible logs:

```text
INFO
WARN
ERROR
```

---

Hidden logs:

```text
TRACE
DEBUG
```

---

# 3. Colored Console Logs

One of the most useful features provided by Spring Boot.

---

## What are Colored Logs?

Spring Boot can display different log levels using different colors.

Example:

```text
INFO   → Green

WARN   → Yellow

ERROR  → Red
```

---

# Why Colored Logs?

Without colors:

```text
INFO Application Started

WARN Disk Space Low

ERROR Database Failed
```

Everything looks similar.

---

With colors:

```text
INFO  Application Started

WARN  Disk Space Low

ERROR Database Failed
```

Developers can quickly identify:

```text
Warnings
Errors
Critical Events
```

---

# Spring Boot Color Support

Spring Boot automatically supports:

```text
ANSI Colors
```

through:

```text
spring-boot-starter-logging
```

---

# Default Color Mapping

| Log Level | Color |
|------------|--------|
| TRACE | Gray |
| DEBUG | Blue |
| INFO | Green |
| WARN | Yellow |
| ERROR | Red |

---

# Example

```java
log.info("Application Started");

log.warn("Memory Usage High");

log.error("Database Connection Failed");
```

---

Output (conceptually):

```text
Green  → INFO Application Started

Yellow → WARN Memory Usage High

Red    → ERROR Database Connection Failed
```

---

# Benefits

```text
Easy Monitoring
Fast Issue Detection
Better Readability
Improved Developer Experience
```

---

# 4. ANSI Colors

Spring Boot uses ANSI escape codes for coloring logs.

---

## What is ANSI?

ANSI stands for:

```text
American National Standards Institute
```

---

ANSI escape codes allow terminals to display:

```text
Colors
Styles
Formatting
```

---

# Spring Boot ANSI Support

Controlled using:

```properties
spring.output.ansi.enabled=
```

---

# Available Values

```properties
spring.output.ansi.enabled=ALWAYS
```

Always enable colors.

---

```properties
spring.output.ansi.enabled=NEVER
```

Disable colors.

---

```properties
spring.output.ansi.enabled=DETECT
```

Default value.

Automatically detect terminal support.

---

# Example

```properties
spring.output.ansi.enabled=ALWAYS
```

---

Result:

```text
Colored Logs Always Enabled
```

---

# ANSI Configuration Table

| Value | Description |
|---------|-------------|
| ALWAYS | Always Use Colors |
| NEVER | Disable Colors |
| DETECT | Auto Detect Support |

---

# Recommended Setting

Development:

```properties
spring.output.ansi.enabled=ALWAYS
```

---

Production:

```properties
spring.output.ansi.enabled=DETECT
```

or

```properties
spring.output.ansi.enabled=NEVER
```

depending on logging platform.

---

# 5. Custom Console Patterns

Spring Boot allows customization of console output.

---

## Property

```properties
logging.pattern.console=
```

---

# Syntax

```properties
logging.pattern.console=<pattern>
```

---

# Example

```properties
logging.pattern.console=
%d [%thread] %-5level %logger - %msg%n
```

---

Generated Output

```text
2026-06-15 10:30:25
[main]
INFO
EmployeeService
-
Employee Created
```

---

# Common Pattern Components

| Pattern | Meaning |
|-----------|----------|
| `%d` | Date & Time |
| `%level` | Log Level |
| `%thread` | Thread Name |
| `%logger` | Logger Name |
| `%msg` | Log Message |
| `%n` | New Line |

---

# Example 1: Simple Pattern

Configuration:

```properties
logging.pattern.console=
%msg%n
```

---

Output

```text
Application Started

Employee Created
```

---

# Example 2: Timestamp + Message

Configuration:

```properties
logging.pattern.console=
%d %msg%n
```

---

Output

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

Output

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

Output

```text
2026-06-15 10:30:25
[main]
Application Started
```

---

# Example 5: Production Pattern

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

# 6. Custom Colors in Console Logs

Spring Boot supports color conversion using:

```text
%clr
```

---

# Example

```properties
logging.pattern.console=
%clr(%d){yellow}
%clr(%level){green}
%clr(%msg){blue}%n
```

---

Conceptual Output

```text
Yellow → Timestamp

Green  → INFO

Blue   → Message
```

---

# Supported Colors

```text
black
red
green
yellow
blue
magenta
cyan
white
```

---

# Example

```properties
logging.pattern.console=
%clr(%level){red}
%msg%n
```

---

Output

```text
INFO Application Started
```

with level displayed in red.

---

# 7. Real Production Example

Configuration:

```properties
spring.output.ansi.enabled=ALWAYS

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

Output

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
Timestamp
Thread Information
Severity
Source Class
Message
```

---

# 8. Best Practices

---

## Use Colored Logs During Development

```properties
spring.output.ansi.enabled=ALWAYS
```

---

Benefits:

```text
Better Visibility
Faster Debugging
```

---

## Include Timestamp

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
Quick Severity Detection
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

## Include Thread Information

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

# 9. Common Mistakes

---

## Removing Timestamp

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

## Using Very Long Logger Names

Bad:

```text
%logger
```

in huge applications.

---

Better:

```text
%logger{20}
```

---

## Excessive Colors

Bad:

```text
Every Component Different Color
```

---

Problem:

```text
Hard To Read
```

---

## Disabling Colors During Development

Bad:

```properties
spring.output.ansi.enabled=NEVER
```

---

Makes logs harder to scan.

---

# 10. Interview Questions

### Q1. What is Console Logging?

Displaying logs directly in the terminal or console output.

---

### Q2. Does Spring Boot enable console logging by default?

```text
Yes
```

---

### Q3. Which property customizes console output?

```properties
logging.pattern.console=
```

---

### Q4. What is ANSI?

ANSI provides terminal color and formatting support.

---

### Q5. Which property enables ANSI colors?

```properties
spring.output.ansi.enabled=
```

---

### Q6. What are possible values for ANSI configuration?

```text
ALWAYS
NEVER
DETECT
```

---

### Q7. Which pattern is used for custom colors?

```text
%clr
```

---

### Q8. Why are colored logs useful?

They help quickly identify:

```text
INFO
WARN
ERROR
```

based on color.

---

# Quick Revision

```text
Console Logging
---------------

Default Behavior

✔ Enabled Automatically
✔ Uses Logback
✔ Logs Displayed In Console

--------------------------------

Colored Logs

INFO  → Green

WARN  → Yellow

ERROR → Red

--------------------------------

ANSI Configuration

spring.output.ansi.enabled=

Values

ALWAYS
NEVER
DETECT

--------------------------------

Custom Console Pattern

logging.pattern.console=

Example

%d [%thread]
%-5level
%logger
-
%msg%n

--------------------------------

Useful Pattern Components

%d       → Timestamp

%level   → Severity

%thread  → Thread Name

%logger  → Class Name

%msg     → Message

%n       → New Line

%clr     → Color Formatting

--------------------------------

Remember

Console Logging
       ↓
Terminal Output

ANSI
       ↓
Color Support

Pattern
       ↓
Output Format
```