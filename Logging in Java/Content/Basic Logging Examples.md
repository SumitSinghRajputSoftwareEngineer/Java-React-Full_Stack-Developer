# Basic Logging Examples

> **Goal:** Understand how to write logs in real Spring Boot applications, including logging messages, variables, method entry and exit points, exceptions, and business events. Learn best practices, common mistakes, production-ready logging techniques, and interview concepts.

---

# 1. Why Do We Write Logs?

A logging framework becomes useful only when we start recording meaningful information.

In real-world applications, we log:

```text
Application Events
Business Events
Variable Values
Method Execution
Exceptions
Warnings
System Activities
```

---

## Example

```java
log.info("Application Started");
```

Output:

```text
INFO Application Started
```

This tells us:

```text
Application Started Successfully
```

---

# 2. Logging Messages

The most basic type of logging.

---

## What is Logging a Message?

Writing a simple text message to describe what happened in the application.

---

## Example

```java
log.info("Employee Created Successfully");
```

Output:

```text
INFO Employee Created Successfully
```

---

## Common Message Logs

```java
log.info("Application Started");

log.info("User Logged In");

log.info("Order Created");

log.info("Payment Successful");
```

---

## Real Example

```java
public void createEmployee() {

    log.info(
        "Employee Creation Started");

    // Business Logic

    log.info(
        "Employee Created Successfully");
}
```

---

Output:

```text
INFO Employee Creation Started

INFO Employee Created Successfully
```

---

# Why Log Messages?

They help answer:

```text
What Happened?
When Did It Happen?
Which Operation Was Executed?
```

---

# 3. Logging Variables

Very common during development and troubleshooting.

---

## Why Log Variables?

To verify:

```text
Input Values
Output Values
Calculated Values
Business Data
```

---

## Example

```java
int employeeId = 101;

log.debug(
    "Employee Id = {}",
    employeeId
);
```

Output:

```text
DEBUG Employee Id = 101
```

---

# Parameterized Logging

Recommended approach.

---

## Good Practice

```java
log.info(
    "Employee Id = {}",
    employeeId
);
```

---

## Bad Practice

```java
log.info(
    "Employee Id = " + employeeId
);
```

---

# Why Parameterized Logging?

Benefits:

```text
Better Performance
Cleaner Code
Lazy Evaluation
```

---

# Multiple Variables

```java
log.info(
    "Employee {} belongs to {}",
    employeeId,
    department
);
```

---

Output

```text
Employee 101 belongs to IT
```

---

# Logging Object Properties

```java
log.info(
    "Employee Name = {}",
    employee.getName()
);
```

---

## Real Example

```java
public void saveEmployee(
        Employee employee){

    log.debug(
        "Employee Name = {}",
        employee.getName());

    log.debug(
        "Employee Salary = {}",
        employee.getSalary());
}
```

---

# What Should NOT Be Logged?

Never log:

```text
Passwords
JWT Tokens
API Keys
Credit Card Data
CVV
OTP
```

---

## Bad Example

```java
log.info(
    "Password = {}",
    password
);
```

---

## Good Example

```java
log.info(
    "User Login Successful"
);
```

---

# 4. Logging Method Entry and Exit

One of the most common debugging techniques.

---

## Purpose

Helps track:

```text
Method Execution Flow
Request Flow
Service Flow
Application Flow
```

---

# Method Entry Logging

Log when a method starts.

---

Example

```java
public void createEmployee() {

    log.debug(
        "Entering createEmployee()");

}
```

---

Output

```text
DEBUG Entering createEmployee()
```

---

# Method Exit Logging

Log when method completes.

---

Example

```java
public void createEmployee() {

    log.debug(
        "Entering createEmployee()");

    // Logic

    log.debug(
        "Exiting createEmployee()");
}
```

---

Output

```text
DEBUG Entering createEmployee()

DEBUG Exiting createEmployee()
```

---

# Real Example

```java
public Employee getEmployee(
        Long id){

    log.debug(
        "Entering getEmployee()");

    Employee employee =
        repository.findById(id)
                  .orElseThrow();

    log.debug(
        "Exiting getEmployee()");

    return employee;
}
```

---

# Benefits

```text
Track Execution Flow
Identify Bottlenecks
Debug Complex Applications
Trace Requests
```

---

# Advanced Example

```java
public void processOrder(){

    log.trace(
        "Entering processOrder()");

    // Logic

    log.trace(
        "Exiting processOrder()");
}
```

---

TRACE level is commonly used for:

```text
Method Entry
Method Exit
Execution Tracking
```

---

# Entry and Exit Flow

```text
Method Start
      ↓
Entry Log
      ↓
Business Logic
      ↓
Exit Log
      ↓
Method End
```

---

# 5. Logging Exceptions

One of the most important logging practices.

---

## Why Log Exceptions?

Without exception logs:

```text
Failure Cause Unknown
Hard To Troubleshoot
Long Resolution Time
```

---

## Example

```java
try {

    repository.save(employee);

}
catch(Exception ex){

    log.error(
        "Employee Save Failed"
    );
}
```

---

Problem:

```text
Exception Details Missing
```

---

# Correct Way

```java
try {

    repository.save(employee);

}
catch(Exception ex){

    log.error(
        "Employee Save Failed",
        ex
    );
}
```

---

Output

```text
ERROR Employee Save Failed

java.lang.Exception
...
Stack Trace
...
```

---

# Why Pass Exception Object?

Provides:

```text
Exception Message
Stack Trace
Root Cause
```

---

# Never Do This

```java
log.error(
    ex.getMessage()
);
```

---

Problem:

```text
Stack Trace Lost
```

---

# Real Example

```java
public void processPayment(){

    try {

        paymentService.pay();

    }
    catch(Exception ex){

        log.error(
            "Payment Processing Failed",
            ex
        );
    }
}
```

---

Generated Log

```text
ERROR Payment Processing Failed

java.net.SocketTimeoutException
...
```

---

# Exception Logging Levels

| Scenario | Level |
|----------|--------|
| Business Validation Failure | WARN |
| Retry Operation | WARN |
| Service Failure | ERROR |
| Database Failure | ERROR |
| Unexpected Exception | ERROR |

---

# Example

```java
if(age < 18){

    log.warn(
        "User is below age limit");
}
```

---

# 6. Logging Business Events

This is the most important logging category in production systems.

---

## What is a Business Event?

An action important to the business.

Examples:

```text
User Registration
Order Creation
Payment Completion
Invoice Generation
Account Activation
```

---

# Why Log Business Events?

To answer:

```text
What Did User Do?
What Happened To Order?
Was Payment Successful?
```

---

# Example

```java
log.info(
    "Order Created Successfully"
);
```

---

Output

```text
INFO Order Created Successfully
```

---

# Real E-Commerce Example

```java
log.info(
    "Customer Logged In");

log.info(
    "Product Added To Cart");

log.info(
    "Order Created");

log.info(
    "Payment Successful");
```

---

Generated Logs

```text
INFO Customer Logged In

INFO Product Added To Cart

INFO Order Created

INFO Payment Successful
```

---

# Banking Example

```java
log.info(
    "Account Created");

log.info(
    "Money Transferred");

log.info(
    "Loan Application Submitted");
```

---

# Microservice Example

Order Service:

```java
log.info(
    "Order Created");
```

Payment Service:

```java
log.info(
    "Payment Successful");
```

Shipping Service:

```java
log.info(
    "Shipment Initiated");
```

---

Combined Logs

```text
INFO Order Created

INFO Payment Successful

INFO Shipment Initiated
```

---

# Business Event Best Practices

---

## Log Important Events

Good:

```java
log.info(
    "Order Created");
```

---

Bad:

```java
log.debug(
    "Order Created");
```

Business events should generally be:

```text
INFO
```

---

## Include Context

Good:

```java
log.info(
    "Order {} Created",
    orderId
);
```

---

Output

```text
Order 1001 Created
```

---

# 7. Real Production Example

```java
@Slf4j
@Service
public class EmployeeService {

    public void createEmployee(
            Employee employee){

        log.debug(
            "Entering createEmployee()");

        log.info(
            "Employee Creation Started");

        try{

            log.debug(
                "Employee Name = {}",
                employee.getName());

            repository.save(employee);

            log.info(
                "Employee Created Successfully");

        }
        catch(Exception ex){

            log.error(
                "Employee Creation Failed",
                ex);

        }

        log.debug(
            "Exiting createEmployee()");
    }
}
```

---

Generated Logs

```text
DEBUG Entering createEmployee()

INFO Employee Creation Started

DEBUG Employee Name = John

INFO Employee Created Successfully

DEBUG Exiting createEmployee()
```

---

# 8. Common Logging Mistakes

---

## Logging Sensitive Information

Bad:

```java
log.info(
    "Password = {}",
    password
);
```

---

## String Concatenation

Bad:

```java
log.info(
    "Employee Id = " + id
);
```

---

Good:

```java
log.info(
    "Employee Id = {}",
    id
);
```

---

## Missing Exception Object

Bad:

```java
log.error(
    "Payment Failed"
);
```

---

Good:

```java
log.error(
    "Payment Failed",
    ex
);
```

---

## Logging Every Small Detail As INFO

Bad:

```java
log.info(
    "Loop Iteration = {}",
    i
);
```

Use:

```java
log.debug(
    "Loop Iteration = {}",
    i
);
```

---

# 9. Interview Questions

### Q1. Why do we log variables?

To inspect application state and troubleshoot issues.

---

### Q2. What is the best way to log variables?

```java
log.info(
    "Employee Id = {}",
    employeeId
);
```

Parameterized logging.

---

### Q3. Why should we log method entry and exit?

To trace execution flow and debug complex applications.

---

### Q4. How should exceptions be logged?

```java
log.error(
    "Error While Saving Employee",
    exception
);
```

---

### Q5. Why is logging ex.getMessage() not recommended?

Because stack trace information is lost.

---

### Q6. What are Business Event Logs?

Logs that record important business activities such as:

```text
Order Creation
Payment Completion
User Registration
```

---

### Q7. Which log level is commonly used for business events?

```text
INFO
```

---

# Quick Revision

```text
Basic Logging Examples
----------------------

1. Logging Messages

log.info("Employee Created")

Used For:

✔ Application Events
✔ User Actions
✔ Business Events

--------------------------------

2. Logging Variables

log.info(
    "Employee Id = {}",
    employeeId
)

Best Practice:

✔ Parameterized Logging

--------------------------------

3. Method Entry/Exit

log.debug(
    "Entering Method"
)

log.debug(
    "Exiting Method"
)

Used For:

✔ Flow Tracking
✔ Debugging

--------------------------------

4. Exception Logging

log.error(
    "Operation Failed",
    exception
)

Always Pass:

✔ Exception Object

--------------------------------

5. Business Events

log.info(
    "Order Created"
)

Examples:

✔ User Login
✔ Payment Success
✔ Order Creation
✔ Invoice Generation

Remember

Messages
    ↓
What Happened

Variables
    ↓
Current Data

Entry/Exit
    ↓
Execution Flow

Exceptions
    ↓
Failure Details

Business Events
    ↓
Important Activities
```