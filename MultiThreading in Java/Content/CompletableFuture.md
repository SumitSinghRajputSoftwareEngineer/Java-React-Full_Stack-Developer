
One of the biggest limitations of:

```java
Future
```

is that it provides only a way to:

```text
Submit Task

↓

Wait For Result
```

using:

```java
future.get()
```

which is:

```text
Blocking
```

Modern applications require:

- Non-blocking execution
- Async workflows
- Task chaining
- Parallel execution
- Better exception handling

To solve these problems, Java 8 introduced:

```java
CompletableFuture
```

---

# Why CompletableFuture?

## Problems with Future

### Problem 1

Blocking Result Retrieval

```java
future.get();
```

---

Behavior

```text
Thread Waits
```

until result becomes available.

---

### Problem 2

No Callback Support

Desired:

```text
Task Complete

↓

Execute Another Task
```

---

Future does not support this elegantly.

---

### Problem 3

No Task Chaining

Desired:

```text
Fetch User

↓

Fetch Orders

↓

Generate Report
```

---

Future cannot easily express this flow.

---

### Problem 4

No Combination Support

Desired:

```text
Service A Result

+

Service B Result
```

---

Future lacks built-in mechanisms.

---

### Problem 5

Poor Exception Handling

Errors must be manually extracted from:

```java
ExecutionException
```

---

# Solution

```java
CompletableFuture
```

---

## Benefits

- Non-blocking
- Async execution
- Callback support
- Task chaining
- Combining tasks
- Better error handling

---

# What is CompletableFuture?

## Definition

A `CompletableFuture` is an asynchronous computation that can:

```text
Produce Result

Be Chained

Be Combined

Handle Errors
```

---

## Package

```java
java.util.concurrent.CompletableFuture
```

---

## Class Hierarchy

```text
Future
   |
   v

CompletionStage
   |
   v

CompletableFuture
```

---

## Important

`CompletableFuture` implements:

```java
Future
```

and

```java
CompletionStage
```

---

# Asynchronous Programming

## Synchronous Execution

Tasks execute one after another.

---

Example

```java
task1();

task2();

task3();
```

---

Visualization

```text
Task1

↓

Task2

↓

Task3
```

---

Total Time

```text
Sum Of All Tasks
```

---

# Asynchronous Execution

Tasks execute independently.

---

Example

```java
CompletableFuture.runAsync(task1);

CompletableFuture.runAsync(task2);

CompletableFuture.runAsync(task3);
```

---

Visualization

```text
Task1

Task2

Task3
```

executing simultaneously.

---

## Benefit

```text
Better Throughput

Better Responsiveness
```

---

# Creating CompletableFuture

Several ways exist.

Most important:

```java
runAsync()

supplyAsync()
```

---

# runAsync()

## Definition

Executes a task asynchronously.

---

## Return Type

```java
CompletableFuture<Void>
```

---

## Use When

Task does NOT return a result.

---

## Syntax

```java
CompletableFuture.runAsync(
        () -> {
            System.out.println(
                    "Running"
            );
        }
);
```

---

## Example

```java
CompletableFuture<Void> future =
        CompletableFuture.runAsync(() -> {

            System.out.println(
                    Thread.currentThread()
                          .getName()
            );
        });
```

---

# Visualization

```text
Task Submitted

↓

Background Thread

↓

Execution
```

---

# supplyAsync()

## Definition

Executes a task asynchronously and returns a result.

---

## Return Type

```java
CompletableFuture<T>
```

---

## Syntax

```java
CompletableFuture<Integer> future =
        CompletableFuture.supplyAsync(
                () -> 100
        );
```

---

## Example

```java
CompletableFuture<String> future =
        CompletableFuture.supplyAsync(
                () -> "Hello"
        );
```

---

Retrieve Result

```java
System.out.println(
        future.join()
);
```

---

Output

```text
Hello
```

---

# Difference Between runAsync() and supplyAsync()

| Feature | runAsync() | supplyAsync() |
|-----------|-----------|-----------|
| Returns Result | No | Yes |
| Functional Interface | Runnable | Supplier |
| Return Type | CompletableFuture<Void> | CompletableFuture<T> |

---

# Chaining Operations

One of the biggest advantages of CompletableFuture.

---

## Example Workflow

```text
Fetch User

↓

Fetch Orders

↓

Generate Report

↓

Send Email
```

---

Without blocking.

---

# thenApply()

## Definition

Transforms a result into another value.

---

## Similar To

```java
map()
```

in Streams.

---

## Example

```java
CompletableFuture<Integer> future =
        CompletableFuture
                .supplyAsync(
                        () -> 10
                )
                .thenApply(
                        num -> num * 2
                );
```

---

Output

```text
20
```

---

# Visualization

```text
10

↓

Multiply By 2

↓

20
```

---

# thenAccept()

## Definition

Consumes the result but does not return anything.

---

## Example

```java
CompletableFuture
        .supplyAsync(
                () -> "Java"
        )
        .thenAccept(
                System.out::println
        );
```

---

Output

```text
Java
```

---

## Return Type

```java
CompletableFuture<Void>
```

---

# thenRun()

## Definition

Executes a task after completion of previous stage.

---

## Does NOT Receive Result

---

## Example

```java
CompletableFuture
        .runAsync(
                () -> {
                    System.out.println(
                            "Task Completed"
                    );
                }
        )
        .thenRun(
                () -> {
                    System.out.println(
                            "Cleanup"
                    );
                }
        );
```

---

Output

```text
Task Completed

Cleanup
```

---

# thenCompose()

## Definition

Used when next operation itself returns a CompletableFuture.

---

## Similar To

```text
FlatMap
```

---

## Problem

Without thenCompose:

```text
Nested Futures
```

---

Example

```java
CompletableFuture<
    CompletableFuture<String>
>
```

---

## Solution

```java
thenCompose()
```

---

# Example

```java
CompletableFuture<String> future =
        CompletableFuture
                .supplyAsync(
                        () -> "User"
                )
                .thenCompose(
                        user ->
                        CompletableFuture
                                .supplyAsync(
                                        () -> user + " Data"
                                )
                );
```

---

Output

```text
User Data
```

---

# Visualization

```text
Future A

↓

Future B

↓

Single Future
```

---

# thenCombine()

## Definition

Combines results from two independent futures.

---

# Example

```java
CompletableFuture<Integer> f1 =
        CompletableFuture
                .supplyAsync(() -> 10);

CompletableFuture<Integer> f2 =
        CompletableFuture
                .supplyAsync(() -> 20);
```

---

Combine

```java
CompletableFuture<Integer> result =
        f1.thenCombine(
                f2,
                Integer::sum
        );
```

---

Output

```text
30
```

---

# Visualization

```text
Future1 -> 10

Future2 -> 20

↓

Combine

↓

30
```

---

# Exception Handling

One of the strongest features of CompletableFuture.

---

# exceptionally()

## Definition

Handles exceptions and provides fallback value.

---

## Example

```java
CompletableFuture<Integer> future =
        CompletableFuture
                .supplyAsync(
                        () -> 10 / 0
                )
                .exceptionally(
                        ex -> 0
                );
```

---

Output

```text
0
```

---

# Visualization

```text
Exception

↓

Fallback Value
```

---

# handle()

## Definition

Handles both:

```text
Success

Failure
```

---

## Example

```java
CompletableFuture<Integer> future =
        CompletableFuture
                .supplyAsync(
                        () -> 10
                )
                .handle(
                        (result, ex) -> {

                            if(ex != null) {
                                return 0;
                            }

                            return result;
                        }
                );
```

---

# Parameters

```java
result
```

Successful value.

---

```java
ex
```

Exception if occurred.

---

# whenComplete()

## Definition

Executes after completion regardless of success or failure.

---

## Similar To

```text
finally
```

block.

---

## Example

```java
CompletableFuture
        .supplyAsync(
                () -> 100
        )
        .whenComplete(
                (result, ex) -> {

                    System.out.println(
                            "Finished"
                    );
                }
        );
```

---

## Difference

### whenComplete()

Observation only.

---

### handle()

Can modify result.

---

# Combining Multiple Futures

Modern applications frequently call multiple services simultaneously.

---

Example

```text
User Service

Order Service

Payment Service
```

---

Need:

```text
Execute In Parallel
```

---

# allOf()

## Definition

Waits for ALL futures to complete.

---

## Syntax

```java
CompletableFuture.allOf(
        f1,
        f2,
        f3
);
```

---

# Example

```java
CompletableFuture<Void> all =
        CompletableFuture.allOf(
                f1,
                f2,
                f3
        );
```

---

## Visualization

```text
Future1

Future2

Future3

↓

All Complete

↓

Continue
```

---

# Important

Result type:

```java
CompletableFuture<Void>
```

---

Individual results must be extracted separately.

---

# anyOf()

## Definition

Completes when ANY future finishes first.

---

## Syntax

```java
CompletableFuture.anyOf(
        f1,
        f2,
        f3
);
```

---

# Example

```java
CompletableFuture<Object> result =
        CompletableFuture.anyOf(
                f1,
                f2,
                f3
        );
```

---

## Visualization

```text
Future1

Future2

Future3

↓

First Completion

↓

Continue
```

---

## Use Cases

- Fastest service response
- Redundant service calls
- Failover systems

---

# Common Real-World Example

Without CompletableFuture

```text
Call User Service

Wait

Call Order Service

Wait

Call Payment Service
```

---

Total Time

```text
3 Seconds
```

---

With CompletableFuture

```java
CompletableFuture<User> userFuture;

CompletableFuture<Order> orderFuture;

CompletableFuture<Payment> paymentFuture;
```

---

All execute simultaneously.

---

Total Time

```text
~1 Second
```

---

# CompletableFuture vs Future

| Feature | Future | CompletableFuture |
|-----------|-----------|-----------|
| Async Result | Yes | Yes |
| Non-Blocking Chaining | No | Yes |
| Callback Support | No | Yes |
| Combine Tasks | No | Yes |
| Exception Handling | Limited | Powerful |
| Functional Style | No | Yes |
| Completion Stage API | No | Yes |
| Parallel Composition | No | Yes |

---

# Future Workflow

```text
Submit Task

↓

Future

↓

get()

↓

Block
```

---

# CompletableFuture Workflow

```text
Task

↓

thenApply()

↓

thenCompose()

↓

thenCombine()

↓

exceptionally()
```

---

No explicit blocking required.

---

# Common Interview Questions

## Q1: Why was CompletableFuture introduced?

To overcome the limitations of Future, such as blocking, lack of chaining, and poor exception handling.

---

## Q2: Difference between runAsync() and supplyAsync()?

### runAsync()

```text
No Result
```

---

### supplyAsync()

```text
Returns Result
```

---

## Q3: What does thenApply() do?

Transforms the result and returns a new CompletableFuture.

---

## Q4: Difference between thenApply() and thenAccept()?

### thenApply()

Returns transformed result.

---

### thenAccept()

Consumes result and returns nothing.

---

## Q5: What does thenCompose() do?

Flattens nested CompletableFutures and chains dependent async operations.

---

## Q6: What does thenCombine() do?

Combines results from two independent CompletableFutures.

---

## Q7: Difference between handle() and exceptionally()?

### exceptionally()

Handles only failures.

---

### handle()

Handles both success and failure.

---

## Q8: What does whenComplete() do?

Executes after completion regardless of success or failure.

---

## Q9: Difference between allOf() and anyOf()?

### allOf()

Waits for all futures.

---

### anyOf()

Waits for first completed future.

---

## Q10: Does CompletableFuture use thread pools?

```text
Yes
```

By default it uses:

```java
ForkJoinPool.commonPool()
```

unless a custom Executor is provided.

---

# Key Takeaways

1. `CompletableFuture` was introduced in Java 8 to overcome Future's limitations.
2. It supports non-blocking asynchronous programming.
3. `runAsync()` executes tasks without returning a result.
4. `supplyAsync()` executes tasks and returns a result.
5. `thenApply()` transforms results, while `thenAccept()` consumes results.
6. `thenRun()` executes a follow-up action without receiving the previous result.
7. `thenCompose()` chains dependent asynchronous operations.
8. `thenCombine()` merges results from independent asynchronous computations.
9. `exceptionally()`, `handle()`, and `whenComplete()` provide powerful exception handling mechanisms.
10. `allOf()` waits for all futures, while `anyOf()` waits for the first completed future.
11. By default, CompletableFuture uses `ForkJoinPool.commonPool()`.
12. CompletableFuture is the foundation of modern asynchronous programming in Java and is widely used in Spring Boot, Microservices, Reactive Systems, and High-Performance Applications.
13. Mastering CompletableFuture is essential before learning ForkJoin Framework, Parallel Streams, Reactive Programming, and Project Loom concepts.