
## Definition

`@CookieValue` is a Spring MVC annotation used to extract the value of an HTTP Cookie and bind it directly to a controller method parameter.

It tells Spring:

> Read a cookie from the incoming HTTP request and inject its value into the controller method.

Package:

```java
import org.springframework.web.bind.annotation.CookieValue;
```

---

# What is a Cookie?

A Cookie is a small piece of data stored by the browser and automatically sent with future requests to the same server.

---

Example:

Server Response:

```http
Set-Cookie: userId=101
```

---

Browser stores:

```text
userId=101
```

---

Future Request:

```http
Cookie: userId=101
```

---

Spring can access this cookie using:

```java
@CookieValue
```

---

# Why Do We Need @CookieValue?

Cookies are commonly used for:

```text
Session Tracking
Authentication
Remember Me Functionality
User Preferences
Language Selection
Shopping Cart Tracking
Theme Selection
Analytics
```

---

Without `@CookieValue`, developers would need:

```java
HttpServletRequest request

request.getCookies()
```

and manually iterate through cookies.

---

With:

```java
@CookieValue
```

Spring automatically extracts the cookie.

---

# What Problem Does It Solve?

Suppose browser sends:

```http
Cookie: userId=101
```

---

Without Spring:

```java
Cookie[] cookies =
        request.getCookies();

for(Cookie cookie : cookies){
    ...
}
```

---

With Spring:

```java
@CookieValue("userId")
Long userId
```

---

Much cleaner.

---

# Basic Syntax

```java
@GetMapping("/profile")
public String profile(

        @CookieValue("userId")
        Long userId) {

    return "User : " + userId;

}
```

---

Request:

```http
Cookie: userId=101
```

---

Response:

```text
User : 101
```

---

# What Happens Internally?

Request:

```http
Cookie: userId=101
```

---

Spring Flow:

```text
HTTP Request
      │
      ▼
DispatcherServlet
      │
      ▼
CookieValueMethodArgumentResolver
      │
      ▼
Extract Cookie
      │
      ▼
Convert Type
      │
      ▼
Inject Into Method
```

---

# Simple Example

Controller:

```java
@RestController
public class UserController {

    @GetMapping("/home")
    public String home(

            @CookieValue("username")
            String username) {

        return username;

    }

}
```

---

Request:

```http
Cookie: username=John
```

---

Response:

```text
John
```

---

# Cookie Header Structure

Browser sends:

```http
Cookie: userId=101; theme=dark; lang=en
```

---

Cookies:

```text
userId = 101
theme = dark
lang = en
```

---

Spring can read any of them.

---

# Reading Specific Cookie

---

```java
@GetMapping("/test")
public String test(

        @CookieValue("theme")
        String theme) {

    return theme;

}
```

---

Request:

```http
Cookie: theme=dark
```

---

Response:

```text
dark
```

---

# Named Cookie Mapping

---

```java
@CookieValue("userId")
Long id
```

---

Maps:

```text
Cookie Name → userId
```

to:

```java
Variable Name → id
```

---

# Automatic Type Conversion

Spring automatically converts:

```text
String
   ↓
Target Type
```

---

Example:

```java
@CookieValue("userId")
Long userId
```

---

Cookie:

```http
Cookie: userId=101
```

---

Result:

```java
userId = 101L
```

---

# Supported Types

```java
String
Integer
Long
Double
Boolean
UUID
Enum
Date
LocalDate
LocalDateTime
```

---

Example:

```java
@CookieValue UUID sessionId
```

---

Cookie:

```http
sessionId=550e8400-e29b-41d4-a716-446655440000
```

---

Spring converts automatically.

---

# Optional Cookie

By default:

```java
@CookieValue
```

is required.

---

Controller:

```java
@GetMapping("/home")
public String home(

        @CookieValue("userId")
        Long userId) {

    return userId.toString();

}
```

---

If cookie missing:

```java
MissingRequestCookieException
```

occurs.

---

# Making Cookie Optional

---

```java
@GetMapping("/home")
public String home(

        @CookieValue(
            value = "userId",
            required = false
        )
        Long userId) {

    return String.valueOf(userId);

}
```

---

Now:

```java
null
```

is injected if absent.

---

# Using Default Value

Very common.

---

```java
@GetMapping("/theme")
public String theme(

        @CookieValue(
            value = "theme",
            defaultValue = "light"
        )
        String theme) {

    return theme;

}
```

---

Request:

```http
(no theme cookie)
```

---

Response:

```text
light
```

---

# Reading Multiple Cookies

---

```java
@GetMapping("/dashboard")
public String dashboard(

        @CookieValue("userId")
        Long userId,

        @CookieValue("theme")
        String theme) {

    return userId + " " + theme;

}
```

---

Request:

```http
Cookie:
userId=101;
theme=dark
```

---

Response:

```text
101 dark
```

---

# Reading Cookie Object

Instead of only value:

---

```java
@GetMapping("/home")
public String home(

        @CookieValue("userId")
        Cookie cookie) {

    return cookie.getValue();

}
```

---

Access:

```java
cookie.getName()
cookie.getValue()
```

---

Useful for advanced cookie handling.

---

# Session Tracking Example

Very common.

---

Cookie:

```http
JSESSIONID=ABC123XYZ
```

---

Controller:

```java
@GetMapping("/session")
public String session(

        @CookieValue("JSESSIONID")
        String sessionId) {

    return sessionId;

}
```

---

Response:

```text
ABC123XYZ
```

---

# Remember Me Example

Spring Security often stores:

```http
remember-me=xyz123
```

---

Controller:

```java
@GetMapping("/me")
public String me(

        @CookieValue("remember-me")
        String token) {

    return token;

}
```

---

# User Preference Example

Cookie:

```http
theme=dark
```

---

Controller:

```java
@GetMapping("/theme")
public String theme(

        @CookieValue("theme")
        String theme) {

    return theme;

}
```

---

Response:

```text
dark
```

---

# Language Preference Example

Cookie:

```http
lang=en
```

---

Controller:

```java
@GetMapping("/language")
public String language(

        @CookieValue("lang")
        String language) {

    return language;

}
```

---

Response:

```text
en
```

---

# @CookieValue vs @RequestHeader

Very Important Interview Question.

---

Cookie arrives inside:

```http
Cookie Header
```

---

But Spring provides specialized support.

---

## Using RequestHeader

```java
@RequestHeader("Cookie")
String cookieHeader
```

---

Returns:

```text
userId=101; theme=dark
```

---

Manual parsing required.

---

## Using CookieValue

```java
@CookieValue("userId")
Long userId
```

---

Returns:

```java
101
```

directly.

---

Comparison:

| Feature | @CookieValue | @RequestHeader |
|----------|-------------|---------------|
| Cookie Parsing | Automatic | Manual |
| Specific Cookie | Yes | No |
| Type Conversion | Yes | Limited |
| Recommended | Yes | No |

---

# @CookieValue vs @RequestParam

| Feature | @CookieValue | @RequestParam |
|----------|-------------|--------------|
| Source | Cookie | Query Parameter |
| Sent Automatically | Yes | No |
| Browser Stored | Yes | No |
| User Input | Usually No | Yes |

---

# Internal Architecture

Cookies are resolved by:

```java
ServletCookieValueMethodArgumentResolver
```

---

Flow:

```text
DispatcherServlet
      │
      ▼
ServletCookieValueMethodArgumentResolver
      │
      ▼
request.getCookies()
      │
      ▼
Extract Cookie
      │
      ▼
Type Conversion
      │
      ▼
Controller Method
```

---

# Internal Classes

Resolver:

```java
ServletCookieValueMethodArgumentResolver
```

---

Parent:

```java
AbstractCookieValueMethodArgumentResolver
```

---

Used by:

```java
RequestMappingHandlerAdapter
```

---

# Common Mistakes

## Mistake 1

Cookie Missing

Controller:

```java
@CookieValue("userId")
Long userId
```

---

Request:

```http
(no cookie)
```

---

Exception:

```java
MissingRequestCookieException
```

---

# Mistake 2

Wrong Cookie Name

Cookie:

```http
userId=101
```

---

Code:

```java
@CookieValue("id")
```

---

Result:

```text
Cookie Not Found
```

---

# Mistake 3

Wrong Type

Cookie:

```http
userId=abc
```

---

Code:

```java
@CookieValue Long userId
```

---

Exception:

```java
MethodArgumentTypeMismatchException
```

---

# Mistake 4

Using RequestParam Instead

Wrong:

```java
@RequestParam("userId")
```

---

Correct:

```java
@CookieValue("userId")
```

---

# Common Interview Questions

## What is @CookieValue?

Used to read cookies from an HTTP request.

---

## Which component resolves @CookieValue?

```java
ServletCookieValueMethodArgumentResolver
```

---

## Is @CookieValue required by default?

Yes.

---

## How to make it optional?

```java
@CookieValue(
    required = false
)
```

---

## How to provide default values?

```java
@CookieValue(
    defaultValue = "guest"
)
```

---

## Can Spring automatically convert cookie values?

Yes.

Examples:

```java
String → Long
String → UUID
String → Enum
```

---

## Can we access the Cookie object itself?

Yes.

```java
@CookieValue Cookie cookie
```

---

## Common Real-World Uses?

```text
Session Tracking
Remember Me
Theme Selection
Language Preferences
Authentication Tokens
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/profile")
public class ProfileController {

    @GetMapping
    public ResponseEntity<String>
    profile(

            @CookieValue(
                value = "theme",
                defaultValue = "light"
            )
            String theme,

            @CookieValue(
                value = "JSESSIONID",
                required = false
            )
            String sessionId) {

        return ResponseEntity.ok(
                "Theme : " + theme);

    }

}
```

---

Request:

```http
Cookie:
theme=dark;
JSESSIONID=ABC123
```

---

Response:

```text
Theme : dark
```

---

# Internal Flow Diagram

```text
Browser
   │
   ▼
Cookie Header
   │
   ▼
DispatcherServlet
   │
   ▼
ServletCookieValueMethodArgumentResolver
   │
   ▼
request.getCookies()
   │
   ▼
Extract Cookie
   │
   ▼
Type Conversion
   │
   ▼
Controller Method
```

---

# Key Points To Remember

- `@CookieValue` extracts cookie values from incoming HTTP requests.
- Cookies are browser-stored data automatically sent with requests.
- Supports automatic type conversion.
- Cookies are required by default.
- Supports optional cookies and default values.
- Can retrieve both cookie values and complete Cookie objects.
- Commonly used for sessions, authentication, user preferences, and localization.
- Internally resolved by `ServletCookieValueMethodArgumentResolver`.
- More convenient than manually parsing the Cookie header.
- Frequently asked in Spring MVC and Spring Security interviews.