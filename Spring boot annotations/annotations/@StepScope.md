
## Definition

`@StepScope` is a Spring Batch annotation used to create a **bean whose lifecycle is bound to a single Step execution**.

It tells Spring:

> Create a fresh instance of this bean for each step run, and allow access to job/step runtime parameters.

Package:

```java
import org.springframework.batch.core.scope.context.StepScope;
```

---

# Simple Understanding

Think of Spring Beans normally:

```text
Singleton Bean → Created once for entire application
```

---

But in Batch:

```text
@StepScope → New instance per step execution
```

---

So:

```text
@StepScope = Step-specific bean (runtime-aware)
```

---

# Real-Life Analogy

Imagine a factory:

```text
Normal Bean → One machine used forever
```

---

StepScoped Bean:

```text
One machine per production batch
Reset and reused for each batch job
```

---

Or:

```text
Each worker gets fresh tools per task
```

---

# Why Do We Need @StepScope?

In Spring Batch, we often need:

```text
Job parameters (dynamic values)
Step-specific state
Late binding values
```

---

Without `@StepScope`:

```text
You cannot access JobParameters safely in beans
```

---

With `@StepScope`:

```text
You can inject runtime values using SpEL
```

---

# Basic Example

```java
@Bean
@StepScope
public ItemReader<String> reader() {
    return new ListItemReader<>(List.of("A", "B", "C"));
}
```

---

But real power is here 👇

---

# Using Job Parameters

```java
@Bean
@StepScope
public FlatFileItemReader<String> reader(
    @Value("#{jobParameters['fileName']}") String fileName) {

    FlatFileItemReader<String> reader =
        new FlatFileItemReader<>();

    reader.setResource(new FileSystemResource(fileName));

    return reader;
}
```

---

# What Happens Internally?

When job starts:

```text
Job Parameters are passed
        ↓
Step starts execution
        ↓
@StepScope bean is created
        ↓
Values injected dynamically
        ↓
Step executes
```

---

# Key Feature: Late Binding

Without `@StepScope`:

```text
Values resolved at application startup ❌
```

---

With `@StepScope`:

```text
Values resolved at runtime ✔
```

---

# Example: Dynamic File Processing

```java
@Bean
@StepScope
public FlatFileItemReader<String> reader(
    @Value("#{jobParameters['inputFile']}")
    String inputFile) {

    FlatFileItemReader<String> reader =
        new FlatFileItemReader<>();

    reader.setResource(
        new FileSystemResource(inputFile)
    );

    return reader;
}
```

---

Run job:

```text
--inputFile=/data/users.csv
```

---

# StepScope Flow

```text
Job Starts
   ↓
JobParameters Provided
   ↓
Step Starts
   ↓
@StepScope Bean Created
   ↓
Parameters Injected
   ↓
Processing Begins
```

---

# Where is @StepScope Used?

## 1. ItemReader

```text
Read dynamic files or DB queries
```

---

## 2. ItemProcessor

```text
Use runtime values for transformation
```

---

## 3. ItemWriter

```text
Write to dynamic destinations
```

---

# Example: StepScoped Processor

```java
@Bean
@StepScope
public ItemProcessor<String, String> processor(
    @Value("#{jobParameters['prefix']}")
    String prefix) {

    return item -> prefix + item;
}
```

---

Input:

```text
prefix=USER_
```

Output:

```text
USER_A
USER_B
```

---

# @StepScope vs Singleton Bean

| Feature | @StepScope | Singleton Bean |
|----------|------------|----------------|
| Lifecycle | Per Step | Application-wide |
| Job Parameters | Accessible | Not reliable |
| Memory Usage | Higher | Lower |
| Use Case | Batch processing | General services |

---

# @StepScope vs @JobScope

| Feature | @StepScope | @JobScope |
|----------|------------|------------|
| Scope | Step level | Job level |
| Lifecycle | Each step | Entire job |
| Granularity | Fine | Coarse |

---

# Real Production Use Cases

## File Processing

```text
CSV/Excel ingestion
Dynamic file paths
```

---

## ETL Pipelines

```text
Different input sources per job run
```

---

## Data Migration

```text
Environment-specific config
```

---

## Report Generation

```text
Date-based reports
```

---

# Without @StepScope Problem

```java
@Bean
public ItemReader reader() {
    // Cannot access jobParameters here safely
}
```

---

Problem:

```text
Values resolved too early → NULL or incorrect values
```

---

# With @StepScope Solution

```java
@Bean
@StepScope
public ItemReader reader(
    @Value("#{jobParameters['date']}")
    String date) {

    return new CustomReader(date);
}
```

---

Now:

```text
Correct runtime value injected
```

---

# Spring Internals

When you use @StepScope:

Spring creates:

```text
Proxy Bean
    ↓
Resolves actual bean at runtime
    ↓
Injects JobContext
    ↓
Creates step-specific instance
```

---

# Common Mistakes

## Mistake 1: Missing @StepScope

```text
JobParameters becomes NULL
```

---

## Mistake 2: Using in Singleton Bean

```text
Runtime values not available
```

---

## Mistake 3: Forgetting SpEL syntax

Correct:

```java
@Value("#{jobParameters['file']}")
```

Wrong:

```java
@Value("jobParameters.file")
```

---

# Common Interview Questions

## What is @StepScope?

Creates a bean per step execution.

---

## Why do we need it?

To access JobParameters at runtime.

---

## Difference from singleton bean?

Singleton = one instance  
StepScope = per step execution

---

## Can we inject JobParameters without it?

No (recommended way is StepScope).

---

## Where is it used?

Batch processing (readers, processors, writers).

---

# Enterprise Best Practice

Use `@StepScope` when:

```text
You need dynamic job parameters
You process files or DB dynamically
You require runtime configuration
```

---

Avoid when:

```text
Static services
Shared business logic
Controllers
```

---

# Key Points To Remember

- `@StepScope` creates step-specific beans.
- Bean is recreated for each step execution.
- Allows access to JobParameters using SpEL.
- Used in Spring Batch (Reader/Processor/Writer).
- Supports late binding (runtime resolution).
- Avoid using singleton scope for dynamic batch values.
- Works closely with JobScope.
- Essential for file-based batch processing.
- Improves flexibility in ETL pipelines.
- Frequently asked Spring Batch interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableBatchProcessing` | Enable batch framework |
| `@StepScope` | Step-level bean lifecycle |
| `@JobScope` | Job-level bean lifecycle |
| `ItemReader` | Read data |
| `ItemProcessor` | Transform data |
| `ItemWriter` | Write data |

---

# Interview Shortcut

```java
@Bean
@StepScope
public ItemReader reader(
    @Value("#{jobParameters['fileName']}") String fileName) {

    return new FlatFileItemReader<>();
}
```

Meaning:

```text
Create bean per step execution

Inject runtime job parameters

Enable dynamic batch processing
```

---

# Real Production Example

```text
ETL File Processing System
```

Flow:

```text
Job Starts
   ↓
Pass fileName as parameter
   ↓
Step starts
   ↓
@StepScope bean created
   ↓
File dynamically loaded
   ↓
Data processed
   ↓
Job completes
```

Result:

```text
Flexible batch execution
Dynamic file handling
Robust enterprise ETL system
```

---

# Final Summary

`@StepScope` is a Spring Batch annotation that creates a step-specific bean instance and allows access to runtime JobParameters, making it essential for dynamic, parameter-driven batch processing such as file ingestion, ETL pipelines, and large-scale data processing systems.
```