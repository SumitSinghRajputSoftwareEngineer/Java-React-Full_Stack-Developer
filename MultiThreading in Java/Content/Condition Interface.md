# 16. Condition Interface

When learning synchronization, you already studied:

```java
wait()
notify()
notifyAll()
```

These methods allow threads to communicate and coordinate with each other.

However, they have several limitations:

- Tightly coupled with `synchronized`
- Only one wait-set per object
- Difficult to manage multiple conditions
- Less flexible than modern concurrency utilities

To solve these limitations, Java introduced:

```java
Condition
```

which works together with:

```java
Lock
```

and provides a more powerful mechanism for thread coordination.

---

# What is Condition?

## Definition

A `Condition` is an object that allows threads to:

```text
Wait
Signal
Notify
Coordinate
```

while working with explicit locks.

---

## Package

```java
java.util.concurrent.locks.Condition
```

---

## Relationship

```text
Lock
   |
   v
Condition
```

A `Condition` is always associated with a `Lock`.

---

## Simple Definition

```text
Condition is to Lock

what

wait()/notify()

is to synchronized
```

---

## Analogy

Think of:

```java
synchronized + wait()/notify()
```

as:

```java
Lock + Condition
```

---

# Creating a Condition

## Example

```java
Lock lock =
        new ReentrantLock();

Condition condition =
        lock.newCondition();
```

---

## Explanation

### Step 1

Create lock.

```java
ReentrantLock
```

---

### Step 2

Create condition associated with that lock.

```java
lock.newCondition();
```

---

## Important Rule

A thread must own the lock before calling:

```java
await()
signal()
signalAll()
```

Otherwise:

```java
IllegalMonitorStateException
```

or equivalent lock-state errors may occur.

---

# Why Do We Need Condition?

Suppose we have:

```text
Producer Thread
Consumer Thread
```

---

Consumer wants:

```text
Wait Until Data Available
```

---

Producer wants:

```text
Notify Consumer
```

---

Using:

```java
wait()
notify()
```

works.

---

But what if we have:

```text
Condition A

Condition B

Condition C
```

---

With `wait()`:

```text
Single Wait Queue
```

---

With `Condition`:

```text
Multiple Independent Wait Queues
```

---

Result:

```text
Better Coordination
```

---

# await()

## Definition

Causes the current thread to:

```text
Release Lock
Wait
```

until another thread signals it.

---

## Similar To

```java
wait()
```

---

## Syntax

```java
condition.await();
```

---

# Example

```java
lock.lock();

try {

    while(!dataAvailable) {

        condition.await();
    }

    processData();

}
finally {

    lock.unlock();
}
```

---

## What Happens Internally?

### Step 1

Thread acquires lock.

---

### Step 2

Calls:

```java
await()
```

---

### Step 3

Lock released automatically.

---

### Step 4

Thread enters waiting state.

---

### Step 5

Another thread signals.

---

### Step 6

Thread wakes up.

---

### Step 7

Thread re-acquires lock.

---

### Step 8

Execution continues.

---

# Visualization

```text
Thread
  |
Acquire Lock
  |
await()
  |
Release Lock
  |
WAITING
  |
signal()
  |
Wake Up
  |
Reacquire Lock
  |
Continue
```

---

# InterruptedException

Like:

```java
wait()
```

`await()` can throw:

```java
InterruptedException
```

---

## Example

```java
try {

    condition.await();

}
catch(InterruptedException e) {

    Thread.currentThread()
          .interrupt();
}
```

---

## Best Practice

Restore interrupt status.

```java
Thread.currentThread()
      .interrupt();
```

---

# Timed await()

Condition supports waiting with timeout.

---

## Example

```java
condition.await(
        5,
        TimeUnit.SECONDS
);
```

---

## Benefit

Avoids:

```text
Infinite Waiting
```

---

# signal()

## Definition

Wakes up:

```text
One Waiting Thread
```

associated with the condition.

---

## Similar To

```java
notify()
```

---

## Syntax

```java
condition.signal();
```

---

## Example

```java
lock.lock();

try {

    dataAvailable = true;

    condition.signal();

}
finally {

    lock.unlock();
}
```

---

## Behavior

Suppose:

```text
Thread A Waiting

Thread B Waiting

Thread C Waiting
```

---

Call:

```java
signal();
```

---

Result:

```text
One Thread Wakes Up
```

---

## Which Thread?

Not guaranteed.

JVM decides.

---

# Visualization

```text
Waiting Queue

A
B
C

signal()

     |
     v

A Wakes Up
```

---

# signalAll()

## Definition

Wakes up:

```text
All Waiting Threads
```

associated with the condition.

---

## Similar To

```java
notifyAll()
```

---

## Syntax

```java
condition.signalAll();
```

---

## Example

```java
lock.lock();

try {

    dataAvailable = true;

    condition.signalAll();

}
finally {

    lock.unlock();
}
```

---

# Behavior

Waiting Threads

```text
A
B
C
```

---

Call:

```java
signalAll()
```

---

Result:

```text
A Wakes Up
B Wakes Up
C Wakes Up
```

---

## Important

Only one thread can acquire the lock at a time.

Therefore:

```text
All Wake Up

But Execute One By One
```

---

# Producer Consumer Example

One of the most common uses of `Condition`.

---

## Example

```java
class SharedBuffer {

    private Queue<Integer> queue =
            new LinkedList<>();

    private Lock lock =
            new ReentrantLock();

    private Condition notEmpty =
            lock.newCondition();

    public void consume()
            throws InterruptedException {

        lock.lock();

        try {

            while(queue.isEmpty()) {

                notEmpty.await();
            }

            System.out.println(
                    queue.remove()
            );

        }
        finally {

            lock.unlock();
        }
    }

    public void produce(int value) {

        lock.lock();

        try {

            queue.add(value);

            notEmpty.signal();

        }
        finally {

            lock.unlock();
        }
    }
}
```

---

# Why Use while Instead of if?

Bad

```java
if(queue.isEmpty()) {

    condition.await();
}
```

---

Good

```java
while(queue.isEmpty()) {

    condition.await();
}
```

---

Reason:

```text
Spurious Wakeups
```

can occur.

---

# Spurious Wakeup

## Definition

A thread may wake up from:

```java
await()
```

without receiving:

```java
signal()
```

---

## Rare But Possible

Therefore:

Always use:

```java
while(conditionNotSatisfied)
```

---

Never:

```java
if(conditionNotSatisfied)
```

---

# Multiple Conditions

Major advantage over:

```java
wait()/notify()
```

---

## Example

```java
Condition notFull;
Condition notEmpty;
```

---

Producer waits on:

```java
notFull
```

---

Consumer waits on:

```java
notEmpty
```

---

Result:

```text
Precise Notification
```

---

Instead of waking unnecessary threads.

---

# Condition vs wait/notify

One of the most important interview questions.

---

| Feature | Condition | wait()/notify() |
|-----------|-----------|-----------|
| Works With | Lock | synchronized |
| Multiple Wait Queues | Yes | No |
| Explicit Locking | Yes | No |
| Flexibility | High | Low |
| Readability | Better | Lower |
| Timed Waiting | Yes | Yes |
| Interrupt Support | Yes | Yes |
| Fine-Grained Signaling | Yes | No |

---

# Example Comparison

## wait()/notify()

```java
synchronized(lock) {

    while(!ready) {

        lock.wait();
    }
}
```

---

## Condition

```java
lock.lock();

try {

    while(!ready) {

        condition.await();
    }

}
finally {

    lock.unlock();
}
```

---

# Advantages of Condition

## 1. Multiple Conditions

Example

```java
Condition notFull;
Condition notEmpty;
```

---

## 2. Better Readability

Coordination logic becomes clearer.

---

## 3. Fine-Grained Notification

Wake only required threads.

---

## 4. Better Performance

Less unnecessary wake-up activity.

---

## 5. Works With Lock Framework

Integrates naturally with:

```java
ReentrantLock
```

---

# Common Mistakes

---

# Mistake 1

Calling await() Without Lock

Bad

```java
condition.await();
```

without:

```java
lock.lock();
```

---

Result:

```text
IllegalMonitorStateException
```

or lock state error.

---

# Mistake 2

Forgetting unlock()

Bad

```java
lock.lock();

condition.await();
```

---

Always:

```java
try-finally
```

---

# Mistake 3

Using if Instead Of while

Bad

```java
if(queue.isEmpty())
```

---

Good

```java
while(queue.isEmpty())
```

---

# Mistake 4

Calling signal() Without Lock

Bad

```java
condition.signal();
```

---

Must hold lock first.

---

# Condition Lifecycle

```text
Acquire Lock
      |
      v

Condition Check
      |
      v

await()
      |
      v

WAITING
      |
      v

signal()/signalAll()
      |
      v

Wake Up
      |
      v

Reacquire Lock
      |
      v

Continue Execution
```

---

# Common Interview Questions

## Q1: What is Condition?

```text
A thread coordination mechanism used with Lock objects, similar to wait()/notify().
```

---

## Q2: Which package contains Condition?

```java
java.util.concurrent.locks
```

---

## Q3: What is await()?

Releases the lock and puts the thread into waiting state until signaled.

---

## Q4: What is signal()?

Wakes one waiting thread.

---

## Q5: What is signalAll()?

Wakes all waiting threads.

---

## Q6: What is the biggest advantage of Condition over wait()/notify()?

```text
Multiple Independent Wait Queues
```

---

## Q7: Must the lock be held before calling await()?

```text
Yes
```

---

## Q8: Why should await() be used inside a while loop?

Because:

```text
Spurious Wakeups
```

may occur.

---

## Q9: Does await() release the lock?

```text
Yes
```

temporarily.

---

## Q10: What happens after signal()?

The waiting thread wakes up and attempts to re-acquire the lock before continuing.

---

# Key Takeaways

1. `Condition` provides a modern alternative to `wait()`, `notify()`, and `notifyAll()`.
2. A `Condition` is always associated with a `Lock`.
3. `await()` releases the lock and places the thread into a waiting state.
4. `signal()` wakes one waiting thread, while `signalAll()` wakes all waiting threads.
5. A thread must hold the lock before calling `await()`, `signal()`, or `signalAll()`.
6. `Condition` supports multiple independent waiting queues, unlike intrinsic monitor methods.
7. Always use `while` loops around `await()` to protect against spurious wakeups.
8. `Condition` improves readability and provides finer control over thread coordination.
9. It integrates naturally with `ReentrantLock` and the Locks Framework.
10. `Condition` is widely used in advanced concurrent utilities such as blocking queues and thread coordination mechanisms.
11. Understanding `Condition` is essential before learning Semaphores, BlockingQueue, Executor Framework internals, and advanced concurrency patterns.