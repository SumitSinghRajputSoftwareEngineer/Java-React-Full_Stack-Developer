
Deadlock is one of the most dangerous and frequently asked topics in Java Multithreading interviews.

A multithreaded application may be perfectly synchronized, but if locks are acquired incorrectly, threads can become permanently blocked.

The result:

```text
Application Stops Making Progress
```

even though threads are still alive.

---

# What is Deadlock?

## Definition

A Deadlock is a situation where two or more threads are waiting indefinitely for each other to release resources, and none of them can proceed.

---

## Simple Definition

```text
Thread A waits for Thread B

Thread B waits for Thread A
```

Neither thread can continue.

---

## Visualization

```text
Thread A
   |
Waiting for Lock B

Thread B
   |
Waiting for Lock A
```

Result:

```text
Forever Waiting
```

---

## Real-World Analogy

Imagine:

### Person A

Has:

```text
Pen
```

Needs:

```text
Notebook
```

---

### Person B

Has:

```text
Notebook
```

Needs:

```text
Pen
```

---

Situation:

```text
Person A waits for Notebook

Person B waits for Pen
```

Neither releases what they already hold.

Result:

```text
Deadlock
```

---

# Why Deadlocks Are Dangerous

Deadlocks cause:

- Application hangs
- Resource wastage
- Poor user experience
- System downtime
- Difficult debugging

---

## Symptoms

```text
CPU Usage Low

Threads Alive

Application Frozen
```

---

# Conditions for Deadlock

Deadlock can occur only when all four conditions are present simultaneously.

These are called:

```text
Coffman Conditions
```

---

# 1. Mutual Exclusion

## Definition

A resource can be used by only one thread at a time.

---

## Example

```java
synchronized(lock) {

}
```

Only one thread can hold the lock.

---

## Visualization

```text
Lock
 |
 +-- Thread A

Thread B waits
```

---

## Why Necessary?

If multiple threads could use the same resource simultaneously:

```text
Deadlock Impossible
```

---

# 2. Hold and Wait

## Definition

A thread holds at least one resource while waiting for another resource.

---

## Example

### Thread A

```text
Holding Lock1
Waiting For Lock2
```

---

### Thread B

```text
Holding Lock2
Waiting For Lock1
```

---

## Visualization

```text
Thread A
   |
Lock1 Acquired
   |
Waiting Lock2
```

---

Deadlock becomes possible.

---

# 3. No Preemption

## Definition

A resource cannot be forcibly taken away from a thread.

---

## Example

Suppose:

```text
Thread A owns Lock1
```

Java cannot do:

```text
Force Release Lock1
```

---

The thread must release it voluntarily.

---

## Why Important?

If locks could be forcibly removed:

```text
Deadlock could be broken.
```

---

# 4. Circular Wait

## Definition

A circular chain exists where each thread waits for a resource held by another thread.

---

## Example

```text
Thread A -> Waiting For Lock2

Thread B -> Waiting For Lock1
```

---

## Visualization

```text
Thread A
   |
Needs Lock2
   |
Held By
   |
Thread B
   |
Needs Lock1
   |
Held By
   |
Thread A
```

---

Cycle formed:

```text
A -> B -> A
```

---

## Circular Wait Diagram

```text
Thread A
   |
   v
 Lock2

Thread B
   |
   v
 Lock1

Back To Thread A
```

---

# Summary of Deadlock Conditions

| Condition | Meaning |
|------------|----------|
| Mutual Exclusion | Resource used by one thread only |
| Hold and Wait | Thread holds one resource while waiting for another |
| No Preemption | Resource cannot be forcibly removed |
| Circular Wait | Circular dependency exists |

---

## Important Interview Point

Deadlock occurs only when:

```text
ALL FOUR CONDITIONS
exist simultaneously.
```

Removing any one condition prevents deadlock.

---

# Deadlock Example

## Example Code

```java
public class DeadlockExample {

    private static final Object lock1 =
            new Object();

    private static final Object lock2 =
            new Object();

    public static void main(String[] args) {

        Thread t1 = new Thread(() -> {

            synchronized(lock1) {

                System.out.println(
                        "T1 acquired Lock1"
                );

                try {
                    Thread.sleep(100);
                }
                catch(Exception e) {
                }

                synchronized(lock2) {

                    System.out.println(
                            "T1 acquired Lock2"
                    );
                }
            }
        });

        Thread t2 = new Thread(() -> {

            synchronized(lock2) {

                System.out.println(
                        "T2 acquired Lock2"
                );

                try {
                    Thread.sleep(100);
                }
                catch(Exception e) {
                }

                synchronized(lock1) {

                    System.out.println(
                            "T2 acquired Lock1"
                    );
                }
            }
        });

        t1.start();
        t2.start();
    }
}
```

---

## Execution

### Thread T1

```text
Acquire Lock1
Sleep
Wait For Lock2
```

---

### Thread T2

```text
Acquire Lock2
Sleep
Wait For Lock1
```

---

## Result

```text
T1 waiting for Lock2

T2 waiting for Lock1
```

Neither proceeds.

---

## Output

```text
T1 acquired Lock1
T2 acquired Lock2
```

Application hangs forever.

---

# Deadlock Visualization

```text
T1
 |
Lock1 Acquired
 |
Waiting Lock2
 |
Held By T2

------------------

T2
 |
Lock2 Acquired
 |
Waiting Lock1
 |
Held By T1
```

---

# Thread States During Deadlock

```text
T1 -> BLOCKED

T2 -> BLOCKED
```

---

Both threads remain blocked indefinitely.

---

# Deadlock Detection

## Definition

Deadlock Detection is the process of identifying threads that are permanently waiting due to circular dependencies.

---

## How JVM Detects Deadlocks?

Using:

```text
ThreadMXBean
```

from:

```java
java.lang.management
```

---

## Example

```java
ThreadMXBean bean =
    ManagementFactory.getThreadMXBean();

long[] ids =
    bean.findDeadlockedThreads();
```

---

If:

```text
ids != null
```

deadlock exists.

---

# Detecting Deadlock Using jstack

Java provides:

```bash
jstack <process-id>
```

---

Example:

```bash
jstack 12345
```

---

Output may contain:

```text
Found one Java-level deadlock
```

---

## Information Provided

- Thread Names
- Lock Ownership
- Waiting Threads
- Lock Dependencies

---

# Detecting Deadlock Using VisualVM

Tools:

- VisualVM
- JConsole
- Java Mission Control

can detect deadlocked threads graphically.

---

# Deadlock Prevention

## Definition

Deadlock Prevention ensures that at least one of the four deadlock conditions can never occur.

---

## Principle

```text
Break Any One Condition
=
No Deadlock
```

---

# Prevention Strategy 1

## Consistent Lock Ordering

Always acquire locks in the same order.

---

### Correct

```text
Thread A:
Lock1 -> Lock2

Thread B:
Lock1 -> Lock2
```

---

### Incorrect

```text
Thread A:
Lock1 -> Lock2

Thread B:
Lock2 -> Lock1
```

---

## Example

```java
synchronized(lock1) {

    synchronized(lock2) {

    }
}
```

All threads follow same order.

---

## Most Common Industry Solution

```text
Global Lock Ordering
```

---

# Prevention Strategy 2

## Reduce Nested Locks

Avoid:

```java
synchronized(lock1) {

    synchronized(lock2) {

    }
}
```

whenever possible.

---

## Benefit

Reduces lock dependency complexity.

---

# Prevention Strategy 3

## Minimize Lock Scope

Bad:

```java
synchronized(lock) {

    longRunningOperation();
}
```

---

Better:

```java
synchronized(lock) {

    updateSharedState();
}
```

---

## Benefit

Less lock contention.

---

# Prevention Strategy 4

## Use Timeout-Based Locking

Possible using:

```java
ReentrantLock
```

---

Example:

```java
lock.tryLock(
        5,
        TimeUnit.SECONDS
);
```

---

If lock unavailable:

```text
Proceed Without Waiting Forever
```

---

# Deadlock Avoidance

## Definition

Deadlock Avoidance dynamically decides whether a resource allocation request should be granted.

---

## Goal

Avoid entering an unsafe state.

---

## Difference from Prevention

### Prevention

```text
Break Deadlock Conditions
```

---

### Avoidance

```text
Analyze Future Possibilities
Before Granting Resource
```

---

# Banker's Algorithm

The most famous deadlock avoidance algorithm.

Created by:

:contentReference[oaicite:0]{index=0}

---

## Principle

Before allocating resources:

```text
Can the system still complete safely?
```

---

### If Safe

Grant request.

---

### If Unsafe

Reject request.

---

# Safe State

## Definition

A state where all threads/processes can eventually complete.

---

## Example

```text
Resources Available

Enough For Every Thread

Safe State
```

---

# Unsafe State

## Definition

A state that may lead to deadlock in the future.

---

## Important Point

```text
Unsafe State
≠
Deadlock
```

But:

```text
Deadlock can occur later.
```

---

# Deadlock Prevention vs Avoidance

| Feature | Prevention | Avoidance |
|----------|------------|------------|
| Strategy | Break Conditions | Analyze Requests |
| Complexity | Simple | Complex |
| Runtime Cost | Low | Higher |
| Resource Utilization | Lower | Better |
| Example | Lock Ordering | Banker's Algorithm |

---

# Real-World Example

Suppose an online banking application has:

```text
Account A Lock
Account B Lock
```

---

### Transfer 1

```text
A -> B
```

---

### Transfer 2

```text
B -> A
```

Without lock ordering:

```text
Deadlock Possible
```

---

Solution:

Always lock:

```text
Lower Account ID First
```

Then:

```text
Deadlock Prevented
```

---

# Common Interview Questions

## Q1: What is Deadlock?

```text
A situation where threads wait indefinitely for resources held by each other.
```

---

## Q2: What are the four conditions for Deadlock?

1. Mutual Exclusion
2. Hold and Wait
3. No Preemption
4. Circular Wait

---

## Q3: Can Deadlock occur with one thread?

```text
No
```

Deadlock requires resource dependency involving multiple lock acquisitions.

---

## Q4: Which thread state is commonly seen during deadlock?

```text
BLOCKED
```

---

## Q5: What is the best way to prevent deadlock?

```text
Consistent Lock Ordering
```

---

## Q6: What tool can detect deadlock?

- `jstack`
- `VisualVM`
- `JConsole`
- `ThreadMXBean`

---

## Q7: What is the difference between Deadlock Prevention and Avoidance?

### Prevention

```text
Break one deadlock condition.
```

### Avoidance

```text
Analyze resource allocation before granting it.
```

---

## Q8: What is the most famous deadlock avoidance algorithm?

```text
Banker's Algorithm
```

---

# Key Takeaways

1. Deadlock occurs when threads wait indefinitely for resources held by each other.
2. Deadlocks can freeze an application while threads remain alive.
3. Four conditions are required for deadlock: Mutual Exclusion, Hold and Wait, No Preemption, and Circular Wait.
4. Removing any one of these conditions prevents deadlock.
5. In Java, deadlocks commonly occur because of incorrect lock acquisition order.
6. Consistent lock ordering is the most effective and commonly used prevention strategy.
7. JVM tools such as `ThreadMXBean`, `jstack`, `VisualVM`, and `JConsole` can detect deadlocks.
8. Deadlock Prevention eliminates a deadlock condition.
9. Deadlock Avoidance evaluates resource allocation requests before granting them.
10. The Banker's Algorithm is the classical deadlock avoidance algorithm.
11. Deadlocks are easier to prevent than to debug in production systems.
12. Designing lock acquisition strategies early is one of the most important practices in concurrent application development.