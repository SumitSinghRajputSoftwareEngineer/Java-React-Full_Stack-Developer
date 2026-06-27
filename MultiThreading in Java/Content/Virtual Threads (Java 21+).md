
One of the biggest changes in Java concurrency since the introduction of the Executor Framework is:

```text
Virtual Threads
```

introduced as a final feature in:

```text
Java 21
```

through:

```text
Project Loom
```

Virtual Threads solve one of the biggest scalability problems in traditional Java applications:

```text
Thread Per Request Model
```

which becomes expensive when handling thousands of concurrent requests.

---

# Introduction

Traditionally, Java uses:

```text
Platform Threads
```

which are mapped directly to operating system threads.

---

Example:

```java
new Thread(task).start();
```

creates:

```text
1 Java Thread

↓

1 OS Thread
```

---

OS threads are expensive because they require:

- Memory
- Context Switching
- Scheduling
- Kernel Resources

---

As concurrency increases:

```text
10,000 Requests

↓

10,000 Threads

↓

Huge Resource Consumption
```

---

To solve this problem, Java introduced:

```text
Virtual Threads
```

---

# Why Were Virtual Threads Needed?

Imagine a web application.

---

Request Flow

```text
Request

↓

Database Call

↓

Wait

↓

Response
```

---

During waiting:

```text
Thread Does Nothing
```

---

Yet the OS thread remains occupied.

---

With:

```text
100,000 Concurrent Requests
```

---

Traditional threads become impractical.

---

Virtual Threads allow:

```text
Millions Of Concurrent Tasks
```

using a small number of OS threads.

---

# Platform Threads

## What are Platform Threads?

Traditional Java threads.

---

Example

```java
Thread t =
        new Thread(task);
```

---

Internally:

```text
1 Java Thread

↓

1 Native OS Thread
```

---

# Visualization

```text
Java Thread 1

↓

OS Thread 1
```

---

```text
Java Thread 2

↓

OS Thread 2
```

---

```text
Java Thread 3

↓

OS Thread 3
```

---

# Characteristics

- Heavyweight
- Expensive creation
- Large memory usage
- OS managed
- Context switching overhead

---

# Typical Limits

Many servers struggle beyond:

```text
Few Thousand Threads
```

---

because each thread consumes:

```text
~1 MB Stack
```

by default.

---

# Virtual Threads

## What are Virtual Threads?

Virtual Threads are:

```text
Lightweight Threads
Managed By JVM
```

---

Unlike platform threads:

```text
1 Virtual Thread

≠

1 OS Thread
```

---

Instead:

```text
Many Virtual Threads

↓

Few Platform Threads
```

---

# Visualization

```text
Virtual Thread 1

Virtual Thread 2

Virtual Thread 3

Virtual Thread 4

Virtual Thread 5

↓

Platform Thread 1
```

---

```text
Virtual Thread 6

Virtual Thread 7

Virtual Thread 8

↓

Platform Thread 2
```

---

Thousands of virtual threads can share a small number of OS threads.

---

# Project Loom

## What is Project Loom?

Project Loom is the OpenJDK project that introduced:

- Virtual Threads
- Structured Concurrency
- Scoped Values

---

Main Goal:

```text
Massive Concurrency
With Simpler Programming
```

---

Before Loom:

```text
High Concurrency

↓

CompletableFuture

↓

Reactive Programming

↓

Complex Code
```

---

After Loom:

```text
High Concurrency

↓

Normal Sequential Code
```

---

Much simpler.

---

# Platform Thread Architecture

Traditional model:

```text
Request 1

↓

Platform Thread 1
```

---

```text
Request 2

↓

Platform Thread 2
```

---

```text
Request 3

↓

Platform Thread 3
```

---

Need many OS threads.

---

# Virtual Thread Architecture

```text
Request 1

↓

Virtual Thread 1
```

---

```text
Request 2

↓

Virtual Thread 2
```

---

```text
Request 3

↓

Virtual Thread 3
```

---

Thousands of virtual threads:

```text
Share

A Small Pool

Of Platform Threads
```

---

# Creating Virtual Threads

Java 21 provides simple APIs.

---

# Method 1

## Thread.ofVirtual()

```java
Thread vt =
        Thread.ofVirtual()
              .start(() -> {

                  System.out.println(
                          "Virtual Thread"
                  );

              });
```

---

Creates and starts a virtual thread.

---

# Method 2

## startVirtualThread()

```java
Thread.startVirtualThread(() -> {

    System.out.println(
            "Hello Virtual Thread"
    );
});
```

---

Simplest approach.

---

# Example

```java
for(int i=1;i<=10000;i++) {

    Thread.startVirtualThread(() -> {

        System.out.println(
                Thread.currentThread()
        );
    });
}
```

---

Creating:

```text
10,000 Virtual Threads
```

is completely reasonable.

---

# Identifying Virtual Threads

```java
System.out.println(
        Thread.currentThread()
);
```

---

Output

```text
VirtualThread[#21]
```

---

# Executor with Virtual Threads

One of the most useful APIs.

---

## Virtual Thread Executor

```java
ExecutorService executor =
        Executors.newVirtualThreadPerTaskExecutor();
```

---

Every submitted task gets:

```text
New Virtual Thread
```

---

# Example

```java
try(ExecutorService executor =
        Executors.newVirtualThreadPerTaskExecutor()) {

    for(int i=0;i<100000;i++) {

        executor.submit(() -> {

            Thread.sleep(1000);

            return null;
        });
    }
}
```

---

Handling:

```text
100,000 Tasks
```

becomes feasible.

---

# How Virtual Threads Work Internally

Virtual Threads are scheduled by the JVM.

---

Terminology:

```text
Virtual Thread
```

called:

```text
User Mode Thread
```

---

Platform Thread:

```text
Carrier Thread
```

---

# Execution Flow

```text
Virtual Thread

↓

Carrier Thread

↓

CPU
```

---

When virtual thread blocks:

```java
Thread.sleep()
```

---

JVM detaches it.

---

Carrier thread becomes free.

---

Can execute another virtual thread.

---

# Mounting and Unmounting

Important internal concept.

---

When running:

```text
Virtual Thread

Mounted

On Carrier Thread
```

---

When blocked:

```text
Virtual Thread

Unmounted
```

---

Carrier thread reused elsewhere.

---

This is the secret behind scalability.

---

# Example

Traditional Thread

```text
Database Call

↓

OS Thread Blocked
```

---

Virtual Thread

```text
Database Call

↓

Virtual Thread Suspended

↓

Carrier Thread Reused
```

---

Huge improvement.

---

# Advantages

---

# Advantage 1

Massive Scalability

---

Traditional

```text
10,000 Threads

Impossible
```

---

Virtual Threads

```text
100,000+

Practical
```

---

# Advantage 2

Low Memory Usage

---

Platform Thread

```text
~1 MB Stack
```

---

Virtual Thread

```text
Much Smaller
```

---

Stack grows dynamically.

---

# Advantage 3

Simple Programming Model

---

Use normal:

```java
try

catch

loops

blocking calls
```

---

No reactive complexity.

---

# Advantage 4

Reduced Context Switching

---

Fewer OS threads.

---

Less kernel scheduling overhead.

---

# Advantage 5

Better Resource Utilization

---

Carrier threads remain productive.

---

# Limitations

Virtual Threads are powerful but not magic.

---

# Limitation 1

Not Faster For CPU-Bound Work

---

Example

```java
Prime Number Calculation
```

---

Still limited by:

```text
CPU Cores
```

---

Virtual Threads improve:

```text
Concurrency
```

not raw CPU speed.

---

# Limitation 2

Pinned Threads

Important interview topic.

---

Certain operations can prevent:

```text
Unmounting
```

---

Example

```java
synchronized
```

during blocking operations.

---

Carrier thread becomes:

```text
Pinned
```

---

Scalability decreases.

---

# Example

```java
synchronized(lock) {

    Thread.sleep(10000);
}
```

---

Avoid such patterns.

---

# Limitation 3

Not Ideal For Heavy CPU Computation

Use:

```java
ForkJoinPool
```

or

```java
Fixed Thread Pool
```

for CPU-intensive workloads.

---

# Limitation 4

Third-Party Library Behavior

Some older libraries may not be optimized for virtual threads.

---

Always benchmark.

---

# Virtual Threads vs Platform Threads

| Feature | Platform Thread | Virtual Thread |
|----------|----------|----------|
| Introduced | Java 1.0 | Java 21 |
| Managed By | OS | JVM |
| Creation Cost | High | Very Low |
| Memory Usage | High | Low |
| Context Switching | OS-Level | JVM-Level |
| Scalability | Thousands | Millions |
| Blocking Operations | Expensive | Cheap |
| Suitable For I/O | Good | Excellent |
| Suitable For CPU Work | Good | Good |

---

# Virtual Threads vs Reactive Programming

A common interview question.

---

# Reactive Approach

```java
Mono

Flux

CompletableFuture
```

---

Advantages:

```text
High Scalability
```

---

Disadvantages:

```text
Complex Code
```

---

# Virtual Threads

Advantages:

```text
High Scalability

Simple Code
```

---

Example

Reactive

```java
service()
    .thenCompose(...)
    .thenApply(...)
```

---

Virtual Thread

```java
result = service();

process(result);
```

---

Much easier to read.

---

# Best Use Cases

---

# Use Case 1

Web Applications

---

Example

```text
Spring Boot APIs
```

---

Thousands of requests.

---

# Use Case 2

Database Applications

---

Many blocking queries.

---

Virtual threads shine here.

---

# Use Case 3

Microservices

---

REST calls.

---

External service communication.

---

# Use Case 4

Message Processing

---

Kafka

RabbitMQ

ActiveMQ

---

High concurrency.

---

# Use Case 5

Batch Processing

---

Thousands of independent tasks.

---

# Use Case 6

File Processing

---

Reading thousands of files concurrently.

---

# When NOT To Use Virtual Threads

---

Avoid for:

```text
Heavy CPU Calculations
```

---

Examples

- Image Rendering
- Scientific Computation
- Encryption
- Machine Learning

---

Use:

```java
ForkJoinPool
```

or

```java
Fixed Thread Pool
```

instead.

---

# Common Interview Questions

## Q1: What are Virtual Threads?

Lightweight JVM-managed threads introduced in Java 21.

---

## Q2: Which project introduced Virtual Threads?

```text
Project Loom
```

---

## Q3: What problem do Virtual Threads solve?

The scalability limitations of platform threads.

---

## Q4: Are Virtual Threads OS threads?

```text
No
```

They are managed by the JVM.

---

## Q5: Can millions of Virtual Threads be created?

```text
Yes
```

depending on available memory and workload.

---

## Q6: What is a Carrier Thread?

A platform thread used to execute virtual threads.

---

## Q7: Are Virtual Threads faster?

```text
Not For CPU Work
```

They improve scalability and concurrency.

---

## Q8: What is Thread Pinning?

When a virtual thread cannot be unmounted from its carrier thread during blocking operations.

---

## Q9: Which Executor creates Virtual Threads?

```java
Executors.newVirtualThreadPerTaskExecutor()
```

---

## Q10: What workloads benefit the most?

```text
I/O Bound Workloads
```

---

# Senior Interview Scenario

### Question

You have:

```text
100,000 Concurrent HTTP Requests
```

---

Would you choose:

```java
FixedThreadPool(100000)
```

or

```java
Virtual Threads
```

---

### Answer

Use:

```java
Virtual Threads
```

because:

```text
100,000 Platform Threads

↓

Huge Memory Usage

↓

Context Switching

↓

Poor Scalability
```

---

Whereas:

```text
100,000 Virtual Threads

↓

Small Number Of Carrier Threads

↓

Better Scalability

↓

Lower Resource Usage
```

---

# Key Takeaways

1. Virtual Threads are lightweight JVM-managed threads introduced in Java 21.
2. They are part of Project Loom.
3. Unlike platform threads, virtual threads are not mapped one-to-one with OS threads.
4. Thousands or even millions of virtual threads can share a small number of carrier threads.
5. Virtual threads are ideal for I/O-bound and blocking workloads.
6. They simplify concurrent programming compared to reactive approaches.
7. `Thread.startVirtualThread()` is the easiest way to create a virtual thread.
8. `Executors.newVirtualThreadPerTaskExecutor()` provides executor support.
9. Virtual threads improve scalability, not CPU performance.
10. Thread pinning can reduce virtual thread efficiency.
11. Virtual threads are transforming how modern Java applications handle concurrency.
12. Understanding Virtual Threads is essential for Java 21+, modern Spring Boot applications, microservices, cloud-native systems, and senior Java developer interviews.