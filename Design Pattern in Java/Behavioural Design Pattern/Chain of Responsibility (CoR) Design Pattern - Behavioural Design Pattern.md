# Chain of Responsibility (CoR) Design Pattern

> **Goal:** Understand Chain of Responsibility Pattern from scratch, why it exists, how it works internally, Handler Chains, Request Forwarding, Dynamic Chains, Java examples, Spring examples, advantages, disadvantages, interview questions, and how it differs from Strategy, Command, and Decorator.

---

# 1. What Problem Does Chain of Responsibility Solve?

Suppose a company has an approval process.

Leave request:

```text
1 Day Leave
```

can be approved by:

```text
Team Lead
```

---

Leave request:

```text
5 Days Leave
```

can be approved by:

```text
Manager
```

---

Leave request:

```text
15 Days Leave
```

can be approved by:

```text
Director
```

---

Without CoR:

```java
if(days <= 2)
{
    teamLead.approve();
}
else if(days <= 10)
{
    manager.approve();
}
else
{
    director.approve();
}
```

---

Problem:

Every time a new approver is added:

```text
VP
CEO
HR
```

we modify existing code.

---

Violates:

```text
Open/Closed Principle
```

---

Need a better solution.

---

# 2. Definition

> Chain of Responsibility Pattern passes a request along a chain of handlers until one handler processes it.

---

# 3. Simple Meaning

Imagine:

```text
Customer Complaint
```

goes to:

```text
Support Agent
```

---

If agent cannot solve:

```text
Forward To Manager
```

---

If manager cannot solve:

```text
Forward To Director
```

---

Until someone handles it.

---

This is Chain of Responsibility.

---

# 4. Real-Life Examples

---

## Example 1: Technical Support

```text
Level 1 Support
        ↓
Level 2 Support
        ↓
Level 3 Support
```

---

Issue moves through chain.

---

# Example 2: ATM Cash Withdrawal

```text
₹2000 Notes
       ↓
₹500 Notes
       ↓
₹100 Notes
```

---

Each handler contributes.

---

# Example 3: Company Approval Process

```text
Team Lead
     ↓
Manager
     ↓
Director
     ↓
CEO
```

---

Request moves until approved.

---

# 5. Core Idea

Instead of:

```text
Client
 ↓
Specific Handler
```

Use:

```text
Handler
   ↓
Handler
      ↓
Handler
```

---

Each handler decides:

```text
Handle Request
OR
Forward Request
```

---

# 6. Structure

```text
Client
   ↓
Handler1
   ↓
Handler2
   ↓
Handler3
```

---

Request flows through chain.

---

# 7. Components

---

## 1. Handler

Common interface.

---

Example:

```java
Approver
```

---

## 2. Concrete Handler

Actual handler.

---

Example:

```java
TeamLead

Manager

Director
```

---

## 3. Client

Sends request.

---

# 8. UML Structure

```text
          Handler
             ↑
    -------------------
    ↑        ↑       ↑

TeamLead  Manager Director

       nextHandler
```

---

# 9. Real-World Example

# Leave Approval System

---

## Step 1: Create Request

```java
public class LeaveRequest {

    private int days;

    public LeaveRequest(int days) {

        this.days = days;
    }

    public int getDays() {

        return days;
    }
}
```

---

# Step 2: Create Handler

```java
public abstract class LeaveApprover {

    protected LeaveApprover next;

    public void setNext(
            LeaveApprover next) {

        this.next = next;
    }

    public abstract void approve(
            LeaveRequest request);
}
```

---

Important line:

```java
protected LeaveApprover next;
```

---

This creates the chain.

---

# Step 3: Team Lead

```java
public class TeamLead
        extends LeaveApprover {

    @Override
    public void approve(
            LeaveRequest request) {

        if(request.getDays() <= 2) {

            System.out.println(
                    "Approved by Team Lead");
        }
        else if(next != null) {

            next.approve(request);
        }
    }
}
```

---

# Step 4: Manager

```java
public class Manager
        extends LeaveApprover {

    @Override
    public void approve(
            LeaveRequest request) {

        if(request.getDays() <= 10) {

            System.out.println(
                    "Approved by Manager");
        }
        else if(next != null) {

            next.approve(request);
        }
    }
}
```

---

# Step 5: Director

```java
public class Director
        extends LeaveApprover {

    @Override
    public void approve(
            LeaveRequest request) {

        System.out.println(
                "Approved by Director");
    }
}
```

---

# Step 6: Client

```java
LeaveApprover teamLead =
        new TeamLead();

LeaveApprover manager =
        new Manager();

LeaveApprover director =
        new Director();

teamLead.setNext(manager);

manager.setNext(director);

teamLead.approve(
        new LeaveRequest(8));
```

---

Output:

```text
Approved by Manager
```

---

# 10. Internal Working

Request:

```text
8 Days Leave
```

---

Flow:

```text
Team Lead
     ↓
Manager
```

---

Team Lead checks:

```text
Can I approve?
```

No.

---

Forward:

```text
Manager
```

---

Manager approves.

---

Chain stops.

---

# 11. Request Flow Visualization

Request:

```text
15 Days
```

---

Flow:

```text
Team Lead
    ↓

Manager
    ↓

Director
```

---

Director handles.

---

# 12. Dynamic Chains

One major benefit.

---

Chain can be changed at runtime.

---

Example:

```java
manager.setNext(hr);
```

---

New handler added.

---

No existing code changes.

---

# 13. Types of Chain of Responsibility

Important interview topic.

---

## Type 1

### First Handler Wins

Most common.

---

Example:

```text
Leave Approval
```

---

Only one handler processes request.

---

Flow:

```text
Handler1
 ↓
Handler2
 ↓
Handler3
```

---

Stops when handled.

---

# 14. Type 2

### Multiple Handlers Process Request

Every handler executes.

---

Example:

```text
Servlet Filters
```

---

Flow:

```text
Authentication

Logging

Validation

Audit
```

---

All execute.

---

Very common in Spring.

---

# 15. Example of Multi-Handler Chain

```java
public void process(Request request)
{
    validate();

    next.process(request);
}
```

---

Each handler performs work.

---

Then forwards.

---

# 16. Java Examples

Many Java APIs use CoR.

---

## Exception Handling

```java
catch(IOException e)
```

---

```java
catch(Exception e)
```

---

Exception moves until matching handler found.

---

CoR-like behavior.

---

## Logging Frameworks

Different log handlers process requests.

---

# 17. Servlet Filter Chain

Best real-world example.

---

Components:

```text
Authentication Filter
       ↓
Authorization Filter
       ↓
Logging Filter
       ↓
Controller
```

---

Every request flows through chain.

---

Classic CoR.

---

# 18. Spring Framework Example

## Spring Security Filter Chain

:contentReference[oaicite:0]{index=0}

One of the most important examples.

---

Request Flow:

```text
JWT Filter
      ↓
Authentication Filter
      ↓
Authorization Filter
      ↓
Controller
```

---

Each filter:

```text
Handles
or
Forwards
```

---

Exactly Chain of Responsibility.

---

# 19. Spring Boot JWT Example

Suppose request:

```http
GET /orders
```

---

Flow:

```text
JwtAuthenticationFilter
        ↓
UsernamePasswordFilter
        ↓
AuthorizationFilter
        ↓
Controller
```

---

Each filter decides:

```text
Continue
OR
Stop
```

---

Perfect CoR example.

---

# 20. Advantages

---

## 1. Loose Coupling

Sender doesn't know handler.

---

## 2. Easy Extension

Add new handler easily.

---

## 3. Follows Open/Closed Principle

New handlers without modifying existing ones.

---

## 4. Flexible Processing

Chain can change dynamically.

---

## 5. Cleaner Code

Avoids huge:

```java
if-else
```

blocks.

---

# 21. Disadvantages

---

## 1. Debugging Complexity

Request path may be unclear.

---

## 2. Performance Overhead

Request may traverse many handlers.

---

## 3. Request May Never Be Handled

If chain configured incorrectly.

---

# 22. Chain of Responsibility vs Strategy

Most asked interview question.

---

## Strategy

Chooses one algorithm.

---

Example:

```text
UPI Payment

Card Payment

PayPal Payment
```

---

Client selects strategy.

---

## CoR

Request automatically moves through handlers.

---

Example:

```text
Team Lead
 ↓
Manager
 ↓
Director
```

---

### Memory Trick

```text
Strategy
      ↓
Choose Algorithm

CoR
      ↓
Pass Request
```

---

# 23. CoR vs Decorator

Very confusing because structure looks similar.

---

Both contain:

```java
next/reference
```

---

## Decorator

Adds behavior.

---

Example:

```text
Coffee
 ↓
Milk
 ↓
Sugar
```

---

## CoR

Passes request.

---

Example:

```text
Manager
 ↓
Director
```

---

### Memory Trick

```text
Decorator
      ↓
Enhance Object

CoR
      ↓
Forward Request
```

---

# 24. CoR vs Command

---

## Command

Encapsulates request as object.

---

Example:

```java
command.execute()
```

---

## CoR

Determines who executes request.

---

Example:

```text
Handler Chain
```

---

### Memory Trick

```text
Command
     ↓
What To Execute

CoR
     ↓
Who Will Execute
```

---

# 25. CoR vs Observer

---

## Observer

Broadcasts to many listeners.

---

Example:

```text
One Event
 ↓
Many Observers
```

---

## CoR

Passes request sequentially.

---

Example:

```text
Handler1
 ↓
Handler2
 ↓
Handler3
```

---

Only chain flow.

---

# 26. When To Use Chain of Responsibility

Use when:

- Multiple handlers possible
- Request processor unknown
- Dynamic workflow needed
- Approval chains exist

Examples:

```text
Approval Systems
Filters
Middleware
Security Chains
Logging
Exception Handling
```

---

# 27. When NOT To Use

Avoid when:

- Only one handler exists
- Processing order fixed and simple

---

# 28. Interview Questions

### Q1. What problem does CoR solve?

Passes request through handlers until one processes it.

---

### Q2. What is the most important field?

```java
Handler next;
```

---

Creates the chain.

---

### Q3. Can multiple handlers process the request?

```text
YES
```

Filter chains often do this.

---

### Q4. Give Spring example.

```text
Spring Security Filter Chain
```

---

### Q5. Difference Between Strategy and CoR?

Strategy:

```text
Choose Algorithm
```

CoR:

```text
Pass Request
```

---

### Q6. Difference Between Decorator and CoR?

Decorator:

```text
Add Behavior
```

CoR:

```text
Pass Request
```

---

### Q7. What principle does CoR support?

```text
Open/Closed Principle
```

---

### Q8. What is a real-world example?

```text
Manager Approval Chain

Customer Support Escalation

Spring Security Filters
```

---

# Real-Life Memory Trick

```text
Singleton
     ↓
One Object

Factory
     ↓
Create Object

Builder
     ↓
Build Object

Prototype
     ↓
Clone Object

Adapter
     ↓
Convert Interface

Bridge
     ↓
Separate Abstraction

Composite
     ↓
Tree Structure

Facade
     ↓
Hide Complexity

Decorator
     ↓
Add Behavior

Flyweight
     ↓
Share State

Chain Of Responsibility
     ↓
Pass Request
Through Handlers
```

---

# Quick Revision

```text
Chain Of Responsibility
-----------------------

Purpose:
Pass request through handlers.

Structure:

Handler
   ↓
Handler
   ↓
Handler

Key Components:

1. Request
2. Handler
3. Concrete Handler
4. Client

Types:

1. First Handler Wins
2. Multiple Handlers Execute

Advantages:

✔ Loose Coupling
✔ Dynamic Chains
✔ Easy Extension
✔ Cleaner Code

Disadvantages:

✘ Hard Debugging
✘ Request May Not Be Handled

Examples:

Leave Approval
Customer Support
Servlet Filters
Spring Security Filters

Remember:

Strategy
     ↓
Choose Algorithm

Decorator
     ↓
Add Behavior

CoR
     ↓
Forward Request
To Next Handler
```