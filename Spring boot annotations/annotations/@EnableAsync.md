
## Definition

`@EnableAsync` is a Spring annotation used to enable **asynchronous method execution**.

It tells Spring:

> Allow methods annotated with `@Async` to run in separate threads instead of the caller's thread.

Package:

```java
import org.springframework.scheduling.annotation.EnableAsync;
```

---

# Why Do We Need @EnableAsync?

Consider:

```java
public void sendEmail() {

    Thread.sleep(10000);
}
```

Execution:

```text
User Request
      ↓
Send Email
      ↓
Wait 10 Seconds
      ↓
Response Returned
```

---

Problem:

```text
User Waits Too Long
```

---

Solution:

```java
@Async
```

But Spring won't activate it until:

```java
@EnableAsync
```

is enabled.

---

# Basic Example

```java
@SpringBootApplication

@EnableAsync
public class Application {

    public static void main(
       String[] args) {

       SpringApplication.run(
         Application.class,
         args
       );
    }
}
```

---

Meaning:

```text
Enable Async Processing
```

---

# First Async Example

```java
@Service
public class EmailService {

    @Async
    public void sendEmail() {

        System.out.println(
          "Sending Email"
        );
    }
}
```

---

Controller:

```java
@GetMapping("/send")
public String send() {

    emailService.sendEmail();

    return "Success";
}
```

---

Execution:

```text
Request Thread
      ↓
Calls Async Method
      ↓
New Thread Created
      ↓
Request Returns Immediately
```

---

# What Happens Internally?

Without Async:

```text
Main Thread
      ↓
Method Executes
      ↓
Caller Waits
```

---

With Async:

```text
Main Thread
      ↓
Submit Task
      ↓
Thread Pool
      ↓
Method Executes
```

---

# Internal Workflow

```text
Method Call
      ↓
Spring Proxy
      ↓
Task Executor
      ↓
Worker Thread
      ↓
Method Executes
```

---

# Example

```java
@Service
public class NotificationService {

    @Async
    public void sendNotification() {

        System.out.println(
            Thread.currentThread()
                  .getName()
        );
    }
}
```

---

Output:

```text
task-1
```

Instead of:

```text
http-nio-8080-exec-1
```

---

Meaning:

```text
Different Thread
```

---

# Return Type: void

Most common.

---

```java
@Async
public void sendEmail() {

}
```

---

Caller:

```java
emailService.sendEmail();
```

---

Returns immediately.

---

# Return Type: Future

```java
@Async
public Future<String>
generateReport() {

    return new AsyncResult<>(
        "Done"
    );
}
```

---

Caller:

```java
Future<String> future =
      service.generateReport();

String result =
      future.get();
```

---

Blocks until complete.

---

# Return Type: CompletableFuture

Modern approach.

---

```java
@Async
public CompletableFuture<String>
generateReport() {

    return CompletableFuture
            .completedFuture(
                "Done"
            );
}
```

---

Caller:

```java
CompletableFuture<String>
future =
service.generateReport();
```

---

Non-blocking.

---

# Complete Example

```java
@Service
public class ReportService {

    @Async
    public CompletableFuture<String>
    generateReport() {

        try {

            Thread.sleep(5000);

        } catch(Exception e) {

        }

        return CompletableFuture
                .completedFuture(
                    "Report Ready"
                );
    }
}
```

---

Controller:

```java
@GetMapping("/report")
public String report() {

    reportService
        .generateReport();

    return "Started";
}
```

---

Response:

```text
Started
```

Immediately.

---

# Default Executor

Spring automatically creates:

```text
SimpleAsyncTaskExecutor
```

---

Characteristics:

```text
Creates Threads Dynamically
No Reuse
Not Ideal For Production
```

---

# Production Configuration

Create custom thread pool.

---

```java
@Configuration

@EnableAsync
public class AsyncConfig {

    @Bean
    public Executor
    taskExecutor() {

        ThreadPoolTaskExecutor
          executor =
          new ThreadPoolTaskExecutor();

        executor.setCorePoolSize(5);

        executor.setMaxPoolSize(10);

        executor.setQueueCapacity(100);

        executor.initialize();

        return executor;
    }
}
```

---

Benefits:

```text
Thread Reuse
Better Performance
Controlled Resources
```

---

# Named Executor

Multiple executors.

---

Configuration:

```java
@Bean("emailExecutor")
public Executor emailExecutor() {

    ThreadPoolTaskExecutor
        executor =
        new ThreadPoolTaskExecutor();

    executor.setCorePoolSize(5);

    executor.initialize();

    return executor;
}
```

---

Usage:

```java
@Async("emailExecutor")
public void sendEmail() {

}
```

---

# Exception Handling

Problem:

```java
@Async
public void sendEmail() {

    throw new RuntimeException();
}
```

---

Caller won't see exception.

---

Solution:

```java
try {

}
catch(Exception e) {

   log.error(
     "Error",
     e
   );
}
```

---

Or:

```java
AsyncUncaughtExceptionHandler
```

---

# AsyncUncaughtExceptionHandler

```java
@Configuration
public class AsyncConfig
implements AsyncConfigurer {

    @Override
    public AsyncUncaughtExceptionHandler
    getAsyncUncaughtExceptionHandler() {

        return (ex, method, params) -> {

            System.out.println(
                ex.getMessage()
            );
        };
    }
}
```

---

# Self Invocation Problem

Interview Favorite.

---

Wrong:

```java
@Service
public class EmployeeService {

    public void method1() {

        method2();
    }

    @Async
    public void method2() {

    }
}
```

---

Result:

```text
Not Async
```

---

Because:

```text
Proxy Bypassed
```

---

Must call through:

```java
Spring Bean Proxy
```

---

# Internal Components

`@EnableAsync` activates:

```text
AsyncAnnotationBeanPostProcessor
TaskExecutor
Spring AOP Proxy
```

---

Execution:

```text
Method Call
      ↓
Proxy Intercepts
      ↓
Submit Task
      ↓
Executor
      ↓
Worker Thread
```

---

# Common Use Cases

## Email Sending

```java
@Async
```

---

## SMS Notifications

```java
@Async
```

---

## PDF Generation

```java
@Async
```

---

## Report Generation

```java
@Async
```

---

## Audit Logging

```java
@Async
```

---

## External API Calls

```java
@Async
```

---

# @EnableAsync vs @EnableScheduling

Interview Favorite.

---

## @EnableAsync

Enables:

```java
@Async
```

---

Manual invocation.

---

# @EnableScheduling

Enables:

```java
@Scheduled
```

---

Automatic execution.

---

Comparison:

| Feature | Async | Scheduling |
|----------|--------|-----------|
| Runs In Background | ✅ | ✅ |
| Manual Call | ✅ | ❌ |
| Automatic Timing | ❌ | ✅ |
| Uses Thread Pool | ✅ | ✅ |

---

# Common Mistakes

## Mistake 1

Forgetting EnableAsync

Wrong:

```java
@Async
```

without:

```java
@EnableAsync
```

---

Method runs normally.

---

# Mistake 2

Self Invocation

```java
this.asyncMethod();
```

---

Proxy bypassed.

---

# Mistake 3

Using Default Executor In Production

```text
SimpleAsyncTaskExecutor
```

---

Not recommended.

---

Use:

```java
ThreadPoolTaskExecutor
```

---

# Common Interview Questions

## What is @EnableAsync?

Enables asynchronous method execution.

---

## Which Annotation Does It Activate?

```java
@Async
```

---

## Does Async Create New Thread?

Yes.

Usually through a TaskExecutor.

---

## Can Async Return Values?

Yes.

Using:

```java
Future
CompletableFuture
```

---

## Why Doesn't Self Invocation Work?

Because Spring AOP proxy is bypassed.

---

# Enterprise Best Practice

Always:

```java
@EnableAsync
```

with:

```java
ThreadPoolTaskExecutor
```

---

Avoid:

```text
Default Executor
```

---

Use for:

```text
Email Sending
Notification Processing
Audit Logging
External Calls
Long Running Tasks
```

---

Monitor:

```text
Thread Count
Queue Size
Execution Time
```

---

# Key Points To Remember

- `@EnableAsync` enables asynchronous execution.
- Activates `@Async`.
- Uses Spring AOP proxies internally.
- Executes methods in separate threads.
- Supports `Future` and `CompletableFuture`.
- Requires proper executor configuration in production.
- Self-invocation does not work.
- Improves application responsiveness.
- Commonly used for background tasks.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableAsync` | Enable Async Processing |
| `@Async` | Execute Method In Background |
| `@EnableScheduling` | Enable Scheduler |
| `@Scheduled` | Time-Based Execution |
| `@Transactional` | Transaction Management |

---

# Interview Shortcut

```java
@EnableAsync
```

Meaning:

```text
Enable Async Infrastructure
       ↓
Scan @Async Methods
       ↓
Create Proxies
       ↓
Execute Methods In Separate Threads
```

Example:

```java
@Async
public void sendEmail() {

    Thread.sleep(5000);
}
```

Execution:

```text
Controller Calls Method
        ↓
Task Submitted
        ↓
Thread Pool
        ↓
Method Runs
        ↓
Controller Returns Immediately
```

`@EnableAsync` is the annotation that activates Spring's asynchronous processing infrastructure, allowing methods annotated with `@Async` to execute in background threads without blocking the caller.