# 38. Common Interview Questions

This chapter covers the most frequently asked multithreading interview questions ranging from:

```text
Beginner

↓

Intermediate

↓

Senior Java Developer
```

Many interviewers use these questions to evaluate:

- Core Java knowledge
- Concurrency understanding
- Production experience
- System design thinking

---

# Theoretical Questions

---

# Thread vs Process

## Question

What is the difference between a Thread and a Process?

---

## Process

A process is:

```text
An Independent Program
In Execution
```

---

Examples:

- Chrome
- IntelliJ
- Spotify
- JVM

---

Each process has:

- Own Memory
- Own Heap
- Own Resources

---

# Thread

A thread is:

```text
A Lightweight Unit
Of Execution
Inside A Process
```

---

Multiple threads share:

- Heap Memory
- Open Files
- Process Resources

---

# Comparison

| Feature | Process | Thread |
|----------|----------|----------|
| Memory | Separate | Shared |
| Creation Cost | High | Low |
| Communication | IPC Required | Shared Memory |
| Context Switching | Expensive | Cheaper |
| Isolation | High | Low |

---

## Interview Answer

```text
A Process is an independent execution unit with its own memory space, whereas a Thread is a lightweight execution unit inside a process that shares memory and resources with other threads.
```

---

# Runnable vs Callable

## Question

What is the difference between Runnable and Callable?

---

# Runnable

Introduced in:

```java
java.lang
```

---

Method

```java
void run()
```

---

Characteristics:

- No return value
- Cannot throw checked exceptions

---

Example

```java
Runnable r =
        () -> System.out.println("Task");
```

---

# Callable

Introduced in:

```java
java.util.concurrent
```

---

Method

```java
V call()
```

---

Characteristics:

- Returns value
- Can throw checked exceptions

---

Example

```java
Callable<String> c =
        () -> "Hello";
```

---

# Comparison

| Feature | Runnable | Callable |
|----------|----------|----------|
| Return Value | No | Yes |
| Checked Exception | No | Yes |
| Method | run() | call() |
| Introduced | Java 1.0 | Java 5 |

---

## Interview Answer

```text
Runnable is used when no result is needed. Callable is used when a task must return a value or throw checked exceptions.
```

---

# start() vs run()

## Question

Difference between start() and run()?

---

# start()

```java
thread.start();
```

---

Behavior:

```text
Creates New Thread

↓

Invokes run()
```

---

# run()

```java
thread.run();
```

---

Behavior:

```text
Normal Method Call

↓

No New Thread
```

---

# Example

```java
Thread t =
        new Thread(task);

t.run();
```

---

Executes in:

```text
Main Thread
```

---

# Example

```java
t.start();
```

---

Executes in:

```text
New Thread
```

---

## Interview Answer

```text
start() creates a new thread and invokes run() internally, whereas calling run() directly executes the method in the current thread.
```

---

# sleep() vs wait()

## Question

Difference between sleep() and wait()?

---

# sleep()

Class:

```java
Thread
```

---

Example

```java
Thread.sleep(1000);
```

---

Characteristics:

- Pauses thread
- Does NOT release lock

---

# wait()

Class:

```java
Object
```

---

Example

```java
lock.wait();
```

---

Characteristics:

- Releases monitor lock
- Used for thread communication

---

# Comparison

| Feature | sleep() | wait() |
|----------|----------|----------|
| Class | Thread | Object |
| Releases Lock | No | Yes |
| Requires Synchronization | No | Yes |
| Purpose | Delay | Communication |

---

## Interview Answer

```text
sleep() pauses execution without releasing locks, whereas wait() releases the monitor lock and allows inter-thread communication.
```

---

# wait() vs notify()

## Question

Difference between wait() and notify()?

---

# wait()

```java
wait();
```

---

Behavior:

```text
Current Thread

↓

Moves To Waiting State
```

---

# notify()

```java
notify();
```

---

Behavior:

```text
Wake Up

One Waiting Thread
```

---

# Flow

```text
Thread A

↓

wait()

↓

WAITING
```

---

```text
Thread B

↓

notify()

↓

Thread A Resumes
```

---

## Interview Answer

```text
wait() suspends the current thread, while notify() wakes up one waiting thread on the same monitor.
```

---

# notify() vs notifyAll()

## Question

Difference between notify() and notifyAll()?

---

# notify()

```java
notify();
```

---

Wakes:

```text
One Waiting Thread
```

---

# notifyAll()

```java
notifyAll();
```

---

Wakes:

```text
All Waiting Threads
```

---

# Comparison

| Feature | notify() | notifyAll() |
|----------|-----------|-------------|
| Wakes Threads | One | All |
| Performance | Better | More Expensive |
| Risk Of Missed Thread | Higher | Lower |

---

## Interview Answer

```text
notify() wakes one waiting thread, while notifyAll() wakes all waiting threads. notifyAll() is generally safer in complex systems.
```

---

# synchronized vs Lock

## Question

Difference between synchronized and Lock?

---

# synchronized

Keyword.

---

Example

```java
synchronized(lock) {

}
```

---

Features:

- Automatic lock release
- Simpler syntax

---

# Lock

Interface.

---

Example

```java
lock.lock();

try {

} finally {

    lock.unlock();
}
```

---

Features:

- tryLock()
- timeout support
- interruptible locking
- fairness

---

# Comparison

| Feature | synchronized | Lock |
|-----------|-----------|-----------|
| Type | Keyword | Interface |
| Auto Release | Yes | No |
| tryLock() | No | Yes |
| Timeout Support | No | Yes |
| Interruptible | No | Yes |

---

## Interview Answer

```text
synchronized is simpler and automatically releases locks, while Lock provides advanced features such as tryLock(), timeout, fairness, and interruptible locking.
```

---

# synchronized vs volatile

## Question

Difference between synchronized and volatile?

---

# volatile

Provides:

```text
Visibility
```

---

Does NOT provide:

```text
Atomicity
```

---

Example

```java
volatile boolean running;
```

---

# synchronized

Provides:

```text
Visibility

+

Atomicity

+

Mutual Exclusion
```

---

# Comparison

| Feature | volatile | synchronized |
|----------|-----------|-------------|
| Visibility | Yes | Yes |
| Atomicity | No | Yes |
| Mutual Exclusion | No | Yes |
| Locking | No | Yes |

---

## Interview Answer

```text
volatile guarantees visibility of changes across threads, while synchronized provides visibility, atomicity, and mutual exclusion.
```

---

# Future vs CompletableFuture

## Question

Difference between Future and CompletableFuture?

---

# Future

Introduced in:

```java
Java 5
```

---

Capabilities:

- Submit task
- Get result

---

Limitations:

- Blocking get()
- No chaining

---

# CompletableFuture

Introduced in:

```java
Java 8
```

---

Capabilities:

- Async execution
- Chaining
- Combination
- Exception handling

---

Example

```java
future
    .thenApply(...)
    .thenCompose(...);
```

---

# Comparison

| Feature | Future | CompletableFuture |
|-----------|-----------|-----------|
| Async Chaining | No | Yes |
| Exception Handling | Limited | Rich |
| Combination | No | Yes |
| Non-Blocking APIs | No | Yes |

---

## Interview Answer

```text
Future provides asynchronous result retrieval, whereas CompletableFuture supports asynchronous workflows, chaining, composition, and advanced exception handling.
```

---

# HashMap vs ConcurrentHashMap

## Question

Difference between HashMap and ConcurrentHashMap?

---

# HashMap

```java
HashMap
```

---

Characteristics:

- Not thread-safe
- Fast in single-threaded environments

---

# ConcurrentHashMap

```java
ConcurrentHashMap
```

---

Characteristics:

- Thread-safe
- High concurrency
- Fine-grained locking

---

# Comparison

| Feature | HashMap | ConcurrentHashMap |
|----------|----------|----------|
| Thread Safe | No | Yes |
| Concurrent Access | Unsafe | Safe |
| Performance Under Concurrency | Poor | Good |

---

## Interview Answer

```text
HashMap is not thread-safe, whereas ConcurrentHashMap supports concurrent access with better scalability than synchronized maps.
```

---

# CountDownLatch vs CyclicBarrier

## Question

Difference between CountDownLatch and CyclicBarrier?

---

# CountDownLatch

```text
One-Time Use
```

---

Counter decreases to:

```text
Zero
```

---

Then releases waiting threads.

---

# CyclicBarrier

```text
Reusable
```

---

Waits until:

```text
All Threads Reach Barrier
```

---

Then releases them.

---

# Comparison

| Feature | CountDownLatch | CyclicBarrier |
|-----------|-----------|-----------|
| Reusable | No | Yes |
| Counter Direction | Down | Barrier Count |
| Common Usage | Waiting For Tasks | Synchronizing Phases |

---

## Interview Answer

```text
CountDownLatch is a one-time synchronization aid that waits for tasks to complete, whereas CyclicBarrier synchronizes a group of threads and can be reused.
```

---

# ExecutorService vs Thread

## Question

Why use ExecutorService instead of Thread?

---

# Thread

```java
new Thread(task).start();
```

---

Creates:

```text
New Thread
Every Time
```

---

# ExecutorService

```java
Executors.newFixedThreadPool()
```

---

Provides:

- Thread reuse
- Queue management
- Lifecycle control

---

# Comparison

| Feature | Thread | ExecutorService |
|----------|----------|----------|
| Thread Reuse | No | Yes |
| Task Queue | No | Yes |
| Scalability | Low | High |
| Lifecycle Management | Manual | Built-in |

---

## Interview Answer

```text
ExecutorService is preferred because it reuses threads, manages task execution efficiently, and provides better scalability than creating threads manually.
```

---

# Virtual Thread vs Platform Thread

## Question

Difference between Virtual Threads and Platform Threads?

---

# Platform Thread

Traditional Java thread.

---

Mapping:

```text
1 Java Thread

↓

1 OS Thread
```

---

Characteristics:

- Heavyweight
- Expensive creation
- Limited scalability

---

# Virtual Thread

Introduced in:

```text
Java 21
```

---

Mapping:

```text
Many Virtual Threads

↓

Few Platform Threads
```

---

Characteristics:

- Lightweight
- JVM managed
- Massive scalability

---

# Comparison

| Feature | Platform Thread | Virtual Thread |
|-----------|-----------|-----------|
| Managed By | OS | JVM |
| Creation Cost | High | Very Low |
| Scalability | Thousands | Millions |
| Best For | CPU Tasks | I/O Tasks |

---

## Interview Answer

```text
Platform threads are OS-backed threads with higher resource costs, while virtual threads are lightweight JVM-managed threads designed for highly concurrent workloads.
```

---

# Rapid-Fire Interview Answers

| Question | One-Line Answer |
|-----------|-----------|
| Thread vs Process | Process has separate memory, threads share memory |
| Runnable vs Callable | Callable returns value, Runnable does not |
| start() vs run() | start() creates new thread, run() does not |
| sleep() vs wait() | sleep doesn't release lock, wait does |
| notify() vs notifyAll() | One thread vs all waiting threads |
| synchronized vs Lock | Lock provides advanced features |
| synchronized vs volatile | volatile = visibility, synchronized = visibility + atomicity |
| Future vs CompletableFuture | CompletableFuture supports chaining and async pipelines |
| HashMap vs ConcurrentHashMap | ConcurrentHashMap is thread-safe |
| CountDownLatch vs CyclicBarrier | One-time vs reusable |
| ExecutorService vs Thread | Thread reuse vs manual creation |
| Virtual Thread vs Platform Thread | Lightweight JVM thread vs OS-backed thread |

---

# Senior-Level Interview Tip

For most concurrency interview questions:

```text
Do Not Only Explain

↓

Explain

When To Use

Why To Use

Trade-Offs

Production Impact
```

Example:

Instead of saying:

```text
ConcurrentHashMap is thread-safe.
```

Say:

```text
ConcurrentHashMap is thread-safe and uses finer-grained synchronization, making it more scalable than Hashtable or synchronized HashMap in high-concurrency systems.
```

This is typically what differentiates a:

```text
Mid-Level Developer

from

Senior Developer
```

---

# Key Takeaways

1. These questions form the foundation of Java multithreading interviews.
2. Understanding the "why" behind each concept is more important than memorizing definitions.
3. Interviewers often ask comparison-based questions because they reveal conceptual understanding.
4. Always explain trade-offs, use cases, and production implications.
5. Senior-level interviews focus heavily on Executor Framework, CompletableFuture, Concurrent Collections, Virtual Threads, and synchronization mechanisms.
6. Mastering these questions will cover a significant portion of Java concurrency interview discussions.