
One of the most important skills for a Java developer working in production systems is:

```text
Thread Dump Analysis
```

Many production issues such as:

- Application hangs
- Deadlocks
- Slow response times
- High CPU usage
- Thread pool exhaustion
- Infinite loops

can be diagnosed using:

```text
Thread Dumps
```

---

# What is Thread Dump?

## Definition

A Thread Dump is a snapshot of all threads running inside a JVM at a particular point in time.

---

It contains information about:

- Thread Names
- Thread IDs
- Thread States
- Stack Traces
- Locked Resources
- Waiting Threads
- Deadlocks

---

# Simple Definition

Think of a Thread Dump as:

```text
A Photograph
Of Every Thread
Inside The JVM
```

---

# Why Thread Dumps Are Important?

Suppose a production application becomes:

```text
Slow
```

---

Questions arise:

```text
Which Thread Is Stuck?

Which Thread Is Waiting?

Is There A Deadlock?

Which Thread Is Using CPU?
```

---

A thread dump helps answer these questions.

---

# Real-World Analogy

Imagine a factory.

---

Manager wants to know:

```text
What Every Worker
Is Doing Right Now
```

---

Worker 1

```text
Working
```

---

Worker 2

```text
Waiting
```

---

Worker 3

```text
Blocked
```

---

Thread Dump provides the same information for JVM threads.

---

# What Information Does a Thread Dump Contain?

Example:

```text
"Thread-1" #25 prio=5 os_prio=0 tid=0x000001
java.lang.Thread.State: RUNNABLE

at com.app.Service.process()
at com.app.Controller.execute()
```

---

Contains:

### Thread Name

```text
Thread-1
```

---

### Thread ID

```text
#25
```

---

### Priority

```text
prio=5
```

---

### Thread State

```text
RUNNABLE
```

---

### Stack Trace

```text
process()

↓

execute()
```

---

# Common Use Cases

---

# Use Case 1

Application Hangs

---

Need to identify:

```text
Stuck Threads
```

---

# Use Case 2

Deadlock Investigation

---

Need to identify:

```text
Threads Waiting Forever
```

---

# Use Case 3

High CPU Usage

---

Need to identify:

```text
Busy Threads
```

---

# Use Case 4

Thread Pool Exhaustion

---

Need to identify:

```text
Threads Not Returning
```

---

# Use Case 5

Performance Analysis

---

Need to identify:

```text
Long Running Operations
```

---

# Generating Thread Dumps

There are multiple ways.

---

# jstack

One of the most common tools.

---

## What is jstack?

A JDK utility that generates thread dumps for a running JVM.

---

## Syntax

```bash
jstack <pid>
```

---

# Example

Find JVM process.

```bash
jps
```

Output

```text
12345 MyApplication
```

---

Generate dump.

```bash
jstack 12345
```

---

Output

```text
All JVM Threads
```

---

# Save To File

```bash
jstack 12345 > threaddump.txt
```

---

Very common in production.

---

# Force Dump

```bash
jstack -F 12345
```

---

Useful when JVM is unresponsive.

---

# jcmd

Modern replacement for many JDK tools.

---

## Syntax

```bash
jcmd <pid> Thread.print
```

---

# Example

```bash
jcmd 12345 Thread.print
```

---

Output

```text
Complete Thread Dump
```

---

# Save To File

```bash
jcmd 12345 Thread.print > dump.txt
```

---

# Why Prefer jcmd?

Advantages:

- More powerful
- Better diagnostics
- Supported by modern JVMs

---

# VisualVM

GUI-based JVM monitoring tool.

---

## Features

- Heap Analysis
- CPU Profiling
- Thread Monitoring
- Thread Dumps

---

# Steps

### Open VisualVM

---

### Select JVM Process

---

### Threads Tab

---

### Thread Dump

---

Visualization available.

---

# Example View

```text
Thread A -> RUNNABLE

Thread B -> WAITING

Thread C -> BLOCKED
```

---

Very useful for beginners.

---

# JConsole

Another GUI monitoring tool.

---

## Features

- Thread Monitoring
- Memory Monitoring
- MBeans
- CPU Statistics

---

# Steps

### Start JConsole

```bash
jconsole
```

---

### Connect To JVM

---

### Open Threads Tab

---

### Click Thread Dump

---

Dump generated.

---

# Sample Thread Dump

```text
"Thread-1"

java.lang.Thread.State: RUNNABLE

at com.app.Service.process()
at com.app.Main.main()
```

---

# Reading Thread Dumps

The most important skill.

---

# Structure

```text
Thread Information

↓

Thread State

↓

Stack Trace

↓

Lock Information
```

---

# Example

```text
"worker-1"

java.lang.Thread.State: WAITING

at java.lang.Object.wait()

at com.app.Task.run()
```

---

Meaning:

```text
Thread Waiting
On Object.wait()
```

---

# Thread States in Dumps

---

# RUNNABLE

Example

```text
java.lang.Thread.State: RUNNABLE
```

---

Meaning

```text
Thread Is Running

OR

Ready To Run
```

---

Not necessarily using CPU right now.

---

# BLOCKED

Example

```text
java.lang.Thread.State: BLOCKED
```

---

Meaning

```text
Waiting For Lock
```

---

Common Cause

```java
synchronized
```

contention.

---

# Example

```java
synchronized(lock) {

}
```

---

Another thread already owns:

```text
lock
```

---

Current thread becomes:

```text
BLOCKED
```

---

# WAITING

Example

```text
java.lang.Thread.State: WAITING
```

---

Common Causes

```java
wait()

join()

LockSupport.park()
```

---

Thread waits indefinitely.

---

# TIMED_WAITING

Example

```text
java.lang.Thread.State:
TIMED_WAITING
```

---

Common Causes

```java
sleep()

join(timeout)

await(timeout)
```

---

Waiting with timeout.

---

# TERMINATED

Thread completed execution.

---

Usually not visible in most dumps.

---

# Reading Stack Traces

Suppose dump contains:

```text
"worker-1"

at com.app.OrderService.process()

at com.app.OrderController.execute()

at java.lang.Thread.run()
```

---

Read from:

```text
Top

↓

Bottom
```

---

Top method:

```text
Current Execution Point
```

---

Meaning:

```text
Thread Currently
Inside process()
```

---

# Identifying Busy Threads

High CPU issue.

---

Example

```text
RUNNABLE

at com.app.Calculator.compute()
```

---

Repeated thread dumps show:

```text
Same Method
```

---

Likely CPU-intensive code.

---

# Example

Thread Dump 1

```text
compute()
```

---

Thread Dump 2

```text
compute()
```

---

Thread Dump 3

```text
compute()
```

---

Likely:

```text
Infinite Loop

Heavy Computation
```

---

# Lock Information

Example

```text
- waiting to lock
<0x000001>
```

---

Meaning

```text
Thread Wants Lock

But Doesn't Own It
```

---

Example

```text
- locked
<0x000001>
```

---

Meaning

```text
Thread Owns Lock
```

---

# Detecting Deadlocks

One of the most important uses of thread dumps.

---

# What Is Deadlock?

Thread A waits for:

```text
Lock 1
```

owned by:

```text
Thread B
```

---

Thread B waits for:

```text
Lock 2
```

owned by:

```text
Thread A
```

---

Result

```text
Nobody Progresses
```

---

# Example

Thread A

```java
synchronized(lock1) {

    synchronized(lock2) {

    }
}
```

---

Thread B

```java
synchronized(lock2) {

    synchronized(lock1) {

    }
}
```

---

Potential deadlock.

---

# Deadlock In Thread Dump

JVM automatically detects deadlocks.

---

Example

```text
Found one Java-level deadlock:
=============================
```

---

Thread A

```text
waiting to lock

lock2
```

---

Thread B

```text
waiting to lock

lock1
```

---

JVM reports:

```text
Deadlock Detected
```

---

# Sample Deadlock Output

```text
Found one Java-level deadlock:

Thread-1 waiting for lock2

Thread-2 waiting for lock1
```

---

Extremely useful in production.

---

# Analyzing Deadlocks

Look for:

```text
waiting to lock
```

and

```text
locked
```

---

Example

```text
Thread A

locked lock1

waiting lock2
```

---

```text
Thread B

locked lock2

waiting lock1
```

---

Deadlock confirmed.

---

# Multiple Thread Dumps

A common production technique.

---

Take:

```text
3 Thread Dumps
```

---

Interval

```text
10 Seconds
```

---

Example

```text
Dump 1
```

---

10 seconds later

```text
Dump 2
```

---

10 seconds later

```text
Dump 3
```

---

Compare them.

---

# Why Multiple Dumps?

Single dump shows:

```text
Current State
```

---

Multiple dumps show:

```text
Thread Behavior
Over Time
```

---

Useful for:

- Infinite loops
- Deadlocks
- Thread starvation
- CPU issues

---

# Production Troubleshooting Strategy

---

## Application Slow

Check:

```text
RUNNABLE Threads
```

---

## Application Frozen

Check:

```text
WAITING

BLOCKED
```

---

## High CPU

Check:

```text
RUNNABLE Threads
```

with same stack trace.

---

## Suspected Deadlock

Check:

```text
Deadlock Section
```

---

## Thread Pool Exhausted

Check:

```text
Worker Threads
```

stuck in same operation.

---

# Common Tools Comparison

| Tool | CLI/GUI | Production Friendly |
|--------|--------|--------|
| jstack | CLI | Excellent |
| jcmd | CLI | Excellent |
| VisualVM | GUI | Good |
| JConsole | GUI | Good |

---

# jstack vs jcmd

| Feature | jstack | jcmd |
|----------|----------|----------|
| Older Tool | Yes | No |
| Thread Dump | Yes | Yes |
| JVM Diagnostics | Limited | Extensive |
| Recommended Today | Good | Better |

---

# Common Mistakes

---

## Mistake 1

Taking Only One Dump

---

May miss:

```text
Changing Thread Behavior
```

---

Take multiple dumps.

---

# Mistake 2

Ignoring Thread State

---

Always check:

```text
RUNNABLE

BLOCKED

WAITING
```

---

before reading full stack trace.

---

# Mistake 3

Looking Only At Deadlocks

---

Many issues are:

```text
Thread Pool Exhaustion

Infinite Loops

Lock Contention
```

---

without deadlocks.

---

# Mistake 4

Ignoring Lock Information

---

Look for:

```text
locked
```

and

```text
waiting to lock
```

---

# Common Interview Questions

## Q1: What is a Thread Dump?

A snapshot of all JVM threads, including their states, stack traces, and lock information.

---

## Q2: Why are Thread Dumps useful?

To diagnose:

- Deadlocks
- Hangs
- High CPU usage
- Thread contention
- Performance issues

---

## Q3: How do you generate a Thread Dump?

Using:

```bash
jstack <pid>
```

or

```bash
jcmd <pid> Thread.print
```

---

## Q4: What does RUNNABLE mean in a Thread Dump?

The thread is either running or ready to run.

---

## Q5: What does BLOCKED mean?

The thread is waiting to acquire a monitor lock.

---

## Q6: What does WAITING mean?

The thread is waiting indefinitely for another thread or event.

---

## Q7: How can Thread Dumps help identify high CPU issues?

Repeated dumps showing the same RUNNABLE stack trace often indicate a CPU-intensive loop or long-running computation.

---

## Q8: How can you detect deadlocks?

Look for the JVM-generated:

```text
Found one Java-level deadlock
```

section.

---

## Q9: Why collect multiple Thread Dumps?

To observe thread behavior over time and identify stuck threads.

---

## Q10: Which tool is preferred today?

```text
jcmd
```

is generally preferred over older tools like `jstack`.

---

# Senior Interview Scenario

### Production Issue

```text
Application Response Time Suddenly Increased
```

---

Steps:

1. Capture 3 thread dumps at 10-second intervals.
2. Identify RUNNABLE threads consuming CPU.
3. Check BLOCKED threads for lock contention.
4. Check WAITING threads for external dependencies.
5. Check deadlock section.
6. Analyze thread pool worker threads.
7. Correlate findings with application logs and metrics.

---

# Key Takeaways

1. A Thread Dump is a snapshot of all threads in a JVM.
2. It includes thread names, states, stack traces, and lock information.
3. Thread Dumps are essential for diagnosing production concurrency issues.
4. Common thread states are RUNNABLE, BLOCKED, WAITING, and TIMED_WAITING.
5. `jstack` and `jcmd` are the most commonly used tools for generating thread dumps.
6. VisualVM and JConsole provide GUI-based thread dump analysis.
7. Deadlocks can often be automatically detected by the JVM.
8. Multiple thread dumps are more valuable than a single dump for troubleshooting.
9. Lock information helps identify contention and synchronization bottlenecks.
10. Repeated RUNNABLE stack traces often indicate CPU-intensive or stuck code.
11. Thread Dump analysis is a core skill for Java production support and performance tuning.
12. Mastering Thread Dumps is essential before learning JVM Performance Tuning, Garbage Collection Analysis, Java Flight Recorder (JFR), and Advanced Production Troubleshooting.