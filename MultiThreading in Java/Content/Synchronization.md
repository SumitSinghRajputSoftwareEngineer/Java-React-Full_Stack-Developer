
Synchronization is one of the most important concepts in Java Multithreading.

Multiple threads improve performance, but when they access shared resources concurrently, problems such as:

- Race Conditions
- Data Corruption
- Inconsistent State
- Lost Updates

can occur.

Synchronization provides a mechanism to control access to shared resources and maintain thread safety.

---

# Why Synchronization?

## Problem Without Synchronization

Consider the following example:

```java
class Counter {

    int count = 0;

    public void increment() {
        count++;
    }
}
```

Multiple threads execute:

```java
counter.increment();
```

Developers often assume:

```text
100 Threads
Each increments once

Result = 100
```

Actual result may be:

```text
87
92
95
100
```

---

## Why Does This Happen?

Because:

```java
count++;
```

is not atomic.

Internally:

```text
1. Read count
2. Increment value
3. Write value
```

Multiple threads can interfere between these steps.

---

## Example

Initial value:

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

## Solution

Allow only one thread at a time to execute the critical section.

```java
synchronized
```

provides this capability.

---

# What is Synchronization?

## Definition

Synchronization is a mechanism that restricts access to a shared resource so that only one thread can execute a critical section at a time.

---

## Goal

Ensure:

```text
Mutual Exclusion
+
Visibility
+
Data Consistency
```

---

## Visualization

Without synchronization:

```text
Thread A
Thread B
Thread C

Access Shared Resource
Simultaneously
```

---

With synchronization:

```text
Thread A -> Execute
Thread B -> Wait
Thread C -> Wait
```

---

# synchronized Keyword

## Definition

The `synchronized` keyword is used to acquire a monitor lock before entering a critical section.

---

## Syntax

### Synchronized Method

```java
public synchronized void method() {

}
```

---

### Synchronized Block

```java
synchronized(lockObject) {

}
```

---

## Important Purpose

Ensures:

```text
Only one thread
can execute protected code
for a given lock.
```

---

# Synchronized Method

## Definition

A method declared with `synchronized` automatically acquires the object's monitor lock before execution.

---

## Syntax

```java
public synchronized void increment() {

}
```

---

## Example

```java
class Counter {

    private int count = 0;

    public synchronized void increment() {

        count++;
    }

    public int getCount() {

        return count;
    }
}
```

---

## Execution

Suppose:

```text
Thread A
Thread B
Thread C
```

call:

```java
increment()
```

---

Flow:

```text
Thread A -> Executes

Thread B -> Waits

Thread C -> Waits
```

---

After A completes:

```text
Thread B -> Executes
```

---

## Internal View

Java internally converts:

```java
public synchronized void increment()
```

into:

```java
synchronized(this) {

    count++;
}
```

---

## Lock Used

```text
Current Object Lock
(this)
```

---

# Synchronized Block

## Definition

Synchronizes only a specific portion of code instead of the entire method.

---

## Syntax

```java
synchronized(lockObject) {

    // critical section
}
```

---

## Example

```java
class Counter {

    private int count = 0;

    public void increment() {

        synchronized(this) {

            count++;
        }
    }
}
```

---

## Why Use Synchronized Block?

Suppose:

```java
public void process() {

    step1();

    step2();

    step3();
}
```

Only:

```java
step2();
```

requires synchronization.

---

Instead of synchronizing entire method:

```java
public void process() {

    step1();

    synchronized(this) {

        step2();
    }

    step3();
}
```

---

## Benefit

Smaller critical section.

Result:

```text
Better Performance
```

---

## Example

```java
public void print() {

    System.out.println("Start");

    synchronized(this) {

        updateDatabase();
    }

    System.out.println("End");
}
```

Only database update is protected.

---

# Synchronized Method vs Synchronized Block

| Feature | Synchronized Method | Synchronized Block |
|----------|----------|----------|
| Lock Scope | Entire Method | Specific Code |
| Performance | Lower | Better |
| Flexibility | Less | More |
| Lock Object Choice | Fixed | Customizable |
| Industry Usage | Less | More |

---

# Static Synchronization

## Definition

When a static method is synchronized, locking occurs at the class level instead of the object level.

---

## Syntax

```java
public static synchronized void method() {

}
```

---

## Example

```java
class Counter {

    public static synchronized void print() {

        System.out.println("Printing");
    }
}
```

---

## Lock Used

```text
Counter.class
```

---

## Important Point

Even if multiple objects exist:

```java
Counter c1 = new Counter();
Counter c2 = new Counter();
Counter c3 = new Counter();
```

all share:

```text
One Class Lock
```

---

# Object Level Lock

## Definition

An Object Level Lock is associated with an individual object instance.

---

## Example

```java
class Employee {

    public synchronized void work() {

    }
}
```

Lock:

```text
this
```

---

## Example

```java
Employee e1 = new Employee();
Employee e2 = new Employee();
```

---

### Thread A

```java
e1.work();
```

---

### Thread B

```java
e2.work();
```

Both can execute simultaneously because:

```text
Different Objects
Different Locks
```

---

## Visualization

```text
e1 Lock
e2 Lock

Independent
```

---

# Class Level Lock

## Definition

A Class Level Lock is associated with the Class object.

---

## Example

```java
public static synchronized void work() {

}
```

---

Lock:

```text
Employee.class
```

---

## Example

```java
Employee e1 = new Employee();
Employee e2 = new Employee();
```

---

### Thread A

```java
e1.work();
```

---

### Thread B

```java
e2.work();
```

Only one thread executes because:

```text
Same Class Lock
```

---

## Visualization

```text
Employee.class

Single Shared Lock
```

---

# Object Level Lock vs Class Level Lock

| Feature | Object Lock | Class Lock |
|----------|----------|----------|
| Lock Owner | Object | Class |
| Lock Object | this | ClassName.class |
| Scope | Individual Object | Entire Class |
| Concurrent Execution | Possible | Restricted |
| Used By | Instance Methods | Static Methods |

---

# Monitor Lock

## Definition

Every Java object has an associated monitor lock.

Also known as:

```text
Monitor
```

---

## Purpose

Monitor controls:

```text
Mutual Exclusion
```

for synchronized code.

---

## Example

```java
synchronized(this) {

}
```

Thread must acquire:

```text
this object's monitor
```

before entering.

---

## Visualization

```text
Object
   |
Monitor Lock
   |
Thread Acquires
   |
Execute
   |
Release
```

---

## Important Rule

Only one thread can hold a monitor lock at a time.

---

# Intrinsic Lock

## Definition

The built-in lock associated with every Java object.

---

## Also Called

```text
Monitor Lock
Built-in Lock
Intrinsic Lock
```

All refer to the same concept.

---

## Example

```java
Object obj = new Object();
```

Internally:

```text
obj contains intrinsic lock
```

---

## Example

```java
synchronized(obj) {

}
```

The thread acquires:

```text
obj's intrinsic lock
```

---

## Important Interview Point

### Monitor Lock = Intrinsic Lock

These terms are often used interchangeably.

---

# Reentrant Nature of synchronized

## Definition

Java's synchronized locks are reentrant.

A thread that already owns a lock can acquire the same lock again without deadlocking itself.

---

## Why Is This Important?

Suppose:

```java
methodA()
```

calls:

```java
methodB()
```

and both are synchronized.

Without reentrancy:

```text
Deadlock
```

would occur.

---

## Example

```java
class Example {

    public synchronized void methodA() {

        System.out.println("A");

        methodB();
    }

    public synchronized void methodB() {

        System.out.println("B");
    }
}
```

---

## Execution

```text
Thread enters methodA()
        |
Lock Acquired
        |
Calls methodB()
        |
Same Thread Reuses Lock
        |
Executes Successfully
```

---

## Output

```text
A
B
```

---

## Visualization

```text
Thread A
   |
Acquire Lock
   |
Acquire Same Lock Again
   |
Allowed
```

---

## Interview Definition

```text
A thread can reacquire a lock it already owns.
```

This property is called:

```text
Reentrancy
```

---

# Limitations of synchronized

Although synchronization is useful, it has several drawbacks.

---

# Limitation 1: Performance Overhead

Lock acquisition and release require additional work.

---

## Example

```java
synchronized(this) {

    count++;
}
```

Every entry requires:

```text
Acquire Lock
Execute
Release Lock
```

---

## Result

```text
Slower than normal execution
```

---

# Limitation 2: Reduced Parallelism

Only one thread can execute critical section.

---

## Example

```text
100 Threads
```

but:

```text
1 Thread Executes
99 Threads Wait
```

---

# Limitation 3: Deadlock Possibility

Improper lock ordering can cause deadlocks.

---

## Example

```text
Thread A -> Lock1 -> Lock2

Thread B -> Lock2 -> Lock1
```

Both wait forever.

---

# Limitation 4: No Timeout Support

Cannot specify:

```text
Wait only 5 seconds for lock
```

with synchronized.

---

## Example

Not possible:

```java
synchronized(obj, timeout)
```

---

## Solution

Use:

```java
ReentrantLock
```

---

# Limitation 5: No Fairness Policy

No guarantee regarding:

```text
Which waiting thread gets lock next
```

---

## Result

Possible:

```text
Thread Starvation
```

---

# Limitation 6: Difficult Debugging

Concurrency bugs are often:

- Intermittent
- Timing dependent
- Hard to reproduce

---

# Limitation 7: Cannot Interrupt Lock Waiting

Suppose:

```java
synchronized(obj) {

}
```

Thread waits for lock.

You cannot:

```java
interrupt()
```

the lock acquisition process.

---

## Solution

Use:

```java
ReentrantLock.lockInterruptibly()
```

---

# Real-World Example

Suppose a banking system updates account balances.

Without synchronization:

```text
Two Withdrawals
Same Balance
Incorrect Result
```

---

With synchronization:

```text
Thread A Updates Balance

Thread B Waits

Thread B Updates Balance
```

Result:

```text
Correct Balance
```

---

# Common Interview Questions

## Q1: Why is synchronization needed?

To prevent:

- Race Conditions
- Data Corruption
- Inconsistent Results

---

## Q2: What lock is used by a synchronized instance method?

```text
this
```

(Current Object Lock)

---

## Q3: What lock is used by a synchronized static method?

```text
ClassName.class
```

(Class Level Lock)

---

## Q4: What is the difference between object lock and class lock?

Object lock belongs to an instance.

Class lock belongs to the Class object.

---

## Q5: What is a monitor lock?

Built-in lock associated with every Java object.

---

## Q6: What is an intrinsic lock?

Another name for monitor lock.

---

## Q7: Is synchronized reentrant?

```text
Yes
```

A thread can reacquire a lock it already owns.

---

## Q8: Can synchronized cause deadlock?

```text
Yes
```

Improper lock ordering can create deadlocks.

---

## Q9: Does synchronized guarantee thread safety?

For the protected critical section:

```text
Yes
```

provided all accesses follow the same synchronization strategy.

---

# Key Takeaways

1. Synchronization protects shared resources from concurrent access problems.
2. The `synchronized` keyword provides mutual exclusion and visibility guarantees.
3. Synchronized methods lock the current object (`this`).
4. Synchronized blocks allow finer-grained control and better performance.
5. Static synchronization uses a class-level lock (`ClassName.class`).
6. Every Java object contains an intrinsic (monitor) lock.
7. Monitor Lock and Intrinsic Lock refer to the same built-in locking mechanism.
8. Java synchronization is reentrant, allowing a thread to acquire the same lock multiple times.
9. Synchronization helps prevent race conditions and maintain data consistency.
10. Excessive synchronization can reduce performance and scalability.
11. Advanced concurrency utilities such as `ReentrantLock`, `ReadWriteLock`, and `StampedLock` address several limitations of `synchronized`.