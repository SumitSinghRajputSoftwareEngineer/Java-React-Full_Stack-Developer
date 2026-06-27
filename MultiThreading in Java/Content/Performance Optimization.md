
Writing a multithreaded application is only half the job.

The real challenge is:

```text
Making It Fast
Making It Scalable
Making It Efficient
```

Many applications suffer from:

- Too many threads
- Wrong thread pool sizes
- Excessive synchronization
- Lock contention
- Context switching
- Cache inefficiencies

As a result:

```text
More Threads
≠
Better Performance
```

This chapter focuses on optimizing concurrent Java applications for production environments.

---

# Choosing Correct Pool Size

One of the most important performance decisions is:

```text
Thread Pool Size
```

---

# Why Pool Size Matters?

Too Few Threads:

```text
CPU Underutilized
```

---

Too Many Threads:

```text
Context Switching

Memory Waste

Scheduler Overhead
```

---

Goal:

```text
Maximum Throughput
With Minimum Overhead
```

---

# General Rule

Before choosing pool size, identify:

```text
CPU Bound Task

OR

I/O Bound Task
```

---

# CPU Bound Thread Pools

## What Are CPU Bound Tasks?

Tasks that spend most of their time:

```text
Using CPU
```

---

Examples:

- Sorting
- Encryption
- Compression
- Image Processing
- Mathematical Calculations
- Data Analysis

---

# Example

```java
Arrays.sort(data);
```

---

Thread rarely waits.

---

CPU is continuously busy.

---

# Recommended Pool Size

Formula:

```java
Number of CPU Cores + 1
```

---

Example

Machine:

```text
8 Cores
```

---

Pool Size:

```text
8 or 9
```

---

# Why Not 100 Threads?

Because:

```text
Only 8 Cores
```

can execute simultaneously.

---

Extra threads cause:

```text
Context Switching
```

---

Performance decreases.

---

# Code Example

```java
int cores =
        Runtime.getRuntime()
               .availableProcessors();

ExecutorService pool =
        Executors.newFixedThreadPool(
                cores + 1
        );
```

---

# CPU Bound Visualization

```text
8 CPU Cores

↓

8 Worker Threads

↓

Maximum CPU Utilization
```

---

# I/O Bound Thread Pools

## What Are I/O Bound Tasks?

Tasks that spend most of their time:

```text
Waiting
```

for external systems.

---

Examples:

- Database Queries
- REST API Calls
- File Reading
- Network Operations
- Message Queues

---

# Example

```java
httpClient.send(...)
```

---

Most time spent:

```text
Waiting For Response
```

---

Not using CPU.

---

# Recommended Pool Size

Common formula:

```text
Threads =
Cores * (1 + Wait Time / Compute Time)
```

---

# Example

```text
8 Cores

Wait Time = 90 ms

Compute Time = 10 ms
```

---

Formula:

```text
8 × (1 + 90/10)

=
80 Threads
```

---

More threads are acceptable because many threads remain blocked.

---

# Visualization

```text
Thread 1 Waiting

Thread 2 Waiting

Thread 3 Waiting

Thread 4 Working
```

---

CPU can schedule other threads.

---

# Practical Rule

CPU Bound:

```text
Cores + 1
```

---

I/O Bound:

```text
2x – 10x Cores
```

or more depending on waiting time.

---

# Avoiding Excessive Thread Creation

One of the most common mistakes.

---

# Bad Example

```java
for(int i=0;i<10000;i++) {

    new Thread(task).start();
}
```

---

Problems:

- Huge memory usage
- Context switching
- Scheduler overload
- Poor scalability

---

# Better Approach

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

Threads are:

```text
Created Once

Reused Multiple Times
```

---

# Benefits

- Lower memory consumption
- Less context switching
- Better throughput

---

# Thread Creation Cost

Creating a thread requires:

- Memory allocation
- Native OS thread creation
- Stack allocation
- Scheduler registration

---

Therefore:

```text
Thread Creation
Is Expensive
```

---

# Reducing Lock Contention

One of the biggest scalability bottlenecks.

---

# What is Lock Contention?

Multiple threads compete for the same lock.

---

Example

```java
synchronized(lock) {

    process();
}
```

---

Only one thread can enter.

---

Other threads:

```text
BLOCKED
```

---

# Visualization

```text
Thread A → Lock

Thread B → Waiting

Thread C → Waiting

Thread D → Waiting
```

---

Performance decreases.

---

# Symptoms

- High BLOCKED threads
- Low throughput
- Increased response time

---

# Solution 1

Reduce lock scope.

---

Bad

```java
synchronized(lock) {

    readFile();

    process();

    saveResult();
}
```

---

Good

```java
readFile();

synchronized(lock) {

    updateSharedState();
}

saveResult();
```

---

Critical section becomes smaller.

---

# Solution 2

Use Concurrent Collections.

---

Instead of:

```java
HashMap
```

with synchronization.

---

Use:

```java
ConcurrentHashMap
```

---

Allows greater concurrency.

---

# Solution 3

Use ReadWriteLock.

---

Multiple readers:

```text
Allowed
```

---

Single writer:

```text
Exclusive
```

---

Useful when:

```text
Reads >> Writes
```

---

# Minimizing Synchronization

Synchronization is necessary.

---

But excessive synchronization hurts performance.

---

# Bad Example

```java
public synchronized void print() {

    System.out.println("Hello");
}
```

---

No shared state.

---

Synchronization unnecessary.

---

# Better

```java
public void print() {

    System.out.println("Hello");
}
```

---

# Guideline

Synchronize:

```text
Only Shared Mutable State
```

---

Avoid synchronizing:

```text
Read-Only Data

Local Variables

Immutable Objects
```

---

# Use Immutable Objects

Immutable objects require:

```text
No Synchronization
```

---

Example

```java
String
```

---

Benefits:

- Thread-safe
- No locks
- Better performance

---

# Lock Striping

An advanced optimization technique.

---

# Problem

Single lock protects entire structure.

---

Example

```java
synchronized(map) {

}
```

---

All threads compete for:

```text
One Lock
```

---

# Solution

Use:

```text
Multiple Locks
```

---

Each lock protects part of data.

---

This is called:

```text
Lock Striping
```

---

# Visualization

Without Lock Striping

```text
Map

↓

One Lock
```

---

All threads compete.

---

# With Lock Striping

```text
Bucket 1 → Lock 1

Bucket 2 → Lock 2

Bucket 3 → Lock 3

Bucket 4 → Lock 4
```

---

Different threads can access different buckets simultaneously.

---

# Example

```java
Lock[] locks =
        new ReentrantLock[16];
```

---

Hash determines:

```text
Which Lock To Use
```

---

Concurrency improves dramatically.

---

# Real-World Example

Java 7:

```java
ConcurrentHashMap
```

used:

```text
Segment Locking
```

---

This is a form of:

```text
Lock Striping
```

---

# Benefits

- Less contention
- Higher throughput
- Better scalability

---

# False Sharing

One of the most advanced concurrency topics.

---

# What is False Sharing?

Occurs when:

```text
Two Threads

Modify Different Variables

But Those Variables
Reside In Same CPU Cache Line
```

---

# Why Is This A Problem?

CPU cache works using:

```text
Cache Lines
```

typically:

```text
64 Bytes
```

---

Example

```java
class Counter {

    long count1;

    long count2;
}
```

---

Thread A updates:

```java
count1
```

---

Thread B updates:

```java
count2
```

---

Different variables.

---

Seems safe.

---

But both may reside in:

```text
Same Cache Line
```

---

# Result

CPU repeatedly invalidates caches.

---

Performance drops dramatically.

---

# Visualization

```text
Cache Line

count1 | count2
```

---

Thread A updates:

```text
Cache Invalidated
```

---

Thread B updates:

```text
Cache Invalidated Again
```

---

Continuous cache traffic.

---

# Why Called False Sharing?

Threads are:

```text
Not Actually Sharing
Data
```

---

But hardware behaves as if they are.

---

# Symptoms

- High CPU usage
- Poor scalability
- Unexpected performance degradation

---

# Solution 1

Padding

---

Example

```java
class Counter {

    long count1;

    long p1,p2,p3,p4,p5,p6,p7;

    long count2;
}
```

---

Separates variables into different cache lines.

---

# Solution 2

Use @Contended

---

Example

```java
@Contended
long counter;
```

---

JVM adds padding.

---

Note:

```text
Requires JVM Flags
```

in most environments.

---

# Real-World Usage

High-performance systems:

- Trading Platforms
- Databases
- Distributed Systems
- Messaging Engines

often address false sharing explicitly.

---

# Pool Size Summary

| Workload | Recommended Pool Size |
|-----------|-----------|
| CPU Bound | Cores + 1 |
| I/O Bound | Cores × (1 + Wait/Compute) |
| Mixed | Benchmark & Tune |

---

# Common Optimization Techniques

| Problem | Solution |
|----------|----------|
| Too Many Threads | Thread Pool |
| Context Switching | Reduce Threads |
| Lock Contention | Lock Striping |
| Blocking I/O | Async Programming |
| Excessive Synchronization | Minimize Critical Sections |
| Frequent Updates | Atomic Classes |
| Read-Heavy Workloads | ReadWriteLock |
| Cache Contention | Avoid False Sharing |

---

# Common Interview Questions

## Q1: How do you choose thread pool size?

Depends on whether the workload is CPU-bound or I/O-bound.

---

## Q2: What pool size is recommended for CPU-bound tasks?

```text
CPU Cores + 1
```

---

## Q3: Why can too many threads reduce performance?

Because of:

```text
Context Switching

Memory Overhead

Scheduler Cost
```

---

## Q4: What is Lock Contention?

Multiple threads competing for the same lock.

---

## Q5: How can lock contention be reduced?

- Smaller critical sections
- Concurrent collections
- ReadWriteLock
- Lock striping

---

## Q6: What is Lock Striping?

Using multiple locks instead of one global lock to increase concurrency.

---

## Q7: What is False Sharing?

Multiple threads modify different variables located in the same CPU cache line, causing cache invalidations and performance degradation.

---

## Q8: Does False Sharing affect correctness?

```text
No
```

It affects performance only.

---

## Q9: How can False Sharing be prevented?

- Padding
- @Contended
- Better memory layout

---

## Q10: What is the most common concurrency performance mistake?

```text
Creating Too Many Threads
```

---

# Senior Interview Scenario

### Question

Your application runs on:

```text
8 CPU Cores
```

---

You created:

```java
newFixedThreadPool(200)
```

---

and performance became worse.

Why?

### Answer

Because:

```text
200 Threads

↓

Excessive Context Switching

↓

Cache Misses

↓

Scheduler Overhead

↓

Lower Throughput
```

---

For CPU-bound tasks, a pool size near:

```text
8–9 Threads
```

would likely perform better.

---

# Key Takeaways

1. Performance optimization in concurrent systems is about maximizing throughput while minimizing overhead.
2. Correct thread pool sizing is one of the most important optimization decisions.
3. CPU-bound workloads generally perform best with approximately `cores + 1` threads.
4. I/O-bound workloads can benefit from larger thread pools because threads spend time waiting.
5. Excessive thread creation causes memory overhead and context switching.
6. Lock contention is a major scalability bottleneck and should be minimized.
7. Synchronization should be applied only to shared mutable state.
8. Immutable objects eliminate the need for synchronization.
9. Lock Striping improves concurrency by dividing data across multiple locks.
10. False Sharing is a hardware-level performance issue caused by cache line contention.
11. Advanced systems often optimize cache usage and memory layout for maximum throughput.
12. Understanding these concepts is essential for designing high-performance Java applications, tuning ThreadPoolExecutor, optimizing microservices, and succeeding in senior-level Java interviews.