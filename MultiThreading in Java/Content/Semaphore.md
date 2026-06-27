
In multithreaded applications, sometimes we do **not** want only one thread at a time (like `synchronized`).

Instead, we want:

```text
Allow Limited Number Of Threads
```

to access a resource simultaneously.

Examples:

- Database connection pool
- Printer pool
- ATM machines
- Parking lot
- API rate limiting
- Download manager

To solve such problems, Java provides:

```java
Semaphore
```

---

# What is Semaphore?

## Definition

A `Semaphore` is a synchronization mechanism that controls access to a shared resource using a fixed number of permits.

---

## Package

```java
java.util.concurrent.Semaphore
```

---

## Simple Definition

Think of Semaphore as:

```text
Permission Manager
```

---

A thread must obtain permission before accessing a resource.

---

# Real-World Analogy

Suppose:

```text
Parking Lot
```

has:

```text
5 Parking Spaces
```

---

Rules:

```text
Car Enters
↓

Occupies One Space
↓

Car Leaves
↓

Space Becomes Available
```

---

At any time:

```text
Maximum 5 Cars
```

can be inside.

---

This is exactly how Semaphore works.

---

# Visualization

```text
Available Permits = 3

Thread A → Allowed

Thread B → Allowed

Thread C → Allowed

Thread D → Wait
```

---

When one thread leaves:

```text
Permit Released

↓

Thread D Allowed
```

---

# Why Semaphore?

Suppose we have:

```text
100 Threads
```

trying to access:

```text
10 Database Connections
```

---

Without Semaphore:

```text
All Threads Access Database
```

---

Problems:

- Resource exhaustion
- Performance degradation
- Connection failures

---

With Semaphore:

```text
Only 10 Threads Allowed
```

simultaneously.

---

Remaining threads wait.

---

# Permits

## What is a Permit?

A permit represents:

```text
One Available Resource Slot
```

---

Example

```java
Semaphore semaphore =
        new Semaphore(3);
```

---

Meaning:

```text
3 Permits Available
```

---

Only:

```text
3 Threads
```

can proceed simultaneously.

---

# Visualization

```text
Semaphore(3)

Permits:
[1][2][3]
```

---

Thread A

```text
Consumes Permit 1
```

---

Thread B

```text
Consumes Permit 2
```

---

Thread C

```text
Consumes Permit 3
```

---

Thread D

```text
Waits
```

---

# Creating Semaphore

## Example

```java
Semaphore semaphore =
        new Semaphore(3);
```

---

Available permits:

```text
3
```

---

# Fair Semaphore

```java
Semaphore semaphore =
        new Semaphore(3, true);
```

---

Meaning

```text
FIFO Ordering
```

---

Threads acquire permits in arrival order.

---

# Non-Fair Semaphore

(Default)

```java
Semaphore semaphore =
        new Semaphore(3);
```

---

Meaning

```text
No Ordering Guarantee
```

---

Provides:

```text
Higher Throughput
```

---

# acquire()

## Definition

Acquires a permit.

---

If permit available:

```text
Proceed
```

---

If no permit available:

```text
Block And Wait
```

---

# Syntax

```java
semaphore.acquire();
```

---

# Example

```java
Semaphore semaphore =
        new Semaphore(2);

semaphore.acquire();
```

---

Available permits:

```text
Before = 2

After = 1
```

---

# Visualization

```text
Permits = 2

Thread A acquire()

↓

Permits = 1
```

---

# acquire(int permits)

Acquire multiple permits.

---

Example

```java
semaphore.acquire(2);
```

---

Meaning

```text
Consume 2 Permits
```

---

# tryAcquire()

Attempts to acquire permit immediately.

---

Does not wait.

---

Example

```java
if(semaphore.tryAcquire()) {

    // Access resource
}
```

---

Returns

```java
true
```

if successful.

---

Otherwise

```java
false
```

---

# tryAcquire(timeout)

Waits for a limited time.

---

Example

```java
semaphore.tryAcquire(
        5,
        TimeUnit.SECONDS
);
```

---

Meaning

```text
Wait Up To 5 Seconds
```

---

# release()

## Definition

Returns permit back to semaphore.

---

# Syntax

```java
semaphore.release();
```

---

# Example

```java
semaphore.release();
```

---

Available permits:

```text
Before = 1

After = 2
```

---

# Visualization

```text
Thread Finishes

↓

release()

↓

Permit Available Again
```

---

# Important Rule

Every successful:

```java
acquire()
```

should usually have a matching:

```java
release()
```

---

# Best Practice

Use:

```java
try-finally
```

---

Example

```java
semaphore.acquire();

try {

    // Critical work

} finally {

    semaphore.release();
}
```

---

Ensures permit is always returned.

---

# Complete Example

```java
Semaphore semaphore =
        new Semaphore(2);

Runnable task = () -> {

    try {

        semaphore.acquire();

        System.out.println(
                Thread.currentThread()
                      .getName()
                + " acquired permit"
        );

        Thread.sleep(3000);

    } catch(Exception e) {

        e.printStackTrace();

    } finally {

        semaphore.release();

        System.out.println(
                Thread.currentThread()
                      .getName()
                + " released permit"
        );
    }
};
```

---

If 5 threads execute:

```text
Only 2 Run Simultaneously
```

---

Others wait.

---

# Binary Semaphore

## Definition

Semaphore with:

```text
Only One Permit
```

---

Example

```java
Semaphore semaphore =
        new Semaphore(1);
```

---

Behavior

```text
Only One Thread
Can Access Resource
```

---

# Visualization

```text
Permit = 1

Thread A → Allowed

Thread B → Wait
```

---

# Similar To

```java
synchronized
```

or

```java
ReentrantLock
```

---

# Difference

Semaphore ownership is not enforced.

Any thread can:

```java
release()
```

the permit.

---

# Example

```java
Semaphore semaphore =
        new Semaphore(1);
```

---

Also known as:

```text
Binary Semaphore
```

---

# Counting Semaphore

## Definition

Semaphore with:

```text
Multiple Permits
```

---

Example

```java
Semaphore semaphore =
        new Semaphore(5);
```

---

Meaning

```text
5 Threads
Can Access Simultaneously
```

---

# Visualization

```text
Semaphore(5)

Thread1

Thread2

Thread3

Thread4

Thread5
```

---

Allowed.

---

Thread6

```text
Waits
```

---

# Binary vs Counting Semaphore

| Feature | Binary Semaphore | Counting Semaphore |
|----------|----------|----------|
| Permits | 1 | Multiple |
| Threads Allowed | 1 | Many |
| Similar To | Lock | Resource Pool |
| Use Case | Mutual Exclusion | Limited Resources |

---

# Available Permits

Check current permits.

---

Example

```java
int permits =
        semaphore.availablePermits();
```

---

# Example

```java
Semaphore semaphore =
        new Semaphore(3);

System.out.println(
        semaphore.availablePermits()
);
```

---

Output

```text
3
```

---

# drainPermits()

Removes all available permits.

---

Example

```java
int count =
        semaphore.drainPermits();
```

---

# Effect

```text
Available Permits

↓

0
```

---

# Real-World Examples

---

# Example 1: Database Connection Pool

Suppose:

```text
Database Connections = 20
```

---

Create

```java
Semaphore semaphore =
        new Semaphore(20);
```

---

Before accessing database:

```java
semaphore.acquire();
```

---

After work:

```java
semaphore.release();
```

---

Guarantee:

```text
Maximum 20 Active Connections
```

---

# Example 2: Printer Pool

Suppose:

```text
3 Printers
```

---

Create

```java
Semaphore semaphore =
        new Semaphore(3);
```

---

Only:

```text
3 Print Jobs
```

at a time.

---

# Example 3: ATM Machines

Suppose:

```text
4 ATMs
```

---

Semaphore

```java
new Semaphore(4);
```

---

Only 4 customers can use ATMs simultaneously.

---

# Example 4: Parking Lot

Suppose:

```text
100 Parking Spaces
```

---

Semaphore

```java
new Semaphore(100);
```

---

Car enters

```java
acquire()
```

---

Car leaves

```java
release()
```

---

# Example 5: API Rate Limiting

Suppose:

```text
Only 50 Requests
```

allowed concurrently.

---

Semaphore controls access.

---

# Semaphore vs synchronized

| Feature | Semaphore | synchronized |
|-----------|-----------|-----------|
| Multiple Permits | Yes | No |
| Thread Safe | Yes | Yes |
| Fairness Support | Yes | No |
| Ownership Required | No | Yes |
| Concurrent Access | Limited | Single Thread |

---

# Semaphore vs ReentrantLock

| Feature | Semaphore | ReentrantLock |
|----------|----------|----------|
| Multiple Permits | Yes | No |
| Explicit Unlock | Yes | Yes |
| Fairness Support | Yes | Yes |
| Ownership Tracking | No | Yes |
| Resource Pooling | Excellent | Poor |

---

# Common Mistakes

---

## Mistake 1

Forgetting release()

---

Bad

```java
semaphore.acquire();

// work

// release missing
```

---

Result

```text
Permit Leak
```

---

Eventually:

```text
All Threads Block Forever
```

---

# Mistake 2

Not Using finally

---

Bad

```java
semaphore.acquire();

doWork();
```

---

If exception occurs:

```text
Permit Never Returned
```

---

Always use:

```java
finally
```

---

# Mistake 3

Using Semaphore Instead of Lock

Semaphore is not always the best choice.

---

If only one thread should enter:

```java
synchronized
```

or

```java
ReentrantLock
```

may be simpler.

---

# Internal Workflow

```text
Thread Requests Resource

↓

acquire()

↓

Permit Available?

↓

YES → Proceed

NO → Wait

↓

Work Done

↓

release()

↓

Permit Returned
```

---

# Common Interview Questions

## Q1: What is Semaphore?

A synchronization utility that controls access to resources using permits.

---

## Q2: What is a permit?

A permit represents one available resource slot.

---

## Q3: What happens if no permit is available?

The thread blocks until a permit becomes available.

---

## Q4: Difference between Binary Semaphore and Counting Semaphore?

### Binary Semaphore

```java
new Semaphore(1)
```

Only one thread allowed.

---

### Counting Semaphore

```java
new Semaphore(n)
```

Multiple threads allowed.

---

## Q5: Difference between acquire() and release()?

### acquire()

Obtains a permit.

---

### release()

Returns a permit.

---

## Q6: Is Semaphore thread-safe?

```text
Yes
```

---

## Q7: Can a different thread call release()?

```text
Yes
```

Unlike locks, Semaphore does not enforce ownership.

---

## Q8: What is a Fair Semaphore?

```java
new Semaphore(permits, true)
```

Provides FIFO ordering.

---

## Q9: Which is faster, fair or non-fair semaphore?

```text
Non-Fair
```

because it has less scheduling overhead.

---

## Q10: Give a real-world use case of Semaphore.

- Database connection pool
- Printer pool
- ATM machines
- Parking lot
- API rate limiting

---

# Key Takeaways

1. `Semaphore` controls access to resources using permits.
2. A permit represents one available resource slot.
3. `acquire()` obtains a permit and may block if none are available.
4. `release()` returns a permit back to the semaphore.
5. A Binary Semaphore has one permit and behaves similarly to a lock.
6. A Counting Semaphore has multiple permits and supports controlled concurrency.
7. Fair Semaphores provide FIFO ordering, while non-fair semaphores provide higher throughput.
8. Semaphore is commonly used for resource pools and rate-limiting scenarios.
9. Always pair `acquire()` with `release()`, preferably using `try-finally`.
10. Unlike locks, Semaphore does not enforce thread ownership.
11. Semaphore is ideal when you want to allow a limited number of threads to access a shared resource simultaneously.
12. Understanding Semaphore is essential before learning Rate Limiting, Resource Pooling, Advanced Concurrency Patterns, and High-Scalability System Design.