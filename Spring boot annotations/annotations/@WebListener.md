
## Definition

`@WebListener` is a Java Servlet API annotation used to **register a class as an event listener for the servlet context, session, or request lifecycle events**.

It tells the servlet container (Tomcat/Jetty):

> “This class should listen to web application lifecycle events.”

Package:

```java
import javax.servlet.annotation.WebListener;
```

(or newer Jakarta version)

```java id="1a7k2l"
import jakarta.servlet.annotation.WebListener;
```

---

# Simple Understanding

Normally:

```text id="a1"
Web app runs without tracking lifecycle events
```

---

With listener:

```text id="b2"
Web app notifies your class on lifecycle events
```

---

So:

```text id="c3"
@WebListener = Event observer for web application lifecycle
```

---

# Real-Life Analogy

Think of a building security system:

```text id="d4"
- Entry event → log visitor
- Exit event → update records
- Emergency shutdown → trigger alert
```

---

That event tracking system is:

```text id="e5"
@WebListener
```

---

# Why Do We Need @WebListener?

We use it for:

```text id="f6"
Tracking application startup/shutdown
Monitoring session creation/destruction
Request lifecycle tracking
Resource initialization/cleanup
Analytics and logging
```

---

# Types of Listeners

Java Servlet API provides multiple listener interfaces:

---

## 1. ServletContextListener (App-level events)

```text id="g7"
Triggered when application starts/stops
```

### Example:

```java id="h8"
@WebListener
public class AppStartupListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("Application started");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("Application stopped");
    }
}
```

---

## 2. HttpSessionListener (Session events)

```text id="i9"
Triggered when user session is created or destroyed
```

### Example:

```java id="j10"
@WebListener
public class SessionListener implements HttpSessionListener {

    @Override
    public void sessionCreated(HttpSessionEvent se) {
        System.out.println("Session created: " + se.getSession().getId());
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        System.out.println("Session destroyed");
    }
}
```

---

## 3. ServletRequestListener (Request events)

```text id="k11"
Triggered for every HTTP request
```

### Example:

```java id="l12"
@WebListener
public class RequestListener implements ServletRequestListener {

    @Override
    public void requestInitialized(ServletRequestEvent sre) {
        System.out.println("Request started");
    }

    @Override
    public void requestDestroyed(ServletRequestEvent sre) {
        System.out.println("Request ended");
    }
}
```

---

# How It Works Internally

```text id="m13"
Server starts
   ↓
Scans @WebListener classes
   ↓
Registers event listeners
   ↓
Triggers methods on lifecycle events
```

---

# Real Production Example: Application Startup Logging

```java id="n14"
@WebListener
public class StartupLogger implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("System initialized successfully");
    }
}
```

---

# Real Production Example: Session Tracking

```java id="o15"
@WebListener
public class ActiveUserTracker implements HttpSessionListener {

    private static AtomicInteger activeUsers = new AtomicInteger();

    @Override
    public void sessionCreated(HttpSessionEvent se) {
        activeUsers.incrementAndGet();
        System.out.println("Active users: " + activeUsers.get());
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        activeUsers.decrementAndGet();
        System.out.println("Active users: " + activeUsers.get());
    }
}
```

---

# Real Production Example: Request Timing

```java id="p16"
@WebListener
public class RequestTimingListener implements ServletRequestListener {

    @Override
    public void requestInitialized(ServletRequestEvent sre) {
        sre.getServletRequest().setAttribute("startTime", System.currentTimeMillis());
    }

    @Override
    public void requestDestroyed(ServletRequestEvent sre) {
        long start = (Long) sre.getServletRequest().getAttribute("startTime");
        long duration = System.currentTimeMillis() - start;

        System.out.println("Request processed in: " + duration + " ms");
    }
}
```

---

# Event Flow in Web Application

```text id="q17"
Application Start → contextInitialized()
User Request → requestInitialized()
Session Created → sessionCreated()
Request Ends → requestDestroyed()
Session Ends → sessionDestroyed()
Application Stop → contextDestroyed()
```

---

# @WebListener vs Other Annotations

| Annotation | Purpose |
|------------|--------|
| @WebServlet | Handle HTTP requests |
| @WebFilter | Intercept requests/responses |
| @WebListener | Listen to lifecycle events |

---

# Key Use Cases

## 1. Monitoring

```text id="r18"
Track active users, requests, sessions
```

---

## 2. Resource Initialization

```text id="s19"
Load caches, configs at startup
```

---

## 3. Cleanup Tasks

```text id="t20"
Release DB connections or resources
```

---

## 4. Analytics

```text id="u21"
Track user behavior and request patterns
```

---

# Common Mistakes

## Mistake 1: Confusing with servlet/filter

```text id="v22"
Listener does NOT handle HTTP requests directly
```

---

## Mistake 2: Heavy logic inside listener

```text id="w23"
Can slow application startup or request flow
```

---

## Mistake 3: Forgetting thread safety

```text id="x24"
Listeners may be accessed concurrently
```

---

# Common Interview Questions

## What is @WebListener?

Used to listen to web application lifecycle events.

---

## Which interfaces are commonly used?

ServletContextListener, HttpSessionListener, ServletRequestListener.

---

## When is contextInitialized called?

At application startup.

---

## Difference between filter and listener?

Filter → intercept requests  
Listener → observe lifecycle events

---

## Can multiple listeners exist?

Yes, multiple listeners can be registered.

---

# Enterprise Best Practice

Use `@WebListener` for:

```text id="y25"
Application startup/shutdown tasks
Session tracking
Request monitoring
Resource initialization/cleanup
```

---

Avoid for:

```text id="z26"
Business logic
Request processing logic
Heavy computations
```

---

# Key Points To Remember

- `@WebListener` listens to lifecycle events of web apps.
- Works with ServletContext, Session, and Request events.
- Used for monitoring, logging, and initialization.
- Does NOT handle HTTP requests directly.
- Runs at application, session, or request level.
- Part of Java Servlet API.
- Supports multiple listener types.
- Important for system monitoring and analytics.
- Lightweight event-driven mechanism.
- Frequently asked in Java web interview questions.

---

# Quick Flow Diagram

```text id="a27"
App Start → @WebListener.contextInitialized()

Request → @WebListener.requestInitialized()

Session Start → @WebListener.sessionCreated()

Request End → @WebListener.requestDestroyed()

Session End → @WebListener.sessionDestroyed()

App Stop → @WebListener.contextDestroyed()
```

---

# Interview Shortcut

```java id="b28"
@WebListener
public class AppListener implements ServletContextListener {

    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("App started");
    }

    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("App stopped");
    }
}
```

Meaning:

```text id="c29"
Listen to application lifecycle events

Execute code on startup and shutdown

Useful for initialization and cleanup tasks
```

---

# Final Summary

`@WebListener` is a Java Servlet annotation used to register classes that listen to web application lifecycle events such as application startup, shutdown, session creation, and request handling, enabling monitoring, initialization, and cleanup in web applications.
```