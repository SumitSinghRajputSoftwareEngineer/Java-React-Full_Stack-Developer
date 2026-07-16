# File Logging

> **Goal:** Understand File Logging in Spring Boot, why it is important, how to enable logging to files, configure log file names and directories, understand file logging behavior, production best practices, common mistakes, and interview concepts.

---

# 1. What is File Logging?

By default, Spring Boot writes logs to:

```text
Console
```

Example:

```java
log.info("Application Started");
```

Output:

```text
2026-06-15 10:30:25 INFO
Application Started
```

appears only in the terminal.

---

## Problem with Console Logging

Suppose:

```text
Application Crashes
Server Restarts
Terminal Closes
```

All previous logs may be lost.

---

# Solution

Store logs permanently in a file.

This is called:

```text
File Logging
```

---

## Definition

> File Logging is the process of storing application logs in one or more files instead of only displaying them in the console.

---

# Why File Logging is Important?

File logging allows:

```text
Log Persistence
Audit Tracking
Issue Investigation
Production Monitoring
Compliance Requirements
```

---

# Real Example

A production issue occurs at:

```text
2:00 AM
```

Developers check:

```text
Application Log Files
```

to identify the root cause.

---

Without file logging:

```text
No Historical Logs
```

---

With file logging:

```text
Complete Event History Available
```

---

# File Logging Flow

```text
Application
      ↓
Logger
      ↓
SLF4J
      ↓
Logback
      ↓
Log File
```

---

# 2. Default Logging Behavior

Without configuration:

```properties
Nothing
```

Spring Boot writes logs to:

```text
Console Only
```

---

Example:

```java
log.info("Application Started");
```

Output:

```text
Console
```

---

No log file is created.

---

# 3. Enable File Logging

Spring Boot can automatically create log files.

---

## Property

```properties
logging.file.name=
```

---

# Example

```properties
logging.file.name=app.log
```

---

Meaning:

```text
Create Log File Named app.log
```

---

# Result

Project Directory:

```text
project
│
├── src
│
├── pom.xml
│
└── app.log
```

---

# Example Application

Configuration:

```properties
logging.file.name=app.log
```

---

Code:

```java
log.info("Application Started");

log.info("Employee Created");

log.error("Database Failed");
```

---

Generated File

```text
app.log
```

Contents:

```text
2026-06-15 10:30:25 INFO
Application Started

2026-06-15 10:30:30 INFO
Employee Created

2026-06-15 10:31:00 ERROR
Database Failed
```

---

# Important Point

When:

```properties
logging.file.name=app.log
```

is configured,

Spring Boot logs to:

```text
Console
+
File
```

Both locations.

---

# Visual Representation

```text
Application
      ↓
Logger
      ↓
Logback
     ↙ ↘

Console  File
```

---

# 4. logging.file.name

Most commonly used property.

---

## Syntax

```properties
logging.file.name=<file-name>
```

---

Example

```properties
logging.file.name=application.log
```

---

Generated File

```text
application.log
```

---

# Custom Name Example

```properties
logging.file.name=employee-service.log
```

---

Generated File

```text
employee-service.log
```

---

Useful for:

```text
Microservices
Multiple Applications
Separate Log Files
```

---

# Microservice Example

Order Service:

```properties
logging.file.name=order-service.log
```

---

Payment Service:

```properties
logging.file.name=payment-service.log
```

---

Generated:

```text
order-service.log

payment-service.log
```

---

# 5. Logging to Specific Directory

Sometimes log files should not be stored inside the project directory.

Instead:

```text
Dedicated Log Directory
```

---

# Property

```properties
logging.file.path=
```

---

Example

```properties
logging.file.path=/logs
```

---

Spring Boot automatically creates:

```text
/logs/spring.log
```

---

# Result

```text
logs
│
└── spring.log
```

---

# Important Note

When only:

```properties
logging.file.path=/logs
```

is configured,

Spring Boot creates:

```text
spring.log
```

automatically.

---

# Generated File

```text
/logs/spring.log
```

---

# Real Example

```properties
logging.file.path=/logs
```

---

Output:

```text
logs
│
└── spring.log
```

---

Contents:

```text
INFO Application Started

INFO Employee Created

ERROR Database Failed
```

---

# Common Linux Example

```properties
logging.file.path=/var/logs
```

---

Generated File

```text
/var/logs/spring.log
```

---

Frequently used in:

```text
Linux Servers
Docker Containers
Production Environments
```

---

# 6. Difference Between logging.file.name and logging.file.path

Very common interview question.

---

## logging.file.name

Configuration:

```properties
logging.file.name=app.log
```

---

Result:

```text
app.log
```

created directly.

---

You control:

```text
File Name
```

---

## logging.file.path

Configuration:

```properties
logging.file.path=/logs
```

---

Result:

```text
/logs/spring.log
```

---

You control:

```text
Directory
```

---

# Comparison Table

| Property | Controls |
|-----------|-----------|
| logging.file.name | File Name |
| logging.file.path | Directory |
| Both | File Logging |

---

# Which One Has Higher Priority?

If both are configured:

```properties
logging.file.name=employee.log

logging.file.path=/logs
```

---

Spring Boot uses:

```properties
logging.file.name
```

---

Result:

```text
employee.log
```

---

Reason:

```text
File Name Configuration Takes Priority
```

---

# 7. File Logging Configuration

Basic configuration example.

---

## Example

```properties
logging.file.name=application.log
```

---

Result:

```text
Logs Written To File
```

---

# More Realistic Example

```properties
logging.file.name=employee-service.log

logging.level.root=INFO
```

---

Meaning:

```text
Store Logs In File

Show INFO/WARN/ERROR
```

---

# Production Example

```properties
logging.file.name=production.log

logging.level.root=INFO

logging.level.com.company.payment=DEBUG
```

---

Result:

```text
Application Logs Stored

Payment Module Detailed Logs

Production Monitoring Enabled
```

---

# Development Example

```properties
logging.file.name=dev.log

logging.level.root=DEBUG
```

---

Provides:

```text
Detailed Troubleshooting Logs
```

---

# 8. Real Production Scenario

Suppose an application fails at:

```text
3:00 AM
```

Nobody is monitoring the terminal.

---

Next morning:

```text
Developers Check Log Files
```

---

Example:

```text
production.log
```

contains:

```text
ERROR Database Connection Failed

ERROR Payment Gateway Timeout

WARN High Memory Usage
```

---

This helps identify:

```text
Root Cause
Failure Time
Affected Module
```

---

# 9. File Logging Best Practices

---

## Use Dedicated Log Files

Good:

```properties
logging.file.name=order-service.log
```

---

Avoid:

```properties
logging.file.name=app.log
```

for large microservice systems.

---

# Use Meaningful Names

Good:

```properties
payment-service.log
```

---

Bad:

```properties
a.log
```

---

# Store Logs Outside Application Folder

Good:

```properties
logging.file.path=/var/logs
```

---

Reason:

```text
Cleaner Deployment
Better Maintenance
```

---

# Use INFO in Production

Good:

```properties
logging.level.root=INFO
```

---

Avoid:

```properties
logging.level.root=TRACE
```

---

Reason:

```text
Huge Log Files
Storage Problems
Performance Impact
```

---

# Monitor Log Growth

Large files can consume:

```text
Disk Space
Memory
Backup Storage
```

---

# 10. Common Mistakes

---

## Using TRACE in Production

Bad:

```properties
logging.level.root=TRACE
```

---

Problem:

```text
Massive Log Files
```

---

## Storing Logs Inside Source Folder

Bad:

```text
src/main/resources/logs
```

---

Better:

```text
/var/logs
```

---

## Ignoring File Logging

Bad:

```text
Console Only
```

---

Problem:

```text
Historical Data Lost
```

---

## Confusing file.name and file.path

Remember:

```text
file.name
      ↓
Actual File

file.path
      ↓
Directory
```

---

# 11. Internal Working

When:

```properties
logging.file.name=app.log
```

is configured,

Flow becomes:

```text
Application
      ↓
Logger
      ↓
SLF4J
      ↓
Logback
      ↓
app.log
```

---

Logback creates:

```text
File Appender
```

automatically.

---

# What is a File Appender?

Appender responsible for:

```text
Writing Logs To File
```

---

Flow:

```text
Logger
     ↓
File Appender
     ↓
Log File
```

---

# 12. Interview Questions

### Q1. What is File Logging?

Storing application logs in files instead of only displaying them in the console.

---

### Q2. How do you enable file logging?

```properties
logging.file.name=app.log
```

---

### Q3. What does logging.file.name do?

Creates a log file with the specified name.

---

### Q4. What does logging.file.path do?

Specifies the directory where logs should be stored.

---

### Q5. What file is created when only logging.file.path is configured?

```text
spring.log
```

---

### Q6. Which property has higher priority?

```properties
logging.file.name
```

---

### Q7. Does enabling file logging disable console logging?

```text
No
```

Logs are written to:

```text
Console
+
File
```

---

### Q8. Which component writes logs to files internally?

```text
File Appender
```

---

# Quick Revision

```text
File Logging
------------

Purpose

✔ Store Logs Permanently
✔ Production Monitoring
✔ Troubleshooting
✔ Audit Tracking

--------------------------------

Enable File Logging

logging.file.name=app.log

Creates

app.log

--------------------------------

Store Logs In Directory

logging.file.path=/logs

Creates

/logs/spring.log

--------------------------------

Difference

logging.file.name
       ↓
File Name

logging.file.path
       ↓
Directory

--------------------------------

Priority

logging.file.name
       ↓
Higher Priority

--------------------------------

Flow

Application
      ↓
Logger
      ↓
SLF4J
      ↓
Logback
      ↓
File Appender
      ↓
Log File

--------------------------------

Best Practices

✔ Use Dedicated Log Files
✔ Store Logs Outside App Folder
✔ Use INFO In Production
✔ Monitor Log Size

Remember

Console Logging
      ↓
Temporary

File Logging
      ↓
Persistent
```