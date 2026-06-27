
Synchronization solves the problem of **mutual exclusion**, but sometimes threads need to **communicate** with each other.

Consider the following situations:

- A Producer thread generates data.
- A Consumer thread processes data.
- A thread waits until some condition becomes true.
- A thread must pause until another thread completes a task.

In such cases, simply using `synchronized` is not enough.

Java provides **Inter-Thread Communication** mechanisms through:

```java
wait()
notify()
notifyAll()
```

These methods are defined in:

```java
java.lang.Object
```

---

# What is Inter-Thread Communication?

## Definition

Inter-Thread Communication is a mechanism that allows threads to coordinate execution by waiting for events and notifying other threads when those events occur.

---

## Purpose

Instead of:

```java
while(condition == false) {
    // continuously check
}
```

(which wastes CPU)

A thread can:

```java
wait()
```

and sleep efficiently until notified.

---

## Real-World Analogy

Imagine a restaurant:

### Chef

Produces food.

### Waiter

Serves food.

If food is not ready:

```text
Waiter waits.
```

When food is prepared:

```text
Chef notifies waiter.
```

This is exactly how inter-thread communication works.

---

# wait()

## Definition

The `wait()` method causes the current thread to release the monitor lock and enter the WAITING state until another thread notifies it.

---

## Method Signatures

```java
public final void wait()
        throws InterruptedException
```

```java
public final void wait(long timeout)
        throws InterruptedException
```

```java
public final void wait(long timeout, int nanos)
        throws InterruptedException
```

---

## Important Behavior

When a thread calls:

```java
wait();
```

it:

### Releases Lock

```text
YES
```

---

### Stops Execution

```text
YES
```

---

### Enters WAITING State

```text
YES
```

---

## Example

```java
synchronized(obj) {

    obj.wait();
}
```

---

## Internal Flow

```text
Thread Acquires Lock
         |
         v
      wait()
         |
         v
 Releases Lock
         |
         v
 WAITING State
```

---

## Example

```java
class Shared {

    synchronized void execute()
            throws InterruptedException {

        System.out.println("Waiting");

        wait();

        System.out.println("Resumed");
    }
}
```

---

## State Transition

```text
RUNNING
   |
wait()
   |
WAITING
   |
notify()
   |
RUNNABLE
```

---

# Why Does wait() Release the Lock?

Suppose:

```text
Thread A
```

is waiting for:

```text
Thread B
```

to update data.

If Thread A keeps the lock while waiting:

```text
Thread B can never acquire lock.
```

Result:

```text
Deadlock
```

Therefore:

```java
wait()
```

automatically releases the lock.

---

# notify()

## Definition

The `notify()` method wakes up one thread waiting on the object's monitor.

---

## Method Signature

```java
public final void notify()
```

---

## Important Behavior

When:

```java
notify();
```

is called:

```text
One waiting thread
becomes eligible to run.
```

---

## Important Point

It does NOT immediately run.

It only moves from:

```text
WAITING
```

to:

```text
RUNNABLE
```

The scheduler decides when it executes.

---

## Example

```java
synchronized(obj) {

    obj.notify();
}
```

---

## Flow

```text
Thread A Waiting
       |
notify()
       |
RUNNABLE
       |
Scheduler
       |
Running
```

---

## Example

```java
class Shared {

    synchronized void produce() {

        notify();
    }
}
```

---

# notifyAll()

## Definition

The `notifyAll()` method wakes up all threads waiting on the object's monitor.

---

## Method Signature

```java
public final void notifyAll()
```

---

## Example

```java
synchronized(obj) {

    obj.notifyAll();
}
```

---

## Flow

Before:

```text
Thread A WAITING
Thread B WAITING
Thread C WAITING
```

After:

```text
notifyAll()
```

```text
Thread A RUNNABLE
Thread B RUNNABLE
Thread C RUNNABLE
```

---

## Important Point

Only one thread can acquire the lock first.

Others continue waiting for lock availability.

---

## Visualization

```text
WAITING THREADS

A
B
C

notifyAll()

RUNNABLE

A
B
C
```

---

# notify() vs notifyAll()

| Feature | notify() | notifyAll() |
|----------|----------|-------------|
| Threads Woken | One | All |
| Performance | Better | Lower |
| Risk of Missing Thread | Higher | Lower |
| Common Usage | Rare | Preferred |
| Deadlock Risk | Higher | Lower |

---

## Why notifyAll() is Preferred?

Suppose:

```text
Producer Waiting
Consumer Waiting
```

Using:

```java
notify()
```

may wake the wrong thread.

Using:

```java
notifyAll()
```

ensures every waiting thread gets a chance to recheck conditions.

---

# Producer Consumer Problem

## Definition

The Producer-Consumer Problem is one of the most famous inter-thread communication problems.

---

## Scenario

### Producer

Produces items.

---

### Consumer

Consumes items.

---

## Shared Buffer

```text
Buffer Size = 1
```

---

### Producer Rule

Cannot produce if buffer is full.

---

### Consumer Rule

Cannot consume if buffer is empty.

---

## Visualization

```text
Producer
    |
Produces Data
    |
Buffer
    |
Consumes Data
    |
Consumer
```

---

# Problem Without Communication

Suppose:

```text
Buffer Empty
```

Consumer tries:

```text
Consume Data
```

No data exists.

---

Suppose:

```text
Buffer Full
```

Producer tries:

```text
Produce Data
```

No space exists.

---

Need coordination.

---

# Producer Consumer Solution

```java
class SharedBuffer {

    private int item;
    private boolean available = false;

    public synchronized void produce(int value)
            throws InterruptedException {

        while(available) {

            wait();
        }

        item = value;
        available = true;

        notifyAll();
    }

    public synchronized int consume()
            throws InterruptedException {

        while(!available) {

            wait();
        }

        available = false;

        notifyAll();

        return item;
    }
}
```

---

# Producer Flow

```text
Buffer Full?
     |
     +-- Yes --> wait()
     |
     +-- No
           |
           v
       Produce Item
           |
           v
      notifyAll()
```

---

# Consumer Flow

```text
Buffer Empty?
      |
      +-- Yes --> wait()
      |
      +-- No
            |
            v
        Consume Item
            |
            v
       notifyAll()
```

---

# Communication Flow

## Complete Lifecycle

```text
Thread A
    |
wait()
    |
WAITING
    |
notify()/notifyAll()
    |
RUNNABLE
    |
Lock Acquired
    |
Continue Execution
```

---

## Detailed Flow

```text
Acquire Monitor
       |
       v
Condition False?
       |
      Yes
       |
       v
wait()
       |
Release Lock
       |
WAITING
       |
notify()
       |
RUNNABLE
       |
Acquire Lock Again
       |
Continue
```

---

# Important Rule

## wait(), notify(), notifyAll() Must Be Called Inside Synchronized Context

Correct:

```java
synchronized(obj) {

    obj.wait();
}
```

---

Wrong:

```java
obj.wait();
```

---

Reason:

```text
Thread must own monitor lock.
```

---

# Common Mistakes

---

# IllegalMonitorStateException

## Definition

Thrown when a thread calls:

```java
wait()
notify()
notifyAll()
```

without owning the monitor lock.

---

## Wrong Example

```java
Object obj = new Object();

obj.wait();
```

---

Output

```text
IllegalMonitorStateException
```

---

## Correct Example

```java
synchronized(obj) {

    obj.wait();
}
```

---

## Why?

The thread must own:

```text
obj's monitor lock
```

before calling these methods.

---

# Lost Notifications

## Definition

Occurs when notification happens before another thread starts waiting.

---

## Example

### Thread A

```java
notify();
```

---

### Thread B

```java
wait();
```

---

Problem:

```text
Notification already lost.
```

Thread B waits forever.

---

## Timeline

```text
notify()
     |
     v
(no thread waiting)

Later

wait()
     |
     v
WAIT FOREVER
```

---

## Solution

Use condition variables and proper checks.

---

### Correct Pattern

```java
while(!condition) {

    wait();
}
```

---

# Spurious Wakeups

## Definition

A thread may wake up from `wait()` without receiving:

```java
notify()
```

or

```java
notifyAll()
```

---

## Important Interview Point

The Java specification explicitly allows:

```text
Spurious Wakeups
```

---

## Example

```java
wait();
```

Thread wakes unexpectedly.

---

## Why Is This Dangerous?

Suppose:

```java
wait();
```

returns unexpectedly.

Thread continues even though:

```text
Required condition is false.
```

---

# Wrong Pattern

```java
if(!condition) {

    wait();
}
```

---

## Problem

Spurious wakeup occurs.

Condition remains false.

Execution continues incorrectly.

---

# Correct Pattern

Always use:

```java
while(!condition) {

    wait();
}
```

---

## Why?

After wakeup:

```text
Condition checked again.
```

---

## Recommended Pattern

```java
synchronized(lock) {

    while(!condition) {

        lock.wait();
    }

    // perform work
}
```

---

# Best Practice Pattern

```java
synchronized(lock) {

    while(!condition) {

        lock.wait();
    }

    performWork();
}
```

---

## Why While Instead of If?

Protects against:

- Spurious Wakeups
- Lost Notifications
- Multiple Waiting Threads

---

# wait() vs sleep()

| Feature | wait() | sleep() |
|----------|---------|---------|
| Defined In | Object | Thread |
| Releases Lock | Yes | No |
| Requires Synchronization | Yes | No |
| State | WAITING | TIMED_WAITING |
| Notification Required | Yes | No |
| Used For | Communication | Delay |

---

# Common Interview Questions

## Q1: Why are wait(), notify(), and notifyAll() defined in Object instead of Thread?

Because communication occurs using:

```text
Object Monitors
```

not individual threads.

---

## Q2: Does wait() release the lock?

```text
Yes
```

---

## Q3: Does sleep() release the lock?

```text
No
```

---

## Q4: Can wait() be called outside synchronized block?

```text
No
```

Throws:

```java
IllegalMonitorStateException
```

---

## Q5: What is the difference between notify() and notifyAll()?

```text
notify()
```

wakes one waiting thread.

```text
notifyAll()
```

wakes all waiting threads.

---

## Q6: Why should wait() be used inside a while loop?

To protect against:

- Spurious Wakeups
- Lost Notifications
- Condition Changes

---

## Q7: What is the Producer-Consumer Problem?

A classic synchronization problem where producers generate data and consumers process it using a shared buffer.

---

## Q8: What state does wait() put a thread into?

```text
WAITING
```

or

```text
TIMED_WAITING
```

for timeout versions.

---

# Key Takeaways

1. Inter-Thread Communication allows threads to coordinate execution.
2. `wait()` releases the monitor lock and puts the thread into the WAITING state.
3. `notify()` wakes one waiting thread.
4. `notifyAll()` wakes all waiting threads.
5. `wait()`, `notify()`, and `notifyAll()` are methods of `Object`.
6. These methods must always be called inside synchronized code.
7. The Producer-Consumer Problem is the classic use case for inter-thread communication.
8. `IllegalMonitorStateException` occurs when a thread does not own the monitor lock.
9. Lost Notifications occur when notification happens before waiting begins.
10. Spurious Wakeups are allowed by the JVM and must be handled correctly.
11. Always use `while(condition == false) wait();` instead of `if(condition == false) wait();`.
12. Modern applications often use higher-level constructs such as `BlockingQueue`, `CountDownLatch`, `Semaphore`, and `CompletableFuture`, but understanding `wait()` and `notify()` is essential for mastering Java concurrency.