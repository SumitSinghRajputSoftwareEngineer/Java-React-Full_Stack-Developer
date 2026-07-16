
## Definition

`@Scheduled` is a Spring annotation used to execute a method automatically at a specified time interval, delay, or cron schedule.

It tells Spring:

> Run this method periodically without being called explicitly.

Package:

```java
import org.springframework.scheduling.annotation.Scheduled;
```

---

# Prerequisite

`@Scheduled` works only when scheduling is enabled.

```java
@Configuration
@EnableScheduling
public class SchedulerConfig {
}
```

Without:

```java
@EnableScheduling
```

the method will never execute.

---

# Why Do We Need @Scheduled?

Many enterprise applications require background jobs:

```text
Send Emails
Generate Reports
Refresh Cache
Clean Old Data
Monitor Systems
Process Batch Jobs
Sync Data
```

Instead of:

```java
while(true) {
   processData();
}
```

Spring provides:

```java
@Scheduled
```

---

# Basic Example

```java
@Component
public class ReportJob {

    @Scheduled(
       fixedRate = 5000
    )
    public void generateReport() {

        System.out.println(
          "Generating Report"
        );
    }
}
```

---

Output:

```text
Generating Report
Generating Report
Generating Report
...
```

Every:

```text
5 Seconds
```

---

# What Happens Internally?

Application Starts:

```text
@EnableScheduling
      ↓
Find @Scheduled Methods
      ↓
Register Tasks
      ↓
Scheduler Executes
```

---

Runtime:

```text
Scheduler Thread
      ↓
Calls Method
      ↓
Method Executes
```

---

# fixedRate

Most common scheduling option.

---

Example:

```java
@Scheduled(
   fixedRate = 5000
)
public void task() {
}
```

---

Meaning:

```text
Run Every 5 Seconds
```

Measured from:

```text
Start Time
```

---

Timeline:

```text
0 sec  -> Start
5 sec  -> Start
10 sec -> Start
15 sec -> Start
```

---

# Example

```java
@Scheduled(
  fixedRate = 3000
)
public void heartbeat() {

    System.out.println(
      "Heartbeat"
    );
}
```

---

Output:

```text
Every 3 Seconds
```

---

# fixedDelay

Runs after previous execution finishes.

---

Example:

```java
@Scheduled(
  fixedDelay = 5000
)
public void task()
throws Exception {

    Thread.sleep(2000);
}
```

---

Timeline:

```text
Start
 ↓
2 sec work
 ↓
Wait 5 sec
 ↓
Run Again
```

---

Meaning:

```text
Delay Starts
After Completion
```

---

# Difference Between fixedRate and fixedDelay

## fixedRate

```java
@Scheduled(
 fixedRate = 5000
)
```

---

Next execution based on:

```text
Previous Start Time
```

---

Can overlap.

---

# fixedDelay

```java
@Scheduled(
 fixedDelay = 5000
)
```

---

Next execution based on:

```text
Previous End Time
```

---

No overlap.

---

Comparison:

| Feature | fixedRate | fixedDelay |
|----------|-----------|------------|
| Based On | Start Time | End Time |
| Overlap Possible | Yes | No |
| Best For | Monitoring | Batch Jobs |

---

# initialDelay

Used to delay first execution.

---

Example:

```java
@Scheduled(
   initialDelay = 10000,
   fixedRate = 5000
)
public void task() {
}
```

---

Meaning:

```text
Wait 10 Seconds
Then Run Every 5 Seconds
```

---

Timeline:

```text
0 sec  -> Start App
10 sec -> First Run
15 sec -> Second Run
20 sec -> Third Run
```

---

# Cron Scheduling

Most Important Interview Topic.

---

Example:

```java
@Scheduled(
 cron = "0 0 9 * * *"
)
```

---

Meaning:

```text
Every Day At 9 AM
```

---

# Cron Format

Spring format:

```text
Second
Minute
Hour
Day Of Month
Month
Day Of Week
```

---

Syntax:

```text
* * * * * *
```

---

Example:

```text
0 30 10 * * *
```

---

Meaning:

```text
10:30 AM Daily
```

---

# Common Cron Examples

## Every 10 Seconds

```java
@Scheduled(
 cron = "*/10 * * * * *"
)
```

---

## Every Minute

```java
@Scheduled(
 cron = "0 * * * * *"
)
```

---

## Every Hour

```java
@Scheduled(
 cron = "0 0 * * * *"
)
```

---

## Every Day At Midnight

```java
@Scheduled(
 cron = "0 0 0 * * *"
)
```

---

## Every Sunday

```java
@Scheduled(
 cron = "0 0 0 * * SUN"
)
```

---

## Every Month First Day

```java
@Scheduled(
 cron = "0 0 0 1 * *"
)
```

---

# Complete Cron Example

```java
@Component
public class EmailJob {

    @Scheduled(
      cron = "0 0 8 * * *"
    )
    public void sendEmails() {

        System.out.println(
           "Sending Emails"
        );
    }
}
```

---

Meaning:

```text
Run Daily At 8 AM
```

---

# fixedRateString

Useful for configuration files.

---

```java
@Scheduled(
 fixedRateString =
 "${app.job.rate}"
)
```

---

application.properties:

```properties
app.job.rate=5000
```

---

Benefit:

```text
No Code Changes
```

---

# cron From Properties

```java
@Scheduled(
 cron =
 "${app.cleanup.cron}"
)
```

---

application.properties:

```properties
app.cleanup.cron=0 0 1 * * *
```

---

Meaning:

```text
Run Daily At 1 AM
```

---

# Time Zone Support

```java
@Scheduled(
 cron = "0 0 9 * * *",
 zone = "Asia/Kolkata"
)
```

---

Runs:

```text
9 AM IST
```

---

Useful for global applications.

---

# Multiple Scheduled Methods

```java
@Component
public class Jobs {

    @Scheduled(
      fixedRate = 5000
    )
    public void task1() {
    }

    @Scheduled(
      fixedRate = 10000
    )
    public void task2() {
    }
}
```

---

Both run automatically.

---

# Threading Behavior

Default:

```text
Single Scheduler Thread
```

---

Example:

```java
task1()
```

takes:

```text
20 Seconds
```

---

Then:

```java
task2()
```

must wait.

---

# Production Configuration

```java
@Bean
public TaskScheduler
taskScheduler() {

    ThreadPoolTaskScheduler
      scheduler =
      new ThreadPoolTaskScheduler();

    scheduler.setPoolSize(10);

    return scheduler;
}
```

---

Now:

```text
10 Scheduler Threads
```

---

# Exception Handling

Wrong:

```java
@Scheduled(
 fixedRate = 5000
)
public void task() {

   throw new RuntimeException();
}
```

---

Best Practice:

```java
try {

}
catch(Exception e) {

    log.error(
      "Job Failed",
      e
    );
}
```

---

# Real-World Examples

## Cache Refresh

```java
@Scheduled(
 fixedRate = 60000
)
```

Every minute.

---

## Daily Reports

```java
@Scheduled(
 cron = "0 0 8 * * *"
)
```

Daily 8 AM.

---

## Database Cleanup

```java
@Scheduled(
 cron = "0 0 1 * * *"
)
```

Daily 1 AM.

---

## Health Check

```java
@Scheduled(
 fixedRate = 10000
)
```

Every 10 seconds.

---

# Internal Components

`@Scheduled` uses:

```text
TaskScheduler
ScheduledTaskRegistrar
ScheduledAnnotationBeanPostProcessor
```

---

Execution:

```text
Application Start
       ↓
Find Scheduled Methods
       ↓
Register Jobs
       ↓
Scheduler Triggers Execution
```

---

# Common Mistakes

## Mistake 1

Missing EnableScheduling

Wrong:

```java
@Scheduled(...)
```

without:

```java
@EnableScheduling
```

---

Method never runs.

---

# Mistake 2

Long Running fixedRate Tasks

```java
fixedRate = 1000
```

Task takes:

```text
10 Seconds
```

---

Can create scheduling issues.

---

Use:

```java
fixedDelay
```

or thread pool.

---

# Mistake 3

Using Private Methods

Wrong:

```java
@Scheduled
private void task() {
}
```

---

Prefer:

```java
public
```

or package-private methods.

---

# @Scheduled vs @Async

Interview Favorite.

---

## @Scheduled

Runs automatically.

---

```java
@Scheduled(
 fixedRate = 5000
)
```

---

# @Async

Runs when called.

---

```java
@Async
public void process() {
}
```

---

Comparison:

| Feature | @Scheduled | @Async |
|----------|-----------|---------|
| Automatic | ✅ | ❌ |
| Manual Call Required | ❌ | ✅ |
| Background Thread | ✅ | ✅ |
| Time-Based | ✅ | ❌ |

---

# Common Interview Questions

## What is @Scheduled?

Executes methods automatically at configured intervals.

---

## Does It Require EnableScheduling?

Yes.

```java
@EnableScheduling
```

---

## Difference Between fixedRate and fixedDelay?

### fixedRate

Based on:

```text
Start Time
```

---

### fixedDelay

Based on:

```text
End Time
```

---

## What Is Cron?

A scheduling expression for date/time-based execution.

---

## Can Scheduled Tasks Run Concurrently?

Yes.

Using:

```java
ThreadPoolTaskScheduler
```

---

# Enterprise Best Practice

Use:

```java
@Scheduled
```

for:

```text
Report Generation
Email Jobs
Monitoring
Cleanup Tasks
Cache Refresh
Batch Processing
```

---

Store schedules in:

```properties
application.properties
```

instead of hardcoding.

---

Configure:

```java
ThreadPoolTaskScheduler
```

for production workloads.

---

# Key Points To Remember

- `@Scheduled` executes methods automatically.
- Requires `@EnableScheduling`.
- Supports `fixedRate`, `fixedDelay`, and `cron`.
- Supports `initialDelay`.
- Supports externalized configuration.
- Supports time zones.
- Runs on scheduler threads.
- Can be configured with thread pools.
- Commonly used in enterprise batch processing.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Attribute | Purpose |
|------------|----------|
| `fixedRate` | Run Every N ms (Start Time) |
| `fixedDelay` | Run After Previous Completion |
| `initialDelay` | Delay First Execution |
| `cron` | Time-Based Scheduling |
| `zone` | Time Zone |

---

# Interview Shortcut

```java
@Scheduled(
 fixedRate = 5000
)
public void task() {

    System.out.println(
      "Running..."
    );
}
```

Meaning:

```text
Application Starts
       ↓
Scheduler Registers Method
       ↓
Every 5 Seconds
       ↓
Method Executes Automatically
```

Common production uses:

```text
Email Notifications
Report Generation
Database Cleanup
Cache Refresh
Monitoring
Batch Jobs
```

`@Scheduled` is Spring's primary scheduling annotation used to automatically execute methods at fixed intervals, fixed delays, or cron-based schedules without manual invocation.