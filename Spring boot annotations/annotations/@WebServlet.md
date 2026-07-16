
## Definition

`@WebServlet` is a Java Servlet API annotation used to **declare and configure a Servlet without using `web.xml` configuration**.

It tells the servlet container (like Tomcat):

> “This class is a Servlet and should be mapped to a URL pattern.”

Package:

```java
import javax.servlet.annotation.WebServlet;
```

(or in newer Jakarta versions)

```java
import jakarta.servlet.annotation.WebServlet;
```

---

# Simple Understanding

Traditionally:

```text id="a1"
Servlets were configured in web.xml
```

---

With `@WebServlet`:

```text id="b2"
Servlet is configured directly in Java class using annotation
```

---

So:

```text id="c3"
@WebServlet = URL mapping for a Servlet
```

---

# Real-Life Analogy

Think of a web server routing system:

```text id="d4"
Request: /login
   ↓
Server finds LoginServlet
   ↓
Executes login logic
```

---

Mapping that request → servlet is:

```text id="e5"
@WebServlet
```

---

# Basic Example

```java id="f6"
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {

        resp.getWriter().write("Hello World");
    }
}
```

---

# What Happens Here?

```text id="g7"
User hits /hello URL
   ↓
Servlet container finds HelloServlet
   ↓
doGet() is executed
```

---

# URL Mapping Options

## 1. Single URL

```java id="h8"
@WebServlet("/login")
```

---

## 2. Multiple URLs

```java id="i9"
@WebServlet({"/login", "/signin"})
```

---

## 3. URL Pattern (wildcard)

```java id="j10"
@WebServlet("/user/*")
```

---

## 4. Extension mapping

```java id="k11"
@WebServlet("*.do")
```

---

# How It Works Internally

```text id="l12"
Server starts (Tomcat/Jetty)
   ↓
Scans classes with @WebServlet
   ↓
Registers servlet mappings
   ↓
Maps URLs to servlet methods
```

---

# Real Production Example: Login Servlet

```java id="m13"
@WebServlet("/login")
public class LoginServlet extends HttpServlet {

    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {

        String user = req.getParameter("username");
        String pass = req.getParameter("password");

        resp.getWriter().write("Login processed for " + user);
    }
}
```

---

# Lifecycle of a Servlet

```text id="n14"
1. Class loaded
2. Servlet instance created
3. init() called
4. service() called (doGet/doPost)
5. destroy() called
```

---

# Key Methods

## doGet()

```text id="o15"
Handles HTTP GET requests
```

---

## doPost()

```text id="p16"
Handles HTTP POST requests
```

---

## service()

```text id="q17"
Routes request to appropriate method
```

---

# @WebServlet vs web.xml

| Feature | @WebServlet | web.xml |
|----------|------------|----------|
| Configuration | Annotation-based | XML-based |
| Readability | High | Low |
| Maintenance | Easy | Hard |
| Modern usage | Yes | Legacy |

---

# Real Use Cases

## 1. Legacy Java Web Apps

```text id="r18"
Direct servlet-based applications
```

---

## 2. Lightweight Web Services

```text id="s19"
Without Spring Boot MVC
```

---

## 3. Learning Servlet Internals

```text id="t20"
Understanding HTTP request handling
```

---

# Important Notes

```text id="u21"
- Works only with Servlet containers (Tomcat, Jetty)
- Not part of Spring Boot core MVC (Spring uses @Controller instead)
- Low-level HTTP handling
```

---

# Common Mistakes

## Mistake 1: Using in Spring Boot MVC incorrectly

```text id="v22"
Spring Boot prefers @RestController instead
```

---

## Mistake 2: Wrong URL mapping

```text id="w23"
Incorrect path → 404 error
```

---

## Mistake 3: Forgetting deployment descriptor

```text id="x24"
Servlet container must support annotation scanning
```

---

# Common Interview Questions

## What is @WebServlet?

Annotation used to define and map a servlet to a URL.

---

## What is its alternative?

web.xml configuration.

---

## Which method handles requests?

doGet() and doPost().

---

## Is it used in Spring Boot?

Rarely, Spring uses @Controller instead.

---

## What is servlet lifecycle?

init → service → destroy.

---

# Enterprise Best Practice

Use `@WebServlet` for:

```text id="y25"
Pure Servlet-based applications
Learning HTTP internals
Legacy systems maintenance
```

---

Avoid for:

```text id="z26"
Modern Spring Boot applications
Microservices with REST APIs
Business logic layers
```

---

# Key Points To Remember

- `@WebServlet` defines a servlet and maps it to a URL.
- Replaces web.xml configuration.
- Works in servlet containers like Tomcat.
- Handles HTTP requests via doGet/doPost.
- Part of Java Servlet API, not Spring-specific.
- Used in low-level web applications.
- Supports multiple URL patterns.
- Initialized at server startup.
- Core concept for understanding Java web apps.
- Important servlet interview topic.

---

# Quick Comparison

| Concept | Purpose |
|----------|--------|
| @WebServlet | Servlet URL mapping |
| @Controller | Spring MVC controller |
| web.xml | XML-based servlet config |

---

# Interview Shortcut

```java id="a27"
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {

        resp.getWriter().write("Hello");
    }
}
```

Meaning:

```text id="b28"
Map HTTP request /hello to this servlet

Handle request using doGet()

Return response directly to client
```

---

# Real Production Example Flow

```text id="c29"
Client Request → /login
        ↓
Servlet Container
        ↓
@WebServlet("/login")
        ↓
LoginServlet.doPost()
        ↓
Response returned
```

---

# Final Summary

`@WebServlet` is a Java Servlet annotation used to define and map a servlet to a URL pattern, replacing web.xml configuration and enabling direct HTTP request handling in servlet-based web applications.
```