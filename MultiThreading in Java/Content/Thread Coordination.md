
In a multithreaded application, multiple threads execute independently.

However, in real-world applications, threads often need to coordinate with each other.

Examples:

- Main thread waiting for worker threads to complete
- One thread pausing for a specific duration
- One thread voluntarily giving CPU to another thread
- One thread requesting another thread to stop its work

Java provides several methods for thread coordination:

- `sleep()`
- `join()`
- `yield()`
- `interrupt()`

These methods help control thread execution and communication.

---

# What is Thread Coordination?

## Definition

Thread Coordination refers to techniques that allow multiple threads to cooperate and synchronize their execution flow.

---

## Example

Suppose a report generation application has:

```text
Thread 1 → Generate Report
Thread 2 → Send Email
```

The email should be sent only after the report is generated.

Thread coordination ensures:

```text
Generate Report
       ↓
Send Email
```

---

# sleep()

## Definition

The `sleep()` method pauses the currently executing thread for a specified period of time.

---

## Method Signature

```java
public static void sleep(long millis)
        throws InterruptedException
```

---

## Overloaded Method

```java
public static void sleep(
        long millis,
        int nanos)
        throws InterruptedException
```

---

## Example

```java
System.out.println("Start");

Thread.sleep(3000);

System.out.println("End");
```

Output:

```text
Start
(wait 3 seconds)
End
```

---

## Internal Flow

```text
Running Thread
       |
sleep(3000)
       |
       v
TIMED_WAITING
       |
3 Seconds Completed
       |
       v
RUNNABLE
```

---

## Important Point

`sleep()` is a static method.

Although we often write:

```java
t.sleep(3000);
```

it actually executes:

```java
Thread.sleep(3000);
```

on the current thread.

---

## Example

```java
Thread t = new Thread();

t.sleep(5000);
```

Actually means:

```java
Thread.sleep(5000);
```

Current thread sleeps, not `t`.

---

## Does sleep() Release Locks?

No.

---

## Example

```java
synchronized(obj) {

    Thread.sleep(5000);
}
```

During sleep:

```text
Lock remains held
```

Other threads cannot enter the synchronized block.

---

## Common Use Cases

### Delay Execution

```java
Thread.sleep(1000);
```

---

### Retry Logic

```java
while(true) {

    try {
        connect();
        break;
    }
    catch(Exception e) {

        Thread.sleep(2000);
    }
}
```

---

### Simulations

```java
Game animations
Progress indicators
Testing
```

---

# join()

## Definition

The `join()` method allows one thread to wait for another thread to complete.

---

## Why Do We Need join()?

Suppose:

```text
Worker Thread
```

must finish before:

```text
Main Thread
```

continues.

---

## Example

```java
Thread t = new Thread();

t.start();

t.join();
```

---

## Internal Flow

```text
Main Thread
      |
      v
Waits For Thread t
      |
      v
Thread t Completes
      |
      v
Main Continues
```

---

# join()

## Method Signature

```java
public final void join()
        throws InterruptedException
```

---

## Meaning

Wait indefinitely until target thread terminates.

---

## Example

```java
class Worker extends Thread {

    @Override
    public void run() {

        for(int i=1;i<=5;i++) {

            System.out.println(i);
        }
    }
}

public class Test {

    public static void main(String[] args)
            throws Exception {

        Worker w = new Worker();

        w.start();

        w.join();

        System.out.println("Completed");
    }
}
```

Output:

```text
1
2
3
4
5
Completed
```

---

## State Transition

```text
Main Thread
     |
join()
     |
WAITING
     |
Worker Completes
     |
RUNNABLE
```

---

# join(long millis)

## Definition

Waits for a maximum specified time.

---

## Method Signature

```java
public final void join(long millis)
        throws InterruptedException
```

---

## Example

```java
worker.join(5000);
```

Meaning:

```text
Wait at most 5 seconds
```

---

## Possible Outcomes

### Scenario 1

Worker finishes within 5 seconds.

```text
Main continues immediately.
```

---

### Scenario 2

Worker does not finish.

```text
Main resumes after 5 seconds.
```

---

## State Transition

```text
join(5000)
      |
      v
TIMED_WAITING
      |
5 Seconds
      |
      v
RUNNABLE
```

---

## Real-World Example

Waiting for:

- Report Generation
- File Upload
- API Response

with timeout.

---

# join() vs join(long millis)

| Feature | join() | join(long millis) |
|----------|----------|----------|
| Waiting Time | Unlimited | Limited |
| State | WAITING | TIMED_WAITING |
| Timeout | No | Yes |
| Risk of Infinite Wait | Yes | No |

---

# yield()

## Definition

The `yield()` method hints to the scheduler that the current thread is willing to pause and allow other threads to execute.

---

## Method Signature

```java
public static void yield()
```

---

## Example

```java
Thread.yield();
```

---

## Internal Flow

```text
Thread Running
      |
yield()
      |
      v
RUNNABLE
      |
Scheduler Decision
      |
Another Thread
or Same Thread
```

---

## Important Point

Yield is merely a suggestion.

The scheduler may:

```text
Accept
```

or

```text
Ignore
```

the request.

---

## Example

```java
public void run() {

    for(int i=1;i<=5;i++) {

        System.out.println(i);

        Thread.yield();
    }
}
```

---

## Possible Output

Unpredictable.

---

## Does yield() Release Locks?

No.

---

## Example

```java
synchronized(obj) {

    Thread.yield();
}
```

Lock remains acquired.

---

## Characteristics

### Static Method

```java
Thread.yield();
```

---

### Platform Dependent

Behavior varies across OS implementations.

---

### No Guarantees

Never rely on yield for program correctness.

---

# interrupt()

## Definition

The `interrupt()` method is used to request interruption of another thread.

---

## Method Signature

```java
public void interrupt()
```

---

## Important Interview Point

`interrupt()` does NOT stop a thread.

Instead:

```text
Sets Interrupt Flag
```

and informs the thread that interruption has been requested.

---

## Example

```java
Thread t = new Thread();

t.start();

t.interrupt();
```

---

## Internal Flow

```text
interrupt()
      |
      v
Interrupt Flag Set
      |
      v
Thread Decides
How To React
```

---

# Interrupt Mechanism

## What Happens Internally?

When:

```java
t.interrupt();
```

is called,

JVM sets an internal boolean flag:

```text
Interrupted = true
```

for that thread.

---

## Visualization

```text
Thread
   |
Interrupt Flag = false

interrupt()

Thread
   |
Interrupt Flag = true
```

---

## Thread Response

The thread can:

### Option 1

Ignore interruption.

---

### Option 2

Check interrupt status.

```java
Thread.currentThread()
      .isInterrupted();
```

---

### Option 3

Terminate gracefully.

---

# Example: Checking Interrupt Status

```java
class Worker extends Thread {

    public void run() {

        while(
            !Thread.currentThread()
                   .isInterrupted()
        ) {

            System.out.println("Working...");
        }

        System.out.println(
            "Stopping..."
        );
    }
}
```

---

## Execution

```java
Worker w = new Worker();

w.start();

Thread.sleep(3000);

w.interrupt();
```

Output:

```text
Working...
Working...
Working...
Stopping...
```

---

# InterruptedException

## What is InterruptedException?

An exception thrown when a thread is interrupted while:

- sleeping
- waiting
- joining

---

## Example

```java
Thread.sleep(5000);
```

If another thread calls:

```java
interrupt();
```

then:

```text
InterruptedException
```

is thrown.

---

## Example

```java
try {

    Thread.sleep(5000);

}
catch(InterruptedException e) {

    System.out.println(
        "Interrupted"
    );
}
```

---

## Internal Flow

```text
sleep()
      |
interrupt()
      |
InterruptedException
      |
Wake Up Thread
```

---

## Common Methods That Throw InterruptedException

### sleep()

```java
Thread.sleep(...)
```

---

### join()

```java
thread.join(...)
```

---

### wait()

```java
obj.wait(...)
```

---

### BlockingQueue Operations

```java
take()
put()
```

---

# isInterrupted()

## Definition

Checks interrupt status without clearing it.

---

## Example

```java
t.isInterrupted();
```

---

## Output

```text
true
```

or

```text
false
```

---

## Characteristics

```text
Does Not Clear Flag
```

---

# interrupted()

## Definition

Checks current thread interrupt status and clears it.

---

## Example

```java
Thread.interrupted();
```

---

## Example

```java
Thread.currentThread()
      .interrupt();

System.out.println(
    Thread.interrupted()
);

System.out.println(
    Thread.interrupted()
);
```

Output:

```text
true
false
```

---

## Why?

First call:

```text
Returns true
Clears Flag
```

Second call:

```text
Flag Already Cleared
Returns false
```

---

# isInterrupted() vs interrupted()

| Feature | isInterrupted() | interrupted() |
|----------|----------|----------|
| Static | No | Yes |
| Current Thread Only | No | Yes |
| Clears Flag | No | Yes |
| Returns Status | Yes | Yes |

---

# Best Practices

Thread interruption is one of the most frequently asked interview topics.

---

## 1. Never Use interrupt() to Forcefully Kill Threads

Wrong assumption:

```java
interrupt();
```

means

```text
Stop Thread
```

Not true.

It only requests interruption.

---

## 2. Check Interrupt Status Regularly

Long-running threads should periodically check:

```java
isInterrupted()
```

---

### Example

```java
while(
    !Thread.currentThread()
           .isInterrupted()
) {

    process();
}
```

---

## 3. Do Not Swallow InterruptedException

Bad Practice

```java
catch(InterruptedException e) {

}
```

---

### Why?

Interrupt signal gets lost.

---

## Better

```java
catch(InterruptedException e) {

    Thread.currentThread()
          .interrupt();
}
```

This restores interrupt status.

---

## 4. Use join() for Dependency Management

Instead of:

```java
Thread.sleep(5000);
```

Use:

```java
worker.join();
```

when waiting for another thread.

---

## 5. Avoid Using yield() for Program Logic

Bad Practice

```java
yield();
```

to enforce ordering.

---

### Why?

No guarantees.

Scheduler may ignore it.

---

## 6. Prefer Executor Framework in Production

Modern applications generally use:

```java
ExecutorService
Future
CompletableFuture
```

instead of manually coordinating threads.

---

# Real-World Example

Suppose an application processes files.

```text
Worker Thread
       |
Process File
       |
Check Interrupt Flag
       |
If Interrupted
       |
Gracefully Stop
```

Administrator shuts down application.

```java
worker.interrupt();
```

Worker completes current operation and exits safely.

---

# Common Interview Questions

## Q1: Does sleep() release locks?

```text
No
```

---

## Q2: Does yield() release locks?

```text
No
```

---

## Q3: Does interrupt() stop a thread?

```text
No
```

It only requests interruption.

---

## Q4: Which state does sleep() cause?

```text
TIMED_WAITING
```

---

## Q5: Which state does join() cause?

```text
WAITING
```

or

```text
TIMED_WAITING
```

for timeout versions.

---

## Q6: What exception is thrown when sleep() is interrupted?

```java
InterruptedException
```

---

## Q7: What is the difference between interrupt() and InterruptedException?

| interrupt() | InterruptedException |
|------------|------------|
| Method | Exception |
| Sets interrupt flag | Thrown during blocking operations |
| Request for interruption | Response to interruption |

---

## Q8: What is the difference between isInterrupted() and interrupted()?

```text
isInterrupted() -> Does not clear flag

interrupted() -> Clears flag
```

---

# Key Takeaways

1. Thread coordination allows threads to cooperate and manage execution flow.
2. `sleep()` pauses the current thread for a specified duration.
3. `sleep()` moves a thread to `TIMED_WAITING` and does not release locks.
4. `join()` allows one thread to wait for another thread's completion.
5. `join(long millis)` waits only for a specified duration.
6. `yield()` is a scheduler hint and provides no execution guarantees.
7. `interrupt()` does not stop a thread; it requests interruption by setting the interrupt flag.
8. `InterruptedException` is thrown when a blocked thread is interrupted.
9. `isInterrupted()` checks the interrupt status without clearing it.
10. `interrupted()` checks and clears the interrupt status.
11. Proper interruption handling is essential for building responsive and production-ready multithreaded applications.