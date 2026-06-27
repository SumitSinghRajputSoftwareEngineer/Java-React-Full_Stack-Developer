

> Goal: Learn Multithreading from absolute beginner level to advanced production-level concepts and interview preparation.

---

# 1. [[Introduction to Multithreading]]

## What is a Process?
- Definition of Process
- Process Lifecycle
- Process Memory Structure
- Context Switching
- Examples

## What is a Thread?
- Definition of Thread
- Thread vs Process
- Thread Lifecycle
- Thread Memory Structure
- Advantages of Threads

## Why Multithreading?
- Improved Performance
- Better CPU Utilization
- Concurrent Task Execution
- Responsive Applications
- Real-world Examples

## Single Threaded vs Multithreaded Applications

## Concurrency vs Parallelism

## Synchronous vs Asynchronous Execution

## CPU Bound vs I/O Bound Tasks

---

# 2. [[Java Thread Fundamentals]]

## Thread Class

### Important Methods
- start()
- run()
- sleep()
- join()
- yield()
- interrupt()
- isInterrupted()
- interrupted()
- currentThread()
- getName()
- setName()
- getId()
- setPriority()
- getPriority()
- isAlive()

## Runnable Interface

## Callable Interface

## Future Interface

## FutureTask

---

# 3. [[Ways to Create Threads]]

## Extending Thread Class

## Implementing Runnable Interface

## Implementing Callable Interface

## Using Lambda Expressions

## Using Executor Framework

### Comparison
- Thread vs Runnable
- Runnable vs Callable
- Direct Thread Creation vs Executor Framework

---

# 4. [[Thread Lifecycle]]

## NEW State

## RUNNABLE State

## RUNNING State

## BLOCKED State

## WAITING State

## TIMED_WAITING State

## TERMINATED State

## Complete State Transition Diagram

---

# 5. [[Thread Scheduling]]

## Thread Scheduler

## Time Slicing

## Preemptive Scheduling

## Cooperative Scheduling

## Thread Priorities

### Priority Constants
- MIN_PRIORITY
- NORM_PRIORITY
- MAX_PRIORITY

## Limitations of Priority

---

# 6. [[Thread Coordination]]

## sleep()

## join()

### join()
### join(long millis)

## yield()

## interrupt()

### Interrupt Mechanism

### InterruptedException

### Best Practices

---

# 7. [[Thread Safety Fundamentals]]

## What is Thread Safety?

## Race Condition

## Critical Section

## Shared Resources

## Atomicity

## Visibility

## Ordering

## Happens-Before Relationship

## Data Consistency Problems

---

# 8. [[Synchronization]]

## Why Synchronization?

## synchronized Keyword

### Synchronized Method

### Synchronized Block

### Static Synchronization

### Class Level Lock

### Object Level Lock

## Monitor Lock

## Intrinsic Lock

## Reentrant Nature of synchronized

## Limitations of synchronized

---

# 9. [[Inter-Thread Communication]]

## wait()

## notify()

## notifyAll()

## Producer Consumer Problem

## Communication Flow

## Common Mistakes

### IllegalMonitorStateException

### Lost Notifications

### Spurious Wakeups

---

# 10. [[Deadlock]]

## What is Deadlock?

## Conditions for Deadlock

### Mutual Exclusion

### Hold and Wait

### No Preemption

### Circular Wait

## Deadlock Example

## Deadlock Detection

## Deadlock Prevention

## Deadlock Avoidance

---

# 11. [[Livelock]]

## What is Livelock?

## Livelock Example

## Difference Between Deadlock and Livelock

---

# 12. [[Starvation]]

## What is Starvation?

## Causes of Starvation

## Prevention Techniques

---

# 13. [[Volatile Keyword]]

## What is volatile?

## Visibility Problem

## CPU Cache and Main Memory

## Memory Consistency

## volatile vs synchronized

## When to Use volatile

## When Not to Use volatile

---

# 14. [[Atomic Variables]]

## Why Atomic Classes?

## CAS (Compare And Swap)

## AtomicInteger

## AtomicLong

## AtomicBoolean

## AtomicReference

## AtomicIntegerArray

## LongAdder

## LongAccumulator

---

# 15. [[Locks Framework]]

## Introduction to Explicit Locks

## Lock Interface

### lock()

### unlock()

### tryLock()

### tryLock(timeout)

### lockInterruptibly()

## ReentrantLock

### Fair Lock

### Non-Fair Lock

## ReadWriteLock

## ReentrantReadWriteLock

## StampedLock

## Lock vs synchronized

---

# 16. [[Condition Interface]]

## What is Condition?

## await()

## signal()

## signalAll()

## Condition vs wait/notify

---

# 17. [[Executor Framework]]

## Why Executor Framework?

## Problems with Manual Thread Creation

## Executor Interface

## ExecutorService Interface

## ScheduledExecutorService

## Thread Pooling

### Fixed Thread Pool

### Cached Thread Pool

### Single Thread Executor

### Scheduled Thread Pool

### Work Stealing Pool

## Lifecycle Methods

### shutdown()

### shutdownNow()

### awaitTermination()

---

# 18. [[ThreadPoolExecutor]]

## Internal Architecture

## Core Pool Size

## Maximum Pool Size

## Queue Capacity

## Keep Alive Time

## Rejection Policies

### AbortPolicy

### CallerRunsPolicy

### DiscardPolicy

### DiscardOldestPolicy

## Custom Rejection Handler

## Production Tuning

---

# 19. [[Callable, Future and FutureTask]]

## Callable

## Future

### cancel()

### get()

### isDone()

### isCancelled()

## FutureTask

## Limitations of Future

---

# 20. [[CompletableFuture]]

## Why CompletableFuture?

## Asynchronous Programming

## Creating CompletableFuture

### supplyAsync()

### runAsync()

## Chaining Operations

### thenApply()

### thenAccept()

### thenRun()

### thenCompose()

### thenCombine()

## Exception Handling

### exceptionally()

### handle()

### whenComplete()

## Combining Multiple Futures

### allOf()

### anyOf()

## CompletableFuture vs Future

---

# 21. [[Fork Join Framework]]

## Introduction

## Divide and Conquer Approach

## ForkJoinPool

## RecursiveTask

## RecursiveAction

## Work Stealing Algorithm

## Parallel Processing

---

# 22. [[Concurrent Collections]]

## Why Concurrent Collections?

## Problems with Normal Collections

### ConcurrentModificationException

---

## ConcurrentHashMap

### Internal Working

### Segment Locking (Java 7)

### Bucket Level Locking (Java 8+)

### computeIfAbsent()

### putIfAbsent()

### replace()

---

## CopyOnWriteArrayList

## CopyOnWriteArraySet

## ConcurrentSkipListMap

## ConcurrentSkipListSet

## ConcurrentLinkedQueue

## ConcurrentLinkedDeque

## BlockingQueue

---

# 23. [[Blocking Queues]]

## ArrayBlockingQueue

## LinkedBlockingQueue

## PriorityBlockingQueue

## DelayQueue

## SynchronousQueue

## LinkedTransferQueue

## Producer Consumer Using BlockingQueue

---

# 24. [[Semaphore]]

## What is Semaphore?

## Permits

## acquire()

## release()

## Binary Semaphore

## Counting Semaphore

## Real-world Examples

---

# 25. [[CountDownLatch]]

## What is CountDownLatch?

## countDown()

## await()

## Use Cases

---

# 26. [[CyclicBarrier]]

## What is CyclicBarrier?

## await()

## Barrier Action

## Reusable Nature

## CyclicBarrier vs CountDownLatch

---

# 27. [[Phaser]]

## What is Phaser?

## Dynamic Registration

## Multi-phase Synchronization

## Phaser vs CyclicBarrier

---

# 28. [[Exchanger]]

## What is Exchanger?

## Exchange Data Between Threads

## Real-world Use Cases

---

# 29. [[ThreadLocal]]

## What is ThreadLocal?

## Thread Confinement

## ThreadLocal Methods

### set()

### get()

### remove()

## InheritableThreadLocal

## Memory Leak Issues

## Best Practices

---

# 30. [[Java Memory Model (JMM)]]

## Why JMM?

## Main Memory

## Working Memory

## Visibility

## Reordering

## Happens-Before Rules

## Synchronization Guarantees

## volatile in JMM

## synchronized in JMM

## Lock in JMM

---

# 31. [[Context Switching]]

## What is Context Switching?

## Cost of Context Switching

## Performance Impact

## How to Reduce Context Switching

---

# 32. [[Thread Dump Analysis]]

## What is Thread Dump?

## Generating Thread Dumps

### jstack

### jcmd

### VisualVM

### JConsole

## Reading Thread Dumps

## Detecting Deadlocks

---

# 33. [[Performance Optimization]]

## Choosing Correct Pool Size

## CPU Bound Thread Pools

## I/O Bound Thread Pools

## Avoiding Excessive Thread Creation

## Reducing Lock Contention

## Minimizing Synchronization

## Lock Striping

## False Sharing

---

# 34. [[Virtual Threads (Java 21+)]]

## Introduction

## Platform Threads

## Virtual Threads

## Project Loom

## Creating Virtual Threads

## Executor with Virtual Threads

## Advantages

## Limitations

## Virtual Threads vs Platform Threads

## Best Use Cases

---

# 35. [[Structured Concurrency (Java 21+)]]

## What is Structured Concurrency?

## TaskScope

## Forking Tasks

## Joining Tasks

## Error Handling

## Cancellation

---

# 36. [[Real-World Multithreading Design Patterns]]

## Producer Consumer Pattern

## Thread Pool Pattern

## Future Pattern

## Worker Thread Pattern

## Guarded Suspension Pattern

## Balking Pattern

## Read Write Lock Pattern

## Master Worker Pattern

## Pipeline Pattern

---

# 37. [[Multithreading Best Practices]]

## Immutable Objects

## Minimize Shared State

## Prefer Concurrent Collections

## Prefer Executor Framework

## Avoid Manual Thread Management

## Avoid Nested Locks

## Always Release Locks

## Use Timeouts

## Handle Interruptions Properly

## Thread Naming Conventions

---

# 38. [[Common Interview Questions]]

## Theoretical Questions

### Thread vs Process

### Runnable vs Callable

### start() vs run()

### sleep() vs wait()

### wait() vs notify()

### notify() vs notifyAll()

### synchronized vs Lock

### synchronized vs volatile

### Future vs CompletableFuture

### HashMap vs ConcurrentHashMap

### CountDownLatch vs CyclicBarrier

### ExecutorService vs Thread

### Virtual Thread vs Platform Thread

---

# 39. [[Advanced Interview Scenarios]]

## Implement Producer Consumer

## Implement Custom Thread Pool

## Design Rate Limiter

## Design Web Crawler

## Design Notification Service

## Design Batch Processing System

## Design Parallel File Processor

## Design Concurrent Cache

## Design High Throughput Logging System

---

# 40. Important Classes Summary

## java.lang
- Thread
- Runnable

## java.util.concurrent
- Executor
- ExecutorService
- ScheduledExecutorService
- Future
- CompletableFuture
- ThreadPoolExecutor
- ForkJoinPool
- CountDownLatch
- CyclicBarrier
- Phaser
- Semaphore
- Exchanger
- ThreadLocal
- ConcurrentHashMap
- BlockingQueue

## java.util.concurrent.atomic
- AtomicInteger
- AtomicLong
- AtomicReference
- LongAdder
- LongAccumulator

## java.util.concurrent.locks
- Lock
- ReentrantLock
- ReadWriteLock
- ReentrantReadWriteLock
- StampedLock
- Condition