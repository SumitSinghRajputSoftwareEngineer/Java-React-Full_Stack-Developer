
In the previous topic, we learned:

```java
Executor Framework
```

and various thread pools like:

```java
newFixedThreadPool()

newCachedThreadPool()

newSingleThreadExecutor()
```

However, an important interview question is:

> What actually powers these thread pools internally?

The answer is:

```java
ThreadPoolExecutor
```

Most factory methods from `Executors` internally create a `ThreadPoolExecutor`.

---

# What is ThreadPoolExecutor?

## Definition

`ThreadPoolExecutor` is the core implementation of `ExecutorService` that provides complete control over thread pool behavior.

---

## Package

```java
java.util.concurrent
```

---

## Class Hierarchy

```text
Executor
    |
    v

ExecutorService
    |
    v

AbstractExecutorService
    |
    v

ThreadPoolExecutor
```

---

## Why Learn ThreadPoolExecutor?

Because in production systems we often need to configure:

- Thread count
- Queue size
- Timeout values
- Rejection strategy
- Throughput optimization

---

# Constructor

The most commonly used constructor:

```java
ThreadPoolExecutor(
    int corePoolSize,
    int maximumPoolSize,
    long keepAliveTime,
    TimeUnit unit,
    BlockingQueue<Runnable> workQueue,
    RejectedExecutionHandler handler
)
```

---

# Internal Architecture

Understanding this section is extremely important for interviews.

---

## Components

```text
                    New Task
                        |
                        v

               ThreadPoolExecutor
                        |
        --------------------------------
        |              |              |
        v              v              v

 Core Threads      Task Queue     Extra Threads
                        |
                        v

                Rejection Policy
```

---

## Execution Flow

When a new task arrives:

### Step 1

If:

```text
Active Threads < Core Pool Size
```

Create a new thread.

---

### Step 2

If:

```text
Core Threads Full
```

place task into queue.

---

### Step 3

If queue becomes full:

```text
Create Extra Threads
```

up to:

```text
Maximum Pool Size
```

---

### Step 4

If:

```text
Queue Full

AND

Maximum Threads Reached
```

invoke:

```text
Rejection Policy
```

---

# Visualization

```text
Task Arrives
      |
      v

Core Threads Available?
      |
   Yes ----> Create Thread
      |
      No
      |
      v

Queue Available?
      |
   Yes ----> Add To Queue
      |
      No
      |
      v

Can Create More Threads?
      |
   Yes ----> Create Extra Thread
      |
      No
      |
      v

Reject Task
```

---

# Core Pool Size

## Definition

Minimum number of worker threads maintained by the pool.

---

## Example

```java
corePoolSize = 5
```

---

Meaning:

```text
Pool Tries To Keep

5 Threads Alive
```

---

## Behavior

First five tasks:

```text
Create Threads
```

---

Task 1

```text
Thread 1
```

---

Task 2

```text
Thread 2
```

---

Task 5

```text
Thread 5
```

---

# Example

```java
ThreadPoolExecutor executor =
        new ThreadPoolExecutor(
                5,
                10,
                60,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>()
        );
```

---

## Interview Point

Core threads are generally:

```text
Long-Lived Threads
```

---

# Maximum Pool Size

## Definition

Maximum number of threads that can exist in the pool.

---

## Example

```java
maximumPoolSize = 10
```

---

Meaning:

```text
Pool Can Grow Up To

10 Threads
```

---

## When Used?

Only after:

```text
Queue Becomes Full
```

---

## Important Interview Question

### Does ThreadPoolExecutor create threads until maximumPoolSize immediately?

```text
NO
```

---

Order is:

```text
Core Threads

↓

Queue

↓

Maximum Threads
```

---

# Example

```text
corePoolSize = 5

maxPoolSize = 10

queueSize = 100
```

---

Tasks:

```text
Task 1-5
```

Create threads.

---

Tasks:

```text
Task 6-105
```

Go to queue.

---

Tasks:

```text
Task 106+
```

Create extra threads.

---

# Queue Capacity

## Definition

Maximum number of tasks waiting for execution.

---

## Example

```java
new ArrayBlockingQueue<>(100)
```

---

Queue capacity:

```text
100 Tasks
```

---

# Why Queue Is Important

Without queue:

```text
Pool Creates More Threads
```

quickly.

---

With queue:

```text
Tasks Wait
```

before additional threads are created.

---

# Common Queue Types

---

## LinkedBlockingQueue

```java
new LinkedBlockingQueue<>()
```

---

Characteristics:

```text
Unbounded Queue
```

---

Can grow very large.

---

## ArrayBlockingQueue

```java
new ArrayBlockingQueue<>(100)
```

---

Characteristics:

```text
Fixed Capacity
```

---

Most common production choice.

---

## SynchronousQueue

```java
new SynchronousQueue<>()
```

---

Characteristics:

```text
No Storage
```

Task must be immediately handed to a worker.

---

Used by:

```java
newCachedThreadPool()
```

---

# Keep Alive Time

## Definition

Time that extra threads remain alive when idle.

---

## Example

```java
keepAliveTime = 60 seconds
```

---

Meaning:

```text
Non-Core Threads

Idle For 60 Seconds

Destroyed
```

---

# Example

```java
new ThreadPoolExecutor(
        5,
        10,
        60,
        TimeUnit.SECONDS,
        queue
);
```

---

Pool grows:

```text
5 -> 10 Threads
```

during traffic spike.

---

After workload decreases:

```text
Extra Threads Removed
```

after:

```text
60 Seconds
```

---

# Visualization

```text
Normal Load

5 Threads

Peak Load

10 Threads

Traffic Drops

Back To 5 Threads
```

---

# Rejection Policies

## Why Needed?

Suppose:

```text
Core Threads Full

Queue Full

Maximum Threads Reached
```

---

Now a new task arrives.

---

Question:

```text
What Should Happen?
```

---

Answer:

```text
Rejection Policy
```

decides.

---

# AbortPolicy

## Definition

Default rejection policy.

---

## Behavior

Throws:

```java
RejectedExecutionException
```

---

## Example

```java
new ThreadPoolExecutor.AbortPolicy()
```

---

## Visualization

```text
Task Rejected

Exception Thrown
```

---

## Best For

Critical systems where task loss is unacceptable.

---

# CallerRunsPolicy

## Definition

Submitting thread executes the task.

---

## Example

```java
new ThreadPoolExecutor
        .CallerRunsPolicy()
```

---

## Visualization

```text
Pool Busy

Caller Executes Task
```

---

## Benefit

Provides:

```text
Natural Back Pressure
```

---

### Example

```text
Main Thread Submits Task

Pool Full

Main Thread Executes Task
```

---

Submission rate slows down automatically.

---

# DiscardPolicy

## Definition

Silently discards task.

---

## Example

```java
new ThreadPoolExecutor
        .DiscardPolicy()
```

---

## Behavior

```text
Task Lost

No Exception
```

---

## Risk

Dangerous if tasks are important.

---

# DiscardOldestPolicy

## Definition

Removes oldest queued task and inserts new task.

---

## Example

```java
new ThreadPoolExecutor
        .DiscardOldestPolicy()
```

---

## Visualization

Queue:

```text
Task1
Task2
Task3
```

---

New Task Arrives

```text
Task1 Removed

New Task Added
```

---

## Use Cases

When newer tasks are more valuable than older tasks.

---

# Rejection Policy Comparison

| Policy | Behavior |
|----------|----------|
| AbortPolicy | Throws Exception |
| CallerRunsPolicy | Caller Executes Task |
| DiscardPolicy | Drops Task |
| DiscardOldestPolicy | Removes Oldest Queued Task |

---

# Custom Rejection Handler

Sometimes built-in policies are not sufficient.

---

## Interface

```java
RejectedExecutionHandler
```

---

## Example

```java
class CustomHandler
        implements RejectedExecutionHandler {

    @Override
    public void rejectedExecution(
            Runnable r,
            ThreadPoolExecutor executor) {

        System.out.println(
                "Task Rejected: " + r
        );
    }
}
```

---

## Usage

```java
ThreadPoolExecutor executor =
        new ThreadPoolExecutor(
                5,
                10,
                60,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(100),
                new CustomHandler()
        );
```

---

## Real-World Uses

- Logging rejected tasks
- Alert generation
- Metrics collection
- Retry mechanisms
- Dead-letter queues

---

# Production Tuning

This is one of the most frequently asked senior-level interview topics.

---

# CPU-Bound Tasks

Examples:

```text
Encryption

Compression

Mathematical Computation

Image Processing
```

---

## Recommendation

```java
Pool Size ≈ Number Of CPU Cores
```

---

Formula:

```text
Threads = CPU Cores + 1
```

---

Example

```text
8 Core Machine

Pool Size = 9
```

---

# I/O-Bound Tasks

Examples:

```text
Database Calls

REST API Calls

File Operations

Network Calls
```

---

Threads spend significant time waiting.

---

## Recommendation

Use larger pool.

---

Formula (Approximation)

```text
Threads =

CPU Cores *
(1 + Wait Time / Compute Time)
```

---

Example

```text
8 Cores

Wait Time = 900ms

Compute Time = 100ms

Pool Size ≈ 80
```

---

# Queue Sizing

Small Queue:

```text
More Threads
Less Waiting
```

---

Large Queue:

```text
Fewer Threads
More Waiting
```

---

Balance is required.

---

# Monitoring Metrics

Always monitor:

---

## Active Threads

```java
executor.getActiveCount()
```

---

## Pool Size

```java
executor.getPoolSize()
```

---

## Queue Size

```java
executor.getQueue().size()
```

---

## Completed Tasks

```java
executor.getCompletedTaskCount()
```

---

# Common Production Configuration

```java
ThreadPoolExecutor executor =
    new ThreadPoolExecutor(
        10,
        50,
        60,
        TimeUnit.SECONDS,
        new ArrayBlockingQueue<>(500),
        new ThreadPoolExecutor
                .CallerRunsPolicy()
    );
```

---

# Internal Working Example

Configuration:

```text
Core Pool = 2

Max Pool = 4

Queue = 2
```

---

Tasks Arrive:

```text
Task1 -> Thread1

Task2 -> Thread2

Task3 -> Queue

Task4 -> Queue

Task5 -> Thread3

Task6 -> Thread4

Task7 -> Reject
```

---

This scenario is a very common interview question.

---

# Executors Factory Methods Internally

---

## newFixedThreadPool(5)

Equivalent to:

```java
new ThreadPoolExecutor(
        5,
        5,
        0L,
        TimeUnit.MILLISECONDS,
        new LinkedBlockingQueue<>()
);
```

---

## newCachedThreadPool()

Equivalent to:

```java
new ThreadPoolExecutor(
        0,
        Integer.MAX_VALUE,
        60L,
        TimeUnit.SECONDS,
        new SynchronousQueue<>()
);
```

---

## newSingleThreadExecutor()

Equivalent to:

```java
new ThreadPoolExecutor(
        1,
        1,
        0L,
        TimeUnit.MILLISECONDS,
        new LinkedBlockingQueue<>()
);
```

---

# Common Interview Questions

## Q1: What is ThreadPoolExecutor?

The core implementation of `ExecutorService` that manages thread pools and task execution.

---

## Q2: What is the execution order when a task arrives?

```text
Core Threads

↓

Queue

↓

Maximum Threads

↓

Rejection Policy
```

---

## Q3: What is Core Pool Size?

Minimum number of worker threads maintained by the pool.

---

## Q4: What is Maximum Pool Size?

Maximum number of threads allowed in the pool.

---

## Q5: When are extra threads created?

Only after the task queue becomes full.

---

## Q6: What is Keep Alive Time?

The idle timeout for non-core threads.

---

## Q7: What is the default rejection policy?

```java
AbortPolicy
```

---

## Q8: Which rejection policy provides back pressure?

```java
CallerRunsPolicy
```

---

## Q9: Why is CallerRunsPolicy popular in production?

Because it slows task submission instead of dropping tasks.

---

## Q10: What is the most common mistake with ThreadPoolExecutor?

Assuming:

```text
Tasks Create Threads
Until Maximum Pool Size
```

which is incorrect.

Actual order:

```text
Core Threads

↓

Queue

↓

Maximum Threads
```

---

# Key Takeaways

1. `ThreadPoolExecutor` is the core implementation behind most Java thread pools.
2. Task execution follows the order: Core Threads → Queue → Maximum Threads → Rejection Policy.
3. `corePoolSize` determines the baseline number of worker threads.
4. `maximumPoolSize` limits the total number of threads that can exist.
5. Queue capacity directly affects thread creation behavior and throughput.
6. `keepAliveTime` controls how long idle non-core threads remain alive.
7. Rejection policies define what happens when the pool is saturated.
8. `CallerRunsPolicy` is often preferred because it introduces natural back pressure.
9. Custom rejection handlers allow logging, monitoring, retries, and advanced recovery strategies.
10. CPU-bound and I/O-bound workloads require different thread pool sizing strategies.
11. Monitoring active threads, queue size, and completed tasks is essential in production.
12. Understanding `ThreadPoolExecutor` is critical for Spring Boot, Batch Processing, Microservices, and senior-level Java interviews.
13. Most performance tuning in Java concurrency revolves around proper configuration of `ThreadPoolExecutor`.