
In multithreaded applications, there are scenarios where:

```text
Two Threads Need To Exchange Data
```

with each other at a synchronization point.

Examples:

- Producer hands over a filled buffer to a consumer
- Consumer returns an empty buffer back to producer
- Two algorithms exchange intermediate results
- Two threads swap processing data

To solve this problem, Java provides:

```java
Exchanger
```

---

# What is Exchanger?

## Definition

`Exchanger` is a synchronization utility that allows two threads to exchange objects at a synchronization point.

---

## Package

```java
java.util.concurrent.Exchanger
```

---

## Simple Definition

Think of Exchanger as:

```text
A Meeting Point
```

where:

```text
Thread A Gives Data To Thread B

AND

Thread B Gives Data To Thread A
```

at the same time.

---

# Real-World Analogy

Imagine two people exchanging documents.

---

Person A has:

```text
Document A
```

---

Person B has:

```text
Document B
```

---

At the meeting point:

```text
Person A Gives Document A

↓

Receives Document B
```

---

```text
Person B Gives Document B

↓

Receives Document A
```

---

This is exactly how:

```java
Exchanger
```

works.

---

# Why Exchanger?

Suppose:

```text
Producer Thread
```

creates data.

---

And:

```text
Consumer Thread
```

processes data.

---

Traditional approach:

```java
BlockingQueue
```

---

But sometimes we need:

```text
Direct Data Swap
```

instead of storing data in a queue.

---

Exchanger provides:

```text
Direct Thread-To-Thread Exchange
```

---

# Visualization

```text
Thread A

Object A

↓

Exchange Point

↓

Receives Object B
```

---

```text
Thread B

Object B

↓

Exchange Point

↓

Receives Object A
```

---

# Creating Exchanger

## Syntax

```java
Exchanger<String> exchanger =
        new Exchanger<>();
```

---

# Example

```java
Exchanger<String> exchanger =
        new Exchanger<>();
```

---

Type:

```java
String
```

can be exchanged.

---

# Exchange Data Between Threads

The most important operation.

---

# exchange()

## Definition

Waits for another thread and exchanges data.

---

## Syntax

```java
T result =
        exchanger.exchange(data);
```

---

# How It Works

Thread A

```java
exchanger.exchange("Java");
```

---

Thread B

```java
exchanger.exchange("Spring");
```

---

After exchange:

---

Thread A receives:

```text
Spring
```

---

Thread B receives:

```text
Java
```

---

# Visualization

```text
Thread A

Java

↓

Exchanger

↓

Spring
```

---

```text
Thread B

Spring

↓

Exchanger

↓

Java
```

---

# Complete Example

## Thread A

```java
Exchanger<String> exchanger =
        new Exchanger<>();

Runnable threadA = () -> {

    try {

        String data = "Java";

        System.out.println(
                "Thread A Sent: "
                + data
        );

        String received =
                exchanger.exchange(data);

        System.out.println(
                "Thread A Received: "
                + received
        );

    } catch(Exception e) {

        e.printStackTrace();
    }
};
```

---

## Thread B

```java
Runnable threadB = () -> {

    try {

        String data = "Spring";

        System.out.println(
                "Thread B Sent: "
                + data
        );

        String received =
                exchanger.exchange(data);

        System.out.println(
                "Thread B Received: "
                + received
        );

    } catch(Exception e) {

        e.printStackTrace();
    }
};
```

---

## Start Threads

```java
new Thread(threadA).start();

new Thread(threadB).start();
```

---

# Possible Output

```text
Thread A Sent: Java

Thread B Sent: Spring

Thread A Received: Spring

Thread B Received: Java
```

---

# Internal Workflow

```text
Thread A Arrives

↓

Waits
```

---

```text
Thread B Arrives

↓

Exchange Happens
```

---

```text
Thread A Continues

Thread B Continues
```

---

# Important Behavior

If only one thread calls:

```java
exchange()
```

---

It waits.

---

Until:

```text
Another Thread Arrives
```

---

# Visualization

```text
Thread A

exchange()

↓

Waiting...
```

---

```text
Thread B Arrives

↓

Exchange Occurs
```

---

# Timed Exchange

Wait only for a specific duration.

---

## Syntax

```java
exchange(
        data,
        timeout,
        unit
);
```

---

# Example

```java
String result =
        exchanger.exchange(
                "Java",
                5,
                TimeUnit.SECONDS
        );
```

---

Meaning

```text
Wait Maximum 5 Seconds
```

---

If partner thread does not arrive:

```java
TimeoutException
```

is thrown.

---

# Example

```java
try {

    exchanger.exchange(
            "Java",
            5,
            TimeUnit.SECONDS
    );

} catch(TimeoutException e) {

    System.out.println(
            "Timed Out"
    );
}
```

---

# Producer Consumer Example

One of the most famous Exchanger use cases.

---

# Traditional Approach

```java
BlockingQueue
```

stores data.

---

# Exchanger Approach

Producer and Consumer exchange buffers.

---

# Producer

```text
Filled Buffer
```

---

# Consumer

```text
Empty Buffer
```

---

# Exchange

```text
Producer Gives Filled Buffer

Consumer Gives Empty Buffer
```

---

# Result

Producer immediately gets:

```text
New Empty Buffer
```

---

Consumer immediately gets:

```text
Filled Buffer
```

---

# Visualization

```text
Producer

Filled Buffer

↓

Exchanger

↓

Empty Buffer
```

---

```text
Consumer

Empty Buffer

↓

Exchanger

↓

Filled Buffer
```

---

# Why Efficient?

Avoids:

```text
Constant Buffer Creation
```

---

Buffers are reused.

---

# Real-world Use Cases

---

# Use Case 1: Producer-Consumer Buffer Exchange

Most common use case.

---

Producer

```text
Creates Data
```

---

Consumer

```text
Processes Data
```

---

Buffers exchanged directly.

---

# Use Case 2: Double Buffering

Common in:

- Graphics rendering
- Video processing
- Gaming engines

---

Thread A

```text
Current Frame
```

---

Thread B

```text
Next Frame
```

---

Frames exchanged.

---

# Use Case 3: Algorithm Collaboration

Two algorithms compute different parts.

---

Exchange intermediate results.

---

Example

```text
Sorting

Searching
```

---

# Use Case 4: Financial Processing

Two systems exchange:

```text
Trade Data

Risk Data
```

---

at synchronization points.

---

# Use Case 5: Distributed Simulations

Different simulation components exchange state information.

---

# Exchanger Lifecycle

```text
Thread A Calls exchange()

↓

Wait
```

---

```text
Thread B Calls exchange()

↓

Swap Data
```

---

```text
Thread A Continues

Thread B Continues
```

---

# Exchanger vs BlockingQueue

A common interview topic.

---

# BlockingQueue

```text
Producer

↓

Queue

↓

Consumer
```

---

Data flows:

```text
One Direction
```

---

# Exchanger

```text
Thread A

↔

Thread B
```

---

Data flows:

```text
Two Directions
```

---

# Comparison Table

| Feature | Exchanger | BlockingQueue |
|----------|----------|----------|
| Data Flow | Two-Way | One-Way |
| Participants | Exactly 2 | Multiple |
| Storage | No | Yes |
| Synchronization | Direct | Indirect |
| Buffer Exchange | Excellent | Not Designed For It |

---

# Exchanger vs SynchronousQueue

Another common interview question.

---

# SynchronousQueue

```text
Producer

↓

Consumer
```

---

Only transfers data.

---

# Exchanger

```text
Producer

↔

Consumer
```

---

Both sides exchange data.

---

# Comparison Table

| Feature | Exchanger | SynchronousQueue |
|----------|----------|----------|
| Exchange Both Ways | Yes | No |
| Data Swap | Yes | No |
| Participants | Two | Multiple |
| Buffer Reuse | Excellent | Limited |

---

# Common Mistakes

---

## Mistake 1

Only One Thread Calls exchange()

---

Example

```java
exchanger.exchange("Java");
```

---

No second thread arrives.

---

Result

```text
Thread Waits Forever
```

---

# Solution

Use timed exchange.

---

```java
exchange(
        data,
        timeout,
        unit
);
```

---

# Mistake 2

Using Exchanger For Many Threads

Bad

```text
10 Threads
```

---

Exchanger is designed for:

```text
Exactly Two Threads
```

---

Use:

```java
BlockingQueue
```

or

```java
Phaser
```

instead.

---

# Mistake 3

Ignoring TimeoutException

Always handle:

```java
TimeoutException
```

when using timed exchange.

---

# Common Interview Questions

## Q1: What is Exchanger?

A synchronization utility that allows two threads to exchange data at a synchronization point.

---

## Q2: How many threads participate in Exchanger?

```text
Exactly Two
```

for each exchange operation.

---

## Q3: What does exchange() do?

Exchanges data between two threads and returns the object received from the other thread.

---

## Q4: What happens if only one thread calls exchange()?

```text
It Waits
```

until another thread arrives.

---

## Q5: What exception can occur in timed exchange?

```java
TimeoutException
```

---

## Q6: Is Exchanger reusable?

```text
Yes
```

It can be used repeatedly for multiple exchanges.

---

## Q7: What is the most common use case?

```text
Producer-Consumer Buffer Exchange
```

---

## Q8: Difference between Exchanger and BlockingQueue?

### BlockingQueue

One-way communication.

---

### Exchanger

Two-way communication.

---

## Q9: Difference between Exchanger and SynchronousQueue?

### SynchronousQueue

Transfers data.

---

### Exchanger

Swaps data.

---

## Q10: Give a real-world use case.

- Buffer swapping
- Double buffering
- Graphics rendering
- Financial systems
- Simulation engines

---

# Key Takeaways

1. `Exchanger` is a synchronization utility introduced in Java 5.
2. It allows two threads to exchange data directly.
3. `exchange()` blocks until a matching thread arrives.
4. After exchange, each thread receives the other thread's object.
5. Timed exchanges are supported using `exchange(data, timeout, unit)`.
6. It is designed specifically for two-thread coordination.
7. The most common use case is Producer-Consumer buffer exchange.
8. It is frequently used in double-buffering and high-performance data processing systems.
9. Unlike BlockingQueue, communication is bidirectional.
10. Unlike SynchronousQueue, both threads exchange objects.
11. Exchanger is reusable and can participate in multiple exchange cycles.
12. Understanding Exchanger is important before learning advanced concurrency patterns, high-performance pipelines, and parallel data processing architectures.