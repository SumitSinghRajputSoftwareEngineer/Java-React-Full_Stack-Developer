
As applications become more complex, we often encounter situations where:

- The number of participating threads is not known in advance.
- Threads join and leave dynamically.
- Multiple synchronization phases are required.
- Different phases may have different numbers of participants.

Neither `CountDownLatch` nor `CyclicBarrier` handles all these scenarios efficiently.

To solve these problems, Java provides:

```java
Phaser
```

---

# What is Phaser?

## Definition

`Phaser` is a flexible synchronization utility that supports:

- Dynamic thread registration
- Multi-phase execution
- Reusable synchronization points

---

## Package

```java
java.util.concurrent.Phaser
```

---

## Simple Definition

Think of Phaser as:

```text
CountDownLatch
+
CyclicBarrier
+
Dynamic Registration
```

---

It combines the strengths of both synchronization utilities.

---

# Why Phaser?

Suppose:

```text
Phase 1
```

requires:

```text
5 Threads
```

---

But in:

```text
Phase 2
```

only:

```text
3 Threads
```

participate.

---

With:

```java
CyclicBarrier
```

the participant count is fixed.

---

With:

```java
Phaser
```

threads can:

```text
Join

Leave

Rejoin
```

dynamically.

---

# Real-World Analogy

Imagine a project with multiple stages.

---

Stage 1

```text
5 Employees
```

working.

---

Stage 2

```text
3 Employees
```

continue.

---

Stage 3

```text
2 New Employees Join
```

---

Phaser handles this naturally.

---

# Core Concepts

A Phaser manages:

```text
Parties
```

and

```text
Phases
```

---

# Terminology

## Party

A registered participant.

---

Usually:

```text
Thread
```

---

Example

```text
Thread A

Thread B

Thread C
```

---

Total Parties

```text
3
```

---

## Phase

A synchronization stage.

---

Example

```text
Phase 0

↓

Phase 1

↓

Phase 2
```

---

Every time all parties arrive:

```text
Phase Increments
```

---

# Creating a Phaser

## Empty Phaser

```java
Phaser phaser =
        new Phaser();
```

---

Initial Parties

```text
0
```

---

# Phaser With Parties

```java
Phaser phaser =
        new Phaser(3);
```

---

Meaning

```text
3 Registered Parties
```

---

# Visualization

```text
Phase 0

Party 1

Party 2

Party 3

↓

All Arrive

↓

Phase 1
```

---

# Dynamic Registration

One of the biggest advantages of Phaser.

---

# register()

Adds a new participant.

---

## Syntax

```java
phaser.register();
```

---

# Example

```java
Phaser phaser =
        new Phaser();

phaser.register();
```

---

Registered Parties

```text
Before = 0

After = 1
```

---

# bulkRegister()

Registers multiple parties.

---

## Syntax

```java
phaser.bulkRegister(5);
```

---

# Example

```java
Phaser phaser =
        new Phaser();

phaser.bulkRegister(5);
```

---

Registered Parties

```text
5
```

---

# arrive()

Signals that a participant has completed its work for the current phase.

---

## Syntax

```java
phaser.arrive();
```

---

# Example

```java
phaser.arrive();
```

---

Meaning

```text
This Party Finished
Current Phase Work
```

---

# arriveAndAwaitAdvance()

Most commonly used method.

---

## Definition

Signals arrival and waits for all other parties.

---

## Syntax

```java
phaser.arriveAndAwaitAdvance();
```

---

# Example

```java
phaser.arriveAndAwaitAdvance();
```

---

Behavior

```text
Arrive

↓

Wait

↓

All Parties Arrive

↓

Next Phase Starts
```

---

# Visualization

```text
Thread A Arrives

↓

Wait
```

---

```text
Thread B Arrives

↓

Wait
```

---

```text
Thread C Arrives

↓

All Continue

Phase++
```

---

# Complete Example

```java
Phaser phaser =
        new Phaser(3);

Runnable task = () -> {

    System.out.println(
            Thread.currentThread()
                  .getName()
            + " Phase 1"
    );

    phaser.arriveAndAwaitAdvance();

    System.out.println(
            Thread.currentThread()
                  .getName()
            + " Phase 2"
    );

    phaser.arriveAndAwaitAdvance();
};
```

---

Execution

```text
Phase 1

↓

All Threads Reach Phaser

↓

Phase 2

↓

All Threads Reach Phaser

↓

Completed
```

---

# arriveAndDeregister()

One of the most powerful features.

---

## Definition

Signals arrival and removes the party from future phases.

---

## Syntax

```java
phaser.arriveAndDeregister();
```

---

# Example

```java
phaser.arriveAndDeregister();
```

---

Meaning

```text
Work Completed

↓

Leave Phaser
```

---

# Visualization

Before

```text
5 Parties
```

---

After Deregistration

```text
4 Parties
```

---

# Why Important?

Some threads may finish early.

---

They should not block future phases.

---

# Multi-phase Synchronization

This is where Phaser shines.

---

Suppose:

```text
Phase 0

↓

Phase 1

↓

Phase 2

↓

Phase 3
```

---

Same Phaser coordinates all phases.

---

# Example

```java
for(int phase = 0;
    phase < 3;
    phase++) {

    doWork();

    phaser.arriveAndAwaitAdvance();
}
```

---

# Workflow

```text
Phase 0

↓

All Arrive

↓

Phase 1

↓

All Arrive

↓

Phase 2

↓

All Arrive

↓

Finished
```

---

# Getting Current Phase

## getPhase()

Returns current phase number.

---

```java
int phase =
        phaser.getPhase();
```

---

# Example

```java
System.out.println(
        phaser.getPhase()
);
```

---

Output

```text
0
```

or

```text
1
```

etc.

---

# Getting Registered Parties

## getRegisteredParties()

Returns total parties.

---

```java
phaser.getRegisteredParties();
```

---

# Example

```java
System.out.println(
        phaser.getRegisteredParties()
);
```

---

Output

```text
5
```

---

# Getting Arrived Parties

## getArrivedParties()

Returns arrived parties for current phase.

---

```java
phaser.getArrivedParties();
```

---

# Example

```java
System.out.println(
        phaser.getArrivedParties()
);
```

---

Output

```text
3
```

---

# Getting Unarrived Parties

## getUnarrivedParties()

Returns parties yet to arrive.

---

```java
phaser.getUnarrivedParties();
```

---

# Example

```java
System.out.println(
        phaser.getUnarrivedParties()
);
```

---

Output

```text
2
```

---

# Termination

A Phaser can terminate automatically.

---

Example

All parties deregister.

---

```text
Registered Parties = 0
```

---

Phaser terminates.

---

# isTerminated()

Checks whether Phaser has terminated.

---

```java
phaser.isTerminated();
```

---

Returns

```java
true
```

or

```java
false
```

---

# Custom Phaser

Phaser can be extended.

---

Override:

```java
onAdvance()
```

---

# Example

```java
class MyPhaser
        extends Phaser {

    @Override
    protected boolean onAdvance(
            int phase,
            int registeredParties) {

        System.out.println(
                "Completed Phase "
                + phase
        );

        return false;
    }
}
```

---

# onAdvance()

Called automatically when a phase completes.

---

Useful for:

- Logging
- Monitoring
- Custom termination
- Metrics collection

---

# Common Use Cases

---

# Use Case 1: Multi-Stage Processing

Example

```text
Read Data

↓

Transform Data

↓

Write Data
```

---

All stages synchronized using Phaser.

---

# Use Case 2: Distributed Computation

Different worker threads process parts of a problem.

---

Synchronization required after every iteration.

---

# Use Case 3: Simulation Systems

Example

```text
Game Loop

Physics

AI

Rendering
```

---

All must finish one phase before next begins.

---

# Use Case 4: Dynamic Worker Pools

Workers can:

```text
Join

Leave
```

during execution.

---

Perfect use case for Phaser.

---

# Use Case 5: Parallel Algorithms

Examples:

- Matrix computations
- Scientific calculations
- Large-scale analytics

---

# Phaser vs CyclicBarrier

Most important interview topic.

---

# CyclicBarrier

```text
Fixed Number
Of Participants
```

---

Created as:

```java
new CyclicBarrier(5);
```

---

Cannot dynamically add/remove participants.

---

# Phaser

```text
Dynamic Number
Of Participants
```

---

Participants can:

```text
Register

Deregister
```

at runtime.

---

# Comparison Table

| Feature | CyclicBarrier | Phaser |
|----------|----------|----------|
| Reusable | Yes | Yes |
| Dynamic Registration | No | Yes |
| Dynamic Deregistration | No | Yes |
| Multi-Phase Support | Limited | Excellent |
| Custom Phase Logic | No | Yes |
| Complexity | Simpler | More Powerful |

---

# Visualization

## CyclicBarrier

```text
Thread 1

Thread 2

Thread 3

↓

Barrier Opens

↓

Reset
```

---

Fixed participants.

---

## Phaser

```text
Thread 1

Thread 2

↓

Thread 3 Joins

↓

Thread 2 Leaves

↓

Phase Continues
```

---

Dynamic participants.

---

# Phaser vs CountDownLatch

| Feature | CountDownLatch | Phaser |
|----------|----------|----------|
| Reusable | No | Yes |
| Multi-Phase | No | Yes |
| Dynamic Registration | No | Yes |
| Dynamic Deregistration | No | Yes |
| Count Direction | Down Only | Multi-Phase |

---

# Common Mistakes

---

## Mistake 1

Forgetting Registration

Bad

```java
phaser.arriveAndAwaitAdvance();
```

---

Thread not registered.

---

Always:

```java
register()
```

or initialize with parties.

---

# Mistake 2

Not Deregistering Finished Threads

Bad

```text
Thread Finished
But Still Registered
```

---

Future phases wait unnecessarily.

---

Use:

```java
arriveAndDeregister();
```

---

# Mistake 3

Using Phaser When CountDownLatch Is Enough

For simple one-time waiting:

```java
CountDownLatch
```

is simpler.

---

# Common Interview Questions

## Q1: What is Phaser?

A synchronization utility that supports dynamic registration and multi-phase synchronization.

---

## Q2: Why is Phaser more flexible than CyclicBarrier?

Because participants can register and deregister dynamically.

---

## Q3: What does arriveAndAwaitAdvance() do?

Signals arrival and waits for all other parties before advancing to the next phase.

---

## Q4: What does arriveAndDeregister() do?

Signals arrival and removes the party from future phases.

---

## Q5: Is Phaser reusable?

```text
Yes
```

---

## Q6: What is a phase?

A synchronization stage in which all registered parties must arrive before advancing.

---

## Q7: How can you register new parties dynamically?

```java
register()
```

or

```java
bulkRegister()
```

---

## Q8: What method returns the current phase?

```java
getPhase()
```

---

## Q9: Can Phaser replace CountDownLatch?

```text
Yes
```

for many scenarios, although CountDownLatch is simpler for one-time synchronization.

---

## Q10: Give a real-world use case.

- Multi-stage batch processing
- Distributed computations
- Simulations
- Dynamic worker pools
- Parallel algorithms

---

# Key Takeaways

1. `Phaser` is an advanced synchronization utility introduced in Java 7.
2. It combines features of `CountDownLatch` and `CyclicBarrier`.
3. It supports dynamic registration and deregistration of participants.
4. `arriveAndAwaitAdvance()` is used to synchronize phases.
5. `arriveAndDeregister()` removes participants from future phases.
6. Phaser is reusable and designed for multi-phase workflows.
7. It automatically advances phases when all registered parties arrive.
8. `onAdvance()` can be overridden to execute custom logic during phase transitions.
9. Phaser is ideal for simulations, parallel algorithms, and dynamic worker systems.
10. It is more powerful and flexible than CyclicBarrier but also more complex.
11. Understanding Phaser is essential before learning Structured Concurrency, Reactive Systems, and High-Performance Parallel Computing.