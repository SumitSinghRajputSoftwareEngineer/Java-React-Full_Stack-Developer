
Before learning advanced multithreading concepts such as Synchronization, Executor Framework, and CompletableFuture, it is important to understand the core building blocks of Java threads.

The most important components are:

- Thread Class
- Runnable Interface
- Callable Interface
- Future Interface
- FutureTask

These form the foundation of Java multithreading.

---

# Thread Class

## What is Thread Class?

The `Thread` class represents a thread of execution in Java.

Package:

```java
java.lang.Thread
```

It provides methods for:

- Creating threads
- Starting threads
- Controlling execution
- Managing thread lifecycle
- Coordinating threads

---

## Simplified Hierarchy

```text
Object
   |
 Thread
```

---

## Creating a Thread Object

```java
Thread t = new Thread();
```

At this point:

- Thread is created
- No execution starts
- State = NEW

---

## Important Point

Creating a Thread object does NOT create a new execution path.

```java
Thread t = new Thread();
```

The thread starts executing only after:

```java
t.start();
```

---

# Important Methods of Thread Class

---

# start()

## Definition

Starts a new thread and creates a separate execution path.

```java
public synchronized void start()
```

---

## Example

```java
class MyThread extends Thread {

    public void run() {
        System.out.println("Child Thread");
    }
}

public class Test {

    public static void main(String[] args) {

        MyThread t = new MyThread();

        t.start();

        System.out.println("Main Thread");
    }
}
```

### Possible Output

```text
Main Thread
Child Thread
```

or

```text
Child Thread
Main Thread
```

---

## Internal Flow

```text
start()
   |
   v
Create New Thread
   |
   v
JVM Calls run()
```

---

## Important Interview Question

### What happens when start() is called?

1. New thread is created.
2. Thread enters RUNNABLE state.
3. JVM thread scheduler schedules execution.
4. JVM internally invokes `run()`.

---

## Can start() be called twice?

No.

```java
t.start();
t.start();
```

Output:

```text
java.lang.IllegalThreadStateException
```

---

# run()

## Definition

Contains the actual business logic executed by the thread.

```java
public void run()
```

---

## Example

```java
class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println("Thread Running");
    }
}
```

---

## Important Difference

### Calling run()

```java
t.run();
```

Output:

```text
Runs like normal method
No new thread created
```

---

### Calling start()

```java
t.start();
```

Output:

```text
Creates new thread
Calls run() internally
```

---

## start() vs run()

| Feature | start() | run() |
|----------|----------|----------|
| Creates New Thread | Yes | No |
| New Execution Path | Yes | No |
| Scheduler Involved | Yes | No |
| State Changes | Yes | No |
| Direct Method Call | No | Yes |

---

# sleep()

## Definition

Temporarily pauses the current thread.

```java
public static void sleep(long millis)
```

---

## Example

```java
Thread.sleep(3000);
```

Meaning:

```text
Pause current thread for 3 seconds
```

---

## Example

```java
System.out.println("Start");

Thread.sleep(2000);

System.out.println("End");
```

Output:

```text
Start
(wait 2 sec)
End
```

---

## Characteristics

- Static Method
- Current thread sleeps
- Does not release locks
- Moves thread to TIMED_WAITING state

---

## Throws

```java
InterruptedException
```

---

# join()

## Definition

Makes one thread wait for another thread to finish.

---

## Example

```java
Thread t = new Thread();

t.start();

t.join();
```

---

## Flow

```text
Main Thread
    |
    v
Child Thread Start
    |
    v
Main waits
    |
    v
Child Completes
    |
    v
Main Resumes
```

---

## Example

```java
class MyThread extends Thread {

    public void run() {

        for(int i=1;i<=5;i++) {
            System.out.println(i);
        }
    }
}

public class Test {

    public static void main(String[] args)
        throws Exception {

        MyThread t = new MyThread();

        t.start();

        t.join();

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

## Methods

```java
join()

join(long millis)

join(long millis, int nanos)
```

---

# yield()

## Definition

Hints the scheduler that the current thread is willing to give up CPU.

```java
public static void yield()
```

---

## Example

```java
Thread.yield();
```

---

## Important Point

Yield is only a hint.

Scheduler may:

```text
Accept
OR
Ignore
```

---

## Characteristics

- Static Method
- Platform dependent
- No guarantee

---

# interrupt()

## Definition

Used to interrupt a thread.

```java
public void interrupt()
```

---

## Example

```java
t.interrupt();
```

---

## Purpose

Interrupt does NOT forcibly stop a thread.

Instead:

```text
Sets interrupt flag
```

---

## Example

```java
Thread.sleep(5000);
```

If interrupted:

```text
InterruptedException thrown
```

---

## Flow

```text
Thread Running
      |
interrupt()
      |
Interrupt Flag Set
      |
Thread Checks Flag
```

---

# isInterrupted()

## Definition

Checks whether interrupt flag is set.

```java
public boolean isInterrupted()
```

---

## Example

```java
Thread.currentThread().isInterrupted()
```

---

## Important

Does NOT clear interrupt status.

---

## Example

```java
t.interrupt();

System.out.println(t.isInterrupted());
```

Output:

```text
true
```

---

# interrupted()

## Definition

Checks interrupt status of current thread.

```java
public static boolean interrupted()
```

---

## Important Difference

It clears interrupt flag after checking.

---

## Example

```java
Thread.currentThread().interrupt();

System.out.println(Thread.interrupted());
System.out.println(Thread.interrupted());
```

Output:

```text
true
false
```

---

## interrupted() vs isInterrupted()

| Feature | interrupted() | isInterrupted() |
|----------|----------|----------|
| Static | Yes | No |
| Current Thread | Yes | No |
| Clears Flag | Yes | No |
| Returns Status | Yes | Yes |

---

# currentThread()

## Definition

Returns reference of currently executing thread.

```java
public static Thread currentThread()
```

---

## Example

```java
Thread t = Thread.currentThread();

System.out.println(t.getName());
```

Output:

```text
main
```

---

# getName()

## Definition

Returns thread name.

```java
String getName()
```

---

## Example

```java
System.out.println(
    Thread.currentThread().getName()
);
```

Output:

```text
main
```

---

# setName()

## Definition

Changes thread name.

```java
void setName(String name)
```

---

## Example

```java
Thread.currentThread()
      .setName("Worker-1");
```

Output:

```text
Worker-1
```

---

## Why Useful?

Used for:

- Logging
- Debugging
- Monitoring

---

# getId()

## Definition

Returns unique thread ID.

```java
long getId()
```

---

## Example

```java
System.out.println(
    Thread.currentThread().getId()
);
```

Output:

```text
1
```

---

## Characteristics

- Assigned by JVM
- Unique during thread lifetime

---

# setPriority()

## Definition

Sets thread priority.

```java
void setPriority(int priority)
```

---

## Range

```text
1 → MIN_PRIORITY
5 → NORM_PRIORITY
10 → MAX_PRIORITY
```

---

## Example

```java
t.setPriority(10);
```

---

## Important

Priority is merely a suggestion to scheduler.

No guarantee of execution order.

---

# getPriority()

## Definition

Returns current priority.

```java
int getPriority()
```

---

## Example

```java
System.out.println(t.getPriority());
```

Output:

```text
5
```

Default priority:

```text
NORM_PRIORITY
```

---

# isAlive()

## Definition

Checks whether thread is still running.

```java
boolean isAlive()
```

---

## Example

```java
Thread t = new Thread();

System.out.println(t.isAlive());
```

Output:

```text
false
```

---

## Example

```java
t.start();

System.out.println(t.isAlive());
```

Output:

```text
true
```

---

## States

| State | isAlive() |
|----------|----------|
| NEW | false |
| RUNNABLE | true |
| RUNNING | true |
| BLOCKED | true |
| WAITING | true |
| TERMINATED | false |

---

# Runnable Interface

## What is Runnable?

Runnable is a Functional Interface used to define a task that can be executed by a thread.

Package:

```java
java.lang.Runnable
```

---

## Definition

```java
@FunctionalInterface
public interface Runnable {

    void run();
}
```

---

## Why Runnable?

Java supports:

```text
Single Inheritance
```

If we extend Thread:

```java
class MyThread extends Thread
```

we cannot extend another class.

Runnable solves this problem.

---

## Example

```java
class Task implements Runnable {

    @Override
    public void run() {
        System.out.println("Task Running");
    }
}
```

---

## Execution

```java
Task task = new Task();

Thread t = new Thread(task);

t.start();
```

---

## Lambda Example

```java
Runnable task =
    () -> System.out.println("Running");

new Thread(task).start();
```

---

## Advantages

### Better Design

Task and Thread separated.

### Reusability

Same Runnable can be executed by multiple threads.

### Supports Lambda

Cleaner code.

### Preferred Approach

Used widely in industry.

---

# Callable Interface

## Why Callable?

Runnable has one limitation:

```java
void run()
```

Cannot:

- Return value
- Throw checked exception

---

## Callable Solves This

Package:

```java
java.util.concurrent
```

---

## Definition

```java
public interface Callable<V> {

    V call() throws Exception;
}
```

---

## Features

- Returns value
- Throws checked exceptions
- Generic support

---

## Example

```java
Callable<Integer> task = () -> {

    return 100;
};
```

---

## Comparison

| Feature | Runnable | Callable |
|----------|----------|----------|
| Return Value | No | Yes |
| Checked Exception | No | Yes |
| Method | run() | call() |
| Package | java.lang | java.util.concurrent |

---

# Future Interface

## What is Future?

Represents the result of an asynchronous computation.

---

## Example

```java
ExecutorService service =
    Executors.newFixedThreadPool(1);

Future<Integer> future =
    service.submit(() -> 100);
```

---

## Flow

```text
Submit Task
      |
      v
Background Execution
      |
      v
Future Object
      |
      v
Retrieve Result Later
```

---

## Important Methods

---

### get()

Waits for result.

```java
Integer result = future.get();
```

---

### isDone()

Checks completion.

```java
future.isDone();
```

---

### isCancelled()

Checks cancellation.

```java
future.isCancelled();
```

---

### cancel()

Cancels task.

```java
future.cancel(true);
```

---

## Example

```java
Future<Integer> future =
    executor.submit(() -> 500);

Integer result = future.get();

System.out.println(result);
```

Output:

```text
500
```

---

## Limitation of Future

Cannot:

- Chain operations
- Combine tasks
- Handle async pipelines easily

These limitations led to:

```java
CompletableFuture
```

---

# FutureTask

## What is FutureTask?

A concrete implementation of both:

```java
Future
```

and

```java
Runnable
```

---

## Hierarchy

```text
Runnable
    ^
    |
FutureTask
    |
    v
Future
```

Actually:

```java
class FutureTask<V>
implements RunnableFuture<V>
```

where

```java
RunnableFuture
extends Runnable, Future
```

---

## Why FutureTask?

Provides:

- Runnable behavior
- Future behavior
- Result retrieval
- Cancellation support

---

## Example

```java
Callable<Integer> callable =
        () -> 100;

FutureTask<Integer> task =
        new FutureTask<>(callable);

Thread t = new Thread(task);

t.start();

System.out.println(task.get());
```

Output:

```text
100
```

---

## Internal Flow

```text
Callable
    |
    v
FutureTask
    |
    v
Thread
    |
    v
Execution
    |
    v
Result Stored
    |
    v
get()
```

---

# Runnable vs Callable vs Future vs FutureTask

| Feature | Runnable | Callable | Future | FutureTask |
|----------|----------|----------|----------|----------|
| Represents Task | Yes | Yes | No | Yes |
| Return Value | No | Yes | Result Holder | Yes |
| Checked Exception | No | Yes | N/A | Yes |
| Executable By Thread | Yes | Indirectly | No | Yes |
| Can Retrieve Result | No | Through Future | Yes | Yes |
| Can Cancel Task | No | No | Yes | Yes |
| Package | java.lang | java.util.concurrent | java.util.concurrent | java.util.concurrent |

---

# Key Takeaways

1. `Thread` class represents a thread of execution.
2. `start()` creates a new thread and internally invokes `run()`.
3. Calling `run()` directly does not create a new thread.
4. `sleep()` pauses the current thread.
5. `join()` waits for another thread to finish.
6. `yield()` is merely a scheduler hint.
7. `interrupt()` requests interruption by setting the interrupt flag.
8. `isInterrupted()` checks the flag without clearing it.
9. `interrupted()` checks and clears the flag.
10. `Runnable` is the preferred way to define tasks.
11. `Callable` supports return values and checked exceptions.
12. `Future` represents the result of asynchronous computation.
13. `FutureTask` combines Runnable and Future capabilities.
14. Modern applications generally use `ExecutorService`, `Callable`, `Future`, and `CompletableFuture` instead of manually managing Thread objects.