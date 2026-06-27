
## Definition

`@RestController` is a specialized Spring MVC annotation used to create RESTful Web Services.

It tells Spring:

> This class handles HTTP requests, and the return value of every handler method should be written directly to the HTTP response body.

Unlike:

```java
@Controller
```

which typically returns:

```text
View Names (HTML/JSP/Thymeleaf)
```

`@RestController` returns:

```text
JSON
XML
String
Objects
Collections
ResponseEntity
```

directly to the client.

---

# Internal Definition

One of the most important interview questions.

Internally:

```java
@RestController
```

is equivalent to:

```java
@Controller
@ResponseBody
```

Spring source code (simplified):

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Controller
@ResponseBody
public @interface RestController {

}
```

---

# Why Do We Need @RestController?

Modern applications are typically:

```text
React
Angular
Vue
Mobile Apps
Microservices
External APIs
```

These applications don't need:

```html
home.html
employee.jsp
```

Instead, they need:

```json
{
   "id":1,
   "name":"John"
}
```

Therefore:

```java
@RestController
```

was introduced to simplify API development.

---

# Basic Example

```java
@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {

        return "Hello World";

    }

}
```

---

Request:

```http
GET /hello
```

Response:

```http
Hello World
```

directly in response body.

---

# What Happens Internally?

Suppose:

```java
@RestController
public class EmployeeController {

    @GetMapping("/employee")
    public Employee employee() {

        return new Employee(1L,"John");

    }

}
```

Spring performs:

### Step 1

Receives request.

---

### Step 2

Finds matching endpoint.

---

### Step 3

Invokes method.

---

### Step 4

Gets Java object.

---

### Step 5

Uses:

```java
HttpMessageConverter
```

---

### Step 6

Converts object to JSON.

---

### Step 7

Returns JSON response.

---

Flow:

```text
HTTP Request
      │
      ▼
@RestController
      │
      ▼
Method Execution
      │
      ▼
Java Object
      │
      ▼
HttpMessageConverter
      │
      ▼
JSON Response
```

---

# Returning String

```java
@RestController
public class TestController {

    @GetMapping("/test")
    public String test() {

        return "Welcome";

    }

}
```

Response:

```http
Welcome
```

---

# Returning Object

## Entity

```java
public class Employee {

    private Long id;
    private String name;

}
```

---

## Controller

```java
@RestController
public class EmployeeController {

    @GetMapping("/employee")
    public Employee employee() {

        return new Employee(
                1L,
                "Sumit"
        );

    }

}
```

---

Response:

```json
{
  "id": 1,
  "name": "Sumit"
}
```

---

# Returning List

```java
@GetMapping("/employees")
public List<Employee> employees() {

    return List.of(
            new Employee(1L,"John"),
            new Employee(2L,"David")
    );

}
```

---

Response:

```json
[
  {
    "id":1,
    "name":"John"
  },
  {
    "id":2,
    "name":"David"
  }
]
```

---

# Returning Map

```java
@GetMapping("/info")
public Map<String,Object> info() {

    return Map.of(
            "name","Spring",
            "version","6"
    );

}
```

---

Response:

```json
{
  "name":"Spring",
  "version":"6"
}
```

---

# Returning ResponseEntity

Most common in production applications.

---

```java
@GetMapping("/employee")
public ResponseEntity<Employee>
employee() {

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

with JSON body.

---

# Why Use ResponseEntity?

Allows control over:

- HTTP Status
- Headers
- Body

---

Example:

```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(employee);
```

---

Response:

```http
201 CREATED
```

---

# Request Mapping Example

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

}
```

---

Endpoints:

```java
@GetMapping
```

maps to:

```http
GET /employees
```

---

```java
@PostMapping
```

maps to:

```http
POST /employees
```

---

```java
@PutMapping("/{id}")
```

maps to:

```http
PUT /employees/1
```

---

```java
@DeleteMapping("/{id}")
```

maps to:

```http
DELETE /employees/1
```

---

# Receiving Request Parameters

## Query Parameter

```java
@GetMapping("/employee")
public String employee(
        @RequestParam String name) {

    return name;

}
```

---

Request:

```http
GET /employee?name=Sumit
```

Response:

```http
Sumit
```

---

# Path Variable

```java
@GetMapping("/{id}")
public Long employee(
        @PathVariable Long id) {

    return id;

}
```

---

Request:

```http
GET /employees/10
```

Response:

```http
10
```

---

# Request Body

Most important REST concept.

---

```java
@PostMapping
public Employee save(
        @RequestBody Employee employee) {

    return employee;

}
```

---

Request:

```json
{
  "id":1,
  "name":"John"
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

# JSON Conversion

Handled by:

```java
Jackson
```

dependency.

Spring Boot automatically configures:

```java
MappingJackson2HttpMessageConverter
```

---

Object:

```java
Employee
```

↓

JSON:

```json
{
   "id":1
}
```

---

JSON:

```json
{
   "id":1
}
```

↓

Object:

```java
Employee
```

---

# Validation Example

```java
@PostMapping
public Employee save(
        @Valid
        @RequestBody Employee employee) {

    return employee;

}
```

---

Entity:

```java
@NotBlank
private String name;
```

---

Invalid request:

```json
{
  "name":""
}
```

Response:

```http
400 BAD REQUEST
```

---

# Calling Service Layer

Recommended architecture.

---

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    private final EmployeeService service;

    public EmployeeController(
            EmployeeService service) {

        this.service = service;
    }

}
```

---

```java
@Service
public class EmployeeService {

}
```

---

Architecture:

```text
Client
   │
   ▼
RestController
   │
   ▼
Service
   │
   ▼
Repository
   │
   ▼
Database
```

---

# @RestController vs @Controller

Most Important Interview Question

---

## @Controller

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home() {

        return "home";

    }

}
```

Returns:

```text
home.html
```

---

## @RestController

```java
@RestController
public class HomeController {

    @GetMapping("/")
    public String home() {

        return "home";

    }

}
```

Returns:

```http
home
```

directly.

---

# Comparison Table

| Feature | @Controller | @RestController |
|----------|-------------|----------------|
| View Resolution | Yes | No |
| Returns HTML Pages | Yes | No |
| Returns JSON | Needs @ResponseBody | Yes |
| REST API Development | Not Preferred | Preferred |
| Response Body Automatic | No | Yes |

---

# Internal Components Used

When a request arrives:

```text
DispatcherServlet
       │
       ▼
HandlerMapping
       │
       ▼
Controller Method
       │
       ▼
HttpMessageConverter
       │
       ▼
JSON Response
```

---

# Common Mistakes

## Mistake 1

Returning view name from RestController.

```java
@GetMapping("/")
public String home() {

    return "home";

}
```

Response:

```http
home
```

NOT:

```html
home.html
```

---

## Mistake 2

Putting business logic in controller.

Wrong:

```java
@RestController
public class EmployeeController {

    public void calculateSalary() {

    }

}
```

Business logic belongs in:

```java
@Service
```

---

## Mistake 3

Calling repository directly.

Wrong:

```text
Controller
    ▼
Repository
```

Correct:

```text
Controller
    ▼
Service
    ▼
Repository
```

---

# Common Interview Questions

## What is @RestController?

A specialized controller for REST APIs that automatically writes method return values to the HTTP response body.

---

## What is @RestController internally?

```java
@Controller
+
@ResponseBody
```

---

## What format does it return?

Typically:

```json
JSON
```

but can also return:

```text
XML
String
Byte Arrays
Files
```

---

## How does object-to-JSON conversion happen?

Using:

```java
HttpMessageConverter
```

typically Jackson.

---

## Difference Between @Controller and @RestController?

`@Controller` returns views.

`@RestController` returns response data.

---

## Can it return ResponseEntity?

Yes.

Most production APIs use:

```java
ResponseEntity<T>
```

---

# Real-World Significance

Nearly every modern Spring Boot application uses:

```java
@RestController
```

for:

- REST APIs
- Microservices
- Mobile Backends
- React Applications
- Angular Applications
- Vue Applications
- Public APIs
- Cloud-Native Services

It is one of the most frequently used annotations in modern Spring Boot development.

---

# Key Points To Remember

- Specialized form of `@Controller`.
- Equivalent to `@Controller + @ResponseBody`.
- Used for REST API development.
- Returns data directly in HTTP response body.
- Supports JSON, XML, Strings, Lists, Maps, and Objects.
- Uses `HttpMessageConverter` for serialization.
- Typically works with Jackson for JSON conversion.
- Commonly returns `ResponseEntity`.
- Does not use View Resolvers.
- Foundation of modern Spring Boot REST APIs.