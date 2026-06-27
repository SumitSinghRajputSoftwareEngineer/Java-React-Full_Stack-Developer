# 1. Introduction to Multithreading

Before learning Java Multithreading, it is important to understand some fundamental concepts such as **Process**, **Thread**, **Concurrency**, **Parallelism**, and why multithreading is required in modern applications.

---

# What is a Process?

## Definition of Process

A **Process** is an independent program that is currently being executed by the operating system.

In simple terms:

> A process is a running instance of a program.

When we execute an application, the Operating System (OS) creates a process for it and allocates required resources such as memory, CPU time, files, and network connections.

### Examples

| Program | Process Created |
|----------|----------------|
| Chrome Browser | Chrome Process |
| VS Code | VS Code Process |
| Spotify | Spotify Process |
| Java Application | JVM Process |

For example:

```text
Double-click Chrome
        ↓
Operating System creates
        ↓
Chrome Process
```

---

## Characteristics of a Process

A process has:

- Unique Process ID (PID)
- Own memory space
- Own resources
- One or more threads
- Independent execution environment

---

## Process Lifecycle

A process goes through several states during execution.

```text
        New
         |
         v
      Ready
         |
         v
      Running
      /     \
     /       \
Blocked     Ready
     |
     v
 Running
     |
     v
 Terminated
```

### States

#### 1. New

Process is being created.

#### 2. Ready

Process is ready to execute and waiting for CPU allocation.

#### 3. Running

CPU is executing the process instructions.

#### 4. Blocked / Waiting

Process waits for an external event:

Examples:

- File read
- Network response
- Database query

#### 5. Terminated

Process execution completes.

---

## Process Memory Structure

Every process gets its own memory space.

```text
+-----------------------+
|      Stack            |
+-----------------------+
|       Heap            |
+-----------------------+
| Initialized Data      |
+-----------------------+
| Uninitialized Data    |
+-----------------------+
| Program Code (Text)   |
+-----------------------+
```

### 1. Program Code (Text Segment)

Contains executable instructions.

Example:

```java
System.out.println("Hello");
```

Compiled machine instructions are stored here.

---

### 2. Data Segment

Stores:

- Static variables
- Global variables

Example:

```java
static int count = 10;
```

---

### 3. Heap Memory

Stores dynamically allocated objects.

Example:

```java
Student s = new Student();
```

Object is stored inside Heap.

---

### 4. Stack Memory

Stores:

- Method calls
- Local variables
- Function execution information

Example:

```java
public void display() {
    int x = 10;
}
```

Variable `x` is stored in Stack.

---

## Context Switching

### Definition

When CPU switches from one process to another, it is called:

> Context Switching

The OS saves the current process state and loads another process state.

---

### Example

```text
CPU Executing Process A
          ↓
Process A paused
          ↓
CPU State Saved
          ↓
Process B Loaded
          ↓
CPU Executes Process B
```

---

### Why Context Switching is Expensive?

OS must save:

- Program Counter
- Registers
- Memory Information
- Process State

This consumes CPU resources.

---

## Real World Example

Suppose you have:

- Chrome
- Spotify
- VS Code

all running simultaneously.

```text
CPU
 ↓
Chrome
 ↓
VS Code
 ↓
Spotify
 ↓
Chrome
```

CPU rapidly switches between them creating the illusion of simultaneous execution.

---

# What is a Thread?

## Definition of Thread

A **Thread** is the smallest unit of execution inside a process.

A process may contain:

```text
One Process
    |
    +--- Thread 1
    +--- Thread 2
    +--- Thread 3
```

---

### Important Point

> Every process has at least one thread called the Main Thread.

Without a thread, a process cannot execute.

---

## Real World Analogy

Imagine a restaurant.

### Restaurant = Process

### Workers = Threads

```text
Restaurant (Process)

Chef        -> Thread 1
Cashier     -> Thread 2
Waiter      -> Thread 3
Cleaner     -> Thread 4
```

All workers belong to the same restaurant but perform different tasks.

---

# Thread vs Process

| Feature | Process | Thread |
|----------|----------|----------|
| Definition | Running program | Smallest execution unit |
| Memory | Separate memory | Shared memory |
| Creation Cost | High | Low |
| Communication | Expensive | Easy |
| Context Switching | Slow | Faster |
| Isolation | High | Low |
| Failure Impact | Usually isolated | Can affect entire process |

---

## Example

### Multiple Processes

```text
Chrome Process
VS Code Process
Spotify Process
```

Each has separate memory.

---

### Multiple Threads

```text
Chrome Process
     |
     +-- UI Thread
     +-- Rendering Thread
     +-- Network Thread
```

All share same memory.

---

# Thread Lifecycle

A Java thread passes through multiple states.

```text
NEW
 |
 v
RUNNABLE
 |
 v
RUNNING
 |
 +--------+
 |         |
 v         v
WAITING   BLOCKED
 |         |
 +----+----+
      |
      v
TERMINATED
```

---

## NEW

Thread object created.

```java
Thread t = new Thread();
```

---

## RUNNABLE

After calling:

```java
t.start();
```

Thread becomes eligible for execution.

---

## RUNNING

Thread scheduler allocates CPU.

---

## BLOCKED

Waiting to acquire a lock.

---

## WAITING

Waiting indefinitely for another thread.

Examples:

```java
wait();
join();
```

---

## TIMED_WAITING

Waiting for specified time.

Examples:

```java
sleep(1000);
join(1000);
```

---

## TERMINATED

Execution completed.

---

# Thread Memory Structure

Threads share some memory and have their own private memory.

```text
Process Memory

       Heap
        |
  -----------------
  |       |       |
Thread1 Thread2 Thread3

Stack1  Stack2  Stack3
```

---

## Shared Resources

All threads share:

### Heap Memory

```java
Student s = new Student();
```

Shared among threads.

### Method Area

Class metadata.

### Static Variables

```java
static int count;
```

Shared by all threads.

---

## Private Resources

Each thread has its own:

### Stack

Stores:

- Local Variables
- Method Calls

Example:

```java
int x = 10;
```

Each thread gets its own copy.

---

# Advantages of Threads

## 1. Better Performance

Multiple tasks can execute simultaneously.

Example:

```text
Download File
Play Music
Update UI
```

---

## 2. Faster Context Switching

Switching between threads is cheaper than switching between processes.

---

## 3. Resource Sharing

Threads share memory.

```java
Shared Object
```

No need for expensive IPC.

---

## 4. Improved Responsiveness

UI remains responsive while background work executes.

Example:

```text
UI Thread
Background Download Thread
```

---

## 5. Better CPU Utilization

Modern CPUs contain multiple cores.

Threads can utilize all cores efficiently.

---

# Why Multithreading?

## Definition

Multithreading is the capability of executing multiple threads concurrently within a process.

---

## Why Was It Introduced?

Suppose a browser performs:

1. Rendering webpage
2. Downloading images
3. Playing videos

If only one thread exists:

```text
Render Page
     ↓
Download Image
     ↓
Play Video
```

User experiences delays.

With multithreading:

```text
Thread 1 → Render
Thread 2 → Download
Thread 3 → Play Video
```

Tasks progress simultaneously.

---

## Improved Performance

Multiple tasks execute concurrently.

Example:

```java
Generate Report
Send Email
Update Database
```

---

## Better CPU Utilization

Modern systems contain:

- 2 Core
- 4 Core
- 8 Core
- 16 Core CPUs

Threads allow efficient usage.

---

## Concurrent Task Execution

Example:

E-Commerce Application

```text
Thread 1 → Payment
Thread 2 → Inventory
Thread 3 → Notification
```

---

## Responsive Applications

Example:

```text
UI Thread
Background Processing Thread
```

User interface remains responsive.

---

## Real World Examples

### Browser

```text
UI Thread
Network Thread
Rendering Thread
JavaScript Thread
```

### Banking Application

```text
Transaction Thread
Notification Thread
Fraud Detection Thread
```

### Online Shopping

```text
Payment Thread
Inventory Thread
Order Thread
Email Thread
```

---

# Single Threaded vs Multithreaded Applications

| Feature | Single Threaded | Multithreaded |
|-----------|-----------|-----------|
| Execution | One task at a time | Multiple tasks |
| Performance | Lower | Higher |
| Responsiveness | Poor | Better |
| CPU Usage | Limited | Better |
| Complexity | Simple | Complex |
| Scalability | Lower | Higher |

---

## Single Thread Example

```java
readFile();
processData();
sendEmail();
```

Execution:

```text
Read File
    ↓
Process Data
    ↓
Send Email
```

---

## Multithread Example

```java
Thread A -> readFile()
Thread B -> processData()
Thread C -> sendEmail()
```

All execute concurrently.

---

# Concurrency vs Parallelism

These terms are often confused.

---

## Concurrency

### Definition

Concurrency means:

> Multiple tasks making progress during overlapping time periods.

Tasks may not execute exactly at the same instant.

---

### Example

Single CPU:

```text
Task A
Task B
Task A
Task B
Task A
```

CPU switches rapidly.

---

### Diagram

```text
Time →
A A B A B B A
```

---

## Parallelism

### Definition

Parallelism means:

> Multiple tasks execute at exactly the same time.

Requires multiple CPU cores.

---

### Example

```text
Core 1 → Task A
Core 2 → Task B
```

---

### Diagram

```text
Core 1: AAAAA
Core 2: BBBBB
```

Both run simultaneously.

---

## Concurrency vs Parallelism

| Feature | Concurrency | Parallelism |
|-----------|-----------|-----------|
| Simultaneous Execution | Not required | Required |
| CPU Cores | One or More | Multiple |
| Goal | Responsiveness | Speed |
| Example | Thread switching | Multiple cores executing |

---

# Synchronous vs Asynchronous Execution

---

## Synchronous Execution

Tasks execute one after another.

Next task waits until previous task finishes.

Example:

```java
task1();
task2();
task3();
```

Execution:

```text
Task1
  ↓
Task2
  ↓
Task3
```

---

### Characteristics

- Blocking
- Sequential
- Easy to understand

---

## Asynchronous Execution

Tasks start and continue independently.

Example:

```java
CompletableFuture.runAsync(() -> task1());
```

Main thread continues.

---

### Characteristics

- Non-blocking
- Better performance
- Higher complexity

---

## Comparison

| Feature | Synchronous | Asynchronous |
|-----------|-----------|-----------|
| Blocking | Yes | No |
| Waiting | Required | Not Required |
| Performance | Lower | Better |
| Complexity | Simple | Higher |

---

# CPU Bound vs I/O Bound Tasks

Understanding this concept is critical for thread pool tuning and system design.

---

## CPU Bound Tasks

Tasks that spend most of their time using CPU.

Examples:

- Image Processing
- Video Encoding
- Encryption
- Mathematical Computations
- AI Model Training

---

### Example

```java
for(int i=0; i<1000000000; i++) {
    sum += i;
}
```

CPU is busy all the time.

---

### Optimization

Use:

```text
Number of Threads ≈ Number of CPU Cores
```

---

## I/O Bound Tasks

Tasks spend most time waiting for external resources.

Examples:

- Database Calls
- API Calls
- File Reading
- Network Requests

---

### Example

```java
restTemplate.getForObject(...);
```

Thread waits for server response.

---

### Optimization

Use:

```text
More Threads Than CPU Cores
```

because many threads remain idle while waiting.

---

## CPU Bound vs I/O Bound

| Feature | CPU Bound | I/O Bound |
|-----------|-----------|-----------|
| Bottleneck | CPU | External Resource |
| Waiting Time | Low | High |
| CPU Usage | High | Low |
| Thread Count | Near CPU Cores | Higher Than CPU Cores |
| Examples | Encryption | API Calls |

---

# Key Takeaways

1. A Process is a running instance of a program.
2. A Thread is the smallest unit of execution within a process.
3. Every Java application starts with a Main Thread.
4. Threads share Heap Memory but have separate Stacks.
5. Multithreading improves responsiveness and CPU utilization.
6. Concurrency means tasks make progress together; Parallelism means tasks execute simultaneously.
7. Synchronous execution is blocking; Asynchronous execution is non-blocking.
8. CPU-bound tasks require CPU power; I/O-bound tasks spend time waiting.
9. Context switching between threads is cheaper than switching between processes.
10. Understanding these fundamentals is essential before learning Java Thread creation, Synchronization, Locks, Executor Framework, and CompletableFuture.