
After learning about:

- Thread Safety
- Synchronization
- volatile
- Race Conditions

you may wonder:

```text
Can we perform thread-safe operations
without using synchronized?
```

The answer is:

```text
Yes
```

Java provides a set of classes called:

```java
Atomic Classes
```

that perform thread-safe operations without traditional locking.

These classes are part of:

```java
java.util.concurrent.atomic
```

---

# Why Atomic Classes?

Before understanding Atomic Classes, let's understand the problem they solve.

---

## Problem: Race Condition

Consider:

```java
int count = 0;
```

Multiple threads execute:

```java
count++;
```

---

### Expected

```text
1000 Threads
=
1000
```

---

### Actual

```text
947
981
992
```

---

## Why?

Because:

```java
count++;
```

is not a single operation.

Internally it becomes:

```text
Read Count
Modify Count
Write Count
```

---

### Example

Suppose:

```java
count = 5
```

---

Thread A:

```text
Read 5
```

---

Thread B:

```text
Read 5
```

---

Thread A:

```text
Write 6
```

---

Thread B:

```text
Write 6
```

---

Result:

```text
Lost Update
```

---

Expected:

```text
7
```

Actual:

```text
6
```

---

# Traditional Solution

Use:

```java
synchronized
```

---

Example

```java
synchronized void increment() {

    count++;
}
```

---

## Problem

Synchronization introduces:

- Lock acquisition
- Lock release
- Context switching
- Thread blocking

---

## Performance Impact

Under heavy contention:

```text
Performance Decreases
```

---

# Atomic Classes Solution

Atomic Classes provide:

```text
Lock-Free Thread Safety
```

using:

```text
CAS (Compare-And-Swap)
```

---

## Benefits

- No explicit locking
- Better scalability
- Better throughput
- Reduced contention
- Non-blocking algorithms

---

# CAS (Compare And Swap)

## Definition

CAS is a hardware-supported atomic operation that updates a value only if it matches an expected value.

---

## Simple Formula

```text
If Current Value == Expected Value

    Update Value

Else

    Retry
```

---

## Syntax Conceptually

```text
CAS(
    currentValue,
    expectedValue,
    newValue
)
```

---

# Example

Current Value:

```text
5
```

---

Thread A wants:

```text
5 -> 6
```

---

CAS checks:

```text
Current == Expected ?

5 == 5
```

---

Success:

```text
Value becomes 6
```

---

# Another Example

Current Value:

```text
6
```

---

Thread B expects:

```text
5
```

---

CAS checks:

```text
6 == 5
```

---

Result:

```text
Failure
```

---

Thread B retries.

---

# Visualization

```text
Current = 10

Expected = 10

New = 11

Match?
   |
  Yes
   |
Update
```

---

# CAS Advantages

- Lock-free
- Fast
- Atomic
- Supported by CPU instructions

---

## Interview Definition

```text
CAS is the foundation of Java Atomic Classes.
```

---

# AtomicInteger

## Definition

A thread-safe integer that supports atomic operations without synchronization.

---

## Package

```java
java.util.concurrent.atomic.AtomicInteger
```

---

## Creation

```java
AtomicInteger count =
        new AtomicInteger();
```

---

Or

```java
AtomicInteger count =
        new AtomicInteger(10);
```

---

# Common Methods

---

## get()

Returns current value.

```java
count.get();
```

---

## set()

Updates value.

```java
count.set(100);
```

---

## incrementAndGet()

Increment first, then return.

```java
count.incrementAndGet();
```

---

Example

```java
AtomicInteger count =
        new AtomicInteger(5);

System.out.println(
        count.incrementAndGet()
);
```

Output

```text
6
```

---

## getAndIncrement()

Return first, then increment.

```java
count.getAndIncrement();
```

---

Output

```text
5
```

Current value becomes:

```text
6
```

---

## decrementAndGet()

```java
count.decrementAndGet();
```

---

## addAndGet()

```java
count.addAndGet(10);
```

---

## compareAndSet()

Most important CAS method.

```java
count.compareAndSet(
        10,
        20
);
```

---

Meaning:

```text
If Value == 10

Update To 20
```

---

# Example

```java
AtomicInteger count =
        new AtomicInteger(0);

Runnable task = () -> {

    for(int i=0;i<1000;i++) {

        count.incrementAndGet();
    }
};
```

---

Result:

```text
Always Correct
```

without synchronization.

---

# AtomicLong

## Definition

Thread-safe version of:

```java
long
```

---

## Creation

```java
AtomicLong counter =
        new AtomicLong();
```

---

## Common Methods

```java
get()
set()
incrementAndGet()
decrementAndGet()
addAndGet()
compareAndSet()
```

---

## Example

```java
AtomicLong requests =
        new AtomicLong();

requests.incrementAndGet();
```

---

## Common Use Cases

- Request counters
- Visitor counts
- Metrics collection

---

# AtomicBoolean

## Definition

Thread-safe version of:

```java
boolean
```

---

## Creation

```java
AtomicBoolean running =
        new AtomicBoolean(true);
```

---

## Example

```java
if(running.compareAndSet(
        true,
        false
)) {

    shutdown();
}
```

---

## Benefit

Guarantees only one thread performs shutdown.

---

## Common Methods

```java
get()
set()
compareAndSet()
```

---

# AtomicReference

## Definition

Provides atomic operations on object references.

---

## Why Needed?

AtomicInteger works only for:

```java
int
```

---

What if we need:

```java
Employee
Order
Configuration
```

objects?

---

Use:

```java
AtomicReference<T>
```

---

# Example

```java
AtomicReference<String> ref =
        new AtomicReference<>("A");
```

---

```java
ref.compareAndSet(
        "A",
        "B"
);
```

---

## Generic Syntax

```java
AtomicReference<Employee>
```

---

## Common Use Cases

- Immutable objects
- Configuration updates
- Cache references

---

# AtomicIntegerArray

## Definition

Thread-safe array of integers.

---

## Creation

```java
AtomicIntegerArray array =
        new AtomicIntegerArray(5);
```

---

## Example

```java
array.incrementAndGet(0);
```

---

## Methods

```java
get(index)
set(index,value)
incrementAndGet(index)
compareAndSet(index,...)
```

---

## Benefit

Each element supports atomic operations.

---

# Why LongAdder Was Introduced

Suppose:

```java
100 Threads
```

increment:

```java
AtomicLong
```

continuously.

---

Problem:

All threads compete for:

```text
Single Value
```

---

Result:

```text
High Contention
```

---

Performance decreases.

---

# LongAdder

## Definition

A high-performance counter designed for high-concurrency environments.

---

## Package

```java
java.util.concurrent.atomic.LongAdder
```

---

## Core Idea

Instead of:

```text
One Counter
```

use:

```text
Multiple Internal Counters
```

---

Each thread updates:

```text
Different Counter Cell
```

---

Final result:

```text
Sum Of All Cells
```

---

# Visualization

AtomicLong

```text
Thread A ----\
Thread B -----\
Thread C ------> Counter
Thread D -----/
```

High contention.

---

LongAdder

```text
Thread A -> Cell1

Thread B -> Cell2

Thread C -> Cell3

Thread D -> Cell4
```

---

Less contention.

---

# Example

```java
LongAdder counter =
        new LongAdder();
```

---

Increment

```java
counter.increment();
```

---

Add

```java
counter.add(10);
```

---

Get Result

```java
counter.sum();
```

---

## Common Use Cases

- Web request counting
- Metrics systems
- Monitoring tools
- Analytics

---

## Interview Point

```text
LongAdder performs better than AtomicLong
under heavy contention.
```

---

# LongAccumulator

## Definition

Generalized version of LongAdder.

Allows custom accumulation operations.

---

## Creation

```java
LongAccumulator accumulator =
        new LongAccumulator(
                Long::max,
                Long.MIN_VALUE
        );
```

---

# Parameters

### Function

```java
Long::max
```

---

### Initial Value

```java
Long.MIN_VALUE
```

---

# Example

```java
accumulator.accumulate(10);

accumulator.accumulate(20);

accumulator.accumulate(15);
```

---

Result

```java
accumulator.get();
```

Output

```text
20
```

---

Because:

```java
Long::max
```

returns maximum value.

---

# Another Example

Finding Sum

```java
LongAccumulator sum =
        new LongAccumulator(
                Long::sum,
                0
        );
```

---

## Common Use Cases

- Maximum value tracking
- Minimum value tracking
- Running totals
- Statistical aggregation

---

# Atomic Classes vs volatile

| Feature | volatile | Atomic Classes |
|----------|----------|---------------|
| Visibility | Yes | Yes |
| Atomicity | No | Yes |
| CAS Support | No | Yes |
| Thread Safe Increment | No | Yes |
| Lock Free | Yes | Yes |
| Compound Operations | No | Yes |

---

# Atomic Classes vs synchronized

| Feature | Atomic Classes | synchronized |
|----------|---------------|-------------|
| Lock-Free | Yes | No |
| Blocking | No | Yes |
| Performance | Better | Lower |
| Atomic Operations | Yes | Yes |
| Multiple Variables | Limited | Yes |
| Complex Critical Sections | No | Yes |

---

# When to Use Atomic Classes

Use Atomic Classes when:

- Single variable updates
- Counters
- Flags
- Metrics
- Sequence generators
- Lock-free programming

---

# When Not to Use Atomic Classes

Avoid when:

- Multiple variables must remain consistent
- Complex business transactions exist
- Critical sections involve many operations

---

Example:

```java
withdraw();
deposit();
updateAudit();
```

---

Use:

```java
synchronized
```

or

```java
Lock
```

instead.

---

# Common Interview Questions

## Q1: Why do we need Atomic Classes?

To perform thread-safe operations without using locks.

---

## Q2: What is CAS?

```text
Compare-And-Swap
```

A lock-free atomic update mechanism.

---

## Q3: Is AtomicInteger thread-safe?

```text
Yes
```

---

## Q4: Is AtomicInteger faster than synchronized?

Generally:

```text
Yes
```

under low-to-medium contention.

---

## Q5: What is compareAndSet()?

```java
compareAndSet(
    expected,
    newValue
)
```

Updates value only if expected value matches current value.

---

## Q6: What is the difference between AtomicLong and LongAdder?

### AtomicLong

```text
Single Counter
```

---

### LongAdder

```text
Multiple Internal Counters
```

Better under high contention.

---

## Q7: When should LongAdder be preferred?

When many threads frequently update a shared counter.

---

## Q8: What is AtomicReference?

Allows atomic operations on object references.

---

## Q9: Does AtomicInteger use synchronized internally?

```text
No
```

It uses:

```text
CAS Operations
```

---

## Q10: What is LongAccumulator?

A customizable version of LongAdder that supports user-defined accumulation functions.

---

# Key Takeaways

1. Atomic Classes provide thread-safe operations without explicit locking.
2. They are built on CAS (Compare-And-Swap), a lock-free hardware-supported mechanism.
3. `AtomicInteger`, `AtomicLong`, and `AtomicBoolean` are thread-safe versions of primitive types.
4. `AtomicReference` enables atomic updates to object references.
5. `AtomicIntegerArray` provides atomic operations on array elements.
6. `compareAndSet()` is the core CAS operation used by atomic classes.
7. Atomic Classes guarantee both visibility and atomicity for supported operations.
8. `LongAdder` significantly outperforms `AtomicLong` under high contention.
9. `LongAccumulator` allows custom aggregation operations such as max, min, or sum.
10. Atomic Classes are excellent for counters, metrics, and lock-free algorithms.
11. They are not a replacement for synchronization when multiple variables must be updated together.
12. Understanding Atomic Classes is essential before learning `Lock`, `ReentrantLock`, and advanced concurrent programming techniques.