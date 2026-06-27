
A thread does not start running immediately after it is created. During its execution, it moves through various states defined by the JVM.

Understanding the Thread Lifecycle is extremely important because concepts such as:

- Synchronization
- Locks
- wait()
- notify()
- join()
- sleep()
- Deadlocks

are all closely related to thread states.

---

# What is Thread Lifecycle?

Thread Lifecycle refers to the various states through which a thread passes from its creation until its termination.

In Java, these states are defined by:

```java
java.lang.Thread.State
```

---

## Thread.State Enum

```java
public enum Thread.State {

    NEW,
    RUNNABLE,
    BLOCKED,
    WAITING,
    TIMED_WAITING,
    TERMINATED
}
```

---

## Important Interview Point

Many resources mention a **RUNNING** state.

However:

> RUNNING is a conceptual state, not an official value in `Thread.State`.

Official Java states are:

```text
NEW
RUNNABLE
BLOCKED
WAITING
TIMED_WAITING
TERMINATED
```

The JVM includes both "ready to run" and "currently running" inside:

```text
RUNNABLE
```

For learning purposes, we often discuss RUNNING separately.

---

# Complete Thread Lifecycle

```text
                NEW
                 |
              start()
                 |
                 v
            RUNNABLE
                 |
        Scheduler Selects
                 |
                 v
             RUNNING
            /   |   \
           /    |    \
          /     |     \
         v      v      v
   BLOCKED  WAITING TIMED_WAITING
         \      |      /
          \     |     /
           \    |    /
              RUNNABLE
                 |
                 v
            TERMINATED
```

---

# NEW State

## Definition

A thread is in the NEW state after the Thread object is created but before `start()` is called.

---

## Example

```java
Thread t = new Thread();
```

At this point:

```text
State = NEW
```

---

## Verification

```java
Thread t = new Thread();

System.out.println(t.getState());
```

Output:

```text
NEW
```

---

## Characteristics

- Thread object exists
- JVM thread not created yet
- Scheduler not involved
- Execution has not started

---

## Diagram

```text
Thread t = new Thread()
           |
           v
          NEW
```

---

## What Operations Are Allowed?

```java
t.start();
```

Allowed.

---

## What Happens If start() Is Never Called?

The thread remains forever in:

```text
NEW
```

and never executes.

---

# RUNNABLE State

## Definition

A thread enters RUNNABLE state after:

```java
start()
```

is invoked.

---

## Example

```java
Thread t = new Thread();

t.start();
```

State becomes:

```text
RUNNABLE
```

---

## Meaning

RUNNABLE means:

> Thread is ready to execute and waiting for CPU allocation.

or

> Thread may already be executing.

---

## Important Interview Point

Many developers assume:

```text
RUNNABLE = Running
```

This is incorrect.

RUNNABLE includes:

```text
Ready To Run
+
Actually Running
```

---

## Diagram

```text
NEW
 |
start()
 |
 v
RUNNABLE
```

---

## Characteristics

- Eligible for execution
- Waiting for CPU scheduling
- May or may not currently be executing

---

# RUNNING State (Conceptual)

## Definition

RUNNING refers to the period when CPU is actively executing thread instructions.

---

## Example

```java
public void run() {

    System.out.println("Running");
}
```

When CPU executes this code:

```text
Thread is RUNNING
```

---

## Important Note

Java does NOT provide:

```java
Thread.State.RUNNING
```

Instead:

```text
RUNNING is part of RUNNABLE
```

internally.

---

## Flow

```text
RUNNABLE
    |
Scheduler Chooses Thread
    |
    v
RUNNING
```

---

## Example

Suppose three threads exist:

```text
Thread A
Thread B
Thread C
```

Only one thread per CPU core can actually be:

```text
RUNNING
```

at a given instant.

---

# BLOCKED State

## Definition

A thread enters BLOCKED state when it is waiting to acquire a monitor lock.

---

## Most Common Cause

Synchronization.

---

## Example

```java
synchronized(obj) {

}
```

---

### Scenario

```text
Thread A acquires lock
```

while

```text
Thread B tries same lock
```

Then:

```text
Thread B → BLOCKED
```

---

## Example

```java
class Shared {

    synchronized void display() {

        try {
            Thread.sleep(5000);
        }
        catch(Exception e) {

        }
    }
}
```

---

### Execution

```text
Thread-1 enters display()
        |
Lock Acquired
        |
Thread-2 enters display()
        |
Lock Not Available
        |
BLOCKED
```

---

## Diagram

```text
Lock Available
      |
      v
 Acquire Lock
      |
      v
 Execute

Lock Unavailable
      |
      v
 BLOCKED
```

---

## Characteristics

- Waiting for monitor lock
- Cannot proceed
- CPU not allocated

---

## How to Exit BLOCKED State?

When lock becomes available.

```text
Lock Released
      |
      v
RUNNABLE
```

---

# WAITING State

## Definition

Thread waits indefinitely until another thread explicitly wakes it up.

---

## Common Methods

```java
wait()

join()

LockSupport.park()
```

---

## Example Using join()

```java
Thread t = new Thread();

t.start();

t.join();
```

Main thread enters:

```text
WAITING
```

until child thread completes.

---

## Example Using wait()

```java
synchronized(obj) {

    obj.wait();
}
```

---

## Flow

```text
Thread Waits
      |
      v
WAITING
      |
notify()
      |
      v
RUNNABLE
```

---

## Characteristics

- No timeout
- Waits indefinitely
- Requires external action

---

## Exit Conditions

```java
notify()

notifyAll()

interrupt()
```

---

# TIMED_WAITING State

## Definition

Thread waits for a specified period of time.

---

## Common Methods

```java
sleep()

join(timeout)

wait(timeout)

parkNanos()

parkUntil()
```

---

## Example

```java
Thread.sleep(5000);
```

State:

```text
TIMED_WAITING
```

for 5 seconds.

---

## Example

```java
Thread.sleep(3000);
```

---

## Flow

```text
sleep(3000)
      |
      v
TIMED_WAITING
      |
3 Seconds Expire
      |
      v
RUNNABLE
```

---

## Characteristics

- Time-based waiting
- Automatically resumes
- No notification required

---

## Difference from WAITING

### WAITING

```text
Wait Forever
Until Notified
```

### TIMED_WAITING

```text
Wait Specific Duration
```

---

# TERMINATED State

## Definition

A thread enters TERMINATED state after execution completes.

---

## Example

```java
class MyThread extends Thread {

    @Override
    public void run() {

        System.out.println(
            "Task Finished"
        );
    }
}
```

After `run()` completes:

```text
TERMINATED
```

---

## Diagram

```text
run()
  |
Completed
  |
  v
TERMINATED
```

---

## Characteristics

- Execution finished
- Cannot restart
- Resources eligible for cleanup

---

## Verification

```java
Thread t = new Thread();

t.start();

t.join();

System.out.println(
    t.getState()
);
```

Output:

```text
TERMINATED
```

---

## Can a Terminated Thread Be Restarted?

No.

---

### Example

```java
Thread t = new Thread();

t.start();

t.start();
```

Output:

```text
IllegalThreadStateException
```

---

## Why?

A Java thread can execute only once.

Lifecycle:

```text
NEW
  |
start()
  |
Execution
  |
TERMINATED
```

No path back exists.

---

# Complete State Transition Diagram

```text
                         NEW
                          |
                       start()
                          |
                          v
                     RUNNABLE
                          |
                 Scheduler Selects
                          |
                          v
                      RUNNING
                    /    |     \
                   /     |      \
                  /      |       \
                 v       v        v

          BLOCKED   WAITING   TIMED_WAITING
              |         |           |
              |         |           |
              +---------+-----------+
                        |
                        v
                    RUNNABLE
                        |
                        v
                   TERMINATED
```

---

# State Transition Triggers

| Current State | Event | Next State |
|--------------|---------|------------|
| NEW | start() | RUNNABLE |
| RUNNABLE | Scheduler picks thread | RUNNING |
| RUNNING | Lock unavailable | BLOCKED |
| RUNNING | wait() | WAITING |
| RUNNING | sleep() | TIMED_WAITING |
| RUNNING | join() | WAITING |
| BLOCKED | Lock acquired | RUNNABLE |
| WAITING | notify()/interrupt() | RUNNABLE |
| TIMED_WAITING | Timeout expires | RUNNABLE |
| RUNNING | run() completes | TERMINATED |

---

# Real-World Example

Suppose a banking application processes a transaction.

```text
Thread Created
      |
      v
NEW

      |
start()
      |
      v
RUNNABLE

      |
CPU Assigned
      |
      v
RUNNING

      |
Waiting Database Lock
      |
      v
BLOCKED

      |
Lock Available
      |
      v
RUNNABLE

      |
Database Call
      |
sleep()/wait()
      |
      v
TIMED_WAITING

      |
Response Received
      |
      v
RUNNABLE

      |
Continue Processing
      |
      v
RUNNING

      |
Completed
      |
      v
TERMINATED
```

---

# Common Interview Questions

## Q1: What is the initial state of a thread?

```text
NEW
```

---

## Q2: Which method moves a thread from NEW to RUNNABLE?

```java
start()
```

---

## Q3: Does Java have a RUNNING state?

Officially:

```text
No
```

Conceptually:

```text
Yes
```

Internally JVM includes RUNNING inside RUNNABLE.

---

## Q4: Which methods cause WAITING state?

```java
wait()
join()
LockSupport.park()
```

---

## Q5: Which methods cause TIMED_WAITING?

```java
sleep()
join(timeout)
wait(timeout)
```

---

## Q6: What causes BLOCKED state?

Failure to acquire monitor lock.

---

## Q7: Can a terminated thread be restarted?

```text
No
```

Throws:

```java
IllegalThreadStateException
```

---

# Key Takeaways

1. A thread passes through multiple states from creation to termination.
2. The official Java states are NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, and TERMINATED.
3. RUNNING is a conceptual state and is included within RUNNABLE by the JVM.
4. `start()` moves a thread from NEW to RUNNABLE.
5. BLOCKED occurs when a thread waits for a synchronized lock.
6. WAITING occurs when a thread waits indefinitely for another thread's action.
7. TIMED_WAITING occurs when a thread waits for a specified duration.
8. TERMINATED means the thread has completed execution.
9. A thread can be started only once.
10. Understanding lifecycle transitions is essential for mastering synchronization, locks, wait/notify, and advanced concurrency APIs.