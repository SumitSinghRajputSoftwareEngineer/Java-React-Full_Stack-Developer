# Logback Fundamentals

> **Goal:** Understand Logback, its role in Spring Boot logging, Logback architecture, core components (Logger, Appender, Encoder, Layout), internal working, request flow, production usage, and interview concepts.

---

# 1. What is Logback?

Logback is the default logging framework used by Spring Boot.

Whenever we write:

```java
log.info("Application Started");
```

the actual work of processing and writing logs is performed by:

```text
Logback
```

---

## Definition

> Logback is a powerful, flexible, and high-performance logging framework for Java applications and the default logging implementation used by Spring Boot.

---

# Logback History

Logback was created by:

:contentReference[oaicite:0]{index=0}

who also created:

```text
Log4j
```

---

Logback was developed as the successor to:

```text
Log4j 1.x
```

---

# Why Logback?

Logback provides:

```text
High Performance
Automatic Configuration
Log Rotation
Flexible Formatting
Filtering
Advanced Logging Features
```

---

# Spring Boot Logging Stack

```text
Application
      ↓
SLF4J
      ↓
Logback
      ↓
Console/File
```

---

# Real Example

Code:

```java
log.info("Employee Created");
```

Execution Flow:

```text
Application
      ↓
SLF4J
      ↓
Logback
      ↓
Console Output
```

---

# Why Spring Boot Uses Logback?

Advantages:

```text
Fast
Reliable
Mature
Production Ready
Easy Configuration
```

---

# Default Dependency

When we add:

```xml
spring-boot-starter-web
```

Spring Boot automatically includes:

```text
spring-boot-starter-logging
```

which internally provides:

```text
SLF4J

Logback
```

---

# Internal Flow

```text
Spring Boot
      ↓
SLF4J API
      ↓
Logback Implementation
      ↓
Console/File
```

---

# 2. Why Do We Need Logback?

Suppose we directly use:

```java
System.out.println(
    "Application Started");
```

Problems:

```text
No Log Levels
No Formatting
No File Logging
No Rotation
No Filtering
No Production Features
```

---

Logback solves all of these.

---

Example:

```java
log.info(
    "Application Started");
```

Output:

```text
2026-06-15 10:30:25 INFO
Application Started
```

---

Additional Features:

```text
Log Levels
File Logging
Rolling Logs
Custom Patterns
Async Logging
```

---

# 3. Logback Architecture

One of the most important interview topics.

---

# High-Level Architecture

```text
Application
      ↓
Logger
      ↓
Appender
      ↓
Encoder/Layout
      ↓
Output Destination
```

---

# Visual Architecture

```text
Application

      ↓

   Logger

      ↓

  Appender

      ↓

Encoder/Layout

      ↓

Console/File
```

---

# Detailed Architecture

```text
Application

      ↓

SLF4J

      ↓

Logger

      ↓

Appender

      ↓

Encoder

      ↓

Console/File
```

---

# Example Flow

Code:

```java
log.info(
    "Employee Created");
```

---

Processing:

```text
Logger Receives Event
        ↓

Appender Determines Destination
        ↓

Encoder Formats Message
        ↓

Console/File Output
```

---

Generated Output:

```text
INFO Employee Created
```

---

# 4. Logback Components

Logback consists of several important components.

---

Core Components:

```text
Logger

Appender

Encoder

Layout
```

---

# Component Overview

```text
Logger
    ↓
Creates Log Event

Appender
    ↓
Chooses Destination

Encoder
    ↓
Converts To Text

Layout
    ↓
Formats Message
```

---

# 5. Logger

The most important component.

---

## What is Logger?

A Logger creates log events.

---

Example:

```java
private static final Logger logger =
LoggerFactory.getLogger(
    EmployeeService.class);
```

---

Usage:

```java
logger.info(
    "Employee Created");
```

---

# Responsibility

Logger determines:

```text
Log Message
Log Level
Source Class
```

---

# Example

```java
logger.debug(
    "Employee Id = {}",
    employeeId);
```

---

Generated Event:

```text
DEBUG

Employee Id = 100
```

---

# Logger Flow

```text
Logger
     ↓
Creates Log Event
     ↓
Sends To Appender
```

---

# Logger Hierarchy

Loggers are organized by package structure.

---

Example:

```text
com.company

com.company.service

com.company.controller
```

---

Hierarchy:

```text
Root Logger

     ↓

com.company

     ↓

com.company.service

     ↓

EmployeeService
```

---

# Root Logger

Top-level logger.

---

Example:

```properties
logging.level.root=INFO
```

---

Applies to:

```text
Entire Application
```

---

# 6. Appender

Second major component.

---

## What is an Appender?

Appender decides:

```text
Where Logs Should Be Written
```

---

Examples:

```text
Console

File

Database

Remote Server
```

---

# Appender Flow

```text
Logger
     ↓
Appender
     ↓
Destination
```

---

# Example

Code:

```java
log.info(
    "Application Started");
```

---

Console Appender:

```text
INFO Application Started
```

appears in console.

---

# Common Appenders

---

## ConsoleAppender

Writes logs to:

```text
Console
```

---

Example:

```text
IDE Console

Terminal

Command Prompt
```

---

## FileAppender

Writes logs to:

```text
File
```

---

Example:

```text
application.log
```

---

## RollingFileAppender

Writes logs to:

```text
Rotating Files
```

---

Example:

```text
application.log

application.log.1

application.log.2
```

---

# Appender Architecture

```text
Logger

   ↓

Appender

   ↓

Console/File
```

---

# Multiple Appenders

Possible configuration:

```text
Console Appender

File Appender
```

---

Result:

```text
Logs Written To

Console
+
File
```

---

# Visual Example

```text
Logger

      ↓

  Appender

   ↙    ↘

Console  File
```

---

# 7. Encoder

Very important component in modern Logback.

---

## What is an Encoder?

Encoder converts log events into text format before writing them.

---

Example Log Event:

```text
Level = INFO

Message = Employee Created
```

---

Encoder converts:

```text
Internal Event
```

to

```text
INFO Employee Created
```

---

# Why Encoder?

Computers store:

```text
Objects
```

---

Log files require:

```text
Text
```

---

Encoder performs:

```text
Object → Text Conversion
```

---

# Example

Pattern:

```text
%d %level %msg%n
```

---

Generated Output:

```text
2026-06-15 INFO Employee Created
```

---

# Common Encoder

```text
PatternLayoutEncoder
```

---

Most frequently used in Spring Boot.

---

# Encoder Flow

```text
Log Event

    ↓

Encoder

    ↓

Formatted Text
```

---

# 8. Layout

Important concept often asked in interviews.

---

## What is a Layout?

Layout determines:

```text
How Log Messages Look
```

---

Example:

Without Layout:

```text
Employee Created
```

---

With Layout:

```text
2026-06-15 INFO Employee Created
```

---

# Responsibility

Layout controls:

```text
Timestamp

Log Level

Logger Name

Thread Name

Message Format
```

---

# Example Pattern

```text
%d %level %logger %msg%n
```

---

Output:

```text
2026-06-15
INFO
EmployeeService
Employee Created
```

---

# Layout vs Encoder

Common interview question.

---

## Layout

Responsible for:

```text
Formatting
```

---

## Encoder

Responsible for:

```text
Converting To Text
```

---

# Simplified View

```text
Layout
     ↓
How It Looks

Encoder
     ↓
How It Is Written
```

---

# Modern Logback

In modern Logback:

```text
Encoder
```

often internally uses:

```text
Layout
```

---

Most commonly:

```text
PatternLayoutEncoder
```

combines both concepts.

---

# 9. Complete Logback Flow

Suppose:

```java
log.info(
    "Order Created");
```

---

Step 1

```text
Logger
```

creates log event.

---

Step 2

```text
Appender
```

receives event.

---

Step 3

```text
Encoder/Layout
```

formats event.

---

Step 4

```text
Console/File
```

receives final text.

---

# Full Flow Diagram

```text
Application

      ↓

SLF4J

      ↓

Logger

      ↓

Appender

      ↓

Encoder

      ↓

Console/File
```

---

# Real Example

Code:

```java
log.info(
    "Order Created");
```

---

Output:

```text
2026-06-15 10:30:25
INFO
OrderService
Order Created
```

---

# Who Did What?

```text
Logger
    ↓
Created Event

Appender
    ↓
Selected Destination

Encoder
    ↓
Converted To Text

Layout
    ↓
Formatted Output
```

---

# 10. Logback Advantages

```text
High Performance

Native Spring Boot Support

Easy Configuration

Rolling Files

Flexible Patterns

Multiple Appenders

Production Ready
```

---

# 11. Common Interview Questions

### Q1. What is Logback?

Default logging framework used by Spring Boot.

---

### Q2. What is the relationship between SLF4J and Logback?

```text
SLF4J = Logging API

Logback = Logging Implementation
```

---

### Q3. What are the main Logback components?

```text
Logger

Appender

Encoder

Layout
```

---

### Q4. What does a Logger do?

Creates and manages log events.

---

### Q5. What does an Appender do?

Determines where logs are written.

---

### Q6. What does an Encoder do?

Converts log events into text.

---

### Q7. What does a Layout do?

Defines the log message format.

---

### Q8. What are common Appenders?

```text
ConsoleAppender

FileAppender

RollingFileAppender
```

---

### Q9. Which encoder is most commonly used?

```text
PatternLayoutEncoder
```

---

### Q10. Explain Logback Architecture.

```text
Application
      ↓
SLF4J
      ↓
Logger
      ↓
Appender
      ↓
Encoder/Layout
      ↓
Console/File
```

---

# Quick Revision

```text
Logback Fundamentals
--------------------

What is Logback?

✔ Default Spring Boot Logging Framework
✔ High Performance
✔ Production Ready

--------------------------------

Architecture

Application
      ↓
SLF4J
      ↓
Logger
      ↓
Appender
      ↓
Encoder/Layout
      ↓
Console/File

--------------------------------

Logger

Responsibility

✔ Create Log Events
✔ Manage Log Levels

--------------------------------

Appender

Responsibility

✔ Select Destination

Examples

ConsoleAppender
FileAppender
RollingFileAppender

--------------------------------

Encoder

Responsibility

✔ Convert Event To Text

Common Encoder

PatternLayoutEncoder

--------------------------------

Layout

Responsibility

✔ Format Log Output

Examples

Timestamp
Level
Thread
Logger
Message

--------------------------------

Remember

Logger
    ↓
Creates Event

Appender
    ↓
Chooses Destination

Encoder
    ↓
Converts To Text

Layout
    ↓
Formats Output
```