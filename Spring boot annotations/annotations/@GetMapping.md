
## Definition

`@GetMapping` is a Spring MVC annotation used to map HTTP **GET** requests to a specific controller method.

It tells Spring:

> When an HTTP GET request arrives for a particular URL, execute this method.

Package:

```java
import org.springframework.web.bind.annotation.GetMapping;
```

---

# Why Do We Need @GetMapping?

In REST APIs, different HTTP methods represent different operations.

| HTTP Method | Purpose |
|------------|----------|
| GET | Read Data |
| POST | Create Data |
| PUT | Update Entire Resource |
| PATCH | Partial Update |
| DELETE | Delete Resource |

---

Example:

```http
GET /employees
```

means:

```text
Fetch Employees
```

---

Instead of writing:

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.GET
)
```

Spring provides a cleaner annotation:

```java
@GetMapping("/employees")
```

---

# What is @GetMapping Internally?

Very Important Interview Question.

Internally:

```java
@GetMapping
```

is a shortcut for:

```java
@RequestMapping(
    method = RequestMethod.GET
)
```

---

Equivalent:

```java
@GetMapping("/employees")
```

and

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.GET
)
```

are exactly the same.

---

# Basic Syntax

```java
@GetMapping("/employees")
```

---

Example:

```java
@RestController
public class EmployeeController {

    @GetMapping("/employees")
    public String getEmployees() {

        return "Employee List";

    }

}
```

---

Request:

```http
GET /employees
```

---

Response:

```text
Employee List
```

---

# What Happens Internally?

Suppose:

```java
@GetMapping("/employees")
```

---

Application Startup:

### Step 1

Spring scans controllers.

---

### Step 2

Finds:

```java
@GetMapping("/employees")
```

---

### Step 3

Registers URL mapping.

---

Stored in:

```java
RequestMappingHandlerMapping
```

---

Runtime Flow:

```text
HTTP GET Request
        │
        ▼
DispatcherServlet
        │
        ▼
RequestMappingHandlerMapping
        │
        ▼
Find Matching Method
        │
        ▼
Invoke Controller Method
        │
        ▼
Return Response
```

---

# Simple Example

```java
@RestController
public class EmployeeController {

    @GetMapping("/hello")
    public String hello() {

        return "Hello Spring";

    }

}
```

---

Request:

```http
GET /hello
```

---

Response:

```text
Hello Spring
```

---

# Class-Level + Method-Level Mapping

---

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @GetMapping
    public String getEmployees() {

        return "Employees";

    }

}
```

---

Final URL:

```http
GET /employees
```

---

Another Example:

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @GetMapping("/active")
    public String activeEmployees() {

        return "Active Employees";

    }

}
```

---

Final URL:

```http
GET /employees/active
```

---

# Returning Objects

Most common real-world usage.

---

```java
@RestController
public class EmployeeController {

    @GetMapping("/employee")
    public Employee employee() {

        return new Employee(
                101,
                "John"
        );

    }

}
```

---

Response:

```json
{
  "id": 101,
  "name": "John"
}
```

---

Spring automatically converts Java objects into JSON using:

```java
Jackson ObjectMapper
```

---

# Returning List of Objects

---

```java
@GetMapping("/employees")
public List<Employee> employees() {

    return List.of(
        new Employee(1,"John"),
        new Employee(2,"David")
    );

}
```

---

Response:

```json
[
  {
    "id": 1,
    "name": "John"
  },
  {
    "id": 2,
    "name": "David"
  }
]
```

---

# Using Path Variables

Very common.

---

```java
@GetMapping("/employees/{id}")
public String employee(
        @PathVariable Long id) {

    return "Employee : " + id;

}
```

---

Request:

```http
GET /employees/101
```

---

Response:

```text
Employee : 101
```

---

# Multiple Path Variables

---

```java
@GetMapping(
    "/department/{deptId}/employee/{empId}"
)
public String details(
        @PathVariable Long deptId,
        @PathVariable Long empId) {

    return deptId + " " + empId;

}
```

---

Request:

```http
GET /department/10/employee/100
```

---

Response:

```text
10 100
```

---

# Using Query Parameters

---

```java
@GetMapping("/search")
public String search(
        @RequestParam String name) {

    return name;

}
```

---

Request:

```http
GET /search?name=John
```

---

Response:

```text
John
```

---

# Optional Query Parameter

---

```java
@GetMapping("/search")
public String search(
        @RequestParam(
            required = false
        ) String name) {

    return name;

}
```

---

Valid Requests:

```http
/search
```

or

```http
/search?name=John
```

---

# Default Value

---

```java
@GetMapping("/search")
public String search(
        @RequestParam(
            defaultValue = "Guest"
        ) String name) {

    return name;

}
```

---

Request:

```http
/search
```

---

Response:

```text
Guest
```

---

# Returning ResponseEntity

Production-grade APIs usually return:

```java
ResponseEntity
```

---

Example:

```java
@GetMapping("/employees")
public ResponseEntity<String>
employees() {

    return ResponseEntity.ok(
            "Employee List");

}
```

---

Response:

```http
200 OK
```

```text
Employee List
```

---

# Returning Custom Status

---

```java
@GetMapping("/employees")
public ResponseEntity<String>
employees() {

    return ResponseEntity
            .status(200)
            .body("Employees");

}
```

---

# Produces Attribute

Specify response content type.

---

```java
@GetMapping(
    value = "/employees",
    produces = "application/json"
)
```

---

Response:

```http
Content-Type: application/json
```

---

# Multiple URL Mappings

---

```java
@GetMapping({
    "/employees",
    "/staff",
    "/workers"
})
```

---

All URLs invoke same method.

---

# Example

```java
@GetMapping({
    "/employees",
    "/staff"
})
public String getEmployees() {

    return "Employees";

}
```

---

# GET Request Best Practices

GET requests should be:

```text
Safe
Idempotent
Read-Only
```

---

Safe:

```text
No Data Modification
```

---

Good:

```http
GET /employees
```

---

Bad:

```http
GET /deleteEmployee/101
```

---

GET should never delete data.

---

# GET vs POST

| Feature | GET | POST |
|----------|------|------|
| Read Data | Yes | No |
| Create Data | No | Yes |
| Request Body | Usually No | Yes |
| Cacheable | Yes | No |
| Idempotent | Yes | Usually No |

---

# Common Mistakes

## Mistake 1

Using GET for Data Modification

Wrong:

```java
@GetMapping("/deleteEmployee")
```

---

Should use:

```java
@DeleteMapping
```

---

# Mistake 2

Expecting Request Body in GET

Wrong:

```java
@GetMapping("/employees")
public Employee get(
    @RequestBody Employee emp) {
}
```

---

GET requests generally should not use request bodies.

---

# Mistake 3

Duplicate URL Mapping

---

```java
@GetMapping("/employees")
```

in multiple methods.

---

Result:

```text
Ambiguous Mapping Exception
```

---

# Mistake 4

Forgetting Path Variable

---

```java
@GetMapping("/employees/{id}")
```

Request:

```http
/employees
```

---

Result:

```text
404 Not Found
```

---

# Internal Architecture

Spring stores mappings in:

```java
RequestMappingHandlerMapping
```

---

When request arrives:

```text
DispatcherServlet
        │
        ▼
RequestMappingHandlerMapping
        │
        ▼
Controller Method
        │
        ▼
Response
```

---

# Common Interview Questions

## What is @GetMapping?

Maps HTTP GET requests to controller methods.

---

## What is @GetMapping internally?

```java
@RequestMapping(
    method = RequestMethod.GET
)
```

---

## Which HTTP method does it support?

```http
GET
```

only.

---

## Can @GetMapping return JSON?

Yes.

Spring automatically serializes Java objects into JSON.

---

## Can @GetMapping use Path Variables?

Yes.

Using:

```java
@PathVariable
```

---

## Can @GetMapping use Query Parameters?

Yes.

Using:

```java
@RequestParam
```

---

## Can @GetMapping return ResponseEntity?

Yes.

Most production APIs do.

---

## Is GET idempotent?

Yes.

Multiple identical requests should produce the same result.

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {

    @GetMapping
    public List<Employee> getEmployees() {

        return service.findAll();

    }

    @GetMapping("/{id}")
    public Employee getEmployee(
            @PathVariable Long id) {

        return service.findById(id);

    }

}
```

---

Endpoints:

```http
GET /api/employees
GET /api/employees/101
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
GET Request
   │
   ▼
DispatcherServlet
   │
   ▼
RequestMappingHandlerMapping
   │
   ▼
@GetMapping Method
   │
   ▼
Business Logic
   │
   ▼
JSON Response
```

---

# Key Points To Remember

- `@GetMapping` handles HTTP GET requests.
- Internally equals `@RequestMapping(method = RequestMethod.GET)`.
- Primarily used for fetching/read-only operations.
- Supports Path Variables and Query Parameters.
- Can return String, Objects, Collections, or ResponseEntity.
- Spring automatically converts Java objects to JSON using Jackson.
- GET requests should be safe and idempotent.
- Stored internally in `RequestMappingHandlerMapping`.
- One of the most frequently used annotations in Spring Boot REST APIs.