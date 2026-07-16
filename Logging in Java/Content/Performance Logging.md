# Performance Logging

> **Goal:** Understand Performance Logging, Execution Time Logging, Monitoring Slow APIs, Performance Metrics, production monitoring techniques, performance bottleneck identification, and interview concepts.

---

# 1. Introduction to Performance Logging

Modern applications must not only work correctly but also perform efficiently.

---

# Example

Consider two APIs:

---

API 1

```text
Response Time = 50 ms
```

---

API 2

```text
Response Time = 10 seconds
```

---

Both APIs return correct responses.

---

Question:

```text
Which API provides a better user experience?
```

---

Answer:

```text
API 1
```

because it responds much faster.

---

# Why Performance Matters?

Users expect:

```text
Fast Responses

Low Latency

High Throughput

Good User Experience
```

---

Slow applications can lead to:

```text
Poor User Experience

Customer Complaints

Revenue Loss

System Failures
```

---

# Solution

Use:

```text
Performance Logging
```

---

# What is Performance Logging?

## Definition

> Performance Logging is the practice of measuring and logging application performance characteristics such as execution time, response time, throughput, and resource usage.

---

# Purpose

Performance Logging helps identify:

```text
Slow APIs

Slow Database Queries

Performance Bottlenecks

High Latency Operations
```

---

# Typical Metrics Logged

```text
Execution Time

API Response Time

Database Query Time

Memory Usage

CPU Usage

Throughput
```

---

# Performance Logging Flow

```text
Request

   ↓

Capture Start Time

   ↓

Execute Logic

   ↓

Capture End Time

   ↓

Calculate Duration

   ↓

Log Metrics
```

---

# Benefits

```text
Performance Monitoring

Bottleneck Detection

Optimization

Capacity Planning
```

---

# 2. Execution Time Logging

Most common form of performance logging.

---

## What is Execution Time?

Execution Time is the duration required to execute a piece of code.

---

# Formula

```text
Execution Time

=

End Time

-

Start Time
```

---

# Example

Start:

```text
10:00:00
```

---

End:

```text
10:00:02
```

---

Execution Time:

```text
2000 ms
```

---

# Why Log Execution Time?

Helps determine:

```text
How Long An Operation Took

Whether Performance Is Acceptable

Which Components Are Slow
```

---

# Basic Example

```java
long start =
        System.currentTimeMillis();

processEmployee();

long end =
        System.currentTimeMillis();

log.info(
    "Execution Time: {} ms",
    (end - start)
);
```

---

# Output

```text
Execution Time: 150 ms
```

---

# How It Works

```text
Capture Start Time

      ↓

Execute Logic

      ↓

Capture End Time

      ↓

Calculate Difference
```

---

# Internal Flow

```text
Method Call

      ↓

Start Time

      ↓

Method Execution

      ↓

End Time

      ↓

Duration Logged
```

---

# Production Example

```java
public Employee getEmployee(
        Long id) {

    long start =
        System.currentTimeMillis();

    Employee employee =
            employeeRepository
                    .findById(id);

    long end =
        System.currentTimeMillis();

    log.info(
        "getEmployee() took {} ms",
        (end - start)
    );

    return employee;
}
```

---

# Output

```text
getEmployee() took 42 ms
```

---

# Benefits

```text
Performance Visibility

Slow Method Detection

Optimization Guidance
```

---

# 3. Monitoring Slow APIs

One of the primary goals of performance logging.

---

# What is a Slow API?

An API whose response time exceeds acceptable limits.

---

# Example

Fast API:

```text
50 ms
```

---

Acceptable API:

```text
200 ms
```

---

Slow API:

```text
5000 ms
```

---

# Why Monitor Slow APIs?

Slow APIs can indicate:

```text
Database Problems

Network Issues

Inefficient Code

External Service Delays
```

---

# Example

```java
long start =
        System.currentTimeMillis();

employeeService.createEmployee();

long end =
        System.currentTimeMillis();

long duration =
        end - start;

if(duration > 1000) {

    log.warn(
        "Slow API detected: {} ms",
        duration
    );
}
```

---

# Output

```text
WARN

Slow API detected:
2500 ms
```

---

# Why Use WARN?

Because:

```text
Performance Problem Exists

But Application Is Still Running
```

---

# Monitoring Flow

```text
API Request

      ↓

Measure Duration

      ↓

Threshold Check

      ↓

Normal Or Slow
```

---

# Example Thresholds

| Response Time | Status |
|-------------|---------|
| < 100 ms | Excellent |
| 100-500 ms | Good |
| 500-1000 ms | Acceptable |
| > 1000 ms | Slow |
| > 5000 ms | Critical |

---

# Benefits

```text
Detect Performance Issues Early

Improve User Experience

Prevent Production Incidents
```

---

# 4. Performance Metrics

Performance logging is not limited to execution time.

---

# What are Performance Metrics?

Measurements used to evaluate application performance.

---

# Common Metrics

```text
Response Time

Execution Time

Throughput

Latency

Memory Usage

CPU Usage

Error Rate
```

---

# Response Time

Measures:

```text
Request Received

↓

Response Returned
```

---

Example:

```text
150 ms
```

---

# Why Important?

Represents:

```text
User Experience
```

---

# Throughput

Measures:

```text
Requests Processed

Per Second
```

---

Example:

```text
500 Requests/Second
```

---

# Higher Throughput Means

```text
Better Scalability
```

---

# Latency

Time required for a request to begin receiving a response.

---

Example:

```text
Network Delay
```

---

# Memory Usage

Measures:

```text
RAM Consumed
```

---

Example:

```text
512 MB
```

---

# Why Important?

Excessive memory usage may cause:

```text
Garbage Collection Issues

Out Of Memory Errors
```

---

# CPU Usage

Measures:

```text
Processor Utilization
```

---

Example:

```text
80%
```

---

# High CPU May Indicate

```text
Inefficient Algorithms

Heavy Processing

Infinite Loops
```

---

# Error Rate

Measures:

```text
Failed Requests
```

---

Example:

```text
2%
```

---

# High Error Rate Indicates

```text
Application Problems
```

---

# Metrics Overview

| Metric | Purpose |
|----------|----------|
| Execution Time | Method Performance |
| Response Time | API Performance |
| Throughput | Scalability |
| Latency | Delay Measurement |
| Memory Usage | Resource Consumption |
| CPU Usage | Processing Load |
| Error Rate | Reliability |

---

# 5. Performance Logging Using AOP

Most common production implementation.

---

# Why AOP?

Avoids adding timing code to every method.

---

Without AOP:

```java
long start =
    System.currentTimeMillis();
```

in every method.

---

With AOP:

```java
@Around
```

automatically measures time.

---

# Example

```java
@Around(
    "execution(* com.company.service.*.*(..))"
)
public Object measureTime(
        ProceedingJoinPoint joinPoint)
        throws Throwable {

    long start =
        System.currentTimeMillis();

    Object result =
            joinPoint.proceed();

    long end =
        System.currentTimeMillis();

    log.info(
        "{} executed in {} ms",
        joinPoint.getSignature(),
        (end - start)
    );

    return result;
}
```

---

# Output

```text
EmployeeService.createEmployee()

Executed In

120 ms
```

---

# Advantages

```text
Reusable

Centralized

Easy Maintenance
```

---

# 6. Performance Logging in REST APIs

Common production requirement.

---

# Example

Request:

```http
POST /employees
```

---

Start Time:

```text
10:00:00
```

---

End Time:

```text
10:00:02
```

---

Response Time:

```text
2000 ms
```

---

Log:

```text
POST /employees

Response Time: 2000 ms
```

---

# Benefits

```text
API Monitoring

SLA Tracking

Performance Analysis
```

---

# API Performance Flow

```text
Incoming Request

       ↓

Start Timer

       ↓

Controller

       ↓

Response

       ↓

Stop Timer

       ↓

Log Duration
```

---

# 7. Performance Logging for Database Operations

Database calls are often the biggest bottleneck.

---

# Example

```java
long start =
    System.currentTimeMillis();

employeeRepository.findById(id);

long end =
    System.currentTimeMillis();

log.info(
    "Database Query Took {} ms",
    (end - start)
);
```

---

# Why Measure Database Calls?

Detect:

```text
Slow Queries

Missing Indexes

Database Bottlenecks
```

---

# Example Output

```text
Database Query Took

2500 ms
```

---

Potential issue:

```text
Slow SQL Query
```

---

# 8. Performance Threshold Logging

Production systems often define thresholds.

---

# Example

```java
if(duration > 2000) {

    log.warn(
        "Slow Operation: {} ms",
        duration
    );
}
```

---

# Output

```text
WARN

Slow Operation:
3200 ms
```

---

# Benefits

```text
Early Detection

Automatic Monitoring

Proactive Alerting
```

---

# Threshold Example

| Duration | Log Level |
|-----------|-----------|
| < 500 ms | INFO |
| 500-2000 ms | WARN |
| > 5000 ms | ERROR |

---

# 9. Best Practices

---

## Always Measure Critical APIs

Examples:

```text
Payment APIs

Authentication APIs

Order APIs
```

---

## Log Slow Operations

Helps detect:

```text
Performance Issues
```

---

## Use AOP

For:

```text
Centralized Timing Logic
```

---

## Monitor Database Queries

Database delays are common bottlenecks.

---

## Define Thresholds

Examples:

```text
500 ms

1000 ms

2000 ms
```

---

## Avoid Excessive Performance Logs

Too many timing logs may create:

```text
Noise

Storage Overhead
```

---

# 10. Common Mistakes

---

## Measuring Everything

Problem:

```text
Huge Log Volume
```

---

## Ignoring Slow APIs

Problem:

```text
Performance Issues Remain Hidden
```

---

## No Thresholds

Problem:

```text
Cannot Distinguish Fast And Slow Operations
```

---

## Logging Without Context

Bad:

```text
Execution Time: 500 ms
```

---

Good:

```text
EmployeeService.getEmployee()

Execution Time: 500 ms
```

---

# 11. Interview Questions

### Q1. What is Performance Logging?

Logging performance-related metrics such as execution time and response time.

---

### Q2. Why is Execution Time Logging important?

To identify:

```text
Slow Operations

Performance Bottlenecks
```

---

### Q3. How is Execution Time calculated?

```text
End Time

-

Start Time
```

---

### Q4. Which method is commonly used to measure execution time?

```java
System.currentTimeMillis()
```

---

### Q5. What is a Slow API?

An API whose response time exceeds acceptable thresholds.

---

### Q6. Why should slow APIs be monitored?

To identify:

```text
Database Delays

Network Issues

Performance Problems
```

---

### Q7. Which AOP annotation is commonly used for performance logging?

```java
@Around
```

---

### Q8. What are common performance metrics?

```text
Execution Time

Response Time

Latency

Throughput

Memory Usage

CPU Usage
```

---

### Q9. What is Throughput?

Number of requests processed per unit time.

---

### Q10. What is the biggest benefit of performance logging?

Early detection of performance bottlenecks.

---

# Quick Revision

```text
Performance Logging
-------------------

Purpose

✔ Measure Performance
✔ Detect Bottlenecks
✔ Monitor APIs

--------------------------------

Execution Time

End Time
    -
Start Time

--------------------------------

Example

long start =
    System.currentTimeMillis();

--------------------------------

Measure

✔ Method Time
✔ API Time
✔ Query Time

--------------------------------

Slow API Detection

if(duration > 1000)

WARN

--------------------------------

Common Metrics

✔ Execution Time
✔ Response Time
✔ Throughput
✔ Latency
✔ Memory Usage
✔ CPU Usage
✔ Error Rate

--------------------------------

Best Tool

@Around AOP

--------------------------------

Benefits

✔ Faster Debugging
✔ Better Monitoring
✔ Performance Optimization
✔ Capacity Planning

--------------------------------

Threshold Example

< 500 ms

INFO

500-2000 ms

WARN

> 5000 ms

ERROR

--------------------------------

Remember

Performance Logging

Measures

How Fast
Your Application Performs
```