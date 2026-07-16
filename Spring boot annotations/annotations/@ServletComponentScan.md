
## Definition

`@ServletComponentScan` is a Spring Boot annotation used to **enable scanning of Servlet components like `@WebServlet`, `@WebFilter`, and `@WebListener`** in a Spring Boot application.

It tells Spring Boot:

> “Scan and register all Servlet components automatically from the specified packages.”

Package:

```java
import org.springframework.boot.web.servlet.ServletComponentScan;
```

---

# Simple Understanding

Normally in Spring Boot:

```text id="a1"
@WebServlet, @WebFilter, @WebListener are NOT auto-detected
```

---

With `@ServletComponentScan`:

```text id="b2"
Spring Boot automatically detects and registers servlet components
```

---

So:

```text id="c3"
@ServletComponentScan = Enable servlet annotation scanning in Spring Boot
```

---

# Real-Life Analogy

Think of a security system:

```text id="d4"
Without scanning → security guards are not registered
With scanning → all guards (servlets, filters, listeners) are automatically activated
```

---

That auto-registration system is:

```text id="e5"
@ServletComponentScan
```

---

# Why Do We Need @ServletComponentScan?

We use it for:

```text id="f6"
Enabling @WebServlet, @WebFilter, @WebListener in Spring Boot
Avoiding manual bean registration
Simplifying legacy servlet integration
Auto-registering servlet components
```

---

# Basic Example

## Main Spring Boot Application

```java id="g7"
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

@SpringBootApplication
@ServletComponentScan
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

---

# What Happens Here?

```text id="h8"
Spring Boot starts
   ↓
Scans for @WebServlet, @WebFilter, @WebListener
   ↓
Automatically registers them in servlet container
   ↓
No manual configuration needed
```

---

# Without @ServletComponentScan

```text id="i9"
@WebServlet → ignored
@WebFilter → ignored
@WebListener → ignored
```

---

# With @ServletComponentScan

```text id="j10"
All servlet components are registered automatically
```

---

# Example with @WebServlet

```java id="k11"
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {

        resp.getWriter().write("Hello from Servlet");
    }
}
```

With:

```java id="l12"
@ServletComponentScan
```

Spring Boot automatically maps `/hello`.

---

# Example with @WebFilter

```java id="m13"
@WebFilter("/api/*")
public class AuthFilter implements Filter {

    public void doFilter(ServletRequest req,
                         ServletResponse res,
                         FilterChain chain)
            throws IOException, ServletException {

        System.out.println("Filter executed");
        chain.doFilter(req, res);
    }
}
```

---

# Example with @WebListener

```java id="n14"
@WebListener
public class AppListener implements ServletContextListener {

    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("App started");
    }
}
```

---

# How It Works Internally

```text id="o15"
Spring Boot Application Starts
   ↓
@ServletComponentScan enabled
   ↓
Classpath scanning starts
   ↓
Finds @WebServlet, @WebFilter, @WebListener
   ↓
Registers them into servlet container
```

---

# Package Scanning Scope

## Default behavior:

```text id="p16"
Scans current package and sub-packages
```

---

## Custom base packages:

```java id="q17"
@ServletComponentScan(basePackages = "com.example.web")
```

---

# Real Production Use Cases

## 1. Legacy Servlet Integration

```text id="r18"
Integrating old servlet-based modules into Spring Boot
```

---

## 2. Filter-based Security

```text id="s19"
JWT authentication filters
```

---

## 3. Logging Systems

```text id="t20"
Request/response logging filters
```

---

## 4. Session Tracking

```text id="u21"
User session listeners
```

---

# @ServletComponentScan vs @ComponentScan

| Feature | @ServletComponentScan | @ComponentScan |
|----------|----------------------|----------------|
| Purpose | Servlet API components | Spring Beans |
| Scans | Servlets, Filters, Listeners | @Service, @Component, @Repository |
| Level | Servlet container | Spring context |

---

# Common Mistakes

## Mistake 1: Forgetting annotation

```text id="v22"
@WebServlet exists but never registered
```

---

## Mistake 2: Wrong package scanning

```text id="w23"
Components outside base package are ignored
```

---

## Mistake 3: Mixing with Spring MVC incorrectly

```text id="x24"
Using servlet filters instead of Spring interceptors unnecessarily
```

---

# Common Interview Questions

## What is @ServletComponentScan?

Enables scanning of servlet components in Spring Boot.

---

## Which components are scanned?

@WebServlet, @WebFilter, @WebListener.

---

## Why is it needed?

Spring Boot does not auto-register servlet annotations by default.

---

## What happens without it?

Servlet components are ignored.

---

## Where is it used?

Spring Boot applications integrating servlet API directly.

---

# Enterprise Best Practice

Use `@ServletComponentScan` for:

```text id="y25"
Legacy servlet integration
Filter-based security
Low-level HTTP handling
Session/event tracking
```

---

Avoid for:

```text id="z26"
Pure Spring MVC applications
Business logic layers
Modern REST APIs (use @RestController instead)
```

---

# Key Points To Remember

- `@ServletComponentScan` enables servlet annotation scanning.
- Required for @WebServlet, @WebFilter, @WebListener in Spring Boot.
- Automatically registers servlet components.
- Works at application startup.
- Reduces need for manual servlet configuration.
- Scans base package and sub-packages.
- Bridges servlet API with Spring Boot.
- Used mainly for legacy or low-level web apps.
- Does not replace Spring MVC annotations.
- Important Spring Boot architecture interview topic.

---

# Quick Flow Diagram

```text id="a27"
Spring Boot App Start
      ↓
@ServletComponentScan active
      ↓
Scan for servlet annotations
      ↓
Register Servlets, Filters, Listeners
      ↓
Application ready with servlet components
```

---

# Interview Shortcut

```java id="b28"
@SpringBootApplication
@ServletComponentScan
public class MyApp {
}
```

Meaning:

```text id="c29"
Enable automatic detection of servlet components

Register WebServlet, WebFilter, WebListener

Integrate servlet API with Spring Boot
```

---

# Final Summary

`@ServletComponentScan` is a Spring Boot annotation used to automatically detect and register servlet components such as @WebServlet, @WebFilter, and @WebListener, enabling seamless integration of traditional Servlet API features into Spring Boot applications.
```