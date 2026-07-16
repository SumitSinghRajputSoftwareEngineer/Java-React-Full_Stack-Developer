
## Definition

`@AfterStep` is a Spring Batch annotation used inside a **StepExecutionListener** to execute logic **after a step finishes execution**.

It tells Spring:

> Run this method once after each step completes (success or failure).

Package:

```java
import org.springframework.batch.core.annotation.AfterStep;
```

---

# Simple Understanding

In Spring Batch lifecycle:

```text id="a1k9pq"
Job Start
   ↓
Step 1 → @BeforeStep → execute Step → @AfterStep
   ↓
Step 2 → @BeforeStep → execute Step → @AfterStep
   ↓
Job End
```

---

So:

```text id="x7m2ld"
@AfterStep = Post-processing hook for a single step
```

---

# Real-Life Analogy

Think of a factory production line:

```text id="p3t8qc"
Each step = one stage of production
```

After each stage:

- Check output quality
- Clean machines
- Log results
- Move finished goods forward

---

That cleanup + validation phase is:

```text id="h8v3kd"
@AfterStep
```

---

# Why Do We Need @AfterStep?

We use it for:

```text id="a6m2qp"
Log step completion
Validate output
Clean temporary data
Collect metrics
Decide next flow based on result
```

---

# Basic Example

```java id="k7x2ld"
@Component
public class StepListener implements StepExecutionListener {

    @AfterStep
    public ExitStatus afterStep(StepExecution stepExecution) {

        System.out.println("Step finished: " 
            + stepExecution.getStepName());

        return stepExecution.getExitStatus();
    }
}
```

---

# How It Works

When a step completes:

```text id="q2m8xp"
Step starts
   ↓
@BeforeStep
   ↓
Processing (Reader → Processor → Writer)
   ↓
@AfterStep
   ↓
Step ends
```

---

# Access Step Result

```java id="n6v2mc"
@AfterStep
public ExitStatus afterStep(StepExecution stepExecution) {

    System.out.println("Status: " 
        + stepExecution.getStatus());

    return stepExecution.getExitStatus();
}
```

---

# Check Success or Failure

```java id="t8k2md"
@AfterStep
public ExitStatus afterStep(StepExecution stepExecution) {

    if (stepExecution.getStatus() == BatchStatus.COMPLETED) {
        System.out.println("Step Successful");
    } else {
        System.out.println("Step Failed");
    }

    return stepExecution.getExitStatus();
}
```

---

# Real Production Example: File Processing Step

```java id="f9k2md"
@Component
public class FileStepListener {

    @AfterStep
    public ExitStatus afterStep(StepExecution stepExecution) {

        System.out.println("Step completed: "
            + stepExecution.getStepName());

        System.out.println("Status: "
            + stepExecution.getStatus());

        fileService.archiveTempFiles();

        return stepExecution.getExitStatus();
    }
}
```

---

# Step Lifecycle Flow

```text id="m5v7qp"
Job Triggered
     ↓
Step 1
   ↓
@BeforeStep
   ↓
Processing
   ↓
@AfterStep
     ↓
Step 2
   ↓
@BeforeStep
   ↓
Processing
   ↓
@AfterStep
     ↓
Job Complete
```

---

# Where is @AfterStep Used?

## 1. Logging

```text id="x2n9pd"
Log step completion time
Log execution status
```

---

## 2. Cleanup

```text id="h5m9qp"
Delete temporary files
Close resources
```

---

## 3. Validation

```text id="v6k3ld"
Validate processed data
Check output correctness
```

---

## 4. Metrics Collection

```text id="c8p2mq"
Capture step performance metrics
```

---

## 5. Flow Control

```text id="z9k1ld"
Decide next step based on status
```

---

# Example: Conditional Flow Control

```java id="r6m2ld"
@Component
public class StepListener implements StepExecutionListener {

    @AfterStep
    public ExitStatus afterStep(StepExecution stepExecution) {

        if (stepExecution.getWriteCount() == 0) {
            return new ExitStatus("NO_DATA");
        }

        return stepExecution.getExitStatus();
    }
}
```

---

# @AfterStep vs @BeforeStep

| Feature | @BeforeStep | @AfterStep |
|----------|-------------|------------|
| When | Before step starts | After step ends |
| Purpose | Setup | Cleanup |
| Scope | Per step | Per step |
| Return Value | No | Yes (ExitStatus) |

---

# Spring Internals

When step finishes:

```text id="j8v2md"
Step execution completes
    ↓
Spring finds StepExecutionListener
    ↓
@AfterStep executed
    ↓
ExitStatus returned
```

---

# Important Feature: ExitStatus

`@AfterStep` can modify step outcome:

```java id="k4m8qp"
return new ExitStatus("CUSTOM_STATUS");
```

---

# Correct Pattern

```java id="t7v2md"
@Component
public class MyStepListener {

    @AfterStep
    public ExitStatus afterStep(StepExecution stepExecution) {

        System.out.println("Step finished: "
            + stepExecution.getStepName());

        return stepExecution.getExitStatus();
    }
}
```

---

# Real Production Use Cases

## ETL Pipelines

```text id="p7m8qd"
Validate output data
Archive processed files
```

---

## File Processing

```text id="x6k9ld"
Close file streams
Move processed files
```

---

## Data Migration

```text id="v5m2qp"
Verify migrated records
Log success metrics
```

---

## Batch Processing

```text id="c9n3ld"
Collect step statistics
Trigger next step conditionally
```

---

# Common Mistakes

## Mistake 1: Not returning ExitStatus

```text id="k8v2md"
Step status not updated properly
```

---

## Mistake 2: Heavy processing inside listener

```text id="n7v2ld"
Avoid long-running tasks here
```

---

## Mistake 3: Assuming it runs once per job

```text id="q6m2qp"
It runs after every step
```

---

# Common Interview Questions

## What is @AfterStep?

Runs after each step completes in Spring Batch.

---

## Where is it used?

Inside StepExecutionListener.

---

## Can it modify step status?

Yes, via ExitStatus.

---

## Difference from @AfterJob?

Step-level vs job-level execution.

---

## Does it run on failure?

Yes, always runs after step ends.

---

# Enterprise Best Practice

Use `@AfterStep` for:

```text id="m8v2ld"
Cleanup step resources
Logging execution results
Validating output
Collecting metrics
```

---

Avoid for:

```text id="c7m8qp"
Business logic
Heavy computations
Cross-step orchestration
```

---

# Key Points To Remember

- `@AfterStep` runs after each step execution.
- It is part of `StepExecutionListener`.
- Executes once per step.
- Can access step execution results.
- Can modify ExitStatus.
- Used for cleanup, logging, validation.
- Not for business logic.
- Works alongside `@BeforeStep`.
- Important in Spring Batch lifecycle.
- Frequently asked interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@BeforeStep` | Before step execution |
| `@AfterStep` | After step execution |
| `@BeforeJob` | Before job execution |
| `@AfterJob` | After job execution |

---

# Interview Shortcut

```java id="x5m8qp"
@Component
public class MyStepListener {

    @AfterStep
    public ExitStatus afterStep(StepExecution stepExecution) {
        System.out.println("Step completed: "
            + stepExecution.getStepName());
        return stepExecution.getExitStatus();
    }
}
```

Meaning:

```text id="t7v2ld"
Execute logic after each step

Collect results

Cleanup resources

Optionally change step outcome
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
Step 1 → @BeforeStep → execute → @AfterStep
   ↓
Step 2 → @BeforeStep → execute → @AfterStep
   ↓
Job completes
```

Result:

```text id="c7m2qp"
Controlled execution lifecycle
Proper cleanup per step
Accurate step monitoring
```

---

# Final Summary

`@AfterStep` is a Spring Batch annotation used inside a `StepExecutionListener` to execute logic after each step finishes, typically for cleanup, logging, validation, and status control in enterprise batch processing systems.
```