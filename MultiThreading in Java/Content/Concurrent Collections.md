
When multiple threads access and modify collections simultaneously, normal collections such as:

```java
ArrayList
HashMap
HashSet
LinkedList
TreeMap
```

are not thread-safe.

This can lead to:

- Data corruption
- Race conditions
- Lost updates
- ConcurrentModificationException
- Infinite loops (in older HashMap implementations)

To solve these problems, Java provides:

```java
Concurrent Collections
```

under:

```java
java.util.concurrent
```

These collections are designed for:

```text
High Concurrency

Better Performance

Thread Safety
```

without locking the entire collection.

---

# Why Concurrent Collections?

Suppose multiple threads are updating a shared:

```java
HashMap
```

---

Example

```java
Map<Integer, String> map =
        new HashMap<>();
```

---

Thread A

```java
map.put(1, "Java");
```

---

Thread B

```java
map.put(2, "Spring");
```

---

Thread C

```java
map.remove(1);
```

---

Result

```text
Unpredictable Behavior
```

---

Possible outcomes:

```text
Data Corruption

Lost Updates

Inconsistent State
```

---

# Solution

Use collections designed for concurrency.

Example

```java
ConcurrentHashMap
```

---

# Problems with Normal Collections

## Problem 1: Race Condition

Multiple threads update the same collection simultaneously.

---

Example

```java
list.add("A");
```

executed by multiple threads.

---

Result

```text
Unpredictable Data
```

---

# Problem 2: Data Corruption

Example

```java
HashMap
```

being resized by multiple threads.

---

May result in:

```text
Corrupted Internal Structure
```

---

# Problem 3: Poor Scalability

Using:

```java
Collections.synchronizedMap()
```

locks the entire collection.

---

Result

```text
Reduced Throughput
```

---

# ConcurrentModificationException

One of the most commonly asked interview topics.

---

## What is ConcurrentModificationException?

Occurs when a collection is modified while it is being iterated.

---

# Example

```java
List<String> list =
        new ArrayList<>();

list.add("A");
list.add("B");

for(String value : list) {

    list.remove(value);
}
```

---

Output

```text
ConcurrentModificationException
```

---

# Why Does It Happen?

Iterator detects:

```text
Structural Modification
```

during iteration.

---

This behavior is called:

```text
Fail-Fast
```

---

# Fail-Fast Iterator

Example

```java
ArrayList
HashMap
HashSet
```

---

Behavior

```text
Detect Modification

Throw Exception
```

---

# Concurrent Collections Use

```text
Fail-Safe Iterators
```

instead.

---

Result

```text
No ConcurrentModificationException
```

---

# ConcurrentHashMap

Most important concurrent collection.

---

## Definition

A thread-safe implementation of:

```java
Map
```

designed for high concurrency.

---

## Package

```java
java.util.concurrent.ConcurrentHashMap
```

---

# Example

```java
ConcurrentHashMap<Integer, String> map =
        new ConcurrentHashMap<>();
```

---

## Benefits

- Thread-safe
- High performance
- Lock-free reads
- Minimal locking

---

# Internal Working

Understanding this is a very common interview topic.

---

# Java 7 Internal Architecture

Uses:

```text
Segment Locking
```

---

# Segment Locking (Java 7)

Map divided into:

```text
Segments
```

---

Visualization

```text
Segment 1

Segment 2

Segment 3

Segment 4
```

---

Each segment has its own lock.

---

Result

```text
Multiple Threads

Can Update Different Segments
```

simultaneously.

---

# Example

Thread A

```text
Segment 1
```

---

Thread B

```text
Segment 2
```

---

No contention.

---

# Drawback

Still coarse-grained locking.

---

# Java 8 Internal Architecture

Major redesign.

---

# Bucket Level Locking (Java 8+)

Instead of segment locks:

```text
Bucket Level Locking
```

---

Visualization

```text
Bucket 1

Bucket 2

Bucket 3

Bucket 4
```

---

Lock only the affected bucket.

---

Result

```text
Much Higher Concurrency
```

---

# Additional Optimization

Uses:

```text
CAS
```

(Compare-And-Swap)

for many operations.

---

Reducing locking further.

---

# Read Operations

Most reads are:

```text
Lock-Free
```

---

Example

```java
map.get(key);
```

---

Multiple reads can occur simultaneously.

---

# computeIfAbsent()

One of the most useful methods.

---

## Definition

Computes a value only if key is absent.

---

## Syntax

```java
computeIfAbsent(
        key,
        mappingFunction
);
```

---

# Example

```java
map.computeIfAbsent(
        1,
        key -> "Java"
);
```

---

Behavior

```text
If Key Exists

Return Existing Value
```

---

Otherwise

```text
Create New Value
```

---

# Traditional Approach

```java
if(!map.containsKey(1)) {

    map.put(1, "Java");
}
```

---

Problem

Not atomic.

---

# ConcurrentHashMap Solution

```java
computeIfAbsent()
```

---

Atomic operation.

---

# putIfAbsent()

## Definition

Adds value only if key does not already exist.

---

## Syntax

```java
map.putIfAbsent(
        1,
        "Java"
);
```

---

# Example

```java
map.putIfAbsent(
        1,
        "Spring"
);
```

---

If key already exists:

```text
No Update
```

---

# Benefit

Atomic operation.

---

# replace()

## Definition

Replaces value only when conditions are satisfied.

---

# Example

```java
map.replace(
        1,
        "Java",
        "Spring"
);
```

---

Meaning

```text
Replace Only If

Current Value = Java
```

---

This prevents:

```text
Lost Updates
```

---

# CopyOnWriteArrayList

## Definition

Thread-safe variant of:

```java
ArrayList
```

---

## Package

```java
java.util.concurrent.CopyOnWriteArrayList
```

---

# Internal Working

Whenever modification occurs:

```text
Create New Copy
```

---

Example

```java
list.add("Java");
```

---

Internally

```text
Copy Existing Array

Add Element

Replace Reference
```

---

# Benefit

Readers never block.

---

# Visualization

```text
Old Array

[A,B,C]

↓

Copy

[A,B,C,D]
```

---

# Best Use Case

```text
Many Reads

Few Writes
```

---

Examples

```text
Configuration Data

Reference Data

Subscriber Lists
```

---

# Drawback

Writes are expensive.

---

# CopyOnWriteArraySet

## Definition

Thread-safe Set built using:

```java
CopyOnWriteArrayList
```

---

# Example

```java
CopyOnWriteArraySet<String> set =
        new CopyOnWriteArraySet<>();
```

---

## Characteristics

- Thread-safe
- No duplicates
- Read optimized

---

# ConcurrentSkipListMap

## Definition

Concurrent sorted map.

---

## Package

```java
java.util.concurrent.ConcurrentSkipListMap
```

---

# Characteristics

```text
Sorted Keys

Thread Safe

Scalable
```

---

# Example

```java
ConcurrentSkipListMap<Integer,String> map =
        new ConcurrentSkipListMap<>();
```

---

# Output

Keys remain sorted.

---

```text
1
2
3
4
```

---

# Internal Data Structure

Uses:

```text
Skip List
```

instead of:

```text
Red Black Tree
```

---

# ConcurrentSkipListSet

## Definition

Concurrent sorted set.

---

## Package

```java
ConcurrentSkipListSet
```

---

# Characteristics

```text
Sorted

Thread Safe

Scalable
```

---

# Example

```java
ConcurrentSkipListSet<Integer> set =
        new ConcurrentSkipListSet<>();
```

---

Output

```text
Sorted Elements
```

---

# ConcurrentLinkedQueue

## Definition

Thread-safe non-blocking queue.

---

## Package

```java
java.util.concurrent.ConcurrentLinkedQueue
```

---

# Characteristics

```text
FIFO

Lock-Free

Non-Blocking
```

---

# Example

```java
ConcurrentLinkedQueue<String> queue =
        new ConcurrentLinkedQueue<>();
```

---

Operations

```java
offer()

poll()

peek()
```

---

# Visualization

```text
Head

A -> B -> C

Tail
```

---

# Use Cases

- Message Queues
- Event Processing
- Task Buffers

---

# ConcurrentLinkedDeque

## Definition

Thread-safe double-ended queue.

---

## Package

```java
java.util.concurrent.ConcurrentLinkedDeque
```

---

# Supports

Front Operations

```java
addFirst()

pollFirst()
```

---

Back Operations

```java
addLast()

pollLast()
```

---

# Example

```java
ConcurrentLinkedDeque<String> deque =
        new ConcurrentLinkedDeque<>();
```

---

# Use Cases

- Work Stealing
- Task Scheduling
- Buffer Management

---

# BlockingQueue

One of the most important concurrent collections.

---

## Definition

A thread-safe queue that supports:

```text
Blocking Operations
```

---

## Package

```java
java.util.concurrent.BlockingQueue
```

---

# Why Needed?

Producer

```text
Produces Data
```

---

Consumer

```text
Consumes Data
```

---

Problem

Consumer may find queue empty.

---

# Solution

Consumer waits automatically.

---

# Example

```java
BlockingQueue<Integer> queue =
        new ArrayBlockingQueue<>(10);
```

---

# put()

## Definition

Adds element.

Blocks if queue is full.

---

```java
queue.put(100);
```

---

# take()

## Definition

Removes element.

Blocks if queue is empty.

---

```java
queue.take();
```

---

# Visualization

```text
Producer

↓

Queue

↓

Consumer
```

---

# Common Implementations

---

## ArrayBlockingQueue

Fixed size queue.

---

```java
new ArrayBlockingQueue<>(100);
```

---

# Characteristics

```text
Bounded

Thread Safe
```

---

## LinkedBlockingQueue

Linked-list based queue.

---

```java
new LinkedBlockingQueue<>();
```

---

# Characteristics

```text
Optionally Bounded
```

---

## PriorityBlockingQueue

Priority-based queue.

---

```java
new PriorityBlockingQueue<>();
```

---

Elements processed according to priority.

---

## DelayQueue

Delayed processing queue.

---

Tasks become available only after delay expires.

---

# Concurrent Collections Summary

| Collection | Thread Safe | Sorted | Blocking |
|------------|-------------|---------|----------|
| ConcurrentHashMap | Yes | No | No |
| CopyOnWriteArrayList | Yes | No | No |
| CopyOnWriteArraySet | Yes | No | No |
| ConcurrentSkipListMap | Yes | Yes | No |
| ConcurrentSkipListSet | Yes | Yes | No |
| ConcurrentLinkedQueue | Yes | No | No |
| ConcurrentLinkedDeque | Yes | No | No |
| BlockingQueue | Yes | Depends | Yes |

---

# ConcurrentHashMap vs HashMap

| Feature | HashMap | ConcurrentHashMap |
|----------|----------|----------|
| Thread Safe | No | Yes |
| Concurrent Reads | Unsafe | Safe |
| Concurrent Writes | Unsafe | Safe |
| Locking | None | Fine-Grained |
| Performance | High (Single Thread) | High (Multi Thread) |

---

# Fail-Fast vs Fail-Safe

| Feature | Fail-Fast | Fail-Safe |
|----------|----------|----------|
| Example | ArrayList | CopyOnWriteArrayList |
| Modification During Iteration | Exception | Allowed |
| Iterator View | Current Structure | Snapshot |
| ConcurrentModificationException | Yes | No |

---

# Common Interview Questions

## Q1: Why do we need Concurrent Collections?

To safely support concurrent access and modification by multiple threads.

---

## Q2: What is ConcurrentModificationException?

An exception thrown when a collection is structurally modified while being iterated using a fail-fast iterator.

---

## Q3: Is ConcurrentHashMap fully synchronized?

```text
No
```

It uses fine-grained synchronization and lock-free reads.

---

## Q4: What was used in ConcurrentHashMap Java 7?

```text
Segment Locking
```

---

## Q5: What is used in ConcurrentHashMap Java 8+?

```text
Bucket-Level Locking + CAS
```

---

## Q6: Why is computeIfAbsent() important?

Because it performs:

```text
Check + Create
```

atomically.

---

## Q7: When should CopyOnWriteArrayList be used?

```text
Many Reads

Few Writes
```

---

## Q8: Why are writes expensive in CopyOnWriteArrayList?

Because every modification creates a new copy of the underlying array.

---

## Q9: What is BlockingQueue used for?

Producer-Consumer communication.

---

## Q10: Difference between ConcurrentLinkedQueue and BlockingQueue?

### ConcurrentLinkedQueue

```text
Non-Blocking
```

---

### BlockingQueue

```text
Blocking Operations Supported
```

---

# Key Takeaways

1. Concurrent Collections provide thread-safe alternatives to normal collections.
2. They offer better scalability than `Collections.synchronizedXXX()`.
3. `ConcurrentHashMap` is the most widely used concurrent collection.
4. Java 7 used Segment Locking, while Java 8 uses Bucket-Level Locking and CAS.
5. `computeIfAbsent()`, `putIfAbsent()`, and `replace()` provide atomic operations.
6. `CopyOnWriteArrayList` and `CopyOnWriteArraySet` are ideal for read-heavy workloads.
7. `ConcurrentSkipListMap` and `ConcurrentSkipListSet` maintain sorted data with thread safety.
8. `ConcurrentLinkedQueue` and `ConcurrentLinkedDeque` provide lock-free concurrent queues.
9. `BlockingQueue` is the foundation of Producer-Consumer patterns and Executor Framework internals.
10. Understanding Concurrent Collections is essential before learning Advanced Executor Framework, Reactive Programming, and High-Concurrency System Design.