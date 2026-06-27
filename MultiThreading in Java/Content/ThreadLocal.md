# 29. ThreadLocal

One of the most misunderstood topics in Java Multithreading is:

```java
ThreadLocal
```

Most synchronization mechanisms focus on:

```text
Sharing Data Between Threads
```

such as:

- synchronized
- Lock
- Semaphore
- Concurrent Collections

---

However, sometimes we want exactly the opposite:

```text
Each Thread Should Have Its Own Copy Of Data
```

without sharing it with other threads.

This is where:

```java
ThreadLocal
```

comes into play.

---

# What is ThreadLocal?

## Definition

`ThreadLocal` provides thread-local variables.

Each thread gets its own independent copy of the variable.

---

## Package

```java
java.lang.ThreadLocal
```

---

# Simple Definition

Think of ThreadLocal as:

```text
A Private Storage Box
For Each Thread
```

---

Every thread stores its own value.

---

No synchronization required.

---

# Visualization

Without ThreadLocal:

```text
Thread A
      \
       Shared Variable
      /
Thread B
```

---

Potential problems:

```text
Race Condition

Synchronization Required
```

---

With ThreadLocal:

```text
Thread A → Own Copy

Thread B → Own Copy

Thread C → Own Copy
```

---

No sharing.

---

# Why ThreadLocal?

Suppose:

```java
String userId;
```

---

Thread A

```text
User = John
```

---

Thread B

```text
User = Alice
```

---

If shared:

```text
Data Overwritten
```

---

Incorrect results.

---

Instead:

```java
ThreadLocal<String>
```

gives each thread its own value.

---

# Real-World Analogy

Imagine a hotel.

---

Each guest has:

```text
Private Locker
```

---

Guest A stores:

```text
Passport
```

---

Guest B stores:

```text
Laptop
```

---

Guests cannot access each other's lockers.

---

ThreadLocal works the same way.

---

# Internal Working

Every thread internally maintains:

```java
ThreadLocalMap
```

---

Visualization

```text
Thread A

ThreadLocalMap
    |
    +--> User = John
```

---

```text
Thread B

ThreadLocalMap
    |
    +--> User = Alice
```

---

Notice:

```text
Same ThreadLocal Object

Different Values
```

---

# Creating ThreadLocal

## Syntax

```java
ThreadLocal<String> threadLocal =
        new ThreadLocal<>();
```

---

# Example

```java
ThreadLocal<String> user =
        new ThreadLocal<>();
```

---

Each thread gets:

```text
Independent User Value
```

---

# Thread Confinement

One of the most important ThreadLocal concepts.

---

## What is Thread Confinement?

Keeping data restricted to a single thread.

---

Instead of:

```text
Protect Shared Data
```

we:

```text
Avoid Sharing Data
```

altogether.

---

# Example

Without Thread Confinement

```java
private static String user;
```

---

All threads share it.

---

Need:

```java
synchronized
```

---

With ThreadLocal

```java
private static ThreadLocal<String> user;
```

---

Every thread has:

```text
Own Copy
```

---

No synchronization required.

---

# ThreadLocal Methods

---

# set()

## Definition

Stores value for current thread.

---

## Syntax

```java
threadLocal.set(value);
```

---

# Example

```java
ThreadLocal<String> user =
        new ThreadLocal<>();

user.set("John");
```

---

Current thread stores:

```text
John
```

---

# Visualization

```text
Thread A

user = John
```

---

```text
Thread B

user = Alice
```

---

Independent values.

---

# get()

## Definition

Returns value associated with current thread.

---

## Syntax

```java
threadLocal.get();
```

---

# Example

```java
String value =
        user.get();
```

---

Output

```text
John
```

for current thread.

---

# Important

Thread A cannot read:

```text
Thread B's Value
```

---

# Example

```java
ThreadLocal<String> user =
        new ThreadLocal<>();

user.set("John");

System.out.println(
        user.get()
);
```

---

Output

```text
John
```

---

# remove()

## Definition

Removes current thread's value.

---

## Syntax

```java
threadLocal.remove();
```

---

# Example

```java
user.remove();
```

---

After removal:

```java
user.get()
```

returns:

```text
null
```

(unless initial value is configured)

---

# Why remove() Is Important?

Very important in:

```text
Thread Pools
```

---

Otherwise:

```text
Memory Leaks
```

may occur.

---

# Complete Example

```java
ThreadLocal<String> user =
        new ThreadLocal<>();

Runnable task = () -> {

    user.set(
            Thread.currentThread()
                  .getName()
    );

    System.out.println(
            user.get()
    );

    user.remove();
};
```

---

Output

```text
Thread-1

Thread-2

Thread-3
```

---

Each thread gets its own value.

---

# Initial Value

Instead of returning:

```text
null
```

we can define a default value.

---

## Example

```java
ThreadLocal<String> user =
        ThreadLocal.withInitial(
                () -> "Guest"
        );
```

---

# Output

```java
user.get();
```

returns:

```text
Guest
```

---

# Example

```java
System.out.println(
        user.get()
);
```

---

Output

```text
Guest
```

---

# InheritableThreadLocal

Normal ThreadLocal values are:

```text
NOT Inherited
```

by child threads.

---

# Example

```java
ThreadLocal<String> user =
        new ThreadLocal<>();
```

---

Parent Thread

```java
user.set("John");
```

---

Child Thread

```java
user.get();
```

---

Output

```text
null
```

---

# Solution

Use:

```java
InheritableThreadLocal
```

---

# Definition

Allows child threads to inherit parent thread values.

---

## Syntax

```java
InheritableThreadLocal<String> user =
        new InheritableThreadLocal<>();
```

---

# Example

```java
InheritableThreadLocal<String> user =
        new InheritableThreadLocal<>();

user.set("John");
```

---

Child Thread

```java
System.out.println(
        user.get()
);
```

---

Output

```text
John
```

---

# Visualization

```text
Parent Thread

user = John

↓

Child Thread

user = John
```

---

# Common Use Cases

- Security Context
- User Context
- Request Context
- Correlation IDs

---

# Memory Leak Issues

One of the most important interview topics.

---

# Why Memory Leaks Happen?

Internally:

```java
Thread
```

contains:

```java
ThreadLocalMap
```

---

Visualization

```text
Thread

↓

ThreadLocalMap

↓

Value Object
```

---

In thread pools:

```text
Threads Live Long Time
```

---

If value isn't removed:

```text
Data Remains In Memory
```

---

# Example

```java
threadLocal.set(
        hugeObject
);
```

---

No:

```java
remove()
```

---

Thread reused by pool.

---

Memory remains occupied.

---

# Result

```text
Memory Leak
```

---

# Thread Pool Problem

Very common in:

```java
ExecutorService
```

---

Thread reused:

```text
Task 1
```

stores:

```text
User = John
```

---

Thread reused again:

```text
Task 2
```

---

May unexpectedly see:

```text
John
```

---

Even though it belongs to previous request.

---

# Example

Bad

```java
threadLocal.set("John");
```

---

No cleanup.

---

Thread reused.

---

Unexpected behavior.

---

# Correct Approach

```java
try {

    threadLocal.set("John");

} finally {

    threadLocal.remove();
}
```

---

# Internal Structure

Important interview topic.

---

ThreadLocal stores values in:

```java
ThreadLocalMap
```

inside:

```java
Thread
```

---

Not inside ThreadLocal itself.

---

Visualization

```text
Thread

↓

ThreadLocalMap

↓

Key = ThreadLocal

Value = Actual Value
```

---

# Common Use Cases

---

# Use Case 1: User Context

Store logged-in user.

---

Example

```java
ThreadLocal<User>
```

---

# Use Case 2: Request Context

Store:

```text
Request ID

Session ID
```

---

throughout request processing.

---

# Use Case 3: Database Connection

Per-thread database connection.

---

Example

```java
ThreadLocal<Connection>
```

---

# Use Case 4: Date Formatting

Historically used with:

```java
SimpleDateFormat
```

because it is not thread-safe.

---

Example

```java
ThreadLocal<SimpleDateFormat>
```

---

# Use Case 5: Logging Correlation ID

Store:

```text
Trace ID

Transaction ID
```

for logging.

---

# ThreadLocal vs Shared Variable

| Feature | Shared Variable | ThreadLocal |
|----------|----------|----------|
| Shared Between Threads | Yes | No |
| Synchronization Needed | Yes | No |
| Race Condition | Possible | Impossible |
| Thread Safety | Manual | Automatic |
| Memory Usage | Lower | Higher |

---

# ThreadLocal vs synchronized

| Feature | ThreadLocal | synchronized |
|----------|----------|----------|
| Data Sharing | No | Yes |
| Locking Required | No | Yes |
| Contention | None | Possible |
| Performance | High | Depends |
| Purpose | Thread Confinement | Shared Resource Protection |

---

# Common Mistakes

---

## Mistake 1

Forgetting remove()

Bad

```java
threadLocal.set(value);
```

---

No cleanup.

---

Result

```text
Memory Leak
```

---

# Mistake 2

Using ThreadLocal For Shared Data

Bad

```text
Need Shared Value
```

---

ThreadLocal creates:

```text
Separate Copies
```

---

Use shared variables instead.

---

# Mistake 3

Ignoring Thread Pools

ThreadLocal values survive:

```text
Thread Reuse
```

---

Must clean up.

---

# Mistake 4

Overusing ThreadLocal

Not every variable needs:

```java
ThreadLocal
```

---

Use only when:

```text
Thread-Specific Data
```

is required.

---

# Common Interview Questions

## Q1: What is ThreadLocal?

A utility that provides a separate copy of a variable for each thread.

---

## Q2: Is ThreadLocal thread-safe?

```text
Yes
```

because data is not shared.

---

## Q3: Where are ThreadLocal values stored?

Inside:

```java
ThreadLocalMap
```

which is maintained by each thread.

---

## Q4: What does set() do?

Stores a value for the current thread.

---

## Q5: What does get() do?

Returns the current thread's value.

---

## Q6: What does remove() do?

Removes the current thread's value.

---

## Q7: Why is remove() important?

To prevent:

```text
Memory Leaks
```

especially in thread pools.

---

## Q8: What is Thread Confinement?

Keeping data restricted to a single thread.

---

## Q9: What is InheritableThreadLocal?

A ThreadLocal whose values are inherited by child threads.

---

## Q10: Give a real-world use case.

- User Context
- Request Context
- Security Context
- Logging Correlation IDs
- Database Connections

---

# Key Takeaways

1. `ThreadLocal` provides a separate copy of a variable for each thread.
2. It is based on the concept of Thread Confinement.
3. `set()`, `get()`, and `remove()` are the primary methods.
4. Each thread stores values in its own internal `ThreadLocalMap`.
5. ThreadLocal eliminates the need for synchronization when data should not be shared.
6. `InheritableThreadLocal` allows child threads to inherit parent thread values.
7. Always call `remove()` when using ThreadLocal in thread pools.
8. Failure to clean up ThreadLocal values can lead to memory leaks.
9. ThreadLocal is commonly used for user context, request context, security information, and logging metadata.
10. It improves performance by avoiding lock contention.
11. ThreadLocal is not a replacement for synchronization; it is a solution for thread-specific state.
12. Understanding ThreadLocal is essential before learning Framework Internals, Spring Transaction Context, Logging Correlation IDs (MDC), Reactive Programming Caveats, and Enterprise Application Design.