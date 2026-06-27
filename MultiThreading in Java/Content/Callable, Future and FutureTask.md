
Before Java 5, asynchronous task execution was primarily done using:

```java
Runnable
```

However, `Runnable` had major limitations:

- Cannot return a value
- Cannot throw checked exceptions
- No way to track task completion
- No way to cancel a running task

To solve these problems, Java introduced:

```java
Callable
Future
FutureTask
```

These are among the most frequently asked topics in Java multithreading interviews.

---

# Why Do We Need Callable and Future?

Suppose we want to execute:

```java
10 + 20
```

in a separate thread.

Using:

```java
Runnable
```

---

Example

```java
Runnable task = () -> {

    int result = 10 + 20;

};
```

---

Problem:

```text
Result Cannot Be Returned
```

---

Question:

How can the main thread receive the result?

---

Solution:

```java
Callable
```

combined with:

```java
Future
```

---

# Callable

## Definition

`Callable` represents a task that:

```text
Returns A Result

Can Throw Exceptions
```

---

## Package

```java
java.util.concurrent.Callable
```

---

## Functional Interface

```java
@FunctionalInterface
public interface Callable<V>
```

---

## Method

```java
V call() throws Exception;
```

---

## Comparison with Runnable

### Runnable

```java
void run();
```

Returns:

```text
Nothing
```

---

### Callable

```java
V call()
```

Returns:

```text
Value
```

---

# Example

```java
Callable<Integer> task = () -> {

    return 10 + 20;
};
```

---

Result:

```text
30
```

---

# Callable Example Using ExecutorService

```java
ExecutorService executor =
        Executors.newSingleThreadExecutor();

Callable<Integer> task = () -> {

    return 100;
};
```

---

Submit Task

```java
Future<Integer> future =
        executor.submit(task);
```

---

Retrieve Result

```java
System.out.println(
        future.get()
);
```

---

Output

```text
100
```

---

# Callable vs Runnable

| Feature | Runnable | Callable |
|-----------|-----------|-----------|
| Return Value | No | Yes |
| Checked Exceptions | No | Yes |
| Method | run() | call() |
| Functional Interface | Yes | Yes |
| Used With Future | No | Yes |

---

# Future

## Definition

A `Future` represents the result of an asynchronous computation.

---

## Simple Meaning

```text
Promise Of A Future Result
```

---

## Package

```java
java.util.concurrent.Future
```

---

## Visualization

```text
Submit Task
      |
      v

Future Object
      |
      v

Task Executes
      |
      v

Result Available Later
```

---

# Example

```java
Future<Integer> future =
        executor.submit(task);
```

---

At this point:

```text
Task May Still Be Running
```

---

Later:

```java
Integer result =
        future.get();
```

---

Result becomes available.

---

# Future Lifecycle

```text
Task Submitted
      |
      v

RUNNING
      |
      v

COMPLETED
```

---

Or

```text
Task Submitted
      |
      v

RUNNING
      |
      v

CANCELLED
```

---

# cancel()

## Definition

Attempts to cancel task execution.

---

## Syntax

```java
future.cancel(true);
```

---

## Parameters

### true

```text
Interrupt Running Thread
```

---

### false

```text
Cancel Only If Not Started
```

---

# Example

```java
Future<?> future =
        executor.submit(task);

future.cancel(true);
```

---

## Return Value

```java
boolean
```

---

### true

Task cancelled.

---

### false

Cancellation failed.

---

# Example

```java
boolean cancelled =
        future.cancel(true);
```

---

# get()

## Definition

Returns the result of the computation.

---

## Syntax

```java
future.get();
```

---

# Example

```java
Future<Integer> future =
        executor.submit(() -> {

            return 50;
        });

System.out.println(
        future.get()
);
```

---

Output

```text
50
```

---

# Important

If task is not completed:

```text
get() Blocks
```

until result becomes available.

---

# Visualization

```text
Main Thread

future.get()

     |
     v

Wait

     |
     v

Result Arrives
```

---

# Timed get()

To avoid infinite waiting:

```java
future.get(
        5,
        TimeUnit.SECONDS
);
```

---

Meaning:

```text
Wait Maximum 5 Seconds
```

---

If timeout occurs:

```java
TimeoutException
```

is thrown.

---

# Exceptions from get()

---

## InterruptedException

Thread interrupted while waiting.

---

## ExecutionException

Task threw exception.

---

## TimeoutException

Timed wait expired.

---

# Example

```java
try {

    Integer result =
            future.get();

}
catch(ExecutionException e) {

    System.out.println(
            e.getCause()
    );
}
```

---

# isDone()

## Definition

Checks whether task has completed.

---

## Syntax

```java
future.isDone();
```

---

## Returns

```java
true
```

if:

- Completed
- Failed
- Cancelled

---

Otherwise:

```java
false
```

---

# Example

```java
while(!future.isDone()) {

    System.out.println(
            "Still Running..."
    );
}
```

---

# Visualization

```text
Task Running

isDone()

false
```

---

After completion:

```text
true
```

---

# isCancelled()

## Definition

Checks whether task was cancelled.

---

## Syntax

```java
future.isCancelled();
```

---

## Example

```java
future.cancel(true);

System.out.println(
        future.isCancelled()
);
```

---

Output

```text
true
```

---

# Future Example

```java
ExecutorService executor =
        Executors.newFixedThreadPool(2);

Future<Integer> future =
        executor.submit(() -> {

            Thread.sleep(2000);

            return 100;
        });

System.out.println(
        future.isDone()
);

Integer result =
        future.get();

System.out.println(result);

executor.shutdown();
```

---

Possible Output

```text
false
100
```

---

# FutureTask

## Definition

`FutureTask` is a concrete implementation of:

```java
Future
```

and

```java
Runnable
```

---

## Class Declaration

```java
public class FutureTask<V>
        implements RunnableFuture<V>
```

---

## Relationship

```text
Runnable
     |
     v

FutureTask

     ^
     |

Future
```

---

# Why FutureTask?

Provides:

```text
Runnable + Future
```

in a single object.

---

# Example

```java
Callable<Integer> callable =
        () -> 100;
```

---

Create FutureTask

```java
FutureTask<Integer> futureTask =
        new FutureTask<>(callable);
```

---

Run In Thread

```java
Thread thread =
        new Thread(futureTask);

thread.start();
```

---

Retrieve Result

```java
System.out.println(
        futureTask.get()
);
```

---

Output

```text
100
```

---

# FutureTask Workflow

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

Result Available
```

---

# FutureTask Example

```java
FutureTask<Integer> task =
        new FutureTask<>(() -> {

            return 500;
        });

Thread t =
        new Thread(task);

t.start();

System.out.println(
        task.get()
);
```

---

Output

```text
500
```

---

# Why FutureTask Was Important

Before widespread usage of:

```java
ExecutorService
```

developers often used:

```java
FutureTask
```

to bridge:

```text
Thread + Callable
```

---

Even today it is used internally in several concurrency utilities.

---

# Limitations of Future

Although `Future` was revolutionary, it has several limitations.

These limitations eventually led to:

```java
CompletableFuture
```

in Java 8.

---

# Limitation 1

Blocking get()

---

Example

```java
future.get();
```

---

Behavior

```text
Caller Waits
```

until result arrives.

---

Problem

```text
Reduced Responsiveness
```

---

# Limitation 2

No Callback Support

---

Example

Desired:

```text
Task Complete

↓

Automatically Execute Logic
```

---

Future cannot do this directly.

---

Need:

```java
future.get();
```

and manual handling.

---

# Limitation 3

No Task Chaining

---

Desired

```text
Task A

↓

Task B

↓

Task C
```

---

Future does not support fluent chaining.

---

# Limitation 4

No Combining Multiple Futures

---

Desired

```text
Future1

+

Future2

+

Future3
```

---

Future offers no elegant mechanism.

---

# Limitation 5

Manual Completion Handling

Need polling:

```java
future.isDone();
```

---

or blocking:

```java
future.get();
```

---

# Limitation 6

Difficult Error Handling

Errors appear wrapped inside:

```java
ExecutionException
```

---

Need additional code:

```java
e.getCause();
```

---

# Why CompletableFuture Was Introduced

To solve:

- Blocking
- Chaining
- Combining Results
- Async Callbacks
- Better Exception Handling

---

Example

```java
CompletableFuture
        .supplyAsync(...)
        .thenApply(...)
        .thenAccept(...);
```

---

# Callable vs Future vs FutureTask

| Feature | Callable | Future | FutureTask |
|-----------|-----------|-----------|-----------|
| Represents Task | Yes | No | Yes |
| Returns Result | Yes | Yes | Yes |
| Runnable | No | No | Yes |
| Tracks Completion | No | Yes | Yes |
| Cancel Support | No | Yes | Yes |
| Concrete Class | No | No | Yes |

---

# Relationship Diagram

```text
Callable
    |
    v

ExecutorService.submit()

    |
    v

Future
```

---

or

```text
Callable
    |
    v

FutureTask
    |
    v

Thread
```

---

# Common Interview Questions

## Q1: Why do we need Callable?

Because Runnable cannot return values or throw checked exceptions.

---

## Q2: What is the difference between Runnable and Callable?

### Runnable

```java
void run()
```

---

### Callable

```java
V call()
```

returns a value and may throw checked exceptions.

---

## Q3: What is Future?

A placeholder representing the result of an asynchronous computation.

---

## Q4: What does get() do?

Returns the result and blocks until it becomes available.

---

## Q5: What does cancel(true) do?

Attempts to cancel the task and interrupts the executing thread.

---

## Q6: Difference between isDone() and isCancelled()?

### isDone()

Task finished, failed, or cancelled.

---

### isCancelled()

Task specifically cancelled.

---

## Q7: What is FutureTask?

A concrete implementation that combines:

```text
Runnable + Future
```

---

## Q8: Can FutureTask be executed using Thread?

```java
new Thread(futureTask).start();
```

```text
Yes
```

---

## Q9: What is the biggest limitation of Future?

```text
Blocking get()
```

---

## Q10: Which API was introduced to overcome Future limitations?

```java
CompletableFuture
```

---

# Key Takeaways

1. `Callable` is similar to `Runnable` but can return a value and throw checked exceptions.
2. `Future` represents the result of an asynchronous computation.
3. `ExecutorService.submit()` returns a `Future`.
4. `get()` retrieves the result but blocks until completion.
5. `cancel()` attempts to stop task execution.
6. `isDone()` checks whether execution has completed.
7. `isCancelled()` checks whether the task was cancelled.
8. `FutureTask` combines `Runnable` and `Future` in a single implementation.
9. `FutureTask` can be executed directly using a `Thread`.
10. Traditional `Future` lacks callbacks, chaining, and composition features.
11. `CompletableFuture` was introduced in Java 8 to overcome these limitations.
12. Understanding `Callable`, `Future`, and `FutureTask` is essential before learning `CompletableFuture`, `ForkJoinPool`, and advanced asynchronous programming.