
As applications process larger datasets and perform more complex computations, a traditional thread pool is often not enough.

Consider tasks such as:

- Processing millions of records
- Image processing
- File indexing
- Searching large datasets
- Scientific computations
- Big data analytics

These tasks can often be broken into smaller independent tasks and executed simultaneously.

To efficiently support such workloads, Java 7 introduced:

```java
Fork Join Framework
```

---

# Introduction

## What is Fork Join Framework?

The Fork Join Framework is a specialized framework designed for:

```text
Recursive Parallel Processing
```

using:

```text
Divide And Conquer Strategy
```

---

## Package

```java
java.util.concurrent
```

---

## Main Components

```text
ForkJoinPool

RecursiveTask

RecursiveAction
```

---

## Goal

Efficiently utilize all CPU cores.

---

# Why Fork Join Framework?

Suppose we need to calculate:

```text
Sum Of Numbers
1 To 1,000,000
```

---

Traditional Approach

```text
One Thread

Processes Entire Data
```

---

Problem

```text
Slow Execution
```

---

Better Approach

```text
Split Data

Process Simultaneously

Merge Results
```

---

Example

```text
1 - 250000

250001 - 500000

500001 - 750000

750001 - 1000000
```

---

Each part executes independently.

---

Result

```text
Better CPU Utilization

Faster Processing
```

---

# Divide and Conquer Approach

Fork Join Framework is built on:

```text
Divide And Conquer
```

---

## Idea

Instead of solving a large problem directly:

### Step 1

Split into smaller problems.

---

### Step 2

Solve sub-problems in parallel.

---

### Step 3

Combine results.

---

# Visualization

```text
Large Problem
      |
      v

Divide

      |
      v

Sub Task A
Sub Task B

      |
      v

Process In Parallel

      |
      v

Combine Results
```

---

# Example

Find sum:

```text
1 to 16
```

---

Split

```text
1-8

9-16
```

---

Further Split

```text
1-4
5-8

9-12
13-16
```

---

Process independently.

---

Combine.

---

Final Sum.

---

# Fork and Join

These are the two core operations.

---

# Fork

## Definition

Creates a new subtask and schedules it for execution.

---

Example

```java
task.fork();
```

---

Meaning

```text
Execute This Task
Possibly On Another Worker Thread
```

---

# Join

## Definition

Waits for subtask completion and retrieves result.

---

Example

```java
Integer result =
        task.join();
```

---

Meaning

```text
Wait For Completion

Return Result
```

---

# Visualization

```text
Task

↓

Fork

↓

Subtask A
Subtask B

↓

Join

↓

Combined Result
```

---

# ForkJoinPool

## Definition

A specialized thread pool used by Fork Join Framework.

---

## Package

```java
java.util.concurrent.ForkJoinPool
```

---

## Purpose

Efficient execution of recursive tasks.

---

## Example

```java
ForkJoinPool pool =
        new ForkJoinPool();
```

---

# Default Parallelism

Typically:

```text
Number Of CPU Cores
```

---

Example

```text
8 Core Machine

Parallelism = 8
```

---

# Common Pool

Java provides:

```java
ForkJoinPool.commonPool()
```

---

Used internally by:

```java
CompletableFuture

Parallel Streams
```

---

# Example

```java
ForkJoinPool pool =
        ForkJoinPool.commonPool();
```

---

# Submitting Tasks

```java
Integer result =
        pool.invoke(task);
```

---

# Visualization

```text
ForkJoinPool

Worker 1

Worker 2

Worker 3

Worker 4
```

---

Tasks distributed automatically.

---

# RecursiveTask

## Definition

A Fork Join task that:

```text
Returns Result
```

---

## Generic Type

```java
RecursiveTask<T>
```

---

## Method

```java
protected T compute()
```

---

# Example

```java
class SumTask
        extends RecursiveTask<Integer> {

    @Override
    protected Integer compute() {

        return 100;
    }
}
```

---

# Real Example

Calculate sum.

```java
class SumTask
        extends RecursiveTask<Long> {

    private long start;
    private long end;

    public SumTask(
            long start,
            long end) {

        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {

        if(end - start <= 10) {

            long sum = 0;

            for(long i = start;
                i <= end;
                i++) {

                sum += i;
            }

            return sum;
        }

        long middle =
                (start + end) / 2;

        SumTask left =
                new SumTask(
                        start,
                        middle
                );

        SumTask right =
                new SumTask(
                        middle + 1,
                        end
                );

        left.fork();

        Long rightResult =
                right.compute();

        Long leftResult =
                left.join();

        return leftResult +
               rightResult;
    }
}
```

---

# Execution Flow

```text
1-1000

↓

1-500

501-1000

↓

Recursive Splitting

↓

Small Tasks

↓

Compute

↓

Combine
```

---

# RecursiveAction

## Definition

A Fork Join task that:

```text
Does NOT Return Result
```

---

## Similar To

```java
Runnable
```

---

## Generic Type

```text
No Generic Type
```

---

# Method

```java
protected void compute()
```

---

# Example

```java
class PrintTask
        extends RecursiveAction {

    @Override
    protected void compute() {

        System.out.println(
                "Hello"
        );
    }
}
```

---

# Example with Splitting

```java
class PrintNumbers
        extends RecursiveAction {

    private int start;
    private int end;

    public PrintNumbers(
            int start,
            int end) {

        this.start = start;
        this.end = end;
    }

    @Override
    protected void compute() {

        if(end - start <= 5) {

            for(int i = start;
                i <= end;
                i++) {

                System.out.println(i);
            }

        } else {

            int middle =
                    (start + end) / 2;

            invokeAll(

                    new PrintNumbers(
                            start,
                            middle
                    ),

                    new PrintNumbers(
                            middle + 1,
                            end
                    )
            );
        }
    }
}
```

---

# RecursiveTask vs RecursiveAction

| Feature | RecursiveTask | RecursiveAction |
|-----------|-----------|-----------|
| Returns Value | Yes | No |
| Generic Type | Yes | No |
| compute() Return | Result | void |
| Similar To | Callable | Runnable |

---

# Work Stealing Algorithm

One of the most important concepts of Fork Join Framework.

---

# Problem

Suppose:

```text
Worker 1
```

has many tasks.

---

```text
Worker 2
```

becomes idle.

---

Without optimization:

```text
Worker 2 Waits
```

---

CPU wasted.

---

# Solution

```text
Work Stealing
```

---

# What is Work Stealing?

Idle worker thread steals tasks from busy worker threads.

---

# Visualization

```text
Worker A Queue

Task1
Task2
Task3
Task4
```

---

```text
Worker B Queue

Empty
```

---

Worker B steals:

```text
Task4
```

---

Result

```text
Better Load Balancing
```

---

# Work Stealing Example

Before

```text
Worker A -> 100 Tasks

Worker B -> 0 Tasks
```

---

After Stealing

```text
Worker A -> 50 Tasks

Worker B -> 50 Tasks
```

---

Result

```text
Faster Completion
```

---

# Why Work Stealing Is Powerful

Traditional Thread Pool

```text
Central Queue
```

---

ForkJoinPool

```text
Per Worker Queue
```

---

Benefits

```text
Less Contention

Better Scalability

Higher Throughput
```

---

# Parallel Processing

## Definition

Executing multiple computations simultaneously using multiple CPU cores.

---

# Sequential Processing

```text
Task1

↓

Task2

↓

Task3

↓

Task4
```

---

# Parallel Processing

```text
Task1

Task2

Task3

Task4
```

execute simultaneously.

---

# Example

Image Processing

---

Sequential

```text
Process Entire Image
```

---

Parallel

```text
Split Image

↓

Process Each Segment

↓

Merge Results
```

---

# Parallel Sum Example

Without Fork Join

```java
long sum = 0;

for(int i = 1;
    i <= 1000000;
    i++) {

    sum += i;
}
```

---

Single thread.

---

With Fork Join

```java
ForkJoinPool pool =
        new ForkJoinPool();

Long result =
        pool.invoke(
                new SumTask(
                        1,
                        1000000
                )
        );
```

---

Multiple cores utilized.

---

# invokeAll()

Useful utility method.

---

## Example

```java
invokeAll(
        task1,
        task2
);
```

---

Equivalent to:

```text
Fork Both Tasks

Wait For Completion
```

---

# ForkJoinPool Lifecycle

```text
Create Pool
      |
      v

Submit Task
      |
      v

Fork Tasks
      |
      v

Execute In Parallel
      |
      v

Join Results
      |
      v

Return Final Result
```

---

# Real-World Use Cases

## Big Data Processing

```text
Large Dataset Splitting
```

---

## Search Engines

```text
Index Processing
```

---

## Image Processing

```text
Parallel Pixel Operations
```

---

## Scientific Computing

```text
Matrix Calculations
```

---

## Financial Systems

```text
Risk Calculations
```

---

## Parallel Streams

Internally uses:

```java
ForkJoinPool.commonPool()
```

---

Example

```java
list.parallelStream()
```

---

# ForkJoinPool vs ThreadPoolExecutor

| Feature | ForkJoinPool | ThreadPoolExecutor |
|-----------|-----------|-----------|
| Purpose | Recursive Tasks | General Tasks |
| Work Stealing | Yes | No |
| Divide & Conquer | Yes | No |
| CPU-Bound Tasks | Excellent | Good |
| Recursive Processing | Excellent | Difficult |
| Queue Model | Per Thread Queue | Shared Queue |

---

# Common Mistakes

---

# Mistake 1

Using Fork Join for Small Tasks

Bad

```text
Huge Splitting Overhead
```

---

# Mistake 2

Using For I/O Operations

Examples

```text
Database Calls

REST Calls

File Reads
```

---

Fork Join is optimized for:

```text
CPU-Bound Work
```

---

# Mistake 3

Creating Too Many Tiny Tasks

Bad

```text
Millions Of Small Tasks
```

---

Overhead exceeds benefit.

---

# Common Interview Questions

## Q1: What is Fork Join Framework?

A framework for recursive parallel processing using the divide-and-conquer strategy.

---

## Q2: In which Java version was it introduced?

```text
Java 7
```

---

## Q3: What are the main components?

```text
ForkJoinPool

RecursiveTask

RecursiveAction
```

---

## Q4: What does fork() do?

Creates and schedules a subtask.

---

## Q5: What does join() do?

Waits for task completion and returns the result.

---

## Q6: Difference between RecursiveTask and RecursiveAction?

### RecursiveTask

Returns value.

---

### RecursiveAction

Returns nothing.

---

## Q7: What is Work Stealing?

Idle worker threads steal tasks from busy worker threads.

---

## Q8: Which pool is used by Parallel Streams?

```java
ForkJoinPool.commonPool()
```

---

## Q9: Is Fork Join suitable for database calls?

```text
No
```

It is designed primarily for CPU-bound workloads.

---

## Q10: What is the biggest advantage of ForkJoinPool?

```text
Work Stealing
```

which provides excellent CPU utilization and load balancing.

---

# Key Takeaways

1. Fork Join Framework was introduced in Java 7 for recursive parallel processing.
2. It follows the Divide and Conquer strategy.
3. Large tasks are recursively split into smaller tasks.
4. `ForkJoinPool` is the specialized thread pool used by the framework.
5. `RecursiveTask<T>` is used when a result is required.
6. `RecursiveAction` is used when no result is required.
7. `fork()` schedules subtasks for execution.
8. `join()` waits for task completion and retrieves results.
9. Work Stealing is the key optimization that keeps CPU cores busy.
10. Each worker thread maintains its own task queue.
11. Fork Join Framework is ideal for CPU-bound, computationally intensive tasks.
12. It is not typically recommended for I/O-bound operations such as database and network calls.
13. Parallel Streams and many modern Java concurrency features internally leverage `ForkJoinPool`.
14. Understanding Fork Join Framework is essential before learning Parallel Streams, Reactive Programming, Virtual Threads, and advanced concurrency optimization techniques.