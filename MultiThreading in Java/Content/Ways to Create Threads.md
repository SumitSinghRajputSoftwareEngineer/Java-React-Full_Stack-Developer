# 3. Ways to Create Threads

Creating threads is one of the first practical skills in Java Multithreading.

Java provides multiple ways to create and execute threads. Over time, Java has evolved from manual thread creation to advanced thread management using the Executor Framework.

---

# Overview

Java provides the following approaches:

1. Extending Thread Class
2. Implementing Runnable Interface
3. Implementing Callable Interface
4. Using Lambda Expressions
5. Using Executor Framework

---

# Extending Thread Class

## What is It?

In this approach, a class extends the `Thread` class and overrides the `run()` method.

The `run()` method contains the logic that will be executed by the new thread.

---

## Syntax

```java
class MyThread extends Thread {

    @Override
    public void run() {

    }
}
```

---

## Example

```java
class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println(
            "Child Thread: " +
            Thread.currentThread().getName()
        );
    }
}

public class Test {

    public static void main(String[] args) {

        MyThread t = new MyThread();

        t.start();

        System.out.println(
            "Main Thread: " +
            Thread.currentThread().getName()
        );
    }
}
```

---

## Possible Output

```text
Main Thread: main
Child Thread: Thread-0
```

or

```text
Child Thread: Thread-0
Main Thread: main
```

---

## Internal Flow

```text
Create Thread Object
        |
        v
     start()
        |
        v
 JVM Creates New Thread
        |
        v
      run()
```

---

## Advantages

### Simple to Understand

Easy for beginners.

### Direct Access

Can directly use Thread methods.

```java
setName()
setPriority()
```

---

## Disadvantages

### Single Inheritance Limitation

Java supports only one class inheritance.

```java
class MyThread extends Thread
```

Now:

```java
class MyThread extends Employee
```

is impossible.

---

### Tight Coupling

Task and Thread become tightly coupled.

```text
Business Logic + Thread Logic
```

in same class.

---

### Not Recommended in Enterprise Applications

Modern applications rarely use this approach.

---

## Real-World Analogy

Imagine:

```text
Employee = Thread
Task = Work
```

In this approach:

```text
Employee and Task become same object.
```

Not flexible.

---

# Implementing Runnable Interface

## What is Runnable?

Runnable represents a task that can be executed by a thread.

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

## Syntax

```java
class Task implements Runnable {

    @Override
    public void run() {

    }
}
```

---

## Example

```java
class Task implements Runnable {

    @Override
    public void run() {

        System.out.println(
            "Running Task"
        );
    }
}

public class Test {

    public static void main(String[] args) {

        Task task = new Task();

        Thread t =
            new Thread(task);

        t.start();
    }
}
```

---

## Internal Flow

```text
Runnable Object
        |
        v
Thread Object
        |
        v
start()
        |
        v
run()
```

---

## Advantages

### Supports Multiple Inheritance

```java
class Task extends Employee
          implements Runnable
```

Possible.

---

### Better Design

Separates:

```text
Task Logic
```

from

```text
Thread Logic
```

---

### Reusability

Same Runnable can be executed by multiple threads.

```java
Runnable task = new Task();

new Thread(task).start();
new Thread(task).start();
```

---

### Industry Standard

Most applications prefer Runnable over Thread inheritance.

---

## Disadvantages

### Cannot Return Value

```java
run()
```

returns:

```java
void
```

---

### Cannot Throw Checked Exceptions

```java
public void run()
```

No checked exception support.

---

# Implementing Callable Interface

## Why Callable?

Runnable has two major limitations:

### Limitation 1

Cannot return value.

### Limitation 2

Cannot throw checked exceptions.

---

## Solution

Use:

```java
Callable<V>
```

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

## Syntax

```java
Callable<Integer> task =
    () -> {

        return 100;
    };
```

---

## Example

```java
Callable<Integer> task =
    () -> {

        return 500;
    };
```

Submitting:

```java
ExecutorService executor =
    Executors.newSingleThreadExecutor();

Future<Integer> future =
    executor.submit(task);
```

---

## Getting Result

```java
Integer result =
    future.get();

System.out.println(result);
```

Output:

```text
500
```

---

## Advantages

### Returns Value

```java
Integer
String
Employee
List
```

Anything.

---

### Supports Checked Exceptions

```java
throws Exception
```

---

### Works Well with Executor Framework

Most modern applications use:

```java
Callable
+
ExecutorService
```

---

## Disadvantages

Cannot execute directly using:

```java
new Thread(callable)
```

because:

```java
Thread accepts Runnable
```

not Callable.

---

# Using Lambda Expressions

## Why Lambda?

Since Java 8, Runnable is a Functional Interface.

Therefore:

```java
Anonymous Class
```

can be replaced by:

```java
Lambda Expression
```

---

## Traditional Approach

```java
Runnable task =
    new Runnable() {

        @Override
        public void run() {

            System.out.println(
                "Running"
            );
        }
    };
```

---

## Lambda Approach

```java
Runnable task =
    () -> System.out.println(
        "Running"
    );
```

---

## Complete Example

```java
public class Test {

    public static void main(String[] args) {

        Thread t =
            new Thread(
                () -> {

                    System.out.println(
                        Thread.currentThread()
                              .getName()
                    );
                }
            );

        t.start();
    }
}
```

---

## Output

```text
Thread-0
```

---

## Advantages

### Less Boilerplate

Cleaner code.

---

### Easy to Read

Very common in modern Java.

---

### Perfect for Small Tasks

Example:

```java
new Thread(
    () -> sendEmail()
).start();
```

---

# Using Executor Framework

## Why Executor Framework?

Suppose we create threads manually.

```java
for(int i=0;i<10000;i++) {

    new Thread(task).start();
}
```

Problems:

- Huge memory usage
- Thread creation overhead
- CPU overhead
- Context switching overhead

---

## Solution

Executor Framework

Package:

```java
java.util.concurrent
```

---

## Definition

Executor Framework manages thread creation and execution automatically.

---

## Architecture

```text
Task
  |
  v
ExecutorService
  |
  v
Thread Pool
  |
  v
Execution
```

---

## Example

```java
ExecutorService executor =
    Executors.newFixedThreadPool(3);

executor.submit(
    () -> {

        System.out.println(
            Thread.currentThread()
                  .getName()
        );
    }
);

executor.shutdown();
```

---

## Output

```text
pool-1-thread-1
```

---

## What Happens Internally?

```text
Task Submitted
      |
      v
Thread Pool Receives Task
      |
      v
Available Thread Executes Task
```

---

## Benefits

### Thread Reuse

Threads are reused.

No repeated creation.

---

### Better Performance

Less thread creation overhead.

---

### Resource Management

Controls:

- Number of threads
- Queue size
- Shutdown process

---

### Scalability

Can handle thousands of tasks.

---

## Types of Executors

### Fixed Thread Pool

```java
Executors.newFixedThreadPool(5)
```

Fixed number of threads.

---

### Single Thread Executor

```java
Executors.newSingleThreadExecutor()
```

Only one worker thread.

---

### Cached Thread Pool

```java
Executors.newCachedThreadPool()
```

Creates threads as needed.

---

### Scheduled Thread Pool

```java
Executors.newScheduledThreadPool(3)
```

Supports scheduling.

---

## Industry Usage

In real applications:

```text
ExecutorService
ThreadPoolExecutor
CompletableFuture
```

are preferred.

Direct thread creation is rarely used.

---

# Comparison

# Thread vs Runnable

| Feature | Thread | Runnable |
|----------|----------|----------|
| Type | Class | Interface |
| Inheritance Needed | Yes | No |
| Multiple Inheritance | Not Possible | Possible |
| Reusability | Low | High |
| Task Separation | No | Yes |
| Preferred | No | Yes |

---

## Example

### Thread

```java
class MyThread extends Thread
```

---

### Runnable

```java
class Task implements Runnable
```

Runnable is generally preferred.

---

# Runnable vs Callable

| Feature | Runnable | Callable |
|----------|----------|----------|
| Package | java.lang | java.util.concurrent |
| Method | run() | call() |
| Return Value | No | Yes |
| Checked Exception | No | Yes |
| Generic Support | No | Yes |
| Executor Compatible | Yes | Yes |

---

## Runnable Example

```java
Runnable task =
    () -> System.out.println("Hello");
```

---

## Callable Example

```java
Callable<Integer> task =
    () -> 100;
```

---

## When to Use?

### Runnable

When no result is required.

Example:

```text
Logging
Email Sending
Notification
```

---

### Callable

When result is required.

Example:

```text
Database Query
Calculation
API Response
```

---

# Direct Thread Creation vs Executor Framework

| Feature | Direct Thread Creation | Executor Framework |
|----------|----------|----------|
| Thread Management | Manual | Automatic |
| Thread Reuse | No | Yes |
| Scalability | Poor | Excellent |
| Performance | Lower | Higher |
| Resource Control | Limited | Strong |
| Production Ready | No | Yes |
| Preferred | Rarely | Yes |

---

## Direct Thread Creation

```java
new Thread(task).start();
```

Problems:

- Creates new thread every time
- Memory overhead
- Context switching overhead

---

## Executor Framework

```java
executor.submit(task);
```

Benefits:

- Thread pooling
- Reuse
- Better performance
- Better resource utilization

---

## Real-World Example

Suppose:

```text
10,000 Customer Requests
```

### Direct Thread Creation

```text
10,000 Threads
```

Huge memory consumption.

---

### Executor Framework

```text
Thread Pool
Size = 50
```

Only 50 threads process all requests.

Much more efficient.

---

# Which Approach Should You Use?

| Scenario | Recommended Approach |
|-----------|---------------------|
| Learning Basics | Thread Class |
| General Multithreading | Runnable |
| Need Result | Callable |
| Small Task | Lambda |
| Production Applications | Executor Framework |
| Large Scale Systems | Executor Framework + Thread Pool |

---

# Key Takeaways

1. Java provides multiple ways to create threads.
2. Extending `Thread` is simple but rarely used in production.
3. Implementing `Runnable` is the most common basic approach.
4. `Callable` is preferred when a task must return a value or throw checked exceptions.
5. Lambda expressions make thread creation concise and readable.
6. The Executor Framework is the industry-standard approach for managing threads.
7. Thread pools significantly reduce thread creation overhead.
8. `Runnable` separates task logic from thread management.
9. `Callable` works closely with `Future` and `ExecutorService`.
10. In modern enterprise applications, `ExecutorService`, `ThreadPoolExecutor`, and `CompletableFuture` are preferred over direct thread creation.