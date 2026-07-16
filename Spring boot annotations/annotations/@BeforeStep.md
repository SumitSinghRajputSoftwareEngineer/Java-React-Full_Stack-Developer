
## Definition

`@BeforeStep` is a Spring Batch annotation used inside a **StepExecutionListener** to execute logic **before a step starts execution**.

It tells Spring:

> Run this method once before each step begins.

Package:

```java
import org.springframework.batch.core.annotation.BeforeStep;
```

---

# Simple Understanding

In Spring Batch lifecycle:

```text id="a1k9pq"
Job Start
   ↓
Step 1 → @BeforeStep → execute Step
   ↓
Step 2 → @BeforeStep → execute Step
   ↓
@AfterJob
```

---

So:

```text id="x7m2ld"
@BeforeStep = Pre-processing hook for a single step
```

---

# Real-Life Analogy

Think of a factory pipeline:

```text id="p3t8qc"
Each step = one stage of production
```

Before each stage:

- Load materials
- Configure machine
- Check inputs

---

That preparation phase is:

```text id="h8v3kd"
@BeforeStep
```

---

# Why Do We Need @BeforeStep?

We use it for:

```text id="a6m2qp"
Initialize step-specific variables
Log step start
Read step parameters
Set up context for processing
Validate inputs
```

---

# Basic Example

```java id="k7x2ld"
@Component
public class StepListener implements StepExecutionListener {

    @BeforeStep
    public void beforeStep(StepExecution stepExecution) {

        System.out.println("Step starting: " 
            + stepExecution.getStepName());
    }
}
```

---

# How It Works

When a step starts:

```text id="q2m8xp"
Job starts
   ↓
Step 1 begins
   ↓
@BeforeStep executed
   ↓
Reader → Processor → Writer
   ↓
Step ends
```

---

# Access Step Information

```java id="n6v2mc"
@BeforeStep
public void beforeStep(StepExecution stepExecution) {

    String stepName =
        stepExecution.getStepName();

    System.out.println("Starting step: " + stepName);
}
```

---

# Access Job Parameters

```java id="t8k2md"
@BeforeStep
public void beforeStep(StepExecution stepExecution) {

    JobParameters params =
        stepExecution.getJobParameters();

    String fileName =
        params.getString("fileName");

    System.out.println("Processing file: " + fileName);
}
```

---

# Real Production Example: File Processing Step

```java id="f9k2md"
@Component
public class FileStepListener {

    @BeforeStep
    public void beforeStep(StepExecution stepExecution) {

        System.out.println(
            "Starting step: " + stepExecution.getStepName()
        );

        System.out.println(
            "Preparing resources for step..."
        );
    }
}
```

---

# Step Lifecycle Flow

```text id="m5v7qp"
Job Triggered
     ↓
Step 1 starts
     ↓
@BeforeStep
     ↓
Reader → Processor → Writer
     ↓
@AfterStep
     ↓
Step 2 starts
     ↓
@BeforeStep
     ↓
...
```

---

# Where is @BeforeStep Used?

## 1. Logging

```text id="x2n9pd"
Log step start time
Log step name
```

---

## 2. Initialization

```text id="h5m9qp"
Initialize step variables
Setup temporary storage
```

---

## 3. Validation

```text id="v6k3ld"
Check input data before processing
```

---

## 4. Context Setup

```text id="c8p2mq"
Prepare resources for step execution
```

---

# Example: File Reader Setup

```java id="r6m2ld"
@Component
public class FileStepListener {

    @BeforeStep
    public void beforeStep(StepExecution stepExecution) {

        System.out.println("Opening file for step: "
            + stepExecution.getStepName());
    }
}
```

---

# @BeforeStep vs @BeforeJob

| Feature | @BeforeJob | @BeforeStep |
|----------|------------|-------------|
| Scope | Entire Job | Single Step |
| Execution | Once per job | Before each step |
| Use Case | Global setup | Step-level setup |

---

# @BeforeStep vs @AfterStep

| Feature | @BeforeStep | @AfterStep |
|----------|-------------|------------|
| When | Before step starts | After step ends |
| Purpose | Setup | Cleanup |
| Frequency | Every step | Every step |

---

# Spring Internals

When step starts:

```text id="j8v2md"
JobLauncher
    ↓
Step begins
    ↓
Spring detects StepExecutionListener
    ↓
@BeforeStep executed
    ↓
Step processing runs
```

---

# Important Note

`@BeforeStep` works only when used inside:

```java id="k4m8qp"
StepExecutionListener
```

---

# Correct Pattern

```java id="t7v2md"
@Component
public class MyStepListener {

    @BeforeStep
    public void beforeStep(StepExecution stepExecution) {
        System.out.println("Step starting...");
    }
}
```

---

# Real Production Use Cases

## ETL Pipelines

```text id="p7m8qd"
Prepare data source for step
Validate input chunk
```

---

## File Processing

```text id="x6k9ld"
Open file stream
Set file pointers
```

---

## Data Migration

```text id="v5m2qp"
Initialize target schema context
```

---

## Batch Processing

```text id="c9n3ld"
Initialize step-level counters
```

---

# Common Mistakes

## Mistake 1: Not using StepExecutionListener

```text id="k8v2md"
@BeforeStep alone won't work
```

---

## Mistake 2: Using for heavy processing

```text id="n7v2ld"
Avoid business logic here
```

---

## Mistake 3: Assuming it runs once per job

```text id="q6m2qp"
It runs for every step
```

---

# Common Interview Questions

## What is @BeforeStep?

Runs before each step in a Spring Batch job.

---

## Where is it used?

Inside StepExecutionListener.

---

## How many times does it execute?

Once per step.

---

## Difference from @BeforeJob?

Job-level vs step-level execution.

---

## Can we access JobParameters?

Yes via StepExecution.

---

# Enterprise Best Practice

Use `@BeforeStep` for:

```text id="m8v2ld"
Step initialization
Logging step start
Validating inputs
Setting context
```

---

Avoid for:

```text id="c7m8qp"
Business processing logic
Heavy computations
Cross-step logic
```

---

# Key Points To Remember

- `@BeforeStep` runs before each step execution.
- It is part of `StepExecutionListener`.
- Executes once per step.
- Used for initialization and setup.
- Can access step and job context.
- Not for business logic.
- Works alongside `@AfterStep`.
- Important in ETL and batch pipelines.
- Frequently asked Spring Batch concept.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@BeforeJob` | Before entire job |
| `@AfterJob` | After entire job |
| `@BeforeStep` | Before each step |
| `@AfterStep` | After each step |

---

# Interview Shortcut

```java id="x5m8qp"
@Component
public class MyStepListener {

    @BeforeStep
    public void beforeStep(StepExecution stepExecution) {
        System.out.println("Step starting: "
            + stepExecution.getStepName());
    }
}
```

Meaning:

```text id="t7v2ld"
Execute logic before each step

Initialize step resources

Prepare execution context
```

---

# Real Production Example

```text id="k8m2qp"
ETL Batch System
```

Flow:

```text id="n6v2ld"
Job starts
   ↓
Step 1 → @BeforeStep → execute
   ↓
Step 2 → @BeforeStep → execute
   ↓
Step N → @BeforeStep → execute
   ↓
Job completes
```

Result:

```text id="c7m2qp"
Controlled step execution
Proper initialization per step
Better batch reliability
```

---

# Final Summary

`@BeforeStep` is a Spring Batch annotation used inside a `StepExecutionListener` to execute logic before each step starts, typically for initialization, logging, validation, or resource setup in enterprise batch processing systems.
```