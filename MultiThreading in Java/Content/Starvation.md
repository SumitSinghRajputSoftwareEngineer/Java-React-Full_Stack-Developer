
After learning about:

- Deadlock
- Livelock

there is another important concurrency problem called:

```text
Starvation
```

Unlike Deadlock and Livelock, the system as a whole continues to work.

However:

```text
One or More Threads
Never Get A Chance To Execute
```

or

```text
Wait Excessively Long
```

for resources.

As a result, some threads make progress while others suffer indefinitely.

---

# What is Starvation?

## Definition

Starvation occurs when a thread is unable to gain regular access to required resources because other threads continuously consume those resources.

---

## Simple Definition

```text
Some Threads Progress

One Thread Waits Forever
```

or

```text
One Thread Gets Very Little CPU Time
```

---

## Important Interview Definition

```text
Starvation occurs when a thread is perpetually denied access to resources needed for execution.
```

---

# Real-World Analogy

Imagine a restaurant.

---

### VIP Customers

Always allowed first.

---

### Regular Customer

Keeps waiting.

---

Every time:

```text
New VIP Arrives
```

the regular customer is pushed back.

---

Result:

```text
Regular Customer Never Gets Served
```

This is starvation.

---

# Characteristics of Starvation

## Application Still Runs

Unlike deadlock:

```text
System Continues Working
```

---

## Some Threads Progress

```text
Yes
```

---

## One Or More Threads Suffer

```text
Yes
```

---

## Difficult To Notice Initially

Application may appear healthy.

---

## Throughput Exists

Work is still being completed.

---

## Unfair Resource Allocation

Main reason for starvation.

---

# Example of Starvation

## Scenario

Three threads:

```text
Thread A (High Priority)

Thread B (High Priority)

Thread C (Low Priority)
```

---

Scheduler always chooses:

```text
A
B
A
B
A
B
```

---

Thread C rarely gets CPU.

---

Result:

```text
Thread C Starves
```

---

## Visualization

```text
CPU Scheduler

A
B
A
B
A
B
A
B
```

---

Thread C:

```text
Waiting...
Waiting...
Waiting...
```

---

# Example Using Thread Priorities

```java
class Worker extends Thread {

    public Worker(String name) {

        super(name);
    }

    @Override
    public void run() {

        while(true) {

            System.out.println(
                    getName()
            );
        }
    }
}
```

---

```java
Worker high =
    new Worker("HIGH");

Worker low =
    new Worker("LOW");

high.setPriority(
        Thread.MAX_PRIORITY
);

low.setPriority(
        Thread.MIN_PRIORITY
);

high.start();
low.start();
```

---

## Possible Result

```text
HIGH
HIGH
HIGH
HIGH
HIGH
HIGH
...
```

---

Low priority thread may get very little execution time.

---

## Important Note

Modern JVMs and Operating Systems try to reduce starvation.

Therefore:

```text
Behavior Not Guaranteed
```

but starvation remains possible.

---

# Causes of Starvation

Starvation usually occurs because resources are allocated unfairly.

---

# Cause 1

## Thread Priorities

High-priority threads continuously receive CPU time.

---

### Example

```java
Thread.MAX_PRIORITY
```

dominates:

```java
Thread.MIN_PRIORITY
```

---

## Result

Low-priority thread may rarely execute.

---

# Cause 2

## Unfair Lock Acquisition

Suppose:

```text
Thread A
Thread B
Thread C
```

wait for a lock.

---

Whenever lock becomes available:

```text
Thread A always gets it.
```

---

Thread C waits forever.

---

## Visualization

```text
Lock Released

Thread A Acquires

Lock Released

Thread A Acquires Again

Thread C Never Gets Chance
```

---

# Cause 3

## Excessive Synchronization

Large synchronized blocks can prevent fair access.

---

## Example

```java
synchronized(lock) {

    performLongOperation();
}
```

---

Result:

```text
Other Threads Wait Long Time
```

---

# Cause 4

## Nested Locks

Threads repeatedly acquiring locks may prevent others from progressing.

---

Example:

```text
Thread A acquires locks rapidly.

Thread B constantly loses race.
```

---

# Cause 5

## Busy Waiting

A thread repeatedly checks a condition.

---

Example

```java
while(!available) {

}
```

---

Result:

```text
CPU Consumed Excessively
```

Other threads may struggle to execute.

---

# Cause 6

## Unfair Scheduling Algorithms

Some scheduling algorithms favor certain threads.

---

Example

```text
Shortest Job First
```

may cause long-running tasks to wait indefinitely.

---

# Visualization of Starvation

```text
Resource

      |
      v

Thread A -> Gets Resource

Thread B -> Gets Resource

Thread A -> Gets Resource

Thread B -> Gets Resource

Thread C -> Waiting
```

---

Result:

```text
Thread C Starves
```

---

# Starvation vs Deadlock

| Feature | Starvation | Deadlock |
|-----------|-----------|-----------|
| System Progress | Yes | No |
| Some Threads Complete | Yes | No |
| Waiting Forever | One Thread | All Involved Threads |
| Resource Availability | Exists | Locked In Cycle |
| Recovery | Easier | Harder |

---

# Starvation vs Livelock

| Feature | Starvation | Livelock |
|-----------|-----------|-----------|
| Threads Active | Some | All |
| Progress Made | Some Threads | None |
| CPU Usage | Normal/Variable | Usually High |
| Root Cause | Unfair Resource Access | Continuous Reactions |

---

# Prevention Techniques

The goal is:

```text
Fair Resource Allocation
```

---

# Technique 1

## Fair Locking

Use locks that provide fairness.

---

### Example

```java
ReentrantLock lock =
    new ReentrantLock(true);
```

---

Parameter:

```java
true
```

means:

```text
Fair Lock
```

---

Threads acquire lock in:

```text
FIFO Order
```

---

## Benefit

Prevents starvation.

---

# Technique 2

## Avoid Excessive Thread Priorities

Bad Practice

```java
MAX_PRIORITY
```

for many threads.

---

Better

```java
NORM_PRIORITY
```

for most applications.

---

## Benefit

Balanced CPU allocation.

---

# Technique 3

## Reduce Lock Scope

Bad

```java
synchronized(lock) {

    complexCalculation();
}
```

---

Better

```java
complexCalculation();

synchronized(lock) {

    updateSharedData();
}
```

---

## Benefit

Threads wait less time.

---

# Technique 4

## Use Concurrent Utilities

Modern Java provides:

```java
BlockingQueue
Semaphore
CountDownLatch
ReentrantLock
ExecutorService
```

---

These are designed to improve fairness.

---

# Technique 5

## Use Thread Pools

Instead of:

```java
Creating Unlimited Threads
```

Use:

```java
ExecutorService
```

---

Benefit:

```text
Better Resource Distribution
```

---

# Technique 6

## Avoid Busy Waiting

Bad

```java
while(!ready) {

}
```

---

Better

```java
wait();
```

or

```java
BlockingQueue.take();
```

---

Benefit:

```text
CPU Not Wasted
```

---

# Technique 7

## Fair Scheduling Policies

Use algorithms that ensure:

```text
Every Thread Gets Opportunity
```

---

Examples

```text
Round Robin
Fair Queues
FIFO Locks
```

---

# Real-World Example

Suppose a web application processes requests.

---

### Premium Users

Assigned high-priority threads.

---

### Free Users

Assigned low-priority threads.

---

If premium traffic remains constant:

```text
Free User Requests
May Never Execute
```

---

This is starvation.

---

Solution:

```text
Fair Request Scheduling
```

---

# Starvation in Database Systems

Example:

```text
Read Operations
```

continuously acquire locks.

---

```text
Write Operation
```

keeps waiting.

---

Result:

```text
Writer Starvation
```

---

Solution:

```text
Read-Write Locks
With Fairness Policy
```

---

# Starvation in Executor Framework

Suppose:

```java
ExecutorService pool =
    Executors.newFixedThreadPool(2);
```

---

Two long-running tasks occupy both threads.

---

New tasks:

```text
Wait Indefinitely
```

---

This can resemble starvation.

---

Solution:

- Proper pool sizing
- Queue management
- Task prioritization

---

# Common Interview Questions

## Q1: What is Starvation?

```text
A condition where a thread is indefinitely denied access to resources needed for execution.
```

---

## Q2: Does the application stop during starvation?

```text
No
```

The application continues running.

---

## Q3: What causes starvation?

- Thread priorities
- Unfair locking
- Excessive synchronization
- Busy waiting
- Unfair scheduling

---

## Q4: Can starvation occur without deadlock?

```text
Yes
```

Very commonly.

---

## Q5: What is the difference between starvation and deadlock?

### Deadlock

```text
Nobody Progresses
```

---

### Starvation

```text
Some Progress

Some Wait Forever
```

---

## Q6: How can starvation be prevented?

- Fair locks
- Fair scheduling
- Reduced lock contention
- Concurrent utilities
- Proper thread pool management

---

## Q7: Which lock supports fairness?

```java
ReentrantLock(true)
```

---

## Q8: Can thread priority cause starvation?

```text
Yes
```

High-priority threads may dominate CPU access.

---

# Common Interview Comparison

| Problem | Threads Blocked? | Progress Made? | CPU Usage |
|----------|----------|----------|----------|
| Deadlock | Yes | No | Low |
| Livelock | No | No | High |
| Starvation | Some | Yes (Partial) | Variable |

---

# Key Takeaways

1. Starvation occurs when a thread is repeatedly denied access to required resources.
2. Unlike deadlock, the system continues to function and some threads continue making progress.
3. Thread priorities, unfair locks, and poor scheduling policies are common causes of starvation.
4. Starvation is primarily a fairness problem rather than a locking problem.
5. Fair locks such as `ReentrantLock(true)` help prevent starvation.
6. Reducing lock scope and avoiding excessive synchronization improve fairness.
7. Busy waiting can contribute to starvation and should be replaced with proper waiting mechanisms.
8. Concurrent utilities in `java.util.concurrent` provide better resource management than manual synchronization.
9. Starvation is often harder to notice because the application appears to be functioning normally.
10. Understanding the differences between Deadlock, Livelock, and Starvation is essential for designing reliable concurrent systems.