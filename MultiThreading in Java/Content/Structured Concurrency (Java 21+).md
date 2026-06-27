
One of the major additions introduced through:

```text
Project Loom
```

is:

```text
Structured Concurrency
```

Structured Concurrency aims to solve a long-standing problem in concurrent programming:

```text
Managing Related Tasks
Safely And Predictably
```

---

# Why Was Structured Concurrency Needed?

Consider a typical application flow:

```text
Get User Details

Get Orders

Get Payment Details

Combine Results
```

---

A developer may execute them concurrently:

```java
ExecutorService executor =
        Executors.newFixedThreadPool(3);
```

---

Submit tasks:

```java
Future<User> userFuture = ...;
Future<Order> orderFuture = ...;
Future<Payment> paymentFuture = ...;
```

---

Problems:

- Who cancels tasks if one fails?
- Who waits for completion?
- How are errors propagated?
- What if parent thread exits early?
- What if one task hangs forever?

---

Managing all of this manually becomes:

```text
Complex
Error-Prone
```

---

Structured Concurrency solves these problems.

---

# What is Structured Concurrency?

## Definition

Structured Concurrency is a programming model where:

```text
Concurrent Tasks

Are Treated As

A Single Unit Of Work
```

---

All child tasks belong to a:

```text
Parent Scope
```

---

The parent controls:

- Creation
- Waiting
- Cancellation
- Error propagation

---

# Simple Definition

Think of it as:

```text
try-with-resources

For Concurrent Tasks
```

---

When the scope ends:

```text
All Child Tasks
Must Be Finished
```

---

# Real-World Analogy

Imagine a project manager.

---

Manager assigns:

```text
Task A

Task B

Task C
```

to team members.

---

Manager cannot leave until:

```text
All Tasks Complete
```

or

```text
Project Is Cancelled
```

---

Structured Concurrency works the same way.

---

# Traditional Concurrency Problem

Example

```java
ExecutorService executor =
        Executors.newFixedThreadPool(3);
```

---

Submit tasks:

```java
executor.submit(...)
```

---

Issues:

```text
Tasks Can Outlive
Their Parent
```

---

Example

```text
Request Finished

↓

Child Threads Still Running
```

---

This can cause:

- Resource leaks
- Wasted computation
- Unexpected behavior

---

# Structured Concurrency Solution

```text
Parent Scope

↓

Creates Child Tasks

↓

Waits For Completion

↓

Automatically Cleans Up
```

---

# Structured Concurrency Architecture

```text
Parent Task
      |
      +---- Child Task A
      |
      +---- Child Task B
      |
      +---- Child Task C
```

---

All child tasks are tied to:

```text
Parent Lifecycle
```

---

# StructuredTaskScope

The central API.

---

## Class

```java
StructuredTaskScope
```

---

Package

```java
java.util.concurrent
```

---

Introduced through:

```text
Project Loom
```

---

# Purpose

Provides:

```text
Task Management

Cancellation

Joining

Error Propagation
```

---

# Basic Example

```java
try(var scope =
        new StructuredTaskScope<>()) {

}
```

---

Scope controls all child tasks.

---

When scope closes:

```text
Tasks Are Managed Automatically
```

---

# TaskScope

## What is TaskScope?

A scope representing:

```text
A Group Of Related Tasks
```

---

Everything inside the scope is:

```text
One Logical Operation
```

---

Example

```text
Fetch Customer Profile
```

---

May require:

```text
User Service

Order Service

Payment Service
```

---

All become child tasks.

---

# Forking Tasks

## What is Forking?

Forking means:

```text
Starting Child Tasks
```

inside a scope.

---

# fork()

## Syntax

```java
scope.fork(task);
```

---

# Example

```java
try(var scope =
        new StructuredTaskScope<>()) {

    var userTask =
            scope.fork(() ->
                    fetchUser());

    var orderTask =
            scope.fork(() ->
                    fetchOrders());

}
```

---

Both tasks run concurrently.

---

# Visualization

```text
Parent Scope

↓

Fork User Task

↓

Fork Order Task
```

---

```text
User Task
     \
      Parallel Execution
     /
Order Task
```

---

# Fork Result

The returned object is:

```java
Subtask<T>
```

---

Example

```java
Subtask<User> userTask =
        scope.fork(...);
```

---

Used later to obtain results.

---

# Joining Tasks

## What is Joining?

Waiting for all child tasks to finish.

---

# join()

## Syntax

```java
scope.join();
```

---

# Example

```java
scope.join();
```

---

Parent thread waits until:

```text
All Child Tasks Complete
```

---

# Visualization

```text
Fork A

Fork B

Fork C

↓

join()

↓

Wait For All
```

---

# Complete Example

```java
try(var scope =
        new StructuredTaskScope<>()) {

    var user =
            scope.fork(() -> fetchUser());

    var orders =
            scope.fork(() -> fetchOrders());

    scope.join();

}
```

---

Execution Flow

```text
Fork User

Fork Orders

↓

Wait

↓

Both Complete

↓

Continue
```

---

# Getting Results

After join:

```java
user.get();
```

---

Example

```java
User result =
        user.get();
```

---

# Example

```java
try(var scope =
        new StructuredTaskScope<>()) {

    var user =
            scope.fork(() -> fetchUser());

    scope.join();

    User result =
            user.get();

}
```

---

# StructuredTaskScope.ShutdownOnFailure

One of the most important variants.

---

# Purpose

If any child task fails:

```text
Cancel Remaining Tasks
```

---

# Example

```java
try(var scope =
        new StructuredTaskScope
                .ShutdownOnFailure()) {

}
```

---

# Scenario

Tasks:

```text
User Service

Order Service

Payment Service
```

---

Order Service fails.

---

Result:

```text
Cancel User Task

Cancel Payment Task
```

---

No wasted work.

---

# Visualization

```text
Task A Failed

↓

Cancel B

↓

Cancel C

↓

Propagate Error
```

---

# Error Handling

One of the biggest benefits of Structured Concurrency.

---

# Traditional Approach

```java
Future.get()
```

throws:

```java
ExecutionException
```

---

Need manual handling.

---

Often complicated.

---

# Structured Concurrency

Errors propagate naturally through the scope.

---

Example

```java
scope.throwIfFailed();
```

---

# Example

```java
try(var scope =
        new StructuredTaskScope
                .ShutdownOnFailure()) {

    scope.fork(() -> fetchUser());

    scope.fork(() -> fetchOrders());

    scope.join();

    scope.throwIfFailed();
}
```

---

Behavior:

```text
Any Child Failure

↓

Exception Thrown

↓

Scope Cleaned Up
```

---

# Benefits

- Simpler code
- Automatic propagation
- Easier debugging

---

# Cancellation

One of the most powerful features.

---

# What is Cancellation?

Stopping unnecessary child tasks.

---

Example

```text
Task A Failed

↓

Cancel Remaining Tasks
```

---

Without:

```text
Manual Intervention
```

---

# Manual Cancellation

## shutdown()

```java
scope.shutdown();
```

---

Cancels active subtasks.

---

# Example

```java
scope.shutdown();
```

---

Result

```text
All Running Tasks
Receive Cancellation Signal
```

---

# Automatic Cancellation

Using:

```java
ShutdownOnFailure
```

---

Example

```java
Task A Failed

↓

Task B Cancelled

↓

Task C Cancelled
```

---

# Why Cancellation Matters?

Suppose:

```text
Database Query Failed
```

---

No reason to continue:

```text
Payment Lookup

Order Lookup

Inventory Lookup
```

---

Cancellation saves:

- CPU
- Memory
- Network Calls
- Database Load

---

# StructuredTaskScope Variants

---

# ShutdownOnFailure

If one task fails:

```text
Cancel Remaining Tasks
```

---

Useful when:

```text
All Results Required
```

---

# ShutdownOnSuccess

If one task succeeds:

```text
Cancel Remaining Tasks
```

---

Useful for:

```text
Fastest Response Wins
```

---

Example

```text
Call Multiple Servers

↓

First Response Wins
```

---

# Example

```java
try(var scope =
    new StructuredTaskScope
        .ShutdownOnSuccess<String>()) {

}
```

---

# Traditional Executor vs Structured Concurrency

| Feature | ExecutorService | Structured Concurrency |
|----------|----------|----------|
| Task Grouping | No | Yes |
| Parent-Child Relationship | No | Yes |
| Automatic Cancellation | No | Yes |
| Error Propagation | Manual | Automatic |
| Resource Cleanup | Manual | Automatic |
| Readability | Moderate | Excellent |

---

# Structured Concurrency and Virtual Threads

Designed to work perfectly with:

```text
Virtual Threads
```

---

Example

```java
scope.fork(() ->
        fetchCustomer());
```

---

Each task may execute using:

```text
Virtual Thread
```

---

This allows:

```text
Massive Concurrency
```

with:

```text
Simple Sequential Code
```

---

# Real-World Use Cases

---

# Use Case 1

Microservices Aggregation

---

Request requires:

```text
User Service

Order Service

Payment Service
```

---

Run in parallel.

---

# Use Case 2

Dashboard Data Loading

---

Fetch:

```text
Statistics

Notifications

Reports
```

simultaneously.

---

# Use Case 3

Search Across Multiple Sources

---

Query:

```text
Database

Cache

External API
```

---

Use fastest response.

---

# Use Case 4

Parallel File Processing

---

Read multiple files concurrently.

---

# Use Case 5

Distributed Systems

---

Call multiple downstream services in parallel.

---

# Common Mistakes

---

## Mistake 1

Not Calling join()

---

Bad

```java
scope.fork(...);
```

---

No:

```java
join()
```

---

Results may not be ready.

---

# Mistake 2

Ignoring Errors

---

Always consider:

```java
throwIfFailed();
```

---

# Mistake 3

Using ExecutorService For Everything

---

Structured Concurrency is often simpler for:

```text
Related Tasks
```

---

# Common Interview Questions

## Q1: What is Structured Concurrency?

A concurrency model where related tasks are treated as a single unit of work with a parent-child relationship.

---

## Q2: Which project introduced Structured Concurrency?

```text
Project Loom
```

---

## Q3: What is StructuredTaskScope?

A class used to manage groups of related concurrent tasks.

---

## Q4: What does fork() do?

Starts a child task inside a scope.

---

## Q5: What does join() do?

Waits for all child tasks to complete.

---

## Q6: What is ShutdownOnFailure?

A scope that cancels all remaining tasks when one task fails.

---

## Q7: What is ShutdownOnSuccess?

A scope that cancels remaining tasks when one task succeeds.

---

## Q8: Why is Structured Concurrency better than ExecutorService for related tasks?

Because it provides:

- Automatic cancellation
- Error propagation
- Parent-child lifecycle management

---

## Q9: How does error handling work?

Using:

```java
throwIfFailed();
```

which propagates child task failures.

---

## Q10: Does Structured Concurrency work well with Virtual Threads?

```text
Yes
```

It was designed alongside Project Loom.

---

# Senior Interview Scenario

### Question

A request requires:

```text
Customer Data

Order Data

Payment Data
```

from three different services.

---

One service fails.

---

Should the other two continue?

### Answer

Usually:

```text
No
```

---

Use:

```java
StructuredTaskScope
    .ShutdownOnFailure
```

---

Behavior:

```text
One Task Fails

↓

Remaining Tasks Cancelled

↓

Error Propagated

↓

Resources Saved
```

---

# Key Takeaways

1. Structured Concurrency is a Project Loom feature designed to manage related concurrent tasks safely.
2. It introduces a parent-child relationship between tasks.
3. `StructuredTaskScope` is the central API.
4. `fork()` starts child tasks.
5. `join()` waits for child task completion.
6. `throwIfFailed()` propagates errors from child tasks.
7. `ShutdownOnFailure` cancels remaining tasks when one task fails.
8. `ShutdownOnSuccess` cancels remaining tasks when one task succeeds.
9. Structured Concurrency improves readability, maintainability, and reliability.
10. It eliminates much of the manual task management required with `ExecutorService`.
11. It integrates naturally with Virtual Threads.
12. Structured Concurrency is becoming a key concurrency model for modern Java applications built on Java 21+.