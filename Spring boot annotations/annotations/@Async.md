
## Definition

`@Async` is a Spring annotation used to execute a method **asynchronously in a separate thread**.

It tells Spring:

> Run this method in the background and do not block the caller thread.

Package:

```java
import org.springframework.scheduling.annotation.Async;
```

---

# Prerequisite

Before using `@Async`, async support must be enabled.

```java
@Configuration
@EnableAsync
public class AsyncConfig {
}
```

Without:

```java
@EnableAsync
```

`@Async` is ignored.

---

# Why Do We Need @Async?

Consider an API:

```java
@PostMapping("/email")
public String sendEmail() {

    emailService.sendEmail();

    return "Success";
}
```

---

Email method:

```java
public void sendEmail() {

    Thread.sleep(10000);
}
```

---

Execution:

```text
Request
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
Poor User Experience
```

---

Solution:

```java
@Async
```

---

Execution:

```text
Request
   ↓
Submit Task
   ↓
Response Returned Immediately
   ↓
Background Thread Executes
```

---

# Basic Example

```java
@Service
public class EmailService {

    @Async
    public void sendEmail() {

        System.out.println(
            "Email Sent"
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

    return "Request Accepted";
}
```

---

Response:

```text
Request Accepted
```

Immediately.

---

# What Happens Internally?

Normal Method:

```text
Caller Thread
      ↓
Method Executes
      ↓
Caller Waits
```

---

Async Method:

```text
Caller Thread
      ↓
Proxy Intercepts
      ↓
Task Executor
      ↓
Worker Thread
      ↓
Method Executes
```

---

# Thread Demonstration

```java
@Async
public void task() {

    System.out.println(
      Thread.currentThread()
            .getName()
    );
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

Shows:

```text
Separate Thread
```

---

# Void Return Type

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
service.sendEmail();
```

---

Returns immediately.

---

# Returning Future

```java
@Async
public Future<String>
generateReport() {

    return new AsyncResult<>(
        "Completed"
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

`get()` waits until completion.

---

# Returning CompletableFuture

Modern and preferred approach.

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

Non-blocking operations possible.

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

# Custom Thread Pool

Production applications should not use the default executor.

---

Configuration:

```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean
    public Executor taskExecutor() {

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
Resource Control
```

---

# Named Executor

Multiple thread pools.

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

Now email tasks use:

```text
emailExecutor
```

---

# Multiple Async Tasks

```java
@Async
public CompletableFuture<String>
task1() {
    return CompletableFuture
            .completedFuture(
               "Task1"
            );
}

@Async
public CompletableFuture<String>
task2() {
    return CompletableFuture
            .completedFuture(
               "Task2"
            );
}
```

---

Caller:

```java
CompletableFuture<String> t1 =
    service.task1();

CompletableFuture<String> t2 =
    service.task2();

CompletableFuture.allOf(
    t1, t2
).join();
```

---

Both execute concurrently.

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

Caller does not receive exception.

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

Or configure:

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

Very Important Interview Question.

---

Wrong:

```java
@Service
public class EmployeeService {

    public void process() {

        sendEmail();
    }

    @Async
    public void sendEmail() {

    }
}
```

---

Result:

```text
Runs Synchronously
```

---

Why?

```text
Proxy Is Bypassed
```

---

Call must go through:

```java
Spring Proxy
```

---

Correct:

```java
@Service
public class EmployeeService {

    @Autowired
    private EmailService emailService;

    public void process() {

        emailService.sendEmail();
    }
}
```

---

# Transaction + Async

Interview Favorite.

---

```java
@Transactional
public void save() {

    asyncService.sendEmail();
}
```

---

Async method:

```java
@Async
public void sendEmail() {

}
```

---

Runs:

```text
Different Thread
Different Context
Different Transaction
```

---

Transaction does not propagate automatically.

---

# Internal Components

`@Async` uses:

```text
Spring AOP Proxy
TaskExecutor
AsyncAnnotationBeanPostProcessor
```

---

Flow:

```text
Method Call
      ↓
Proxy Intercepts
      ↓
Task Submitted
      ↓
Executor
      ↓
Worker Thread
      ↓
Method Executes
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

# @Async vs CompletableFuture.supplyAsync()

Interview Question.

---

## @Async

Managed by Spring.

---

```java
@Async
```

---

Uses Spring executor.

---

# supplyAsync()

```java
CompletableFuture
   .supplyAsync(...)
```

---

Pure Java.

---

Comparison:

| Feature | @Async | supplyAsync |
|----------|--------|-------------|
| Spring Managed | ✅ | ❌ |
| Dependency Injection | ✅ | ❌ |
| Custom Executor | ✅ | ✅ |
| Declarative | ✅ | ❌ |

---

# Common Mistakes

## Mistake 1

Missing EnableAsync

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

Not asynchronous.

---

# Mistake 3

Using Default Executor

Default:

```text
SimpleAsyncTaskExecutor
```

---

Not ideal for production.

---

Use:

```java
ThreadPoolTaskExecutor
```

---

# Common Interview Questions

## What is @Async?

Executes a method asynchronously in a separate thread.

---

## Does It Require EnableAsync?

Yes.

```java
@EnableAsync
```

---

## Can Async Methods Return Values?

Yes.

```java
Future
CompletableFuture
```

---

## Why Doesn't Self Invocation Work?

Spring proxy is bypassed.

---

## Does Async Share Transaction?

No.

Different thread.

---

## Which Executor Is Used?

Configured:

```java
TaskExecutor
```

or default executor.

---

# Enterprise Best Practice

Always:

```java
@EnableAsync
```

and

```java
ThreadPoolTaskExecutor
```

---

Use:

```java
CompletableFuture
```

instead of:

```java
Future
```

for modern applications.

---

Monitor:

```text
Pool Size
Queue Size
Execution Time
Failures
```

---

# Key Points To Remember

- `@Async` executes methods in background threads.
- Requires `@EnableAsync`.
- Uses Spring AOP proxies.
- Supports `void`, `Future`, and `CompletableFuture`.
- Self-invocation does not work.
- Transactions do not automatically propagate.
- Uses `TaskExecutor`.
- Improves responsiveness of applications.
- Commonly used for long-running tasks.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableAsync` | Enable Async Support |
| `@Async` | Run Method In Background |
| `@EnableScheduling` | Enable Scheduling |
| `@Scheduled` | Time-Based Execution |
| `@Transactional` | Transaction Management |

---

# Interview Shortcut

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
Spring Proxy Intercepts
         ↓
Task Submitted To Executor
         ↓
Worker Thread Executes
         ↓
Controller Continues Immediately
```

Equivalent to:

```java
executor.submit(
    () -> sendEmail()
);
```

but managed automatically by Spring.

---

# Real Production Scenario

```java
@PostMapping("/order")
public String placeOrder() {

    orderService.saveOrder();

    emailService.sendConfirmation();

    return "Order Placed";
}
```

```java
@Async
public void sendConfirmation() {

    // Send Email
}
```

Flow:

```text
Save Order
      ↓
Return Response
      ↓
Background Thread Sends Email
```

Result:

```text
Fast API Response
Better User Experience
Scalable Application
```

`@Async` is Spring's declarative asynchronous programming annotation that executes methods in separate threads using a TaskExecutor, allowing long-running operations to run in the background without blocking the caller.