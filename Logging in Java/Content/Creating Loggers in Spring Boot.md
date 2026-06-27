# Creating Loggers in Spring Boot

> **Goal:** Understand how to create loggers in Spring Boot using SLF4J and Lombok, how loggers work internally, different ways of creating loggers, advantages and disadvantages of each approach, best practices, real-world examples, and interview questions.

---

# 1. What is a Logger?

A Logger is an object responsible for generating log messages.

Whenever we write:

```java
log.info("Application Started");
```

or

```java
logger.error("Database Connection Failed");
```

the object generating those logs is called:

```text
Logger
```

---

## Definition

> A Logger is an object provided by a logging framework that records application events, warnings, debugging information, and errors.

---

## Simple Flow

```text
Application
      ↓
   Logger
      ↓
   SLF4J
      ↓
  Logback
      ↓
Console/File
```

---

# Why Do We Need a Logger?

Without a logger:

```java
System.out.println("User Created");
```

Problems:

```text
No Log Levels
No Formatting
No File Logging
No Filtering
No Production Monitoring
```

---

Using a logger:

```java
logger.info("User Created");
```

provides:

```text
Log Levels
Log Formatting
File Logging
Monitoring Support
Production Readiness
```

---

# 2. Creating Logger Using SLF4J

This is the traditional and most widely used approach.

---

## Required Imports

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
```

---

## Logger Declaration

```java
private static final Logger logger =
        LoggerFactory.getLogger(EmployeeService.class);
```

---

# Understanding Each Keyword

```java
private static final Logger logger =
        LoggerFactory.getLogger(EmployeeService.class);
```

---

## private

```text
Accessible Only Inside Class
```

---

## static

```text
Single Logger Instance
Shared Across Objects
```

Logger creation is expensive.

Using static ensures:

```text
Only One Logger Is Created
```

---

## final

```text
Cannot Be Reassigned
```

Example:

```java
logger = anotherLogger;
```

Not allowed.

---

## Logger

Type provided by:

```java
org.slf4j.Logger
```

---

## LoggerFactory

Factory class responsible for creating logger objects.

---

## getLogger()

Creates or returns a logger instance.

---

## EmployeeService.class

Represents:

```text
Current Class Name
```

---

# Complete Example

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class EmployeeService {

    private static final Logger logger =
            LoggerFactory.getLogger(
                    EmployeeService.class);

}
```

---

# Internal Working

When application starts:

```java
LoggerFactory.getLogger(
        EmployeeService.class);
```

SLF4J creates:

```text
EmployeeService Logger
```

Internally:

```text
EmployeeService
        ↓
     Logger
        ↓
     SLF4J
        ↓
    Logback
```

---

# Using Logger

---

## INFO Log

```java
logger.info("Employee Created");
```

Output:

```text
INFO Employee Created
```

---

## DEBUG Log

```java
logger.debug("Employee ID = 101");
```

Output:

```text
DEBUG Employee ID = 101
```

---

## WARN Log

```java
logger.warn("Salary Not Provided");
```

Output:

```text
WARN Salary Not Provided
```

---

## ERROR Log

```java
logger.error("Database Connection Failed");
```

Output:

```text
ERROR Database Connection Failed
```

---

# Parameterized Logging

Best Practice.

---

Bad:

```java
logger.info(
    "Employee Id = " + employeeId);
```

---

Good:

```java
logger.info(
    "Employee Id = {}",
    employeeId);
```

---

Benefits:

```text
Better Performance
Cleaner Code
Lazy Evaluation
```

---

# Multiple Parameters

```java
logger.info(
    "Employee {} belongs to {}",
    employeeId,
    department
);
```

Output:

```text
Employee 101 belongs to IT
```

---

# Exception Logging

Best Practice:

```java
try {

}
catch(Exception ex){

    logger.error(
        "Error while saving employee",
        ex
    );
}
```

---

Output:

```text
Message
Stack Trace
Root Cause
```

---

Never do:

```java
logger.error(ex.getMessage());
```

because:

```text
Stack Trace Lost
```

---

# 3. Creating Logger Using Lombok @Slf4j

Modern Spring Boot projects often use Lombok.

---

## What is Lombok?

Lombok is a Java library that reduces boilerplate code.

Examples:

```text
Getter
Setter
Constructor
Builder
Logger
```

can be generated automatically.

---

## Required Dependency

```xml
<dependency>
    <groupId>org.projectlombok</groupId>

    <artifactId>lombok</artifactId>

    <optional>true</optional>
</dependency>
```

---

## Required Import

```java
import lombok.extern.slf4j.Slf4j;
```

---

## Usage

```java
@Slf4j
public class EmployeeService {

}
```

---

That's it.

No manual logger creation needed.

---

# What Does Lombok Generate?

When compiler processes:

```java
@Slf4j
public class EmployeeService {

}
```

Lombok automatically generates:

```java
private static final Logger log =
        LoggerFactory.getLogger(
                EmployeeService.class);
```

behind the scenes.

---

# Equivalent Code

---

Using SLF4J

```java
private static final Logger logger =
    LoggerFactory.getLogger(
        EmployeeService.class);
```

---

Using Lombok

```java
@Slf4j
public class EmployeeService {

}
```

Both produce:

```text
Same Result
```

---

# Example

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class EmployeeService {

    public void createEmployee(){

        log.info(
            "Employee Created Successfully");

    }
}
```

---

Output

```text
INFO Employee Created Successfully
```

---

# Internal Working

```text
Compiler
      ↓
Lombok Annotation Processor
      ↓
Generates Logger
      ↓
Compiled Class
```

---

Developer writes:

```java
log.info();
```

Generated code internally:

```java
private static final Logger log =
LoggerFactory.getLogger(
EmployeeService.class);
```

---

# Benefits of @Slf4j

---

## Less Boilerplate

Without Lombok:

```java
private static final Logger logger =
LoggerFactory.getLogger(
EmployeeService.class);
```

---

With Lombok:

```java
@Slf4j
```

Only one annotation.

---

## Cleaner Code

Code becomes:

```text
Smaller
Readable
Maintainable
```

---

## Standardized Logging

Every class follows:

```java
log.info()
```

pattern.

---

# 4. Different Lombok Logger Annotations

Although Spring Boot mostly uses:

```java
@Slf4j
```

Lombok supports other logging frameworks.

---

## @Slf4j

Generates:

```java
org.slf4j.Logger
```

Recommended.

---

## @Log

Generates:

```java
java.util.logging.Logger
```

---

## @Log4j

Generates:

```java
Log4j Logger
```

---

## @Log4j2

Generates:

```java
Log4j2 Logger
```

---

## @CommonsLog

Generates:

```java
Apache Commons Logger
```

---

# Most Common Choice

```java
@Slf4j
```

because Spring Boot uses:

```text
SLF4J + Logback
```

by default.

---

# 5. Logger vs @Slf4j

Most Asked Interview Question.

---

| Feature | Logger | @Slf4j |
|----------|----------|----------|
| Boilerplate Code | More | Less |
| Manual Creation | Yes | No |
| Dependency Required | SLF4J | SLF4J + Lombok |
| Readability | Good | Better |
| Logger Generated Automatically | No | Yes |
| Popular In Modern Projects | Medium | High |

---

# Traditional Approach

```java
private static final Logger logger =
LoggerFactory.getLogger(
EmployeeService.class);
```

---

# Lombok Approach

```java
@Slf4j
public class EmployeeService {

}
```

---

# Memory Trick

```text
Logger
 ↓
Manual

@Slf4j
 ↓
Automatic
```

---

# 6. Which One Should You Use?

---

## Use Logger When

```text
Project Does Not Use Lombok
Need Explicit Logger Creation
Legacy Application
```

---

## Use @Slf4j When

```text
Project Uses Lombok
Want Cleaner Code
Modern Spring Boot Applications
```

---

# Industry Recommendation

For modern Spring Boot projects:

```java
@Slf4j
```

is preferred.

Reason:

```text
Less Boilerplate
Cleaner Classes
Standard Practice
```

---

# 7. Common Logging Practices

---

## Always Use Parameterized Logging

Good:

```java
log.info(
    "Employee Id = {}",
    employeeId
);
```

---

Bad:

```java
log.info(
    "Employee Id = " + employeeId
);
```

---

## Log Exceptions Properly

Good:

```java
log.error(
    "Failed to save employee",
    exception
);
```

---

Bad:

```java
log.error(
    exception.getMessage()
);
```

---

## Use Correct Log Levels

```java
log.info("Employee Created");

log.warn("Department Missing");

log.error("Database Failed");
```

---

Avoid:

```java
log.error("Employee Created");
```

Wrong severity.

---

# 8. Real Production Example

```java
@Slf4j
@Service
public class EmployeeService {

    public void createEmployee(
            Employee employee){

        log.info(
            "Employee Creation Started");

        try {

            log.debug(
                "Employee Id = {}",
                employee.getId());

            repository.save(employee);

            log.info(
                "Employee Created Successfully");

        }
        catch(Exception ex){

            log.error(
                "Employee Creation Failed",
                ex);

        }
    }
}
```

---

Generated Logs:

```text
INFO Employee Creation Started

DEBUG Employee Id = 101

INFO Employee Created Successfully
```

or

```text
ERROR Employee Creation Failed
```

with stack trace.

---

# 9. Interview Questions

### Q1. What is a Logger?

A Logger is an object used to generate log messages in an application.

---

### Q2. Which class creates loggers in SLF4J?

```java
LoggerFactory
```

---

### Q3. Why is Logger declared static?

To create only one logger instance per class.

---

### Q4. Why is Logger declared final?

To prevent reassignment.

---

### Q5. What does @Slf4j do?

Automatically creates an SLF4J logger using Lombok.

---

### Q6. Does @Slf4j create a logger at runtime?

No.

Lombok generates logger code during compilation.

---

### Q7. Which is better: Logger or @Slf4j?

Modern Spring Boot projects usually prefer:

```java
@Slf4j
```

because it reduces boilerplate code.

---

### Q8. What is generated by @Slf4j?

Equivalent to:

```java
private static final Logger log =
LoggerFactory.getLogger(
EmployeeService.class);
```

---

# Quick Revision

```text
Creating Loggers in Spring Boot
-------------------------------

Traditional Approach

private static final Logger logger =
LoggerFactory.getLogger(
EmployeeService.class);

Components

Logger
    ↓
Creates Log Messages

LoggerFactory
    ↓
Creates Logger Objects

Lombok Approach

@Slf4j

Compiler Generates

private static final Logger log =
LoggerFactory.getLogger(
CurrentClass.class);

Advantages of @Slf4j

✔ Less Boilerplate
✔ Cleaner Code
✔ Industry Standard
✔ Automatic Logger Creation

Best Practices

✔ Use Parameterized Logging
✔ Log Exceptions With Stack Trace
✔ Use Correct Log Levels

Remember

Logger
   ↓
Manual Logger Creation

@Slf4j
   ↓
Automatic Logger Creation
```