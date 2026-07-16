# Common Logging Mistakes

> **Goal:** Understand the most common logging mistakes made in real-world applications, their impact on performance, security, maintainability, troubleshooting, and how to avoid them in production systems.

---

# 1. Introduction to Common Logging Mistakes

Logging is one of the most important aspects of software development.

---

Good logging helps:

```text
Debug Issues

Monitor Systems

Track Business Events

Analyze Performance

Investigate Security Incidents
```

---

However, poor logging practices can cause:

```text
Performance Problems

Security Risks

Storage Issues

Difficult Troubleshooting

Operational Confusion
```

---

# Reality

Many production incidents become difficult to diagnose because of:

```text
Poor Logging Practices
```

---

# Goal

Understand:

```text
What Mistakes To Avoid

Why They Are Dangerous

How To Fix Them
```

---

# Most Common Logging Mistakes

```text
Excessive Logging

Missing Exception Stack Traces

Logging Sensitive Data

Incorrect Log Levels

Duplicate Logs
```

---

# 2. Excessive Logging

One of the most common mistakes.

---

## What is Excessive Logging?

### Definition

> Logging too much information that provides little or no value.

---

# Example

```java
log.debug("Method Started");
log.debug("Step 1");
log.debug("Step 2");
log.debug("Step 3");
log.debug("Step 4");
log.debug("Step 5");
log.debug("Method Completed");
```

---

# Problem

For a single request:

```text
Many Log Entries
```

---

For thousands of requests:

```text
Millions Of Log Entries
```

---

# Consequences

```text
Huge Log Files

Storage Costs

Performance Overhead

Difficult Troubleshooting
```

---

# Why Is It Bad?

Important logs become buried inside:

```text
Noise
```

---

Example

Useful Log:

```text
Database Connection Failed
```

---

Hidden among:

```text
Step 1 Completed

Step 2 Completed

Step 3 Completed

Step 4 Completed
```

---

# Production Impact

```text
Higher Disk Usage

Increased Log Processing

More Network Traffic

Slower Searches
```

---

# Bad Example

```java
log.info("Entering Method");
log.info("Variable Loaded");
log.info("Loop Started");
log.info("Loop Finished");
```

---

# Better Example

```java
log.info(
    "Employee Created Successfully. EmployeeId={}",
    employeeId
);
```

---

# Best Practice

Log:

```text
Important Business Events

Errors

Warnings

Performance Metrics
```

---

Avoid:

```text
Unnecessary Internal Steps
```

---

# Rule

```text
Log Information

Not Noise
```

---

# 3. Missing Exception Stack Traces

Extremely common mistake.

---

## What is a Stack Trace?

A stack trace shows:

```text
Where Exception Occurred

How Execution Reached That Point

Root Cause
```

---

# Example Exception

```java
ArithmeticException
```

---

Without stack trace:

```java
log.error(
    "Calculation Failed"
);
```

---

# Output

```text
Calculation Failed
```

---

Question:

```text
Why Did It Fail?
```

---

Impossible to know.

---

# Correct Approach

```java
log.error(
    "Calculation Failed",
    ex
);
```

---

# Output

```text
Calculation Failed

java.lang.ArithmeticException:
Divide By Zero
...
```

---

# Benefits

```text
Root Cause Analysis

Faster Debugging

Complete Context
```

---

# Common Wrong Approach

```java
log.error(
    ex.getMessage()
);
```

---

Output

```text
Divide By Zero
```

---

Problem:

```text
No Stack Trace
```

---

# Best Practice

Always log:

```java
log.error(
    "Operation Failed",
    exception
);
```

---

# Rule

```text
Never Lose The Stack Trace
```

---

# 4. Logging Sensitive Data

One of the most dangerous mistakes.

---

## What is Sensitive Data?

Information that should remain confidential.

---

Examples

```text
Passwords

Credit Card Numbers

JWT Tokens

API Keys

Access Tokens

Personal Information
```

---

# Bad Example

```java
log.info(
    "Password={}",
    password
);
```

---

# Generated Log

```text
Password=admin123
```

---

# Security Risk

```text
Critical
```

---

# Why Dangerous?

Logs are often:

```text
Stored For Months

Shared Across Teams

Sent To Centralized Systems
```

---

Sensitive data may become visible to:

```text
Developers

Support Teams

Third-Party Platforms
```

---

# Another Bad Example

```java
log.info(
    "JWT={}",
    jwtToken
);
```

---

# Risk

```text
Account Compromise

Unauthorized Access
```

---

# Correct Approach

```java
log.info(
    "Authentication Successful"
);
```

---

Or:

```java
log.info(
    "JWT Token Validated"
);
```

---

# Best Practice

Never log:

```text
Passwords

JWT Tokens

API Keys

Credit Cards

Secret Keys
```

---

# Use Data Masking

Example

```text
************1111
```

instead of:

```text
4111111111111111
```

---

# Rule

```text
Log Events

Never Log Secrets
```

---

# 5. Incorrect Log Levels

Very common in enterprise applications.

---

## What is an Incorrect Log Level?

Using the wrong severity level for a log message.

---

# Example

Application startup:

```java
log.error(
    "Application Started"
);
```

---

# Problem

Startup is not an error.

---

# Correct

```java
log.info(
    "Application Started"
);
```

---

# Example 2

Validation failure:

```java
log.info(
    "Invalid User Input"
);
```

---

Better:

```java
log.warn(
    "Invalid User Input"
);
```

---

# Example 3

Database connection failure:

```java
log.info(
    "Database Connection Failed"
);
```

---

Correct:

```java
log.error(
    "Database Connection Failed"
);
```

---

# Why Important?

Monitoring tools often trigger alerts based on:

```text
ERROR Logs

WARN Logs
```

---

Incorrect levels cause:

```text
Missed Alerts

False Alerts

Monitoring Issues
```

---

# Proper Log Level Usage

| Level | Usage |
|---------|---------|
| TRACE | Detailed Internal Flow |
| DEBUG | Troubleshooting |
| INFO | Business Events |
| WARN | Recoverable Issues |
| ERROR | Failures |
| OFF | Disable Logging |

---

# Best Practice

Choose the level that accurately reflects:

```text
Severity

Impact

Urgency
```

---

# 6. Duplicate Logs

Another common production problem.

---

## What are Duplicate Logs?

The same event being logged multiple times.

---

# Example

Service Layer

```java
log.error(
    "Database Error"
);
```

---

Repository Layer

```java
log.error(
    "Database Error"
);
```

---

Controller Layer

```java
log.error(
    "Database Error"
);
```

---

# Result

Same issue logged:

```text
Three Times
```

---

# Problems

```text
Log Noise

Storage Waste

Confusing Analysis
```

---

# Example Output

```text
Database Error

Database Error

Database Error
```

---

Question:

```text
Did One Error Occur?

Or Three?
```

---

Confusing.

---

# Root Cause

Same exception logged at multiple layers.

---

# Recommended Approach

Log exception once at the appropriate layer.

---

Example

```text
Repository Throws Exception

↓

Service Handles

↓

Controller Logs
```

---

# Result

Single Clear Log Entry.

---

# Benefits

```text
Cleaner Logs

Reduced Storage

Easier Troubleshooting
```

---

# Rule

```text
Log Once

Handle Everywhere
```

---

# 7. Real Production Example

Imagine a payment failure.

---

Poor Logging:

```java
log.info("Step 1");
log.info("Step 2");
log.info("Step 3");

log.error("Payment Failed");

log.error("Payment Failed");

log.error("Payment Failed");
```

---

Problems:

```text
Noise

Duplicates

Missing Details
```

---

Good Logging:

```java
log.error(
    "Payment Failed. OrderId={}",
    orderId,
    ex
);
```

---

Provides:

```text
Business Context

Order ID

Stack Trace

Root Cause
```

---

# 8. Impact of Logging Mistakes

| Mistake | Impact |
|----------|---------|
| Excessive Logging | Large Logs, Performance Issues |
| Missing Stack Traces | Difficult Debugging |
| Logging Sensitive Data | Security Breaches |
| Incorrect Log Levels | Monitoring Problems |
| Duplicate Logs | Log Noise |

---

# 9. How to Avoid Logging Mistakes

---

## Review Log Statements

Ask:

```text
Is This Useful?
```

---

## Log Exceptions Properly

Always include:

```java
exception
```

---

## Use Correct Levels

Follow:

```text
TRACE

DEBUG

INFO

WARN

ERROR
```

---

## Avoid Sensitive Information

Mask:

```text
Passwords

Cards

Tokens
```

---

## Prevent Duplicate Logging

Log:

```text
At The Right Layer
```

---

# Logging Quality Checklist

Before deployment:

```text
✔ No Sensitive Data

✔ Correct Log Levels

✔ Stack Traces Included

✔ No Duplicate Logs

✔ Meaningful Messages

✔ Business Context Included
```

---

# 10. Best Practices

---

## Log Important Events

Examples:

```text
Order Created

Payment Completed

User Logged In
```

---

## Log Exceptions With Stack Traces

Example

```java
log.error(
    "Payment Failed",
    ex
);
```

---

## Use Proper Severity Levels

Improves monitoring.

---

## Avoid Excessive Logging

Focus on valuable information.

---

## Log Once

Avoid duplication.

---

## Protect Sensitive Data

Mask or remove confidential information.

---

# Golden Principle

```text
Every Log Entry

Should Provide Value
```

---

# 11. Interview Questions

### Q1. What is excessive logging?

Logging too much unnecessary information that creates noise and impacts performance.

---

### Q2. Why should stack traces be logged?

To identify the exact cause and location of an exception.

---

### Q3. What is wrong with logging passwords?

It creates serious security risks and compliance violations.

---

### Q4. Why are incorrect log levels dangerous?

They can cause missed alerts or false monitoring signals.

---

### Q5. What are duplicate logs?

The same event being logged multiple times.

---

### Q6. Why should duplicate logs be avoided?

They create confusion, storage waste, and noisy logs.

---

### Q7. What is the correct way to log exceptions?

```java
log.error(
    "Operation Failed",
    ex
);
```

---

### Q8. Which log level should be used for application startup?

```text
INFO
```

---

### Q9. Which log level should be used for system failures?

```text
ERROR
```

---

### Q10. What is the golden rule of logging?

```text
Every Log Entry

Must Provide Value
```

---

# Quick Revision

```text
Common Logging Mistakes
-----------------------

1. Excessive Logging

Problem

✔ Huge Logs
✔ Performance Impact
✔ Noise

--------------------------------

2. Missing Stack Traces

Wrong

log.error("Failed");

Correct

log.error("Failed", ex);

--------------------------------

3. Logging Sensitive Data

Never Log

✘ Passwords
✘ JWT Tokens
✘ API Keys
✘ Credit Cards

--------------------------------

4. Incorrect Log Levels

INFO

Business Events

WARN

Recoverable Issues

ERROR

Failures

--------------------------------

5. Duplicate Logs

Problem

Same Event Logged
Multiple Times

--------------------------------

Best Practices

✔ Meaningful Messages
✔ Proper Levels
✔ Include Stack Traces
✔ Avoid Sensitive Data
✔ Log Once
✔ Focus On Business Value

--------------------------------

Golden Rule

Every Log Entry

Should Help

Debug

Monitor

Or

Troubleshoot

The Application
```