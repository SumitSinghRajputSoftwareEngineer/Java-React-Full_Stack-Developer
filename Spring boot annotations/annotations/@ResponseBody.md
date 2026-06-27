
## Definition

`@ResponseBody` is a Spring MVC annotation that tells Spring:

> Do not return a View (JSP/HTML/Thymeleaf). Instead, write the returned object directly into the HTTP Response Body.

Package:

```java
import org.springframework.web.bind.annotation.ResponseBody;
```

---

# Why Do We Need @ResponseBody?

To understand `@ResponseBody`, first understand how Spring MVC originally worked.

---

## Traditional Spring MVC

Controller:

```java
@Controller
public class EmployeeController {

    @GetMapping("/employee")
    public String employee() {

        return "employee";

    }

}
```

Spring interprets:

```java
"employee"
```

as:

```text
View Name
```

and tries to locate:

```text
employee.jsp
employee.html
employee.ftl
```

depending on configuration.

---

Flow:

```text
Controller
    ↓
View Resolver
    ↓
JSP/HTML Page
```

---

But in REST APIs we usually want:

```json
{
  "id":1,
  "name":"John"
}
```

instead of:

```html
employee.html
```

---

This is why `@ResponseBody` exists.

---

# What Problem Does It Solve?

Without `@ResponseBody`

```java
@Controller
public class TestController {

    @GetMapping("/hello")
    public String hello() {

        return "hello";

    }

}
```

Spring interprets:

```text
hello
```

as:

```text
View Name
```

---

Result:

```text
404 View Not Found
```

(if view does not exist)

---

With:

```java
@ResponseBody
```

Spring interprets:

```text
hello
```

as:

```text
Actual Response Data
```

and sends it directly to client.

---

# Basic Syntax

```java
@Controller
public class TestController {

    @GetMapping("/hello")

    @ResponseBody
    public String hello() {

        return "Hello World";

    }

}
```

---

Response:

```text
Hello World
```

---

# What Happens Internally?

Request:

```http
GET /hello
```

---

Spring Flow:

```text
DispatcherServlet
      │
      ▼
Controller Method
      │
      ▼
@ResponseBody Detected
      │
      ▼
HttpMessageConverter
      │
      ▼
HTTP Response Body
```

---

# Returning Plain Text

---

```java
@Controller
public class TestController {

    @GetMapping("/hello")

    @ResponseBody
    public String hello() {

        return "Hello Spring";

    }

}
```

---

Response:

```text
Hello Spring
```

---

Content-Type:

```http
text/plain
```

---

# Returning POJO Object

DTO:

```java
public class Employee {

    private Long id;
    private String name;

}
```

---

Controller:

```java
@Controller
public class EmployeeController {

    @GetMapping("/employee")

    @ResponseBody
    public Employee employee() {

        return new Employee(
                1L,
                "John"
        );

    }

}
```

---

Response:

```json
{
  "id":1,
  "name":"John"
}
```

---

# Returning List

---

```java
@GetMapping("/employees")

@ResponseBody
public List<String> employees() {

    return List.of(
            "John",
            "David",
            "Mike"
    );

}
```

---

Response:

```json
[
  "John",
  "David",
  "Mike"
]
```

---

# Returning Map

---

```java
@GetMapping("/data")

@ResponseBody
public Map<String,Object> data() {

    Map<String,Object> map =
            new HashMap<>();

    map.put("name","John");
    map.put("salary",90000);

    return map;

}
```

---

Response:

```json
{
  "name":"John",
  "salary":90000
}
```

---

# How Object Becomes JSON?

Very Important Interview Topic.

---

Spring uses:

```java
HttpMessageConverter
```

---

Specifically:

```java
MappingJackson2HttpMessageConverter
```

---

Flow:

```text
Java Object
      │
      ▼
Jackson ObjectMapper
      │
      ▼
JSON
      │
      ▼
HTTP Response
```

---

# Content Negotiation

Spring automatically decides response format.

---

Request:

```http
Accept: application/json
```

Response:

```json
{
  "name":"John"
}
```

---

Request:

```http
Accept: application/xml
```

(if XML converter configured)

Response:

```xml
<Employee>
    <name>John</name>
</Employee>
```

---

This mechanism is called:

```text
Content Negotiation
```

---

# ResponseBody with ResponseEntity

Very common.

---

```java
@GetMapping("/employee")

@ResponseBody
public ResponseEntity<Employee> employee() {

    return ResponseEntity.ok(
            new Employee(
                    1L,
                    "John"
            )
    );

}
```

---

Response:

```http
200 OK
```

```json
{
  "id":1,
  "name":"John"
}
```

---

# Method-Level Usage

Most common.

---

```java
@ResponseBody
@GetMapping("/hello")
public String hello() {

}
```

---

Applies only to that method.

---

# Class-Level Usage

Instead of placing on every method:

```java
@Controller
@ResponseBody
public class EmployeeController {

}
```

---

Every method automatically becomes:

```java
@ResponseBody
```

enabled.

---

Equivalent to:

```java
@RestController
```

---

# @RestController Relationship

Very Important Interview Question.

---

```java
@RestController
```

is actually:

```java
@Controller
+
@ResponseBody
```

---

Internally:

```java
@Target(TYPE)
@Retention(RUNTIME)

@Controller
@ResponseBody
public @interface RestController {

}
```

---

Therefore:

```java
@RestController
```

means:

```java
All methods return response body.
```

---

# @ResponseBody vs @Controller

## Controller

```java
@Controller
```

Returns:

```text
View Name
```

---

Example:

```java
return "employee";
```

means:

```text
employee.jsp
```

---

## ResponseBody

```java
@ResponseBody
```

Returns:

```text
Actual Data
```

---

Example:

```java
return "employee";
```

means:

```text
employee
```

---

# @ResponseBody vs @RestController

Comparison:

| Feature | @ResponseBody | @RestController |
|----------|--------------|----------------|
| Scope | Method/Class | Class |
| Returns Data | Yes | Yes |
| View Resolution | Disabled | Disabled |
| Common in REST APIs | Yes | Yes |

---

# Internal Architecture

Very Important.

---

Spring detects:

```java
@ResponseBody
```

through:

```java
RequestResponseBodyMethodProcessor
```

---

Flow:

```text
DispatcherServlet
       │
       ▼
Handler Adapter
       │
       ▼
Controller Method
       │
       ▼
RequestResponseBodyMethodProcessor
       │
       ▼
HttpMessageConverter
       │
       ▼
HTTP Response
```

---

# Internal Classes

Main Processor:

```java
RequestResponseBodyMethodProcessor
```

---

Converter:

```java
HttpMessageConverter
```

---

JSON Converter:

```java
MappingJackson2HttpMessageConverter
```

---

Serializer:

```java
ObjectMapper
```

---

# Common Mistakes

## Mistake 1

Forgetting @ResponseBody

Controller:

```java
@Controller
public class TestController {

    @GetMapping("/hello")
    public String hello() {

        return "hello";

    }

}
```

---

Spring searches:

```text
hello.jsp
```

instead of sending:

```text
hello
```

---

# Mistake 2

Using @ResponseBody with JSP Pages

Wrong:

```java
@ResponseBody
public String home() {

    return "home";

}
```

---

Response:

```text
home
```

---

Not:

```text
home.jsp
```

---

# Mistake 3

Returning Non-Serializable Objects

Example:

```java
Socket
InputStream
Thread
```

---

Jackson cannot serialize them.

---

Exception:

```java
HttpMessageNotWritableException
```

---

# Mistake 4

Circular References

Example:

```java
Employee
   ↓
Department
   ↓
Employee
```

---

Can cause:

```java
StackOverflowError
```

or

```java
JsonMappingException
```

---

Use:

```java
@JsonManagedReference
@JsonBackReference
@JsonIgnore
```

---

# Common Interview Questions

## What is @ResponseBody?

Writes return value directly to HTTP response body.

---

## Does it return View Name?

No.

It bypasses View Resolution.

---

## Which component handles @ResponseBody?

```java
RequestResponseBodyMethodProcessor
```

---

## How does object become JSON?

Using:

```java
MappingJackson2HttpMessageConverter
```

and

```java
ObjectMapper
```

---

## Difference Between Controller and ResponseBody?

Controller:

```text
View Resolution
```

ResponseBody:

```text
Direct Response
```

---

## Difference Between ResponseBody and RestController?

```java
@RestController
=
@Controller
+
@ResponseBody
```

---

## Can @ResponseBody Return XML?

Yes.

If XML converter is configured.

---

## Does Spring Use Jackson Automatically?

Yes.

When:

```java
spring-boot-starter-web
```

is present.

---

# Real-World Example

```java
@Controller
@RequestMapping("/api/employees")
public class EmployeeController {

    @GetMapping("/{id}")

    @ResponseBody
    public Employee getEmployee(
            @PathVariable Long id) {

        return service.findById(id);

    }

}
```

---

Request:

```http
GET /api/employees/1
```

---

Response:

```json
{
  "id":1,
  "name":"John",
  "salary":90000
}
```

---

# Internal Flow Diagram

```text
HTTP Request
      │
      ▼
DispatcherServlet
      │
      ▼
Controller Method
      │
      ▼
@ResponseBody Found
      │
      ▼
RequestResponseBodyMethodProcessor
      │
      ▼
HttpMessageConverter
      │
      ▼
Jackson ObjectMapper
      │
      ▼
JSON Response
      │
      ▼
Client
```

---

# Key Points To Remember

- `@ResponseBody` writes return values directly to HTTP response body.
- It bypasses Spring View Resolution.
- Commonly used in REST APIs.
- Supports String, POJO, List, Map, DTOs, and ResponseEntity.
- Uses `HttpMessageConverter` internally.
- JSON conversion is handled by `MappingJackson2HttpMessageConverter`.
- `@RestController` is a combination of `@Controller` and `@ResponseBody`.
- Processed internally by `RequestResponseBodyMethodProcessor`.
- Supports JSON, XML, and other response formats through Content Negotiation.
- One of the most important Spring MVC and Spring Boot interview annotations.