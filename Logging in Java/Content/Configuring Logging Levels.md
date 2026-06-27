# Configuring Logging Levels

> **Goal:** Understand how to configure logging levels in Spring Boot using `application.properties` and `application.yml`, configure root, package-level, and class-level logging, understand logging hierarchy, real-world production configurations, best practices, common mistakes, and interview concepts.

---

# 1. Why Configure Logging Levels?

Every application generates logs.

Examples:

```text
Application Started
User Logged In
Payment Successful
Database Connection Failed
```

Not all logs are equally important.

Sometimes we need:

```text
More Logs During Development
Less Logs In Production
Detailed Logs For Specific Modules
```

This is achieved through:

```text
Logging Level Configuration
```

---

## Example

Development:

```text
DEBUG Logs Needed
```

Production:

```text
Only INFO, WARN, ERROR Needed
```

---

# Definition

> Logging Level Configuration controls which log messages are displayed and which are ignored.

---

# 2. Default Logging Level in Spring Boot

Without any configuration:

```properties
Nothing
```

Spring Boot automatically uses:

```text
INFO
```

as the default root logging level.

---

Example:

```java
log.trace("Trace");

log.debug("Debug");

log.info("Info");

log.warn("Warn");

log.error("Error");
```

---

Output:

```text
INFO Info

WARN Warn

ERROR Error
```

---

Ignored:

```text
TRACE
DEBUG
```

---

# 3. Configuring Logging Levels Using application.properties

Most common approach.

---

## Syntax

```properties
logging.level.<logger-name>=<level>
```

---

# Root Logger Configuration

The root logger affects the entire application.

---

## Example

```properties
logging.level.root=INFO
```

---

Meaning:

```text
TRACE ❌
DEBUG ❌
INFO ✔
WARN ✔
ERROR ✔
```

---

# Visual Representation

```text
Application
      ↓
 Root Logger
      ↓
 INFO
```

---

# Example

Configuration:

```properties
logging.level.root=WARN
```

Application:

```java
log.info("Info");

log.warn("Warn");

log.error("Error");
```

Output:

```text
WARN Warn

ERROR Error
```

---

INFO is ignored.

---

# Common Root Configurations

Development:

```properties
logging.level.root=DEBUG
```

---

Testing:

```properties
logging.level.root=INFO
```

---

Production:

```properties
logging.level.root=INFO
```

or

```properties
logging.level.root=WARN
```

---

# 4. Package-Level Logging

One of the most powerful logging features.

---

## What is Package-Level Logging?

Allows different logging levels for different packages.

---

Example Project:

```text
com.company.controller

com.company.service

com.company.repository
```

---

Suppose:

```text
Only Service Layer Needs DEBUG Logs
```

---

Configuration:

```properties
logging.level.com.company.service=DEBUG
```

---

Result:

```text
Controller → INFO

Repository → INFO

Service → DEBUG
```

---

# Example

```properties
logging.level.root=INFO

logging.level.com.company.service=DEBUG
```

---

Application:

```java
// Service Class
log.debug("Service Debug");


// Controller Class
log.debug("Controller Debug");
```

---

Output:

```text
DEBUG Service Debug
```

---

Controller debug logs remain hidden.

---

# Real Production Example

```properties
logging.level.root=INFO

logging.level.com.company.payment=DEBUG
```

---

Reason:

```text
Payment Issues Being Investigated
```

Only payment module gets detailed logging.

---

# Package Hierarchy Behavior

Configuration:

```properties
logging.level.com.company=DEBUG
```

Applies to:

```text
com.company.controller

com.company.service

com.company.repository

com.company.*
```

---

Example:

```text
com.company
     ↓
 All Child Packages
```

---

# Specific Package Override

Configuration:

```properties
logging.level.com.company=INFO

logging.level.com.company.payment=DEBUG
```

---

Result:

```text
com.company              → INFO

com.company.payment      → DEBUG
```

---

More specific configuration wins.

---

# 5. Class-Level Logging

Provides the highest level of control.

---

## What is Class-Level Logging?

Allows configuring logging for a specific class.

---

Example Class:

```java
EmployeeService
```

---

Configuration:

```properties
logging.level.com.company.service.EmployeeService=DEBUG
```

---

Only:

```text
EmployeeService
```

gets DEBUG logging.

---

Other classes remain unchanged.

---

# Example

Configuration:

```properties
logging.level.root=INFO

logging.level.com.company.service.EmployeeService=DEBUG
```

---

Classes:

```java
EmployeeService
```

```java
OrderService
```

---

Output:

```text
EmployeeService → DEBUG

OrderService → INFO
```

---

# Real Scenario

Suppose:

```text
Employee Creation Issue
```

exists in production.

Instead of:

```properties
logging.level.root=DEBUG
```

Use:

```properties
logging.level.com.company.service.EmployeeService=DEBUG
```

---

Benefits:

```text
Smaller Logs
Focused Troubleshooting
Less Performance Impact
```

---

# 6. Configuring Logging Levels Using application.yml

Spring Boot supports YAML configuration.

---

Equivalent of:

```properties
logging.level.root=INFO
```

becomes:

```yaml
logging:
  level:
    root: INFO
```

---

# Package-Level Configuration

Properties:

```properties
logging.level.com.company=DEBUG
```

---

YAML:

```yaml
logging:
  level:
    com.company: DEBUG
```

---

# Class-Level Configuration

Properties:

```properties
logging.level.com.company.service.EmployeeService=DEBUG
```

---

YAML:

```yaml
logging:
  level:
    com.company.service.EmployeeService: DEBUG
```

---

# Full Example

```yaml
logging:
  level:
    root: INFO
    com.company.service: DEBUG
    com.company.service.EmployeeService: TRACE
```

---

Result:

```text
Entire Application → INFO

Service Package → DEBUG

EmployeeService → TRACE
```

---

# Properties vs YAML

---

## application.properties

```properties
logging.level.root=INFO

logging.level.com.company=DEBUG
```

---

## application.yml

```yaml
logging:
  level:
    root: INFO
    com.company: DEBUG
```

---

Same behavior.

---

# Which Should You Use?

Depends on project standards.

---

Properties:

```text
Simple
Easy
Common
```

---

YAML:

```text
Cleaner
Hierarchical
More Readable
```

---

# 7. Logging Configuration Hierarchy

Very Important Interview Topic.

---

Suppose:

```properties
logging.level.root=INFO

logging.level.com.company=DEBUG

logging.level.com.company.service.EmployeeService=TRACE
```

---

Hierarchy:

```text
EmployeeService
      ↓
TRACE

com.company
      ↓
DEBUG

Root
      ↓
INFO
```

---

# Which Level Wins?

Answer:

```text
Most Specific Configuration
```

wins.

---

# Example

EmployeeService:

```text
TRACE
```

---

Other Service Classes:

```text
DEBUG
```

---

Remaining Application:

```text
INFO
```

---

# Hierarchy Order

```text
Class Level
      ↓
Package Level
      ↓
Root Level
```

---

# 8. Real Production Configurations

---

## Development

```properties
logging.level.root=DEBUG
```

Reason:

```text
Need Detailed Logs
```

---

## QA

```properties
logging.level.root=INFO
```

Reason:

```text
Focus On Functional Testing
```

---

## Production

```properties
logging.level.root=INFO
```

or

```properties
logging.level.root=WARN
```

Reason:

```text
Reduce Log Volume
Improve Performance
```

---

## Temporary Troubleshooting

```properties
logging.level.com.company.payment=DEBUG
```

Reason:

```text
Investigate Specific Module
```

---

# 9. Logging Configuration Examples

---

## Example 1

```properties
logging.level.root=ERROR
```

Visible:

```text
ERROR
```

Only.

---

## Example 2

```properties
logging.level.root=DEBUG
```

Visible:

```text
DEBUG
INFO
WARN
ERROR
```

---

## Example 3

```properties
logging.level.root=TRACE
```

Visible:

```text
TRACE
DEBUG
INFO
WARN
ERROR
```

---

## Example 4

```properties
logging.level.root=OFF
```

Visible:

```text
Nothing
```

---

# 10. Common Mistakes

---

## Setting DEBUG In Production

Bad:

```properties
logging.level.root=DEBUG
```

Can cause:

```text
Huge Log Files
Storage Problems
Performance Issues
```

---

## Using Root Logger For Troubleshooting

Bad:

```properties
logging.level.root=DEBUG
```

---

Better:

```properties
logging.level.com.company.payment=DEBUG
```

---

## Conflicting Configurations

Example:

```properties
logging.level.root=INFO

logging.level.com.company=DEBUG

logging.level.com.company.service.EmployeeService=TRACE
```

Understand hierarchy properly.

---

# 11. Interview Questions

### Q1. What is the default logging level in Spring Boot?

```text
INFO
```

---

### Q2. How do you configure the root logging level?

```properties
logging.level.root=INFO
```

---

### Q3. How do you configure package-level logging?

```properties
logging.level.com.company.service=DEBUG
```

---

### Q4. How do you configure class-level logging?

```properties
logging.level.com.company.service.EmployeeService=DEBUG
```

---

### Q5. Which configuration has highest priority?

```text
Class Level
```

---

### Q6. Which configuration has lowest priority?

```text
Root Level
```

---

### Q7. Which wins if multiple levels are configured?

```text
Most Specific Configuration
```

---

### Q8. How do you configure logging in YAML?

```yaml
logging:
  level:
    root: INFO
```

---

# Quick Revision

```text
Configuring Logging Levels
--------------------------

Root Logger

logging.level.root=INFO

Applies To:

✔ Entire Application

--------------------------------

Package Level

logging.level.com.company=DEBUG

Applies To:

✔ Package
✔ Child Packages

--------------------------------

Class Level

logging.level.com.company.service.EmployeeService=TRACE

Applies To:

✔ Single Class

--------------------------------

Hierarchy

Class Level
      ↓
Package Level
      ↓
Root Level

Most Specific Wins

--------------------------------

application.properties

logging.level.root=INFO

--------------------------------

application.yml

logging:
  level:
    root: INFO

--------------------------------

Production Recommendation

Development → DEBUG
QA          → INFO
Production  → INFO/WARN

Remember

Root
 ↓
Entire Application

Package
 ↓
Specific Module

Class
 ↓
Single Class

Specificity Wins
```