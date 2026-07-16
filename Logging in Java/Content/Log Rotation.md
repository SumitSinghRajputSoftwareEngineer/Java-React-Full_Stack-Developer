# Log Rotation

> **Goal:** Understand Log Rotation in Spring Boot, why it is required, how rolling log files work, size-based and time-based rotation strategies, Spring Boot Logback rolling policies, important configuration properties, production best practices, and interview concepts.

---

# 1. What is Log Rotation?

When an application runs continuously, log files keep growing.

Example:

```text
Day 1 → 100 MB

Day 5 → 500 MB

Day 30 → 5 GB

Day 180 → 50 GB
```

---

## Problem

Large log files can cause:

```text
Disk Space Issues
Performance Problems
Difficult Log Analysis
Backup Challenges
Server Crashes
```

---

## Solution

Automatically create new log files when certain conditions are met.

This process is called:

```text
Log Rotation
```

---

## Definition

> Log Rotation is the process of automatically archiving the current log file and creating a new one when a predefined size or time limit is reached.

---

# Real World Example

Current Log File:

```text
application.log
```

After reaching rotation condition:

```text
application.log

application.log.1

application.log.2

application.log.3
```

---

# Benefits of Log Rotation

```text
Prevents Huge Log Files
Improves Log Management
Saves Disk Space
Supports Archiving
Simplifies Troubleshooting
```

---

# Without Rotation

```text
application.log

Size = 50 GB
```

---

# With Rotation

```text
application.log

application.log.1

application.log.2

application.log.3
```

Each file remains manageable.

---

# Log Rotation Flow

```text
Application
      ↓
Logger
      ↓
Logback
      ↓
Current Log File
      ↓
Rotation Trigger
      ↓
Archive Old File
      ↓
Create New File
```

---

# 2. Rolling Log Files

Log Rotation is also called:

```text
Rolling Logs
```

because logs "roll" from one file to another.

---

## What is a Rolling Log File?

A rolling log file automatically creates new log files when configured conditions are met.

---

# Example

Current File:

```text
application.log
```

After rotation:

```text
application.log

application.log.1
```

---

More rotations:

```text
application.log

application.log.1

application.log.2

application.log.3
```

---

# Visual Representation

```text
application.log
       ↓
Rotation
       ↓
application.log.1

New application.log Created
```

---

# Why Rolling Files?

Without rolling:

```text
Single Huge File
```

With rolling:

```text
Multiple Manageable Files
```

---

# Common Rotation Triggers

```text
File Size
Date/Time
Application Restart
```

---

# 3. Size-Based Rotation

Most commonly used rotation strategy.

---

## What is Size-Based Rotation?

A new log file is created when the current file reaches a specified size.

---

# Example

Configuration:

```text
Max File Size = 10 MB
```

---

Current State:

```text
application.log
```

Size:

```text
9 MB
```

No rotation.

---

After:

```text
10 MB
```

Reached:

```text
application.log
      ↓
application.log.1

New application.log
```

---

# Visual Example

```text
application.log

Size = 10 MB
      ↓
Rotate
      ↓

application.log.1

application.log (new)
```

---

# Spring Boot Property

```properties
logging.logback.rollingpolicy.max-file-size=10MB
```

---

Meaning:

```text
Rotate Log File After 10 MB
```

---

# Example

```properties
logging.file.name=application.log

logging.logback.rollingpolicy.max-file-size=50MB
```

---

Result:

```text
application.log reaches 50 MB
      ↓
Rotated
      ↓
application.log.1
```

---

# Why Use Size-Based Rotation?

Benefits:

```text
Controls Disk Usage
Prevents Huge Files
Improves Search Speed
Simplifies Backup
```

---

# Real Production Example

```properties
logging.logback.rollingpolicy.max-file-size=100MB
```

---

Common sizes:

```text
10 MB

50 MB

100 MB

500 MB
```

depending on system requirements.

---

# 4. Time-Based Rotation

Another popular strategy.

---

## What is Time-Based Rotation?

Creates a new log file based on time intervals.

---

Examples:

```text
Daily
Weekly
Monthly
Hourly
```

---

# Daily Rotation Example

Day 1:

```text
application-2026-06-15.log
```

---

Day 2:

```text
application-2026-06-16.log
```

---

Day 3:

```text
application-2026-06-17.log
```

---

# Visual Representation

```text
June 15
     ↓
application-2026-06-15.log

June 16
     ↓
application-2026-06-16.log

June 17
     ↓
application-2026-06-17.log
```

---

# Why Time-Based Rotation?

Useful for:

```text
Daily Monitoring
Compliance Requirements
Log Archiving
Production Support
```

---

# Typical Usage

Large enterprises often store:

```text
One Log File Per Day
```

---

Examples:

```text
payment-service-2026-06-15.log

payment-service-2026-06-16.log

payment-service-2026-06-17.log
```

---

# Time-Based Rotation Advantages

```text
Easy Date-Based Search
Easy Archiving
Better Organization
```

---

# Size-Based vs Time-Based Rotation

| Feature | Size-Based | Time-Based |
|----------|------------|------------|
| Trigger | File Size | Date/Time |
| Example | 100 MB | Daily |
| File Naming | log.1, log.2 | log-2026-06-15 |
| Common Usage | Small Systems | Enterprise Systems |

---

# Real Production Usage

Often both strategies are combined.

---

Example:

```text
Rotate Daily

OR

Rotate At 100 MB
```

whichever occurs first.

---

# 5. Spring Boot Rolling Policy

Spring Boot uses:

```text
Logback Rolling Policy
```

for log rotation.

---

# What is Rolling Policy?

Rules that determine:

```text
When To Rotate
How Many Files To Keep
Maximum File Size
Retention Duration
```

---

# Example

```properties
logging.logback.rollingpolicy.max-file-size=50MB

logging.logback.rollingpolicy.max-history=30
```

---

Meaning:

```text
Rotate At 50 MB

Keep Logs For 30 Rotations
```

---

# 6. max-file-size Property

One of the most important logging properties.

---

## Property

```properties
logging.logback.rollingpolicy.max-file-size
```

---

# Purpose

Defines:

```text
Maximum Size Of A Log File
Before Rotation Occurs
```

---

# Example

```properties
logging.logback.rollingpolicy.max-file-size=10MB
```

---

Behavior:

```text
0 MB → 9 MB
      ↓
No Rotation

10 MB
      ↓
Rotation Happens
```

---

# Another Example

```properties
logging.logback.rollingpolicy.max-file-size=100MB
```

---

Meaning:

```text
Rotate After 100 MB
```

---

# Supported Units

```text
KB

MB

GB
```

---

Examples:

```properties
logging.logback.rollingpolicy.max-file-size=500KB
```

---

```properties
logging.logback.rollingpolicy.max-file-size=50MB
```

---

```properties
logging.logback.rollingpolicy.max-file-size=1GB
```

---

# Production Recommendation

```properties
logging.logback.rollingpolicy.max-file-size=100MB
```

---

Very common configuration.

---

# 7. max-history Property

Another very important production setting.

---

## Property

```properties
logging.logback.rollingpolicy.max-history
```

---

# Purpose

Determines:

```text
How Many Archived Log Files
Should Be Retained
```

---

# Example

```properties
logging.logback.rollingpolicy.max-history=30
```

---

Meaning:

```text
Keep Last 30 Archived Logs
```

---

# Example

Current Logs:

```text
application.log

application.log.1

application.log.2

...

application.log.30
```

---

When:

```text
application.log.31
```

would be created,

oldest log is deleted.

---

Result:

```text
application.log.1 removed

application.log.31 added
```

---

# Why max-history Is Important?

Without it:

```text
Infinite Log Growth
```

---

Can cause:

```text
Disk Space Exhaustion
Server Problems
Backup Issues
```

---

# Common Values

Development:

```properties
logging.logback.rollingpolicy.max-history=7
```

---

QA:

```properties
logging.logback.rollingpolicy.max-history=15
```

---

Production:

```properties
logging.logback.rollingpolicy.max-history=30
```

or

```properties
logging.logback.rollingpolicy.max-history=90
```

---

# 8. Complete Configuration Example

```properties
logging.file.name=application.log

logging.logback.rollingpolicy.max-file-size=100MB

logging.logback.rollingpolicy.max-history=30
```

---

Meaning:

```text
Store Logs In application.log

Rotate At 100 MB

Keep 30 Archived Logs
```

---

# Generated Files

```text
application.log

application.log.1

application.log.2

...

application.log.30
```

---

# 9. Real Production Scenario

Payment Service:

```properties
logging.file.name=payment-service.log

logging.logback.rollingpolicy.max-file-size=200MB

logging.logback.rollingpolicy.max-history=60
```

---

Behavior:

```text
Current Log File

200 MB Reached
      ↓
Rotate

Keep Last 60 Archives
```

---

Benefits:

```text
Reliable Troubleshooting
Controlled Disk Usage
Long-Term History
```

---

# 10. Best Practices

---

## Always Enable Rotation

Good:

```text
File Logging + Rotation
```

---

Bad:

```text
Single Unlimited Log File
```

---

## Configure max-file-size

Good:

```properties
logging.logback.rollingpolicy.max-file-size=100MB
```

---

Reason:

```text
Prevent Huge Files
```

---

## Configure max-history

Good:

```properties
logging.logback.rollingpolicy.max-history=30
```

---

Reason:

```text
Prevent Unlimited Storage Growth
```

---

## Use Meaningful File Names

Good:

```properties
logging.file.name=payment-service.log
```

---

Bad:

```properties
logging.file.name=app.log
```

for large enterprise systems.

---

# 11. Common Mistakes

---

## No Rotation

Bad:

```properties
logging.file.name=application.log
```

without rolling policy.

---

Problem:

```text
Massive Log Files
```

---

## Huge max-history

Bad:

```properties
logging.logback.rollingpolicy.max-history=1000
```

---

Problem:

```text
Disk Space Consumption
```

---

## Tiny max-file-size

Bad:

```properties
logging.logback.rollingpolicy.max-file-size=100KB
```

---

Problem:

```text
Excessive File Creation
```

---

# 12. Interview Questions

### Q1. What is Log Rotation?

Automatically creating new log files when size or time limits are reached.

---

### Q2. Why is Log Rotation needed?

To prevent log files from growing indefinitely.

---

### Q3. What is a Rolling Log File?

A log file that automatically rotates based on configured rules.

---

### Q4. Which property controls file size rotation?

```properties
logging.logback.rollingpolicy.max-file-size
```

---

### Q5. What does max-file-size do?

Defines the maximum size before a log file is rotated.

---

### Q6. Which property controls log retention?

```properties
logging.logback.rollingpolicy.max-history
```

---

### Q7. What does max-history do?

Determines how many archived log files should be retained.

---

### Q8. What are the two major rotation strategies?

```text
Size-Based Rotation

Time-Based Rotation
```

---

### Q9. Which strategy is commonly used in enterprise applications?

```text
Both Together
```

---

# Quick Revision

```text
Log Rotation
------------

Purpose

✔ Prevent Huge Log Files
✔ Save Disk Space
✔ Easier Maintenance
✔ Better Monitoring

--------------------------------

Rolling Log Files

application.log
       ↓
Rotate
       ↓
application.log.1

--------------------------------

Size-Based Rotation

Property

logging.logback.rollingpolicy.max-file-size=100MB

Trigger

File Size Reached

--------------------------------

Time-Based Rotation

Trigger

Daily
Weekly
Monthly

Example

application-2026-06-15.log

--------------------------------

Retention

Property

logging.logback.rollingpolicy.max-history=30

Meaning

Keep Last 30 Archives

--------------------------------

Common Production Setup

logging.file.name=application.log

logging.logback.rollingpolicy.max-file-size=100MB

logging.logback.rollingpolicy.max-history=30

--------------------------------

Remember

max-file-size
        ↓
When To Rotate

max-history
        ↓
How Long To Keep Logs

Rotation
        ↓
Manageable Log Files
```