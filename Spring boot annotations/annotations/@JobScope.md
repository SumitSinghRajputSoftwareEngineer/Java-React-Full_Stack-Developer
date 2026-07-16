
## Definition

`@JobScope` is a Spring Batch annotation used to create a **bean whose lifecycle is bound to a single Job execution**.

It tells Spring:

> Create a new instance of this bean for each job run and allow access to JobParameters at runtime.

Package:

```java
import org.springframework.batch.core.configuration.annotation.JobScope;
```

---

# Simple Understanding

In Spring:

```text id="q8m7nx"
Singleton Bean → One instance for entire application
```

---

In Spring Batch:

```text id="xk2p9d"
@JobScope → One instance per Job execution
```

---

So:

```text id="h4t7kc"
@JobScope = Job-specific bean with runtime parameters
```

---

# Real-Life Analogy

Imagine a factory order system:

```text id="k9v3aa"
Each customer order = one Job
```

---

Now:

```text id="p7d2er"
@JobScope = All resources allocated per order
```

---

While:

```text id="m3x8ql"
@StepScope = Resources per step inside order
```

---

# Why Do We Need @JobScope?

We need `@JobScope` when:

```text id="u6t9wb"
We want job-level parameters
We want dynamic configuration per job run
We want late binding of values
```

---

# Basic Example

```java id="n3v1qp"
@Bean
@JobScope
public JobExecutionListener listener() {
    return new JobExecutionListener() {

        @Override
        public void beforeJob(JobExecution jobExecution) {
            System.out.println("Job started");
        }
    };
}
```

---

# Using Job Parameters

Most powerful use case:

```java id="c9r2ld"
@Bean
@JobScope
public String jobName(
    @Value("#{jobParameters['jobName']}")
    String jobName) {

    return jobName;
}
```

---

# JobScope in Step Configuration

```java id="t5k8mn"
@Bean
@JobScope
public Step step1(StepBuilderFactory steps) {

    return steps.get("step1")
            .tasklet((contribution, chunkContext) -> {
                System.out.println("Executing Step 1");
                return RepeatStatus.FINISHED;
            })
            .build();
}
```

---

# What Happens Internally?

When job starts:

```text id="b8x2pk"
Job triggered
     ↓
JobParameters passed
     ↓
@JobScope beans created
     ↓
Values injected at runtime
     ↓
Steps executed
     ↓
Job completes
     ↓
Beans destroyed
```

---

# Key Feature: Late Binding

Without @JobScope:

```text id="j7c0ld"
Values resolved at application startup ❌
```

---

With @JobScope:

```text id="r2m8pw"
Values resolved at job execution time ✔
```

---

# Example: Dynamic Job Configuration

```java id="k4n1sd"
@Bean
@JobScope
public String reportType(
    @Value("#{jobParameters['type']}")
    String type) {

    return type;
}
```

---

Run:

```text id="p6t9xa"
--type=DAILY_REPORT
```

---

# JobScope Flow

```text id="y1m8vd"
Job Starts
    ↓
JobParameters provided
    ↓
@JobScope bean created
    ↓
Values injected
    ↓
Steps execute
    ↓
Job completes
```

---

# @JobScope vs @StepScope

| Feature | @JobScope | @StepScope |
|----------|-----------|------------|
| Lifecycle | Entire Job | Single Step |
| Scope | Coarse | Fine-grained |
| When Created | Once per job | Per step execution |
| Use Case | Job-level config | Step-level processing |

---

# Real Production Use Cases

## 1. Report Generation

```text id="q1v8mc"
Generate daily/weekly/monthly reports
```

---

## 2. ETL Jobs

```text id="t9k3pd"
Job-level configuration (source, target)
```

---

## 3. Data Migration

```text id="h7n2wx"
Environment-based job execution
```

---

## 4. Batch Processing Control

```text id="z3c8qr"
Job-wide flags like "full load" or "incremental"
```

---

# Example: Dynamic Report Job

```java id="f8x2qw"
@Bean
@JobScope
public Job reportJob(JobBuilderFactory jobs, Step step1,
    @Value("#{jobParameters['reportType']}")
    String reportType) {

    return jobs.get("reportJob")
            .start(step1)
            .build();
}
```

---

# Example: Job Listener with JobScope

```java id="m5t9ld"
@Bean
@JobScope
public JobExecutionListener listener(
    @Value("#{jobParameters['jobName']}")
    String jobName) {

    return new JobExecutionListener() {

        @Override
        public void beforeJob(JobExecution jobExecution) {
            System.out.println("Starting job: " + jobName);
        }
    };
}
```

---

# Spring Internals

When using @JobScope:

```text id="x4b7md"
Spring creates proxy bean
        ↓
Waits for job execution
        ↓
Resolves JobParameters
        ↓
Creates actual bean instance
        ↓
Injects into job lifecycle
```

---

# Common Mistakes

## Mistake 1: Not passing JobParameters

```text id="d6p1rk"
Bean receives null values
```

---

## Mistake 2: Using @JobScope in wrong place

```text id="s9x2mn"
Using in Controllers or Services ❌
```

---

## Mistake 3: Wrong SpEL syntax

Correct:

```java id="j8c4vd"
@Value("#{jobParameters['fileName']}")
```

Wrong:

```java id="k2v9mq"
@Value("jobParameters.fileName")
```

---

# When to Use @JobScope?

Use it when:

```text id="w1n8cd"
You need job-level parameters
You configure entire job dynamically
You want late binding at job start
```

---

Do NOT use it when:

```text id="r3k9xp"
Static beans
Reusable services
Non-batch components
```

---

# Common Interview Questions

## What is @JobScope?

Creates a bean per job execution.

---

## Difference between JobScope and StepScope?

JobScope = whole job  
StepScope = single step

---

## Why is it needed?

To access JobParameters dynamically.

---

## Is it singleton?

No, it is job-bound.

---

## Where is it used?

Spring Batch configurations.

---

# Enterprise Best Practice

Use `@JobScope` for:

```text id="a8m3pq"
Job-level configuration
Dynamic batch execution
Report generation setup
ETL orchestration
```

---

# Key Points To Remember

- `@JobScope` creates a bean per job execution.
- Lifecycle is tied to Job, not application.
- Supports JobParameters via SpEL.
- Enables late binding (runtime resolution).
- Used in Spring Batch configurations.
- Works alongside StepScope.
- Ideal for job-level configuration.
- Not used for business services.
- Important for ETL and batch pipelines.
- Common Spring Batch interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableBatchProcessing` | Enable batch framework |
| `@JobScope` | Job-level bean lifecycle |
| `@StepScope` | Step-level bean lifecycle |
| `Job` | Entire batch execution |
| `Step` | Part of job |

---

# Interview Shortcut

```java id="v7k2md"
@Bean
@JobScope
public String jobName(
    @Value("#{jobParameters['jobName']}") String jobName) {

    return jobName;
}
```

Meaning:

```text id="c9x3ql"
Create bean per job execution

Inject runtime parameters

Configure job dynamically
```

---

# Real Production Example

```text id="n4p7kd"
Banking Batch System
```

Flow:

```text id="u2m8qp"
Job triggered with parameters
     ↓
@JobScope beans created
     ↓
Job configuration injected
     ↓
Steps execute
     ↓
Batch processing completes
```

Result:

```text id="e8v1mq"
Dynamic job execution
Flexible batch configuration
Scalable enterprise processing
```

---

# Final Summary

`@JobScope` is a Spring Batch annotation that creates a job-specific bean instance and allows access to runtime JobParameters, making it essential for dynamic batch job configuration such as ETL pipelines, reporting systems, and large-scale data processing workflows.
```