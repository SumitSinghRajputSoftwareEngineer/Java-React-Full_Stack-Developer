
In multithreaded applications, sometimes one thread must wait until several other threads complete their work.

Examples:

- Wait for all microservices to respond
- Wait for all workers to finish processing
- Wait for multiple files to load
- Wait for all test threads to complete
- Wait for application initialization

Without proper coordination, a thread may proceed too early and produce incorrect results.

To solve this problem, Java provides:

```java
CountDownLatch
```

---

# What is CountDownLatch?

## Definition

`CountDownLatch` is a synchronization utility that allows one or more threads to wait until a set of operations performed by other threads completes.

---

## Package

```java
java.util.concurrent.CountDownLatch
```

---

## Simple Definition

Think of it as:

```text
A Countdown Timer
```

When the count reaches:

```text
0
```

waiting threads are released.

---

# Real-World Analogy

Imagine a race.

---

There are:

```text
5 Players
```

and a referee.

---

The referee waits until all players reach the starting position.

---

Each player signals:

```text
Ready
```

---

When all players are ready:

```text
Count = 0
```

---

Race starts.

---

This is exactly how:

```java
CountDownLatch
```

works.

---

# Visualization

```text
Count = 3

Worker 1 Finished

Count = 2

Worker 2 Finished

Count = 1

Worker 3 Finished

Count = 0

↓

Main Thread Continues
```

---

# Why CountDownLatch?

Suppose:

```text
Main Thread
```

depends on:

```text
Thread A

Thread B

Thread C
```

---

Main thread should continue only when:

```text
A Completed

B Completed

C Completed
```

---

Without CountDownLatch

Need:

```java
join()

Manual Tracking

Shared Variables
```

---

Complex and error-prone.

---

# Solution

```java
CountDownLatch
```

---

# Creating CountDownLatch

## Syntax

```java
CountDownLatch latch =
        new CountDownLatch(3);
```

---

Meaning

```text
Initial Count = 3
```

---

Three events must occur before waiting threads proceed.

---

# Internal Workflow

```text
Count = 3

↓

countDown()

↓

Count = 2

↓

countDown()

↓

Count = 1

↓

countDown()

↓

Count = 0

↓

All Waiting Threads Released
```

---

# countDown()

## Definition

Decrements the count by:

```text
1
```

---

## Syntax

```java
latch.countDown();
```

---

# Example

```java
CountDownLatch latch =
        new CountDownLatch(3);

latch.countDown();
```

---

Count

```text
Before = 3

After = 2
```

---

# Important

When count reaches:

```text
0
```

all waiting threads are released.

---

# Example

```java
latch.countDown();
latch.countDown();
latch.countDown();
```

---

Count

```text
3

↓

2

↓

1

↓

0
```

---

Waiting threads continue.

---

# Important Point

Calling:

```java
countDown()
```

after count becomes:

```text
0
```

has no effect.

---

# Example

```java
CountDownLatch latch =
        new CountDownLatch(1);

latch.countDown();

latch.countDown();
```

---

Count remains:

```text
0
```

---

# await()

## Definition

Causes the current thread to wait until the count reaches zero.

---

## Syntax

```java
latch.await();
```

---

# Example

```java
latch.await();
```

---

Behavior

```text
Wait Until Count = 0
```

---

# Visualization

```text
Main Thread

↓

await()

↓

Blocked

↓

Count Reaches 0

↓

Continues
```

---

# Example

```java
CountDownLatch latch =
        new CountDownLatch(3);

latch.await();
```

---

Main thread waits until:

```text
Three countDown()
Calls Occur
```

---

# Timed await()

Wait only for a specific duration.

---

## Syntax

```java
latch.await(
        5,
        TimeUnit.SECONDS
);
```

---

Meaning

```text
Wait Maximum 5 Seconds
```

---

Returns

```java
boolean
```

---

### true

Count reached zero.

---

### false

Timeout occurred.

---

# Complete Example

## Worker Thread

```java
class Worker
        implements Runnable {

    private CountDownLatch latch;

    public Worker(
            CountDownLatch latch) {

        this.latch = latch;
    }

    @Override
    public void run() {

        try {

            Thread.sleep(2000);

            System.out.println(
                    Thread.currentThread()
                          .getName()
                    + " completed"
            );

        } catch(Exception e) {

            e.printStackTrace();

        } finally {

            latch.countDown();
        }
    }
}
```

---

# Main Thread

```java
public static void main(
        String[] args)
        throws Exception {

    CountDownLatch latch =
            new CountDownLatch(3);

    for(int i = 1;
        i <= 3;
        i++) {

        new Thread(
                new Worker(latch)
        ).start();
    }

    System.out.println(
            "Waiting..."
    );

    latch.await();

    System.out.println(
            "All Workers Completed"
    );
}
```

---

# Possible Output

```text
Waiting...

Thread-1 completed

Thread-2 completed

Thread-3 completed

All Workers Completed
```

---

# Execution Flow

```text
Main Thread

↓

await()

↓

Waiting

↓

Worker 1 countDown()

↓

Worker 2 countDown()

↓

Worker 3 countDown()

↓

Count = 0

↓

Main Thread Resumes
```

---

# Use Cases

---

# Use Case 1: Waiting For Worker Threads

Suppose:

```text
10 Worker Threads
```

process data.

---

Main thread waits until:

```text
All Workers Finish
```

---

# Example

```java
CountDownLatch latch =
        new CountDownLatch(10);
```

---

Each worker:

```java
latch.countDown();
```

---

Main thread:

```java
latch.await();
```

---

# Use Case 2: Application Startup

Application depends on:

```text
Database

Cache

Message Queue
```

---

Application should start only after:

```text
All Services Ready
```

---

Count

```text
3
```

---

Each service initialization:

```java
countDown()
```

---

Main thread:

```java
await()
```

---

# Use Case 3: Microservice Aggregation

Suppose API needs responses from:

```text
User Service

Order Service

Payment Service
```

---

Main thread waits for all responses.

---

Each service completion:

```java
countDown()
```

---

Final processing begins after:

```text
Count = 0
```

---

# Use Case 4: Parallel File Processing

Files:

```text
File1

File2

File3
```

---

Each file processed in a separate thread.

---

Main thread waits for completion.

---

# Use Case 5: Load Testing

Testing tools often create:

```text
1000 Threads
```

---

Need to wait until:

```text
All Threads Complete
```

---

CountDownLatch is ideal.

---

# Important Characteristics

---

## One-Time Use

CountDownLatch cannot be reused.

---

Example

```java
CountDownLatch latch =
        new CountDownLatch(3);
```

---

After count reaches:

```text
0
```

---

Cannot reset.

---

Need:

```java
New CountDownLatch
```

---

# Why?

Internal count only decreases.

---

Never increases.

---

# CountDownLatch Lifecycle

```text
Create Latch

↓

Count > 0

↓

await()

↓

Waiting

↓

countDown()

↓

Count = 0

↓

Threads Released

↓

Finished
```

---

# CountDownLatch vs join()

| Feature | CountDownLatch | join() |
|----------|----------|----------|
| Wait For Multiple Threads | Yes | Difficult |
| Reusable | No | N/A |
| Thread Coordination | Excellent | Limited |
| Count-Based | Yes | No |

---

# CountDownLatch vs Semaphore

| Feature | CountDownLatch | Semaphore |
|----------|----------|----------|
| Purpose | Waiting For Events | Limiting Access |
| Count Direction | Down Only | Acquire/Release |
| Reusable | No | Yes |
| Resource Control | No | Yes |

---

# CountDownLatch vs CyclicBarrier

| Feature | CountDownLatch | CyclicBarrier |
|----------|----------|----------|
| Reusable | No | Yes |
| Count Direction | Downward | Barrier-Based |
| Main Purpose | Wait For Completion | Synchronize Threads |
| Reset Supported | No | Yes |

---

# Common Mistakes

---

## Mistake 1

Forgetting countDown()

---

Bad

```java
latch.await();
```

---

Worker never calls:

```java
countDown();
```

---

Result

```text
Main Thread Waits Forever
```

---

# Mistake 2

Not Using finally

---

Bad

```java
doWork();

latch.countDown();
```

---

If exception occurs:

```text
countDown() Never Executes
```

---

Use

```java
finally
```

---

Example

```java
try {

    doWork();

} finally {

    latch.countDown();
}
```

---

# Mistake 3

Trying To Reuse Latch

---

Bad

```text
Use Same Latch Again
```

---

Not possible.

---

Create a new latch.

---

# Common Interview Questions

## Q1: What is CountDownLatch?

A synchronization utility that allows threads to wait until a specified count reaches zero.

---

## Q2: What does countDown() do?

Decrements the count by one.

---

## Q3: What does await() do?

Blocks the current thread until the count becomes zero.

---

## Q4: Is CountDownLatch reusable?

```text
No
```

It is a one-time synchronization aid.

---

## Q5: What happens when count reaches zero?

All waiting threads are released.

---

## Q6: Can count become negative?

```text
No
```

Once count reaches zero, further countDown() calls have no effect.

---

## Q7: Difference between CountDownLatch and Semaphore?

### CountDownLatch

Used for waiting for events.

---

### Semaphore

Used for controlling resource access.

---

## Q8: Difference between CountDownLatch and CyclicBarrier?

### CountDownLatch

One-time use.

---

### CyclicBarrier

Reusable.

---

## Q9: Can multiple threads call await()?

```text
Yes
```

All waiting threads are released when count reaches zero.

---

## Q10: Give a practical use case.

- Waiting for worker threads
- Application startup
- Parallel processing
- Microservice aggregation
- Load testing

---

# Key Takeaways

1. `CountDownLatch` is a synchronization utility introduced in Java 5.
2. It allows one or more threads to wait until a count reaches zero.
3. `countDown()` decreases the count by one.
4. `await()` blocks the current thread until the count becomes zero.
5. It is commonly used to wait for multiple worker threads to finish.
6. `await(timeout)` supports time-limited waiting.
7. CountDownLatch is a one-time synchronization aid and cannot be reset.
8. Always call `countDown()` inside a `finally` block to avoid deadlocks.
9. It is useful for application startup, parallel processing, testing, and microservice coordination.
10. Unlike Semaphore, it is not used to control resource access.
11. Unlike CyclicBarrier, it cannot be reused.
12. Understanding CountDownLatch is essential before learning CyclicBarrier, Phaser, ForkJoin Framework optimizations, and advanced concurrency coordination patterns.