
Thread Safety is one of the most important concepts in Multithreading.

Creating multiple threads is easy.

The real challenge is:

```text
How do multiple threads safely access shared data?
```

Without proper synchronization, applications may produce:

- Incorrect results
- Random failures
- Data corruption
- Inconsistent state
- Difficult-to-reproduce bugs

Understanding Thread Safety is the foundation for learning:

- synchronized
- volatile
- Atomic Classes
- Locks
- Concurrent Collections
- Executor Framework
- CompletableFuture

---

# What is Thread Safety?

## Definition

A piece of code is Thread Safe if it behaves correctly when accessed simultaneously by multiple threads.

---

## Simple Definition

Regardless of:

- Number of threads
- Execution order
- CPU scheduling

The result remains:

```text
Correct
Predictable
Consistent
```

---

## Example

Suppose multiple threads increment a counter.

```java
count++;
```

Expected Result:

```text
100 Threads
Each increments 1 time

Expected = 100
```

Actual Result may be:

```text
87
92
95
100
```

because:

```text
Code is not thread-safe.
```

---

## Thread Safe Example

```java
synchronized void increment() {

    count++;
}
```

Now:

```text
Result remains consistent.
```

---

## Real-World Analogy

Imagine:

```text
Bank Account Balance
```

Current Balance:

```text
₹10,000
```

Two users withdraw:

```text
₹3,000
₹5,000
```

simultaneously.

Without thread safety:

```text
Incorrect Balance
```

may occur.

With thread safety:

```text
Correct Balance
```

is always maintained.

---

# Race Condition

## Definition

A Race Condition occurs when multiple threads access and modify shared data concurrently and the final result depends on the timing of execution.

---

## Why Called "Race"?

Threads are racing against each other.

The thread that executes first influences the result.

---

## Example

```java
count++;
```

Looks like:

```text
Single Operation
```

Actually:

```text
1. Read count
2. Add 1
3. Write count
```

---

## Scenario

Initial Value:

```text
count = 0
```

---

### Thread A

```text
Read 0
```

---

### Thread B

```text
Read 0
```

---

### Thread A

```text
Write 1
```

---

### Thread B

```text
Write 1
```

---

Expected:

```text
2
```

Actual:

```text
1
```

---

## Visualization

```text
Thread A      Thread B

Read 0
              Read 0

Write 1
              Write 1

Final = 1
```

---

## Result

```text
Lost Update Problem
```

---

## Characteristics

- Unpredictable
- Timing dependent
- Difficult to debug
- Common in multithreaded applications

---

# Critical Section

## Definition

A Critical Section is a block of code that accesses shared resources and must be executed by only one thread at a time.

---

## Example

```java
count++;
```

This is a critical section because:

```text
Shared variable modified
```

---

## Example

```java
public void increment() {

    count++;
}
```

Critical Section:

```java
count++;
```

---

## Protected Version

```java
public synchronized void increment() {

    count++;
}
```

Now:

```text
Only one thread can execute it at a time.
```

---

## Diagram

```text
Thread A
    |
Critical Section
    |
Exit

Thread B
    |
Wait
    |
Enter Later
```

---

## Real-World Example

Bank ATM:

```text
Update Balance
```

Only one transaction should update balance at a time.

---

# Shared Resources

## Definition

A Shared Resource is any data, object, variable, file, database connection, or memory location accessed by multiple threads.

---

## Examples

### Variable

```java
int count;
```

---

### Object

```java
Employee emp;
```

---

### Collection

```java
ArrayList<String> list;
```

---

### File

```java
report.txt
```

---

### Database Connection

```java
Connection conn;
```

---

## Example

```java
class Counter {

    int count = 0;
}
```

If:

```text
Thread A
Thread B
Thread C
```

all access:

```java
count
```

then:

```text
count is a shared resource.
```

---

## Problem

Shared resources are the primary source of:

- Race Conditions
- Data Corruption
- Visibility Issues

---

# Atomicity

## Definition

Atomicity means an operation executes completely or not at all.

No thread can observe it in a partially completed state.

---

## Meaning

```text
Indivisible Operation
```

---

## Example

Developers often assume:

```java
count++;
```

is atomic.

It is NOT.

---

## Actual Operations

```text
Read count
Add 1
Write count
```

Three separate steps.

---

## Example

```java
count++;
```

can be interrupted between:

```text
Read
Write
```

causing race conditions.

---

## Truly Atomic Example

```java
AtomicInteger count =
    new AtomicInteger();

count.incrementAndGet();
```

---

## Visualization

### Non-Atomic

```text
Read
Modify
Write
```

Interrupt possible.

---

### Atomic

```text
Single Indivisible Operation
```

No interruption.

---

## Importance

Atomicity prevents:

```text
Lost Updates
```

---

# Visibility

## Definition

Visibility refers to whether changes made by one thread are visible to other threads.

---

## Problem

Each thread may maintain its own:

```text
CPU Cache
```

instead of reading directly from main memory.

---

## Example

```java
boolean running = true;
```

---

### Thread A

```java
while(running) {

}
```

---

### Thread B

```java
running = false;
```

---

Expected:

```text
Thread A Stops
```

Possible Reality:

```text
Thread A Runs Forever
```

---

## Why?

Thread A may continue reading:

```text
Cached Value = true
```

---

## Diagram

```text
Main Memory

running = false

     ^
     |
CPU Cache

running = true
```

---

## Solution

```java
volatile boolean running;
```

---

## Importance

Visibility ensures:

```text
Latest Value
```

is seen by all threads.

---

# Ordering

## Definition

Ordering refers to the sequence in which operations appear to execute.

---

## Important Fact

The JVM, compiler, and CPU may reorder instructions for performance optimization.

---

## Example

```java
a = 10;
b = true;
```

May internally execute as:

```java
b = true;
a = 10;
```

if no dependency exists.

---

## Why Reordering Happens?

To improve:

- CPU utilization
- Cache performance
- Instruction throughput

---

## Problem

Another thread may observe:

```text
b = true
a = 0
```

which appears impossible logically.

---

## Importance

Incorrect ordering can produce:

```text
Unexpected Results
```

even without race conditions.

---

# Happens-Before Relationship

## Definition

The Happens-Before Relationship is a guarantee provided by the Java Memory Model (JMM) that one action becomes visible to another action.

---

## Purpose

It solves:

- Visibility Problems
- Ordering Problems

---

## Simple Meaning

If:

```text
A Happens-Before B
```

Then:

```text
All effects of A
are visible to B.
```

---

# Example

```java
volatile boolean ready = false;
int data = 0;

data = 100;
ready = true;
```

---

### Thread 2

```java
if(ready) {

    System.out.println(data);
}
```

Because:

```java
ready is volatile
```

Java guarantees:

```text
data = 100
```

is visible before:

```text
ready = true
```

becomes visible.

---

# Common Happens-Before Rules

---

## Rule 1: Program Order Rule

Within the same thread:

```java
a = 10;
b = 20;
```

Happens-before.

---

## Rule 2: Monitor Lock Rule

```java
synchronized
```

unlock happens-before next lock.

---

## Rule 3: Volatile Rule

Write to volatile variable happens-before subsequent read.

---

## Rule 4: Thread Start Rule

```java
t.start();
```

happens-before actions inside thread.

---

## Rule 5: Thread Join Rule

Thread completion happens-before:

```java
t.join();
```

returns.

---

## Interview Definition

```text
Happens-Before guarantees visibility and ordering between threads.
```

---

# Data Consistency Problems

## Definition

Data Consistency Problems occur when shared data enters an invalid or unexpected state due to concurrent access.

---

## Example

Bank Account:

```text
Balance = ₹10,000
```

---

### Thread A

Withdraw:

```text
₹3,000
```

---

### Thread B

Withdraw:

```text
₹5,000
```

---

Expected:

```text
₹2,000
```

---

Possible Result:

```text
₹5,000
₹7,000
₹2,000
```

depending on execution order.

---

## Types of Data Consistency Problems

### Lost Update

```text
One update overwrites another.
```

---

### Dirty Read

```text
Reading partially updated data.
```

---

### Stale Data

```text
Reading old cached value.
```

---

### Inconsistent Object State

Example:

```java
employee.setName("John");
employee.setAge(30);
```

Another thread may see:

```text
Name = John
Age = 0
```

temporarily.

---

## Example

```java
class Account {

    int balance = 1000;

    void withdraw(int amount) {

        balance = balance - amount;
    }
}
```

Multiple threads executing simultaneously may corrupt:

```text
balance
```

---

# Relationship Between Concepts

```text
Shared Resource
       |
       v
Race Condition
       |
       v
Data Inconsistency
       |
       +------------+
       |            |
       v            v

Atomicity     Visibility
Problems      Problems
       |
       v
Need Synchronization
```

---

# Real-World Example

Suppose an e-commerce application has:

```text
Available Stock = 1
```

Two customers purchase simultaneously.

---

### Thread A

Checks:

```text
Stock = 1
```

---

### Thread B

Checks:

```text
Stock = 1
```

---

### Thread A

Purchases item.

---

### Thread B

Purchases item.

---

Result:

```text
Sold 2 items
Available 1 item
```

Impossible state.

---

Cause:

```text
Race Condition
+
Lack of Thread Safety
```

---

# Common Interview Questions

## Q1: What is Thread Safety?

```text
Ability of code to behave correctly when accessed concurrently by multiple threads.
```

---

## Q2: What is a Race Condition?

```text
Multiple threads modify shared data and result depends on execution timing.
```

---

## Q3: What is a Critical Section?

```text
Code that accesses shared resources and must be executed by only one thread at a time.
```

---

## Q4: Is count++ Atomic?

```text
No
```

---

## Q5: What is Visibility?

```text
Ensures changes made by one thread are visible to other threads.
```

---

## Q6: What is Ordering?

```text
The execution sequence of operations observed by threads.
```

---

## Q7: What is Happens-Before?

```text
A Java Memory Model guarantee that ensures visibility and ordering between actions.
```

---

## Q8: What causes Data Consistency Problems?

- Race Conditions
- Lack of Synchronization
- Visibility Issues
- Improper Ordering

---

# Key Takeaways

1. Thread Safety ensures correct behavior under concurrent access.
2. Race Conditions occur when multiple threads modify shared data simultaneously.
3. Critical Sections are code blocks that access shared resources and require protection.
4. Shared Resources are the primary source of concurrency problems.
5. Atomicity ensures operations are executed completely without interruption.
6. Visibility ensures changes made by one thread are seen by others.
7. Ordering refers to the sequence in which operations appear to execute.
8. The Java Memory Model allows instruction reordering unless constraints exist.
9. Happens-Before guarantees visibility and ordering between threads.
10. Data Consistency Problems arise when shared data is accessed without proper synchronization.
11. Understanding these concepts is essential before learning `synchronized`, `volatile`, `AtomicInteger`, Locks, and advanced concurrency APIs.