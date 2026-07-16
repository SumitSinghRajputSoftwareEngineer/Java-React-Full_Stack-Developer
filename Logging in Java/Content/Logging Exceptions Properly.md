# Logging Exceptions Properly

> **Goal:** Understand how to log exceptions correctly in Spring Boot applications, avoid common logging mistakes, capture stack traces properly, perform effective root cause analysis, and follow production-grade logging practices.

---

# 1. Why Exception Logging is Important?

In production systems, failures are inevitable.

Examples:

```text
Database Connection Failure

Null Pointer Exception

Network Timeout

API Failure

File Read Failure
```

---

When an exception occurs, developers need answers to:

```text
What Failed?

Why Did It Fail?

Where Did It Fail?

When Did It Fail?

How Can It Be Fixed?
```

---

Without proper exception logging:

```text
Troubleshooting Becomes Difficult

Root Cause Becomes Unknown

Production Support Suffers
```

---

# Example

Bad:

```java
catch (Exception ex) {
    System.out.println("Error");
}
```

Output:

```text
Error
```

---

Problem:

```text
No Exception Details

No Stack Trace

No Root Cause
```

---

# Proper Logging

```java
catch (Exception ex) {
    log.error(
        "Error while saving employee",
        ex
    );
}
```

---

Output:

```text
ERROR Error while saving employee

java.lang.NullPointerException
...
```

---

Benefits:

```text
Error Message

Exception Details

Complete Stack Trace
```

---

# Why Logging Exceptions Matters

Provides:

```text
Debugging Information

Production Monitoring

Root Cause Analysis

Faster Incident Resolution
```

---

# 2. Correct Way to Log Exceptions

Most important topic.

---

## Recommended Approach

```java
log.error(
    "Error while saving employee",
    ex
);
```

---

# Why Is This Correct?

Because Logback automatically logs:

```text
Custom Message

Exception Type

Exception Message

Complete Stack Trace
```

---

# Example

Code:

```java
try {
    employeeRepository.save(employee);
}
catch (Exception ex) {

    log.error(
        "Error while saving employee",
        ex
    );
}
```

---

Output:

```text
ERROR Error while saving employee

java.sql.SQLException

at com.company.service.EmployeeService
...
```

---

# Internal Flow

```text
Exception Occurs
       ↓
Logger Receives Exception
       ↓
Logback Extracts Stack Trace
       ↓
Writes Full Error Information
```

---

# What Gets Logged?

```text
Timestamp

Log Level

Error Message

Exception Type

Exception Message

Stack Trace
```

---

# Example

```java
log.error(
    "Database operation failed",
    ex
);
```

Output:

```text
ERROR Database operation failed

java.sql.SQLException:
Connection timeout

at EmployeeRepository.save(...)
at EmployeeService.create(...)
```

---

# Why Not Log Only the Message?

Bad:

```java
log.error(
    ex.getMessage()
);
```

Output:

```text
Connection timeout
```

---

Lost Information:

```text
Exception Type

Stack Trace

Execution Path
```

---

# 3. Understanding Stack Trace

One of the most important concepts in exception logging.

---

## What is a Stack Trace?

A stack trace shows:

```text
Where Exception Occurred

How Execution Reached That Point

Method Call Chain
```

---

# Example

```java
service()
   ↓
repository()
   ↓
database()
```

---

If exception occurs:

```text
database()
```

Stack trace shows:

```text
database()

repository()

service()
```

---

# Example Stack Trace

```text
java.lang.NullPointerException

at EmployeeRepository.save(EmployeeRepository.java:45)

at EmployeeService.create(EmployeeService.java:25)

at EmployeeController.create(EmployeeController.java:15)
```

---

# Reading Stack Trace

---

## Top Line

```text
java.lang.NullPointerException
```

Exception type.

---

## First Method

```text
EmployeeRepository.java:45
```

Actual failure location.

---

## Remaining Methods

```text
Execution Path
```

showing how application reached the failure.

---

# Why Stack Trace Is Critical?

Provides:

```text
Failure Location

Call Hierarchy

Execution Context
```

---

Without stack trace:

```text
Guesswork
```

---

With stack trace:

```text
Accurate Debugging
```

---

# 4. Common Mistakes

Very important interview topic.

---

# Mistake 1: Logging Only Exception Message

Bad:

```java
log.error(
    ex.getMessage()
);
```

---

Output:

```text
Database timeout
```

---

Missing:

```text
Stack Trace

Failure Location

Execution Path
```

---

# Correct

```java
log.error(
    "Database operation failed",
    ex
);
```

---

# Mistake 2: Swallowing Exceptions

Bad:

```java
catch (Exception ex) {

}
```

---

Problem:

```text
Exception Completely Lost
```

---

Production Impact:

```text
No Visibility

No Troubleshooting
```

---

# Correct

```java
catch (Exception ex) {

    log.error(
        "Unexpected error",
        ex
    );
}
```

---

# Mistake 3: Using printStackTrace()

Bad:

```java
ex.printStackTrace();
```

---

Problems:

```text
Not Structured

Cannot Be Monitored

Cannot Be Filtered

Not Production Friendly
```

---

# Correct

```java
log.error(
    "Unexpected error",
    ex
);
```

---

# Mistake 4: Logging and Ignoring

Bad:

```java
catch (Exception ex) {

    log.error("Error", ex);

}
```

---

Problem:

```text
Error Hidden

Application Continues Incorrectly
```

---

Better:

```java
catch (Exception ex) {

    log.error(
        "Error while saving employee",
        ex
    );

    throw ex;
}
```

---

# Mistake 5: Duplicate Logging

Bad:

```java
Controller Logs

Service Logs

Repository Logs
```

---

Same exception logged:

```text
Three Times
```

---

Result:

```text
Log Pollution
```

---

Better:

```text
Log Once At Appropriate Layer
```

---

# 5. Stack Trace Logging

One of the most important production topics.

---

## What is Stack Trace Logging?

Logging the complete execution path that caused an exception.

---

# Example

```java
log.error(
    "Failed to create employee",
    ex
);
```

---

Generated Output:

```text
ERROR Failed to create employee

java.lang.NullPointerException

at EmployeeRepository.save()

at EmployeeService.create()

at EmployeeController.create()
```

---

# Advantages

```text
Precise Failure Location

Call Chain Visibility

Easy Debugging
```

---

# Logback Behavior

When exception object is passed:

```java
log.error(
    "Message",
    ex
);
```

Logback automatically logs:

```text
Complete Stack Trace
```

---

No need to do:

```java
ex.printStackTrace()
```

---

# Comparison

Bad:

```java
log.error(ex.getMessage());
```

---

Good:

```java
log.error(
    "Operation failed",
    ex
);
```

---

# Why?

Good version contains:

```text
Message

Exception

Stack Trace
```

---

# 6. Root Cause Analysis (RCA)

One of the primary goals of exception logging.

---

## What is Root Cause Analysis?

Root Cause Analysis (RCA) is the process of identifying the original cause of a failure.

---

# Example

User sees:

```text
Employee Creation Failed
```

---

This is:

```text
Symptom
```

---

Actual root cause may be:

```text
Database Connection Timeout
```

---

# Example Stack Trace

```text
Employee Creation Failed

Caused by:

java.sql.SQLException

Connection Timeout
```

---

Root Cause:

```text
Database Connectivity Issue
```

---

# Why Logging Helps RCA?

Logs provide:

```text
Exception Type

Error Message

Stack Trace

Execution Context
```

---

# Example

```java
log.error(
    "Failed to save employee {}",
    employeeId,
    ex
);
```

---

Output:

```text
Employee ID

Failure Reason

Complete Stack Trace
```

---

Provides context required for RCA.

---

# RCA Process

```text
Production Issue
        ↓
Logs
        ↓
Exception
        ↓
Stack Trace
        ↓
Root Cause
        ↓
Fix
```

---

# Example Investigation

Log:

```text
ERROR Failed to create employee

java.sql.SQLException

Connection Timeout
```

---

Analysis:

```text
Application Code OK

Database Unavailable
```

---

Root Cause Found:

```text
Database Connectivity Problem
```

---

# 7. Best Practices

---

## Always Log Exception Object

Recommended:

```java
log.error(
    "Operation failed",
    ex
);
```

---

## Add Meaningful Messages

Bad:

```java
log.error(
    "Error",
    ex
);
```

---

Better:

```java
log.error(
    "Failed to save employee",
    ex
);
```

---

## Include Business Context

Good:

```java
log.error(
    "Failed to save employee {}",
    employeeId,
    ex
);
```

---

Benefits:

```text
Better Troubleshooting

Faster RCA
```

---

## Log at Correct Level

Exceptions should typically use:

```text
ERROR
```

---

Sometimes:

```text
WARN
```

for recoverable exceptions.

---

## Avoid Sensitive Data

Never log:

```text
Passwords

Tokens

Credit Cards

Secrets
```

---

# 8. Production Example

```java
try {

    employeeRepository.save(employee);

}
catch (Exception ex) {

    log.error(
        "Failed to save employee with id {}",
        employee.getId(),
        ex
    );

    throw ex;
}
```

---

Output:

```text
2026-06-15 10:30:25 ERROR

Failed to save employee with id 101

java.sql.SQLException

Connection Timeout

at EmployeeRepository.save(...)
```

---

Provides:

```text
Business Context

Exception Details

Stack Trace

Root Cause Information
```

---

# 9. Interview Questions

### Q1. What is the correct way to log exceptions?

```java
log.error(
    "Error while saving employee",
    ex
);
```

---

### Q2. Why should we pass the exception object?

To log:

```text
Exception Type

Exception Message

Complete Stack Trace
```

---

### Q3. What is a stack trace?

A sequence of method calls showing how execution reached the exception.

---

### Q4. Why is stack trace logging important?

It helps identify:

```text
Failure Location

Execution Path

Root Cause
```

---

### Q5. Why is `printStackTrace()` discouraged?

Because it is:

```text
Not Structured

Not Production Friendly

Not Monitorable
```

---

### Q6. What is Root Cause Analysis (RCA)?

The process of identifying the original reason behind a failure.

---

### Q7. What is wrong with `log.error(ex.getMessage())`?

It loses:

```text
Stack Trace

Exception Type

Failure Location
```

---

### Q8. Which log level should generally be used for exceptions?

```text
ERROR
```

---

### Q9. Should exceptions be logged multiple times?

```text
No
```

Avoid duplicate logging unless necessary.

---

### Q10. What additional information should be logged?

```text
Employee ID

Order ID

Transaction ID

Request ID
```

when relevant.

---

# Quick Revision

```text
Logging Exceptions Properly
---------------------------

Correct Way

log.error(
    "Operation failed",
    ex
)

--------------------------------

Why?

✔ Logs Exception
✔ Logs Message
✔ Logs Stack Trace

--------------------------------

Avoid

log.error(ex.getMessage())

Reason

✘ No Stack Trace
✘ No Failure Location

--------------------------------

Avoid

ex.printStackTrace()

Reason

✘ Not Structured
✘ Not Production Ready

--------------------------------

Stack Trace

Shows

✔ Failure Location
✔ Method Call Chain
✔ Execution Path

--------------------------------

Root Cause Analysis

Issue
   ↓
Logs
   ↓
Exception
   ↓
Stack Trace
   ↓
Root Cause
   ↓
Fix

--------------------------------

Best Practices

✔ Log Exception Object
✔ Add Meaningful Message
✔ Include Business Context
✔ Use ERROR Level
✔ Avoid Sensitive Data

--------------------------------

Remember

Best Practice

log.error(
    "Meaningful Message",
    ex
)

This Automatically Logs

✔ Exception Type
✔ Exception Message
✔ Complete Stack Trace
```