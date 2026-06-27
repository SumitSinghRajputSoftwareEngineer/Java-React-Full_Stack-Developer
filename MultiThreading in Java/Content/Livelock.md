
After learning about **Deadlock**, many developers assume that if threads are not blocked, the application must be making progress.

Unfortunately, that is not always true.

There is another concurrency problem called:

```text
Livelock
```

In a livelock, threads are:

```text
Active
Running
Responding
```

but:

```text
No Useful Work Is Completed
```

The application appears alive, yet it cannot make progress.

---

# What is Livelock?

## Definition

A Livelock occurs when two or more threads continuously react to each other's actions and keep changing their state without making any actual progress.

---

## Simple Definition

```text
Threads Are Not Blocked

Threads Keep Running

But No Thread Completes Its Task
```

---

## Important Interview Definition

```text
In Deadlock, threads are waiting.

In Livelock, threads are working but not progressing.
```

---

# Real-World Analogy

Imagine two people walking toward each other in a narrow corridor.

---

### Person A

Moves left.

---

### Person B

Also moves left.

---

Both realize collision may happen.

---

### Person A

Moves right.

---

### Person B

Also moves right.

---

Again collision.

---

This continues forever.

```text
Both Are Active

Nobody Moves Forward
```

This is a classic livelock.

---

# Characteristics of Livelock

## Threads Are Not Blocked

Unlike deadlock:

```text
No Thread Waiting Forever
```

---

## CPU Usage May Be High

Threads continuously execute.

---

## State Changes Occur

Threads keep responding to each other.

---

## No Progress

Important work never completes.

---

## Difficult To Detect

Because:

```text
Application Appears Alive
```

---

# Livelock Example

## Scenario

Two workers try to use a shared resource.

Both are polite.

Whenever one notices the other trying to work, it backs off.

As a result:

```text
Both Keep Backing Off Forever
```

---

## Example Code

```java
class Worker {

    private boolean active = true;

    public boolean isActive() {
        return active;
    }

    public void setActive(boolean active) {
        this.active = active;
    }

    public void work(Worker other) {

        while(active) {

            if(other.isActive()) {

                System.out.println(
                        Thread.currentThread().getName()
                        + " giving chance to "
                        + other
                );

                continue;
            }

            System.out.println(
                    Thread.currentThread().getName()
                    + " working"
            );

            active = false;
        }
    }
}
```

---

## Conceptual Execution

### Worker A

```text
Other Worker Active
```

So:

```text
I Will Wait
```

---

### Worker B

```text
Other Worker Active
```

So:

```text
I Will Wait
```

---

### Worker A

Still sees Worker B active.

---

### Worker B

Still sees Worker A active.

---

Result:

```text
Infinite Cooperation
```

---

## Visualization

```text
Worker A
   |
Sees B Active
   |
Back Off

Worker B
   |
Sees A Active
   |
Back Off
```

---

Cycle continues forever.

---

# Another Practical Example

Suppose:

```text
Thread A
Thread B
```

need a lock.

---

### Thread A

Gets lock.

Sees B waiting.

Releases lock.

---

### Thread B

Gets lock.

Sees A waiting.

Releases lock.

---

### Thread A

Gets lock again.

Releases again.

---

### Thread B

Gets lock again.

Releases again.

---

Result:

```text
Constant Activity
No Work Completed
```

---

# State Diagram of Livelock

```text
Thread A
   |
Running
   |
React To B
   |
Running Again

-----------------

Thread B
   |
Running
   |
React To A
   |
Running Again
```

---

## Important Observation

Threads continuously move between:

```text
RUNNABLE
RUNNING
```

states.

They are NOT:

```text
BLOCKED
WAITING
```

---

# Why Does Livelock Occur?

Common reasons:

---

## Overly Polite Resource Sharing

Example:

```text
You First
No, You First
No, You First
```

Forever.

---

## Aggressive Retry Logic

Example:

```java
while(true) {

    if(lockAvailable()) {

        acquireLock();
        break;
    }
}
```

Many threads repeatedly retry without coordination.

---

## Poor Conflict Resolution Strategy

Threads continuously react to each other.

---

## Incorrect Backoff Algorithms

Improper retry mechanisms can cause livelock.

---

# Detecting Livelock

Livelock is harder to detect than deadlock.

---

## Deadlock Detection

Easy because:

```text
Threads Blocked
```

---

## Livelock Detection

Hard because:

```text
Threads Active
```

---

## Common Symptoms

### High CPU Usage

```text
Threads Running Constantly
```

---

### No Useful Output

Application not completing work.

---

### Repeated Logs

Example:

```text
Retrying...
Retrying...
Retrying...
Retrying...
```

---

### Throughput Near Zero

Many operations attempted.

Few completed.

---

# How to Prevent Livelock?

---

# Strategy 1

## Random Backoff

Instead of retrying immediately:

```java
Thread.sleep(
    random.nextInt(100)
);
```

---

### Benefit

Threads stop reacting identically.

---

## Example

```text
Thread A waits 20 ms

Thread B waits 80 ms
```

Now one thread progresses.

---

# Strategy 2

## Retry Limits

Avoid infinite retries.

---

### Example

```java
for(int i=0;i<5;i++) {

    attemptOperation();
}
```

---

If retries exceed limit:

```text
Fail Gracefully
```

---

# Strategy 3

## Resource Ordering

Acquire resources in consistent order.

This also helps prevent deadlocks.

---

# Strategy 4

## Use Concurrent Utilities

Instead of manual coordination:

```java
BlockingQueue
Semaphore
CountDownLatch
ReentrantLock
```

---

These provide well-tested synchronization mechanisms.

---

# Strategy 5

## Exponential Backoff

Widely used in:

- Distributed Systems
- Network Communication
- Microservices

---

### Example

```text
Retry 1 → Wait 100 ms

Retry 2 → Wait 200 ms

Retry 3 → Wait 400 ms

Retry 4 → Wait 800 ms
```

---

Reduces contention significantly.

---

# Difference Between Deadlock and Livelock

| Feature | Deadlock | Livelock |
|----------|-----------|-----------|
| Thread State | Waiting/Blocked | Active/Running |
| CPU Usage | Usually Low | Usually High |
| Progress | No | No |
| Resource Holding | Yes | Usually Released Frequently |
| Detection | Easier | Harder |
| Recovery | Often Restart Required | Backoff Strategy Helps |
| Thread Activity | Stopped | Continuous |

---

# Visualization

## Deadlock

```text
Thread A
   |
Waiting For Lock B

Thread B
   |
Waiting For Lock A
```

Result:

```text
Nobody Moves
```

---

## Livelock

```text
Thread A
   |
React To B

Thread B
   |
React To A
```

Result:

```text
Everybody Moves

Nobody Progresses
```

---

# Deadlock vs Livelock Analogy

## Deadlock

Two cars arrive at a one-lane bridge.

Neither moves.

```text
Stopped Forever
```

---

## Livelock

Two cars continuously reverse and move forward to give way to each other.

```text
Constant Movement
No Crossing
```

---

# Deadlock vs Livelock vs Starvation

| Feature | Deadlock | Livelock | Starvation |
|----------|-----------|-----------|------------|
| Threads Active | No | Yes | Yes |
| Progress Made | No | No | Some Threads Yes |
| Waiting Forever | Yes | No | Possible |
| CPU Usage | Low | High | Variable |
| Cause | Circular Dependency | Continuous Reaction | Resource Unfairness |

---

# Real-World Example

Suppose two microservices attempt to update the same record.

---

### Service A

Detects conflict.

Retries.

---

### Service B

Detects conflict.

Retries.

---

### Service A

Detects conflict again.

Retries.

---

### Service B

Retries again.

---

Result:

```text
Continuous Retries

No Update Completed
```

This is a livelock.

---

# Common Interview Questions

## Q1: What is Livelock?

```text
A situation where threads remain active and continuously react to each other but fail to make progress.
```

---

## Q2: Are threads blocked in a livelock?

```text
No
```

Threads continue running.

---

## Q3: Does livelock consume CPU?

```text
Yes
```

Usually much more than deadlock.

---

## Q4: What is the biggest difference between deadlock and livelock?

### Deadlock

```text
Threads Waiting
```

---

### Livelock

```text
Threads Running
```

But neither completes work.

---

## Q5: Why is livelock difficult to detect?

Because:

```text
Threads Are Alive
CPU Is Active
Application Appears Healthy
```

---

## Q6: How can livelock be prevented?

- Random Backoff
- Retry Limits
- Exponential Backoff
- Proper Synchronization
- Concurrent Utilities

---

## Q7: Is livelock better than deadlock?

Not really.

Both prevent useful work from being completed.

---

# Key Takeaways

1. Livelock occurs when threads remain active but fail to make progress.
2. Unlike deadlock, threads are not blocked; they continuously react to each other.
3. Livelock often results in high CPU usage and low throughput.
4. The application appears alive, making livelock harder to detect than deadlock.
5. Overly cooperative or poorly designed retry mechanisms are common causes.
6. Random backoff and exponential backoff are effective livelock prevention techniques.
7. Deadlock means "waiting forever"; livelock means "working forever without progress."
8. Both deadlock and livelock are concurrency problems that can severely impact application performance and reliability.
9. Modern concurrency utilities help reduce the likelihood of livelock situations.
10. Understanding livelock is essential for designing robust multithreaded and distributed systems.