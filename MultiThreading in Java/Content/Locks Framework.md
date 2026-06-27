
Before Java 5, synchronization was primarily achieved using:

```java
synchronized
```

Although `synchronized` is simple and powerful, it has limitations:

- No timeout support
- No interruptible lock acquisition
- No fairness control
- No non-blocking lock attempts
- Less flexibility

To overcome these limitations, Java introduced the:

```java
java.util.concurrent.locks
```

package.

This package provides:

```text
Explicit Locking Mechanisms
```

that offer more control than `synchronized`.

---

# Introduction to Explicit Locks

## What Are Explicit Locks?

Explicit locks are lock objects that programmers manually acquire and release.

Unlike:

```java
synchronized
```

where the JVM automatically handles locking,

explicit locks require:

```java
lock();
unlock();
```

---

## Example

```java
Lock lock = new ReentrantLock();

lock.lock();

try {

    System.out.println("Critical Section");

}
finally {

    lock.unlock();
}
```

---

## Why Explicit Locks?

They provide:

- More flexibility
- Better scalability
- Fair locking
- Timed lock acquisition
- Interruptible locking
- Read-write locks

---

## Important Rule

Whenever you call:

```java
lock()
```

always release using:

```java
unlock()
```

inside:

```java
finally
```

---

# Lock Interface

## Definition

The `Lock` interface provides explicit lock operations similar to monitor locks used by `synchronized`.

---

## Package

```java
java.util.concurrent.locks.Lock
```

---

## Main Implementations

```java
ReentrantLock
ReentrantReadWriteLock
StampedLock
```

---

## Core Methods

```java
lock()
unlock()
tryLock()
tryLock(timeout)
lockInterruptibly()
```

---

# lock()

## Definition

Acquires the lock.

If the lock is unavailable:

```text
Thread Waits
```

until it becomes available.

---

## Syntax

```java
lock.lock();
```

---

## Example

```java
Lock lock =
        new ReentrantLock();

lock.lock();

try {

    System.out.println(
            "Executing Critical Section"
    );

}
finally {

    lock.unlock();
}
```

---

## Behavior

```text
Lock Available
      |
      v
 Acquire Lock

Lock Not Available
      |
      v
 Wait
```

---

## Important

Unlike:

```java
synchronized
```

lock release is NOT automatic.

---

# unlock()

## Definition

Releases the lock.

---

## Syntax

```java
lock.unlock();
```

---

## Example

```java
lock.lock();

try {

    doWork();
}
finally {

    lock.unlock();
}
```

---

## Why finally?

Suppose:

```java
doWork();
```

throws an exception.

Without:

```java
finally
```

lock may never be released.

---

## Best Practice

```java
lock.lock();

try {

    criticalSection();

}
finally {

    lock.unlock();
}
```

---

# tryLock()

## Definition

Attempts to acquire a lock immediately.

---

## Behavior

If lock available:

```text
Returns true
```

---

If lock unavailable:

```text
Returns false
```

---

No waiting occurs.

---

## Syntax

```java
boolean acquired =
        lock.tryLock();
```

---

## Example

```java
if(lock.tryLock()) {

    try {

        process();

    }
    finally {

        lock.unlock();
    }
}
else {

    System.out.println(
            "Lock Not Available"
    );
}
```

---

## Benefit

Avoids:

```text
Thread Blocking
```

---

# tryLock(timeout)

## Definition

Attempts to acquire the lock within a specified time.

---

## Syntax

```java
lock.tryLock(
        5,
        TimeUnit.SECONDS
);
```

---

## Example

```java
if(lock.tryLock(
        3,
        TimeUnit.SECONDS
)) {

    try {

        doWork();
    }
    finally {

        lock.unlock();
    }
}
else {

    System.out.println(
            "Could Not Acquire Lock"
    );
}
```

---

## Benefit

Prevents:

```text
Infinite Waiting
```

---

## Common Use Cases

- Deadlock prevention
- Resource contention
- Distributed systems

---

# lockInterruptibly()

## Definition

Acquires the lock unless the current thread is interrupted.

---

## Syntax

```java
lock.lockInterruptibly();
```

---

## Example

```java
try {

    lock.lockInterruptibly();

    try {

        doWork();
    }
    finally {

        lock.unlock();
    }

}
catch(InterruptedException e) {

    System.out.println(
            "Thread Interrupted"
    );
}
```

---

## Difference From lock()

### lock()

```text
Ignores Interrupt
```

while waiting.

---

### lockInterruptibly()

```text
Responds To Interrupt
```

while waiting.

---

## Interview Point

```text
lockInterruptibly()
supports cancellation of waiting threads.
```

---

# ReentrantLock

## Definition

A reentrant mutual exclusion lock that provides the same basic behavior as `synchronized` but with additional features.

---

## Package

```java
java.util.concurrent.locks.ReentrantLock
```

---

## Why Reentrant?

A thread that already owns the lock can acquire it again.

---

## Example

```java
class Example {

    private final ReentrantLock lock =
            new ReentrantLock();

    public void methodA() {

        lock.lock();

        try {

            methodB();

        }
        finally {

            lock.unlock();
        }
    }

    public void methodB() {

        lock.lock();

        try {

            System.out.println(
                    "Reentrant Access"
            );
        }
        finally {

            lock.unlock();
        }
    }
}
```

---

## Internal Counter

First acquisition:

```text
Hold Count = 1
```

---

Second acquisition:

```text
Hold Count = 2
```

---

Each unlock decreases count.

---

## Final Release

```text
Hold Count = 0
```

Lock becomes available.

---

# Fair Lock

## Definition

A Fair Lock grants access in the order threads requested it.

---

## Creation

```java
ReentrantLock lock =
        new ReentrantLock(true);
```

---

Parameter:

```java
true
```

means:

```text
Fair Lock
```

---

## Example

Waiting Threads

```text
T1
T2
T3
```

---

Acquisition Order

```text
T1
T2
T3
```

---

## Benefit

Prevents:

```text
Starvation
```

---

## Drawback

```text
Lower Throughput
```

because strict ordering introduces overhead.

---

# Non-Fair Lock

## Definition

Allows lock acquisition without strict ordering.

---

## Creation

```java
ReentrantLock lock =
        new ReentrantLock();
```

---

or

```java
new ReentrantLock(false);
```

---

## Behavior

A newly arrived thread may acquire lock before waiting threads.

---

## Example

Waiting:

```text
T1
T2
```

---

New Thread:

```text
T3
```

---

Possible Order

```text
T3
T1
T2
```

---

## Benefit

```text
Higher Performance
```

---

## Default Behavior

```java
ReentrantLock()
```

creates:

```text
Non-Fair Lock
```

---

# Fair Lock vs Non-Fair Lock

| Feature | Fair Lock | Non-Fair Lock |
|-----------|-----------|-----------|
| Ordering | FIFO | Not Guaranteed |
| Starvation | Less Likely | Possible |
| Performance | Lower | Higher |
| Throughput | Lower | Higher |
| Default | No | Yes |

---

# ReadWriteLock

## Problem

Suppose:

```text
100 Threads Reading

1 Thread Writing
```

Using:

```java
synchronized
```

allows only one thread at a time.

---

Result:

```text
Poor Performance
```

---

## Solution

Allow:

```text
Multiple Readers
```

but

```text
Single Writer
```

---

# ReadWriteLock

## Definition

Provides separate locks for:

```text
Read Operations
Write Operations
```

---

## Interface

```java
ReadWriteLock
```

---

## Methods

```java
readLock()
writeLock()
```

---

## Benefits

- Higher concurrency
- Better scalability
- Better read performance

---

# ReentrantReadWriteLock

## Definition

Most common implementation of:

```java
ReadWriteLock
```

---

## Creation

```java
ReentrantReadWriteLock rwLock =
        new ReentrantReadWriteLock();
```

---

# Read Lock

## Example

```java
rwLock.readLock().lock();

try {

    readData();

}
finally {

    rwLock.readLock().unlock();
}
```

---

## Multiple Readers Allowed

```text
Reader 1
Reader 2
Reader 3
```

can execute simultaneously.

---

# Write Lock

## Example

```java
rwLock.writeLock().lock();

try {

    updateData();

}
finally {

    rwLock.writeLock().unlock();
}
```

---

## Only One Writer

```text
Writer 1
```

allowed.

---

## Readers Blocked During Write

```text
Writer Active

Readers Wait
```

---

# Visualization

```text
Read Lock

Reader A
Reader B
Reader C

All Allowed

------------------

Write Lock

Writer A

Only One Allowed
```

---

# Common Use Cases

- Caches
- Configuration Data
- Reference Data
- In-Memory Databases

---

# StampedLock

Introduced in:

```java
Java 8
```

---

## Definition

A high-performance lock that supports:

- Read Lock
- Write Lock
- Optimistic Read

---

## Package

```java
java.util.concurrent.locks.StampedLock
```

---

# Why StampedLock?

Read-heavy applications often suffer because even reads require locking.

---

## Optimistic Read

Allows reading without immediately acquiring a real lock.

---

# Example

```java
StampedLock lock =
        new StampedLock();

long stamp =
        lock.tryOptimisticRead();
```

---

Read Data

```java
int value = sharedData;
```

---

Validate

```java
if(!lock.validate(stamp)) {

    // retry using real lock
}
```

---

# Optimistic Read Flow

```text
Read Data

Validate Stamp

No Write Occurred?
      |
      +-- Yes --> Use Data
      |
      +-- No --> Retry
```

---

# Advantages

- Very high read performance
- Reduced contention
- Better scalability

---

# Limitations

- More complex
- Not reentrant
- Requires validation

---

# StampedLock Modes

| Mode | Purpose |
|---------|---------|
| Read Lock | Multiple Readers |
| Write Lock | Single Writer |
| Optimistic Read | Lock-Free Read |

---

# Lock vs synchronized

One of the most frequently asked interview questions.

---

| Feature | Lock | synchronized |
|----------|----------|-------------|
| Java Version | Java 5+ | Java 1.0 |
| Explicit Release | Yes | No |
| Automatic Release | No | Yes |
| tryLock() | Yes | No |
| Timeout Support | Yes | No |
| Interruptible Locking | Yes | No |
| Fairness | Yes | No |
| Read-Write Locks | Yes | No |
| Optimistic Reads | Yes | No |
| Simplicity | Lower | Higher |
| Flexibility | Higher | Lower |

---

# When to Use synchronized

Use when:

- Simple locking
- Small critical sections
- Low contention
- Easy maintenance

---

Example

```java
synchronized(this) {

    updateBalance();
}
```

---

# When to Use Locks

Use when:

- Need timeout support
- Need fairness
- Need interruptible locking
- Need read-write separation
- High concurrency requirements

---

Example

```java
ReentrantLock lock =
        new ReentrantLock(true);
```

---

# Common Interview Questions

## Q1: Why was the Locks Framework introduced?

To provide more flexible locking than `synchronized`.

---

## Q2: What is ReentrantLock?

A reentrant mutual exclusion lock with advanced features such as fairness, timeout, and interruptible locking.

---

## Q3: What does tryLock() do?

Attempts to acquire the lock immediately without waiting.

---

## Q4: What is the difference between lock() and lockInterruptibly()?

### lock()

```text
Cannot Be Interrupted
While Waiting
```

---

### lockInterruptibly()

```text
Can Be Interrupted
While Waiting
```

---

## Q5: What is a Fair Lock?

A lock that grants access in FIFO order.

---

## Q6: What is the default ReentrantLock?

```text
Non-Fair Lock
```

---

## Q7: What is ReadWriteLock?

A lock that allows multiple readers but only one writer.

---

## Q8: What is the advantage of StampedLock?

Supports:

```text
Optimistic Reading
```

which improves read performance.

---

## Q9: Is StampedLock reentrant?

```text
No
```

---

## Q10: Which is better: Lock or synchronized?

Depends on requirements.

### Simple Use Cases

```java
synchronized
```

---

### Advanced Concurrency

```java
Lock Framework
```

---

# Key Takeaways

1. The Locks Framework was introduced in Java 5 to provide advanced synchronization features.
2. The `Lock` interface supports explicit lock management through `lock()` and `unlock()`.
3. `tryLock()` enables non-blocking lock acquisition.
4. `tryLock(timeout)` prevents threads from waiting indefinitely.
5. `lockInterruptibly()` allows waiting threads to be interrupted.
6. `ReentrantLock` provides the same basic functionality as `synchronized` plus additional capabilities.
7. Fair locks prevent starvation but may reduce throughput.
8. Non-fair locks provide better performance and are the default behavior.
9. `ReadWriteLock` improves concurrency by allowing multiple readers.
10. `ReentrantReadWriteLock` is ideal for read-heavy systems such as caches and configuration stores.
11. `StampedLock` introduces optimistic reads for maximum read performance.
12. `Lock` provides greater flexibility, while `synchronized` provides simplicity and automatic lock management.
13. Understanding the Locks Framework is essential before learning Semaphores, Thread Pools, ForkJoin Framework, and advanced concurrent programming techniques.