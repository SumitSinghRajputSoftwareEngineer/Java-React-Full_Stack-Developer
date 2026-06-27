# 5. Thread Scheduling

Creating multiple threads does not mean all threads execute simultaneously.

A very important question arises:

> If multiple threads are ready to run, which thread gets the CPU first?

The answer is:

```text
Thread Scheduler
```

Thread Scheduling is the mechanism used by the JVM and Operating System to determine:

- Which thread executes?
- When it executes?
- How long it executes?
- When it should be paused?

Understanding thread scheduling is crucial because it explains why multithreaded programs often produce different outputs on different executions.

---

# What is Thread Scheduling?

## Definition

Thread Scheduling is the process of selecting a thread from multiple runnable threads and allocating CPU time to it.

---

## Example

Suppose we have:

```text
Thread A
Thread B
Thread C
```

All are in:

```text
RUNNABLE
```

state.

Only one CPU core is available.

The scheduler decides:

```text
Which thread runs first?
How long it runs?
When another thread gets CPU?
```

---

## Real-World Analogy

Imagine:

```text
CPU = Teacher

Threads = Students
```

Many students raise their hands.

The teacher decides:

```text
Who speaks first?
Who speaks next?
How long each student speaks?
```

The teacher acts like a scheduler.

---

# Thread Scheduler

## Definition

A Thread Scheduler is a component of the JVM and Operating System responsible for selecting runnable threads for execution.

---

## Important Point

Java does NOT implement its own scheduler.

Instead:

```text
JVM delegates scheduling
to the Operating System.
```

---

## Flow

```text
Multiple Threads
       |
       v
   RUNNABLE
       |
       v
Thread Scheduler
       |
       v
CPU Assigned
       |
       v
Execution
```

---

## Example

```java
class MyThread extends Thread {

    @Override
    public void run() {

        for(int i=1;i<=5;i++) {

            System.out.println(
                Thread.currentThread().getName()
            );
        }
    }
}
```

```java
MyThread t1 = new MyThread();
MyThread t2 = new MyThread();

t1.start();
t2.start();
```

---

## Possible Output

```text
Thread-0
Thread-0
Thread-1
Thread-0
Thread-1
```

or

```text
Thread-1
Thread-1
Thread-0
Thread-1
Thread-0
```

or many other combinations.

---

## Why Output Changes?

Because:

```text
Scheduler decides execution order.
```

and the decision is not guaranteed.

---

# Characteristics of Thread Scheduler

## JVM Does Not Guarantee Order

Incorrect assumption:

```java
t1.start();
t2.start();
```

means

```text
t1 always executes first.
```

Wrong.

---

## Scheduler Is Platform Dependent

Output may differ between:

- Windows
- Linux
- MacOS

---

## Non-Deterministic Behavior

Multiple executions may produce different results.

---

# Time Slicing

## Definition

Time Slicing is a scheduling technique where CPU time is divided into small units called:

```text
Time Slices
```

or

```text
Time Quantum
```

Each runnable thread receives CPU for a short duration.

---

## Example

Suppose:

```text
Time Slice = 100 ms
```

---

### Execution

```text
Thread A -> 100 ms
Thread B -> 100 ms
Thread C -> 100 ms
Thread A -> 100 ms
```

---

## Diagram

```text
Time →
+-----+-----+-----+-----+-----+
|  A  |  B  |  C  |  A  |  B  |
+-----+-----+-----+-----+-----+
```

---

## Benefits

### Fairness

Every thread gets CPU opportunity.

---

### Better Responsiveness

No thread monopolizes CPU.

---

### Concurrent Appearance

Creates illusion of simultaneous execution.

---

## Important Point

Time slicing is:

```text
OS dependent
```

Java does not guarantee its presence.

---

# Preemptive Scheduling

## Definition

In Preemptive Scheduling, the scheduler can forcibly suspend a running thread and allocate CPU to another thread.

---

## How It Works

```text
Thread A Running
       |
Time Slice Expires
       |
Scheduler Stops A
       |
Scheduler Runs B
```

---

## Diagram

```text
Thread A Running
        |
        v
Time Quantum Ends
        |
        v
Scheduler Interrupts
        |
        v
Thread B Runs
```

---

## Example

```text
Thread A
Thread B
Thread C
```

Even if:

```text
Thread A never yields
```

the scheduler can still stop it.

---

## Advantages

### Fair CPU Distribution

No thread can dominate CPU forever.

---

### Better Performance

Improves overall system responsiveness.

---

### Widely Used

Modern operating systems use preemptive scheduling.

Examples:

- Windows
- Linux
- MacOS

---

## Java Uses

```text
Preemptive Scheduling
```

through the operating system scheduler.

---

# Cooperative Scheduling

## Definition

In Cooperative Scheduling, a running thread voluntarily gives up CPU.

---

## Flow

```text
Thread A Running
       |
yield()
       |
Scheduler Selects Another Thread
```

---

## Example

```java
Thread.yield();
```

---

## Diagram

```text
Thread A
    |
yield()
    |
    v
Thread B
```

---

## Problem

Suppose:

```text
Thread A never yields
```

Then:

```text
Thread B may never run
```

---

## Disadvantages

### Starvation Risk

One thread can block others.

---

### Poor Responsiveness

System may appear frozen.

---

## Historical Usage

Older operating systems used cooperative scheduling.

Modern systems primarily use:

```text
Preemptive Scheduling
```

---

# Preemptive vs Cooperative Scheduling

| Feature | Preemptive | Cooperative |
|----------|----------|----------|
| CPU Control | Scheduler | Thread |
| Forced Switch | Yes | No |
| Fairness | Better | Lower |
| Starvation Risk | Low | High |
| Responsiveness | High | Low |
| Modern Usage | Yes | Rare |

---

# Thread Priorities

## Definition

Thread Priority is a numerical value indicating the relative importance of a thread.

---

## Range

```text
1 to 10
```

---

## Default Priority

```text
5
```

---

## Example

```java
Thread t = new Thread();

System.out.println(
    t.getPriority()
);
```

Output:

```text
5
```

---

## Setting Priority

```java
t.setPriority(8);
```

---

## Getting Priority

```java
int priority =
    t.getPriority();
```

---

# Priority Constants

The Thread class defines three constants.

---

## MIN_PRIORITY

```java
Thread.MIN_PRIORITY
```

Value:

```java
1
```

Meaning:

```text
Lowest Priority
```

---

### Example

```java
t.setPriority(
    Thread.MIN_PRIORITY
);
```

Equivalent:

```java
t.setPriority(1);
```

---

# NORM_PRIORITY

```java
Thread.NORM_PRIORITY
```

Value:

```java
5
```

Meaning:

```text
Default Priority
```

---

### Example

```java
t.setPriority(
    Thread.NORM_PRIORITY
);
```

Equivalent:

```java
t.setPriority(5);
```

---

# MAX_PRIORITY

```java
Thread.MAX_PRIORITY
```

Value:

```java
10
```

Meaning:

```text
Highest Priority
```

---

### Example

```java
t.setPriority(
    Thread.MAX_PRIORITY
);
```

Equivalent:

```java
t.setPriority(10);
```

---

# Example of Priorities

```java
class MyThread extends Thread {

    public void run() {

        System.out.println(
            getName()
        );
    }
}

public class Test {

    public static void main(String[] args) {

        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();

        t1.setPriority(1);
        t2.setPriority(10);

        t1.start();
        t2.start();
    }
}
```

---

## Important Interview Point

Output is NOT guaranteed.

Even though:

```java
t2.setPriority(10);
```

it may still execute after `t1`.

---

# Priority Inheritance

When a thread creates another thread:

```text
Child Thread inherits
Parent Thread Priority
```

---

## Example

```java
Thread.currentThread()
      .setPriority(8);

Thread t = new Thread();

System.out.println(
    t.getPriority()
);
```

Output:

```text
8
```

---

# Limitations of Priority

Many developers misunderstand thread priorities.

A common misconception:

> Higher priority means the thread always executes first.

This is incorrect.

---

## Limitation 1: Only a Hint

Priority is merely a suggestion to the scheduler.

```text
Not a guarantee.
```

---

## Example

```java
t1.setPriority(10);
t2.setPriority(1);
```

Possible execution:

```text
t2 executes first
```

---

## Limitation 2: Platform Dependent

Different operating systems treat priorities differently.

---

### Windows

May strongly consider priority.

---

### Linux

May largely ignore Java priorities.

---

### MacOS

Behavior can differ again.

---

# Limitation 3: JVM Dependent

Different JVM implementations may behave differently.

---

# Limitation 4: Cannot Ensure Execution Order

Wrong:

```java
t1.setPriority(10);
t2.setPriority(1);
```

Therefore:

```text
t1 always runs first
```

False.

---

# Limitation 5: Priority Does Not Solve Synchronization Problems

Priority cannot prevent:

- Race Conditions
- Deadlocks
- Data Inconsistency

Example:

```java
count++;
```

remains unsafe regardless of priority.

---

# Best Practices for Priorities

## Use Default Priority

Most applications should use:

```java
Thread.NORM_PRIORITY
```

---

## Avoid Business Logic Depending on Priority

Wrong:

```java
if(priority == 10)
{
   assume executes first
}
```

Never do this.

---

## Use Executor Framework Instead

Modern applications prefer:

```java
ExecutorService
ThreadPoolExecutor
CompletableFuture
```

rather than relying on thread priorities.

---

# Real-World Example

Suppose a video conferencing application has:

```text
Audio Thread
Video Thread
Chat Thread
```

You might assign:

```text
Audio -> Priority 10
Video -> Priority 7
Chat  -> Priority 5
```

to suggest that audio processing is more important.

However:

```text
Execution is still not guaranteed.
```

The scheduler makes the final decision.

---

# Common Interview Questions

## Q1: Who decides which thread runs?

```text
Thread Scheduler
```

---

## Q2: Does JVM guarantee execution order?

```text
No
```

---

## Q3: What is Time Slicing?

CPU is divided into small intervals and allocated to threads one by one.

---

## Q4: What scheduling model does Java use?

```text
Preemptive Scheduling
```

(via the operating system)

---

## Q5: What is the default thread priority?

```java
5
```

or

```java
Thread.NORM_PRIORITY
```

---

## Q6: What is the priority range?

```text
1 to 10
```

---

## Q7: Does higher priority guarantee earlier execution?

```text
No
```

---

## Q8: What happens if an invalid priority is set?

```java
t.setPriority(15);
```

Throws:

```java
IllegalArgumentException
```

---

# Key Takeaways

1. Thread Scheduling determines which runnable thread gets CPU time.
2. Java relies on the operating system scheduler rather than implementing its own scheduler.
3. Time Slicing divides CPU time into small intervals called time quanta.
4. Modern systems use Preemptive Scheduling, where the scheduler can forcibly switch threads.
5. Cooperative Scheduling depends on threads voluntarily releasing CPU time.
6. Thread priorities range from 1 to 10.
7. `MIN_PRIORITY = 1`, `NORM_PRIORITY = 5`, and `MAX_PRIORITY = 10`.
8. Child threads inherit their parent thread's priority by default.
9. Thread priority is only a hint to the scheduler, not a guarantee.
10. Production applications should not rely on priority for correctness or execution order.
11. Understanding scheduling helps explain why multithreaded programs often produce different outputs across executions.