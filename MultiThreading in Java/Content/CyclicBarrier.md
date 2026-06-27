
In many multithreaded applications, multiple threads must reach a common point before any of them can continue.

Examples:

- Multiplayer games waiting for all players to load
- Parallel data processing
- Scientific simulations
- Distributed computations
- Batch processing stages

In such scenarios:

```text
All Threads Must Wait
For Each Other
```

before moving to the next phase.

To solve this problem, Java provides:

```java
CyclicBarrier
```

---

# What is CyclicBarrier?

## Definition

`CyclicBarrier` is a synchronization utility that allows a group of threads to wait for each other at a common barrier point.

---

## Package

```java
java.util.concurrent.CyclicBarrier
```

---

## Simple Definition

Think of it as:

```text
Meeting Point
```

where all participating threads must arrive before anyone proceeds.

---

# Real-World Analogy

Imagine:

```text
5 Friends
```

going on a trip.

---

Rule:

```text
Nobody Leaves

Until Everyone Arrives
```

---

Friend 1 arrives.

```text
Wait
```

---

Friend 2 arrives.

```text
Wait
```

---

Friend 3 arrives.

```text
Wait
```

---

Friend 4 arrives.

```text
Wait
```

---

Friend 5 arrives.

```text
All Proceed Together
```

---

This is exactly how:

```java
CyclicBarrier
```

works.

---

# Why CyclicBarrier?

Suppose we have:

```text
4 Worker Threads
```

processing different parts of a dataset.

---

Phase 1

```text
Worker 1 Done

Worker 2 Done

Worker 3 Done

Worker 4 Done
```

---

Only after ALL workers complete Phase 1 should Phase 2 begin.

---

Without CyclicBarrier:

Need:

```java
Manual Counters

wait()

notify()

Complex Synchronization
```

---

With CyclicBarrier:

```java
barrier.await();
```

---

Java handles everything.

---

# Creating a CyclicBarrier

## Syntax

```java
CyclicBarrier barrier =
        new CyclicBarrier(3);
```

---

Meaning

```text
3 Threads
Must Reach Barrier
```

before proceeding.

---

# Visualization

```text
Barrier Size = 3

Thread A Arrives

↓

Wait

Thread B Arrives

↓

Wait

Thread C Arrives

↓

Barrier Opens

↓

All Continue
```

---

# await()

## Definition

Indicates that the current thread has reached the barrier and is willing to wait for others.

---

## Syntax

```java
barrier.await();
```

---

# Behavior

If all required threads have not arrived:

```text
Thread Waits
```

---

When the last thread arrives:

```text
Barrier Opens

All Threads Continue
```

---

# Example

```java
CyclicBarrier barrier =
        new CyclicBarrier(3);
```

---

Thread

```java
barrier.await();
```

---

Thread waits until:

```text
3 Threads
Call await()
```

---

# Visualization

```text
Thread 1

↓

await()

↓

Waiting
```

---

```text
Thread 2

↓

await()

↓

Waiting
```

---

```text
Thread 3

↓

await()

↓

Barrier Opens
```

---

All continue.

---

# Complete Example

```java
CyclicBarrier barrier =
        new CyclicBarrier(3);

Runnable worker = () -> {

    try {

        System.out.println(
                Thread.currentThread()
                      .getName()
                + " processing"
        );

        Thread.sleep(2000);

        System.out.println(
                Thread.currentThread()
                      .getName()
                + " waiting"
        );

        barrier.await();

        System.out.println(
                Thread.currentThread()
                      .getName()
                + " resumed"
        );

    } catch(Exception e) {

        e.printStackTrace();
    }
};
```

---

Create Threads

```java
new Thread(worker).start();
new Thread(worker).start();
new Thread(worker).start();
```

---

Possible Output

```text
Thread-1 processing
Thread-2 processing
Thread-3 processing

Thread-1 waiting
Thread-2 waiting
Thread-3 waiting

Thread-1 resumed
Thread-2 resumed
Thread-3 resumed
```

---

# Internal Workflow

```text
Barrier Count = 3

↓

Thread 1 await()

↓

Count = 2

↓

Thread 2 await()

↓

Count = 1

↓

Thread 3 await()

↓

Count = 0

↓

Barrier Opens

↓

All Threads Continue
```

---

# Barrier Action

One of the most important features of CyclicBarrier.

---

## Definition

A task executed automatically when the last thread reaches the barrier.

---

# Syntax

```java
CyclicBarrier barrier =
        new CyclicBarrier(
                parties,
                barrierAction
        );
```

---

# Example

```java
CyclicBarrier barrier =
        new CyclicBarrier(
                3,
                () -> {

                    System.out.println(
                            "All Threads Arrived"
                    );
                }
        );
```

---

# Execution Flow

```text
Thread 1 Arrives

↓

Thread 2 Arrives

↓

Thread 3 Arrives

↓

Barrier Action Executes

↓

All Threads Continue
```

---

# Example

```java
CyclicBarrier barrier =
        new CyclicBarrier(
                3,
                () -> {

                    System.out.println(
                            "Phase Completed"
                    );
                }
        );
```

---

Output

```text
Phase Completed
```

before threads proceed.

---

# Reusable Nature

The most important characteristic of CyclicBarrier.

---

## Why "Cyclic"?

Because the barrier:

```text
Resets Automatically
```

after opening.

---

Can be used again.

---

# Example

Phase 1

```text
All Threads Reach Barrier

↓

Barrier Opens
```

---

Phase 2

```text
All Threads Reach Barrier Again

↓

Barrier Opens Again
```

---

No need to create a new barrier.

---

# Visualization

```text
Phase 1

↓

Barrier

↓

Open

↓

Reset

↓

Phase 2

↓

Barrier

↓

Open

↓

Reset
```

---

# Multi-Phase Example

```java
for(int phase = 1;
    phase <= 3;
    phase++) {

    doWork();

    barrier.await();
}
```

---

Barrier reused for every phase.

---

# Why Reusability Matters

Many applications operate in stages.

---

Example

```text
Phase 1

↓

Phase 2

↓

Phase 3
```

---

A reusable barrier is much more efficient than creating:

```java
CountDownLatch
```

for every phase.

---

# Barrier Broken State

If a waiting thread is interrupted:

```java
InterruptedException
```

may occur.

---

Other waiting threads receive:

```java
BrokenBarrierException
```

---

# Example

```java
try {

    barrier.await();

} catch(
        InterruptedException |
        BrokenBarrierException e) {

    e.printStackTrace();
}
```

---

# reset()

Manually resets the barrier.

---

## Syntax

```java
barrier.reset();
```

---

# Example

```java
barrier.reset();
```

---

Waiting threads may receive:

```java
BrokenBarrierException
```

---

Use carefully.

---

# Important Methods

---

## getParties()

Returns total participating threads.

---

```java
barrier.getParties();
```

---

# Example

```java
System.out.println(
        barrier.getParties()
);
```

---

Output

```text
3
```

---

## getNumberWaiting()

Returns number of threads currently waiting.

---

```java
barrier.getNumberWaiting();
```

---

# Example

```java
System.out.println(
        barrier.getNumberWaiting()
);
```

---

Output

```text
2
```

---

# isBroken()

Checks whether barrier is broken.

---

```java
barrier.isBroken();
```

---

Output

```java
true
```

or

```java
false
```

---

# Common Use Cases

---

# Use Case 1: Parallel Data Processing

Suppose:

```text
4 Threads
```

process data.

---

After processing:

```text
Wait For Everyone
```

---

Then start next phase.

---

# Use Case 2: Multiplayer Games

Players must:

```text
Load Resources
```

---

Only after all players finish loading:

```text
Game Starts
```

---

# Use Case 3: Scientific Simulations

Multiple threads perform:

```text
Partial Calculations
```

---

Results combined after all finish.

---

# Use Case 4: Batch Processing

Phase 1

```text
Read Data
```

---

Barrier

---

Phase 2

```text
Transform Data
```

---

Barrier

---

Phase 3

```text
Write Data
```

---

# Use Case 5: Distributed Computation

Each thread computes part of a large problem.

---

Synchronization needed before next iteration.

---

# CyclicBarrier vs CountDownLatch

One of the most important interview questions.

---

# CountDownLatch

```text
Wait For Events To Finish
```

---

Example

```text
Main Thread

Waits For

Worker Threads
```

---

Count decreases:

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

Then released.

---

# CyclicBarrier

```text
All Threads Wait
For Each Other
```

---

Barrier opens when all arrive.

---

# Comparison Table

| Feature | CountDownLatch | CyclicBarrier |
|----------|----------|----------|
| Reusable | No | Yes |
| Count Direction | Downward | Barrier-Based |
| Waiting Threads | One or Many | All Participants |
| Reset Supported | No | Yes |
| Barrier Action | No | Yes |
| Best For | Completion Waiting | Phase Synchronization |

---

# Visualization

## CountDownLatch

```text
Workers

↓

countDown()

↓

Count = 0

↓

Main Thread Continues
```

---

## CyclicBarrier

```text
Thread A

Thread B

Thread C

↓

All Reach Barrier

↓

All Continue
```

---

# CyclicBarrier vs CountDownLatch Example

---

## CountDownLatch

```java
CountDownLatch latch =
        new CountDownLatch(3);
```

---

Workers

```java
latch.countDown();
```

---

Main Thread

```java
latch.await();
```

---

# CyclicBarrier

```java
CyclicBarrier barrier =
        new CyclicBarrier(3);
```

---

Workers

```java
barrier.await();
```

---

All workers wait for each other.

---

# Common Mistakes

---

## Mistake 1

Using CountDownLatch For Multiple Phases

Bad

```text
Create New Latch
Every Phase
```

---

Use:

```java
CyclicBarrier
```

instead.

---

# Mistake 2

Ignoring BrokenBarrierException

---

Always handle:

```java
BrokenBarrierException
```

---

# Mistake 3

Incorrect Barrier Size

Example

```java
new CyclicBarrier(5);
```

---

Only 4 threads arrive.

---

Result

```text
All Threads Wait Forever
```

---

# Common Interview Questions

## Q1: What is CyclicBarrier?

A synchronization utility that allows multiple threads to wait for each other at a common barrier point.

---

## Q2: Why is it called "Cyclic"?

Because it automatically resets and can be reused.

---

## Q3: What does await() do?

Makes the current thread wait until all participating threads reach the barrier.

---

## Q4: What is Barrier Action?

A task executed automatically when the last thread reaches the barrier.

---

## Q5: Is CyclicBarrier reusable?

```text
Yes
```

That is its primary advantage.

---

## Q6: What exception can occur if barrier breaks?

```java
BrokenBarrierException
```

---

## Q7: Difference between CountDownLatch and CyclicBarrier?

### CountDownLatch

Wait for completion.

---

### CyclicBarrier

Synchronize phases.

---

## Q8: Can multiple phases use the same CyclicBarrier?

```text
Yes
```

---

## Q9: What happens if one thread never reaches await()?

```text
Other Threads Wait Forever
```

unless timeout or interruption occurs.

---

## Q10: Give a real-world use case.

- Multiplayer games
- Parallel computation
- Batch processing
- Scientific simulations
- Distributed systems

---

# Key Takeaways

1. `CyclicBarrier` is a synchronization utility introduced in Java 5.
2. It allows multiple threads to wait for each other at a common barrier point.
3. `await()` blocks until all participating threads arrive.
4. Once the last thread arrives, all waiting threads are released.
5. A Barrier Action can be executed automatically when the barrier opens.
6. CyclicBarrier is reusable and automatically resets after each cycle.
7. It is ideal for multi-phase computations and parallel processing.
8. Unlike CountDownLatch, it supports repeated synchronization points.
9. Interrupted or failed threads may cause a `BrokenBarrierException`.
10. `reset()` can manually reset the barrier.
11. CyclicBarrier is commonly used in simulations, games, parallel algorithms, and staged processing workflows.
12. Understanding CyclicBarrier is essential before learning Phaser, Structured Concurrency, Advanced Parallel Algorithms, and High-Performance Distributed Systems.