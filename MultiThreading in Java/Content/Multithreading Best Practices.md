
Learning threads, locks, executors, and concurrent collections is important.

However, in real-world systems, success depends less on knowing APIs and more on:

```text
Following Good Concurrency Practices
```

Most production issues occur because developers:

- Share too much state
- Use unnecessary synchronization
- Create too many threads
- Ignore interruptions
- Forget to release locks
- Introduce deadlocks

This chapter covers industry-proven best practices for writing scalable, maintainable, and high-performance multithreaded applications.

---

# Immutable Objects

One of the most important concurrency best practices.

---

## What is an Immutable Object?

An object whose state cannot change after creation.

---

Examples:

```java
String
```

```java
LocalDate
```

```java
BigInteger
```

---

# Example

```java
public final class Employee {

    private final String name;

    public Employee(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

---

Object state:

```text
Created Once

↓

Never Changes
```

---

# Why Immutable Objects Are Thread Safe?

Because:

```text
No Thread
Can Modify State
```

---

Therefore:

```text
No Race Condition

No Synchronization

No Locks
```

---

# Benefits

- Naturally thread-safe
- No synchronization needed
- Better performance
- Easier debugging
- Easier testing

---

# Recommendation

Prefer:

```text
Immutable Objects
```

whenever possible.

---

# Minimize Shared State

One of the biggest sources of concurrency bugs.

---

## Shared State

Data accessed by multiple threads.

---

Example

```java
int counter;
```

---

Multiple threads:

```java
counter++;
```

---

Potential race condition.

---

# Problem

Shared mutable state causes:

- Race conditions
- Visibility issues
- Deadlocks
- Synchronization overhead

---

# Bad Design

```java
class Counter {

    int count;
}
```

---

Used by:

```text
100 Threads
```

---

# Better Design

Each thread works on:

```text
Its Own Data
```

---

# Example

```java
Runnable task = () -> {

    int localCount = 0;

    localCount++;
};
```

---

Local variables:

```text
Thread-Private
```

---

No synchronization required.

---

# Rule

```text
Share As Little State
As Possible
```

---

# Prefer Concurrent Collections

Many developers still use:

```java
HashMap
ArrayList
HashSet
```

with manual synchronization.

---

# Bad Example

```java
Map<String,String> map =
        Collections.synchronizedMap(
                new HashMap<>()
        );
```

---

Works.

---

But scalability is limited.

---

# Better

Use concurrent collections.

---

Examples

```java
ConcurrentHashMap
```

```java
CopyOnWriteArrayList
```

```java
ConcurrentLinkedQueue
```

```java
BlockingQueue
```

---

# Why?

Designed for concurrency.

---

Benefits:

- Better throughput
- Reduced lock contention
- Fine-grained locking
- Thread-safe operations

---

# Recommendation

Prefer:

```java
ConcurrentHashMap
```

over:

```java
Hashtable
```

or synchronized maps.

---

# Prefer Executor Framework

One of the most important best practices.

---

# Bad Example

```java
new Thread(task).start();
```

---

Repeated:

```text
Thousands Of Times
```

---

Problems:

- Expensive creation
- Memory overhead
- Context switching
- Hard lifecycle management

---

# Better

Use:

```java
ExecutorService
```

---

Example

```java
ExecutorService pool =
        Executors.newFixedThreadPool(10);
```

---

Submit tasks.

```java
pool.submit(task);
```

---

# Benefits

- Thread reuse
- Better performance
- Controlled concurrency
- Easier monitoring

---

# Industry Standard

Modern applications almost always use:

```text
Executor Framework
```

instead of manual thread creation.

---

# Avoid Manual Thread Management

Closely related to the previous point.

---

# Manual Thread Management Problems

Example

```java
Thread t =
        new Thread(task);

t.start();
```

---

Need to manage:

- Creation
- Lifecycle
- Exception handling
- Shutdown
- Monitoring

---

For:

```text
Hundreds Of Threads
```

becomes difficult.

---

# Better Alternatives

Use:

```java
ExecutorService
```

---

or

```java
Virtual Threads
```

(Java 21+)

---

# Recommendation

Avoid:

```java
new Thread()
```

for application-level concurrency.

---

Use:

```text
Thread Pools

Virtual Threads

Executors
```

---

# Avoid Nested Locks

One of the biggest causes of deadlocks.

---

# Bad Example

```java
synchronized(lock1) {

    synchronized(lock2) {

    }
}
```

---

Another thread:

```java
synchronized(lock2) {

    synchronized(lock1) {

    }
}
```

---

Potential:

```text
Deadlock
```

---

# Rule

Avoid acquiring:

```text
Multiple Locks
```

whenever possible.

---

# If Multiple Locks Are Necessary

Always acquire locks in:

```text
Consistent Order
```

---

Example

```text
Always Lock

Account A

↓

Then

Account B
```

---

Never reverse order.

---

# Always Release Locks

Critical best practice.

---

# Problem

Forgetting to release lock.

---

Example

```java
lock.lock();

doWork();
```

---

Exception occurs.

---

```java
unlock()
```

never executed.

---

Other threads:

```text
Blocked Forever
```

---

# Correct Approach

Use:

```java
try-finally
```

---

Example

```java
lock.lock();

try {

    doWork();

} finally {

    lock.unlock();
}
```

---

# Rule

```text
Always Release Lock
Inside finally Block
```

---

# Use Timeouts

Never wait forever unless absolutely necessary.

---

# Problem

Infinite waiting.

---

Example

```java
future.get();
```

---

Can block forever.

---

# Better

```java
future.get(
        5,
        TimeUnit.SECONDS
);
```

---

# Lock Example

```java
lock.tryLock(
        10,
        TimeUnit.SECONDS
);
```

---

# Benefits

- Prevents hanging
- Improves resiliency
- Easier troubleshooting

---

# Recommendation

Prefer:

```text
Timeout-Based Waiting
```

over:

```text
Infinite Waiting
```

---

# Handle Interruptions Properly

One of the most frequently ignored topics.

---

# Problem

Developers swallow interrupts.

---

Bad Example

```java
try {

    Thread.sleep(1000);

} catch(Exception e) {

}
```

---

Interrupt signal lost.

---

# Why Is This Bad?

Thread cannot respond to:

```text
Shutdown

Cancellation

Stop Requests
```

---

# Correct Approach

```java
try {

    Thread.sleep(1000);

} catch(InterruptedException e) {

    Thread.currentThread()
          .interrupt();
}
```

---

# Why Re-interrupt?

Preserves interrupt status.

---

Allows upper layers to react.

---

# Rule

Never silently ignore:

```java
InterruptedException
```

---

# Thread Naming Conventions

Extremely useful in production systems.

---

# Bad Example

Default thread names.

```text
Thread-1

Thread-2

Thread-3
```

---

Thread dump becomes difficult.

---

# Better

Meaningful names.

---

Example

```text
OrderProcessor-1

OrderProcessor-2

OrderProcessor-3
```

---

# Creating Named Threads

```java
Thread t =
        new Thread(
                task,
                "OrderProcessor"
        );
```

---

# Named Thread Pool

```java
ThreadFactory factory =
        r -> new Thread(
                r,
                "OrderWorker"
        );
```

---

# Benefits

- Easier debugging
- Better monitoring
- Better thread dumps
- Faster production troubleshooting

---

# Additional Best Practices

Although not in the syllabus, these are frequently used in industry.

---

# Prefer Atomic Classes

Instead of:

```java
synchronized
```

for counters.

---

Use:

```java
AtomicInteger
```

---

# Keep Critical Sections Small

Bad

```java
synchronized(lock) {

    readFile();

    callAPI();

    updateDB();
}
```

---

Good

```java
readFile();

callAPI();

synchronized(lock) {

    updateCounter();
}
```

---

Less contention.

---

# Prefer ReadWriteLock For Read-Heavy Systems

Example:

```text
Configuration Cache

Product Catalog

Reference Data
```

---

Multiple readers.

---

Few writers.

---

# Avoid Busy Waiting

Bad

```java
while(!flag) {

}
```

---

Consumes CPU continuously.

---

Better

```java
wait()

notify()

BlockingQueue
```

---

# Use Virtual Threads For I/O Workloads

Java 21+

---

Excellent for:

```text
REST Calls

Database Calls

File I/O
```

---

# Best Practices Summary

| Practice | Why? |
|-----------|-----------|
| Immutable Objects | Naturally thread-safe |
| Minimize Shared State | Avoid race conditions |
| Concurrent Collections | Better scalability |
| Executor Framework | Thread reuse |
| Avoid Manual Threads | Easier management |
| Avoid Nested Locks | Prevent deadlocks |
| Always Release Locks | Prevent lock leaks |
| Use Timeouts | Avoid infinite waits |
| Handle Interruptions | Support cancellation |
| Name Threads Properly | Easier debugging |

---

# Common Interview Questions

## Q1: What is the best way to achieve thread safety?

Prefer:

```text
Immutable Objects
```

whenever possible.

---

## Q2: Why should shared mutable state be minimized?

Because it causes:

- Race conditions
- Visibility problems
- Synchronization overhead

---

## Q3: Why are concurrent collections preferred?

They provide:

```text
Better Concurrency

Better Performance

Reduced Lock Contention
```

---

## Q4: Why should ExecutorService be preferred over Thread?

Because it:

- Reuses threads
- Improves scalability
- Simplifies lifecycle management

---

## Q5: Why are nested locks dangerous?

They can cause:

```text
Deadlocks
```

---

## Q6: Why should locks be released in finally?

To guarantee release even if exceptions occur.

---

## Q7: Why use timeouts?

To avoid:

```text
Infinite Waiting
```

and improve resilience.

---

## Q8: Why should InterruptedException not be ignored?

Because it carries thread cancellation and shutdown signals.

---

## Q9: Why are meaningful thread names important?

They simplify:

- Monitoring
- Debugging
- Thread dump analysis

---

## Q10: What is the most important concurrency best practice?

Many senior developers would answer:

```text
Avoid Shared Mutable State
```

because most concurrency bugs originate from it.

---

# Senior Interview Scenario

### Question

A production application occasionally hangs.

Investigation shows:

```java
lock.lock();

processData();

throw new RuntimeException();
```

---

What is wrong?

### Answer

The lock is never released.

---

Correct implementation:

```java
lock.lock();

try {

    processData();

} finally {

    lock.unlock();
}
```

---

Otherwise:

```text
Threads Block Forever

↓

Application Appears Hung
```

---

# Key Takeaways

1. Prefer immutable objects whenever possible.
2. Shared mutable state should be minimized.
3. Concurrent collections are preferred over synchronized collections.
4. Executor Framework should be used instead of manual thread creation.
5. Avoid managing large numbers of threads manually.
6. Nested locks increase deadlock risk.
7. Always release locks in a `finally` block.
8. Use timeouts to avoid infinite waits.
9. Handle interruptions correctly by preserving interrupt status.
10. Use meaningful thread names for easier debugging.
11. Keep synchronization minimal and critical sections small.
12. Following these practices dramatically improves scalability, maintainability, and reliability in multithreaded Java applications.