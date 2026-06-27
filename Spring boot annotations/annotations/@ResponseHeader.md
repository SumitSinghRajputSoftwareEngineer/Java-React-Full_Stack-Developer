
## Important: There is NO `@ResponseHeader` Annotation in Spring

One of the most common Spring interview traps is:

```java
@ResponseHeader
```

There is **no built-in annotation named `@ResponseHeader`** in Spring Framework or Spring Boot.

If you try:

```java
@ResponseHeader
public String test() {
    return "Hello";
}
```

You will get:

```text
Compilation Error
```

because Spring does not provide such an annotation.

---

# Then How Do We Set Response Headers in Spring?

Spring provides several approaches:

1. Using `ResponseEntity`
2. Using `HttpServletResponse`
3. Using Filters
4. Using Interceptors
5. Using `@ControllerAdvice`
6. Using `ResponseBodyAdvice`

---

# Approach 1: Using ResponseEntity (Recommended)

This is the most common and recommended approach.

```java
@GetMapping("/employee")
public ResponseEntity<String> getEmployee() {

    return ResponseEntity
            .ok()
            .header("Company", "OpenAI")
            .header("Version", "v1")
            .body("Employee Data");
}
```

---

Response:

```http
HTTP/1.1 200 OK

Company: OpenAI
Version: v1

Employee Data
```

---

# Approach 2: Using HttpServletResponse

Spring allows direct access to the response object.

```java
@GetMapping("/employee")
public String employee(
        HttpServletResponse response) {

    response.addHeader(
            "Company",
            "OpenAI"
    );

    return "Employee";
}
```

---

Response:

```http
Company: OpenAI
```

---

# Approach 3: Returning Custom Headers

```java
@GetMapping("/test")
public ResponseEntity<String> test() {

    HttpHeaders headers =
            new HttpHeaders();

    headers.add(
            "Application",
            "Employee-Service"
    );

    return new ResponseEntity<>(
            "Success",
            headers,
            HttpStatus.OK
    );
}
```

---

# Approach 4: Global Headers Using Filter

Useful when every request should contain a header.

```java
@Component
public class HeaderFilter
        implements Filter {

    @Override
    public void doFilter(
            ServletRequest request,
            ServletResponse response,
            FilterChain chain)
            throws IOException,
                   ServletException {

        HttpServletResponse res =
                (HttpServletResponse) response;

        res.addHeader(
                "X-App-Version",
                "1.0"
        );

        chain.doFilter(
                request,
                response
        );
    }
}
```

---

Every response gets:

```http
X-App-Version: 1.0
```

---

# Approach 5: Using Interceptor

```java
@Component
public class HeaderInterceptor
        implements HandlerInterceptor {

    @Override
    public void postHandle(
            HttpServletRequest request,
            HttpServletResponse response,
            Object handler,
            ModelAndView modelAndView) {

        response.addHeader(
                "API-Version",
                "v1"
        );
    }
}
```

---

# Approach 6: Using ControllerAdvice

Global response customization.

```java
@RestControllerAdvice
public class GlobalHeaderAdvice {

}
```

Usually combined with:

```java
ResponseBodyAdvice
```

---

# ResponseBodyAdvice Example

```java
@RestControllerAdvice
public class HeaderAdvice
        implements ResponseBodyAdvice<Object> {

    @Override
    public Object beforeBodyWrite(
            Object body,
            MethodParameter returnType,
            MediaType selectedContentType,
            Class selectedConverterType,
            ServerHttpRequest request,
            ServerHttpResponse response) {

        response.getHeaders()
                .add(
                    "Application",
                    "Employee-Service"
                );

        return body;
    }
}
```

---

# Common Response Headers

## Content-Type

```http
Content-Type: application/json
```

---

## Cache-Control

```http
Cache-Control: no-cache
```

---

## Authorization

```http
Authorization: Bearer xxx
```

---

## Location

```http
Location: /employees/101
```

Often returned after:

```http
201 CREATED
```

---

## Custom Headers

```http
X-Correlation-Id
X-App-Version
X-Request-Id
```

---

# Setting Location Header Example

Very common REST interview question.

```java
@PostMapping("/employees")
public ResponseEntity<Employee> create() {

    Employee employee =
            service.save();

    URI location =
            URI.create(
                "/employees/" +
                employee.getId()
            );

    return ResponseEntity
            .created(location)
            .body(employee);
}
```

---

Response:

```http
201 CREATED

Location: /employees/101
```

---

# Why Doesn't Spring Have @ResponseHeader?

Spring designers preferred:

```java
ResponseEntity
```

because it allows:

```text
Headers
Status Code
Response Body
```

to be configured together.

---

Example:

```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .header("Version", "v1")
        .body(employee);
```

---

# Interview Trap Question

## Question

Does Spring Boot provide:

```java
@ResponseHeader
```

?

---

## Answer

```text
No.
```

There is no built-in annotation called:

```java
@ResponseHeader
```

---

Use:

```java
ResponseEntity
```

or

```java
HttpServletResponse
```

instead.

---

# Common Interview Questions

## Is @ResponseHeader a Spring Annotation?

No.

---

## How do we add HTTP response headers?

Using:

```java
ResponseEntity
```

or

```java
HttpServletResponse
```

---

## Recommended Approach?

```java
ResponseEntity
```

---

## How to add global response headers?

Using:

```java
Filter
Interceptor
ResponseBodyAdvice
```

---

## Can @ResponseStatus set headers?

No.

It only sets:

```text
HTTP Status Code
```

---

# Key Points To Remember

- `@ResponseHeader` does not exist in Spring Framework.
- It is a common interview trick question.
- Use `ResponseEntity` to set response headers.
- Use `HttpServletResponse` for low-level header manipulation.
- Use Filters, Interceptors, or `ResponseBodyAdvice` for global headers.
- `ResponseEntity` is the most recommended and REST-friendly approach.
- Headers, Status Codes, and Body can all be controlled through `ResponseEntity`.