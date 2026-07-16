
## Definition

`@WebFilter` is a Java Servlet API annotation used to **define a Filter that can intercept HTTP requests and responses before they reach a Servlet or after they leave it**.

It tells the servlet container (like Tomcat):

> “Apply this filter to matching URL patterns before/after request processing.”

Package:

```java
import javax.servlet.annotation.WebFilter;
```

(or newer Jakarta version)

```java
import jakarta.servlet.annotation.WebFilter;
```

---

# Simple Understanding

Normally:

```text id="a1"
Request → Servlet → Response
```

---

With filter:

```text id="b2"
Request → Filter → Servlet → Filter → Response
```

---

So:

```text id="c3"
@WebFilter = Request/Response interceptor at servlet level
```

---

# Real-Life Analogy

Think of a security checkpoint:

```text id="d4"
User request → Security check → Application → Logging → Response
```

---

That checkpoint system is:

```text id="e5"
@WebFilter
```

---

# Why Do We Need @WebFilter?

We use it for:

```text id="f6"
Authentication checks
Logging requests/responses
Input validation
Request modification
Security filtering
```

---

# Basic Example

```java id="g7"
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;
import java.io.IOException;

@WebFilter("/secure/*")
public class AuthFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request,
                         ServletResponse response,
                         FilterChain chain)
                         throws IOException, ServletException {

        System.out.println("Request intercepted by filter");

        // Continue to next filter or servlet
        chain.doFilter(request, response);

        System.out.println("Response returned from servlet");
    }
}
```

---

# What Happens Here?

```text id="h8"
Client hits /secure/profile
   ↓
AuthFilter runs first
   ↓
Request reaches Servlet
   ↓
Response goes back through filter
```

---

# Filter Lifecycle

```text id="i9"
1. init()      → called once when filter is created
2. doFilter()  → called for every request
3. destroy()   → called when filter is removed
```

---

# URL Mapping Options

## 1. Single URL pattern

```java id="j10"
@WebFilter("/login")
```

---

## 2. Multiple URLs

```java id="k11"
@WebFilter({"/login", "/signup"})
```

---

## 3. Wildcard pattern

```java id="l12"
@WebFilter("/admin/*")
```

---

## 4. All requests

```java id="m13"
@WebFilter("/*")
```

---

# How It Works Internally

```text id="n14"
Server starts (Tomcat)
   ↓
Scans @WebFilter classes
   ↓
Registers filter chain
   ↓
Every request passes through filters first
```

---

# Real Production Example: Logging Filter

```java id="o15"
@WebFilter("/*")
public class LoggingFilter implements Filter {

    public void doFilter(ServletRequest request,
                         ServletResponse response,
                         FilterChain chain)
                         throws IOException, ServletException {

        System.out.println("Request received at: " + System.currentTimeMillis());

        chain.doFilter(request, response);

        System.out.println("Response sent at: " + System.currentTimeMillis());
    }
}
```

---

# Real Production Example: Authentication Filter

```java id="p16"
@WebFilter("/api/*")
public class JwtAuthFilter implements Filter {

    public void doFilter(ServletRequest request,
                         ServletResponse response,
                         FilterChain chain)
                         throws IOException, ServletException {

        HttpServletRequest req = (HttpServletRequest) request;

        String token = req.getHeader("Authorization");

        if (token == null) {
            throw new ServletException("Unauthorized request");
        }

        chain.doFilter(request, response);
    }
}
```

---

# Filter Chain Concept

```text id="q17"
Request → Filter1 → Filter2 → Filter3 → Servlet → Response → back through filters
```

---

# Types of Filters (Real Usage)

## 1. Authentication Filter

```text id="r18"
Validate JWT or session
```

---

## 2. Logging Filter

```text id="s19"
Track requests and response times
```

---

## 3. CORS Filter

```text id="t20"
Allow cross-origin requests
```

---

## 4. Compression Filter

```text id="u21"
Compress response data
```

---

# @WebFilter vs @WebServlet

| Feature | @WebFilter | @WebServlet |
|----------|-----------|-------------|
| Purpose | Intercept requests | Handle requests |
| Position | Before/After servlet | Main handler |
| Role | Middleware | Business logic |

---

# Filter vs Interceptor (Spring Context)

| Feature | Servlet Filter | Spring Interceptor |
|----------|--------------|--------------------|
| Level | Servlet container | Spring MVC |
| Scope | All requests | Controller requests |
| Flexibility | Low-level | High-level |

---

# Common Mistakes

## Mistake 1: Forgetting chain.doFilter()

```text id="v22"
Request gets blocked forever
```

---

## Mistake 2: Wrong URL pattern

```text id="w23"
Filter never triggers
```

---

## Mistake 3: Heavy logic inside filter

```text id="x24"
Slows down every request
```

---

# Common Interview Questions

## What is @WebFilter?

Used to intercept requests and responses at servlet level.

---

## What is FilterChain?

It passes request to next filter or servlet.

---

## When is filter executed?

Before and after servlet execution.

---

## Difference between filter and servlet?

Filter → preprocessing  
Servlet → request handling

---

## Can multiple filters exist?

Yes, they form a filter chain.

---

# Enterprise Best Practice

Use `@WebFilter` for:

```text id="y25"
Logging
Authentication
Request validation
CORS handling
Security enforcement
```

---

Avoid for:

```text id="z26"
Business logic
Database operations
Heavy computations
```

---

# Key Points To Remember

- `@WebFilter` intercepts HTTP requests/responses.
- Works at servlet container level.
- Executes before and after servlet.
- Uses FilterChain to continue execution.
- Useful for security, logging, validation.
- Supports URL pattern mapping.
- Part of Java Servlet API, not Spring-specific.
- Can block or modify requests.
- Executes for every matching request.
- Important web architecture interview topic.

---

# Quick Flow Diagram

```text id="a27"
Client Request
     ↓
@WebFilter (pre-processing)
     ↓
@WebServlet / Controller
     ↓
@WebFilter (post-processing)
     ↓
Response returned
```

---

# Interview Shortcut

```java id="b28"
@WebFilter("/api/*")
public class AuthFilter implements Filter {

    public void doFilter(ServletRequest req,
                         ServletResponse res,
                         FilterChain chain)
                         throws IOException, ServletException {

        // pre-processing
        chain.doFilter(req, res);
        // post-processing
    }
}
```

Meaning:

```text id="c29"
Intercept all API requests

Perform validation before servlet execution

Continue request flow using FilterChain
```

---

# Final Summary

`@WebFilter` is a Java Servlet annotation used to intercept and process HTTP requests and responses before and after they reach a servlet, enabling cross-cutting concerns like authentication, logging, and request validation at the servlet container level.
```