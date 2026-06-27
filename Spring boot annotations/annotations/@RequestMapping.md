
## Definition

`@RequestMapping` is one of the most fundamental annotations in Spring MVC and Spring Boot.

It is used to map incoming HTTP requests to controller methods or controller classes.

It tells Spring:

> When a request matching a specific URL pattern, HTTP method, header, parameter, or content type arrives, execute this controller method.

Package:

```java
import org.springframework.web.bind.annotation.RequestMapping;
```

---

# Why Do We Need @RequestMapping?

Without Spring MVC:

```java
if(url.equals("/employees")) {
    // Execute logic
}
```

Developers would have to manually inspect URLs and route requests.

Spring simplifies this using:

```java
@RequestMapping
```

---

Example:

```java
@RequestMapping("/employees")
```

Spring automatically routes:

```http
GET /employees
```

to the corresponding controller method.

---

# Basic Syntax

```java
@RequestMapping("/employees")
```

---

Example:

```java
@RestController
public class EmployeeController {

    @RequestMapping("/employees")
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

Response:

```text
Employee List
```

---

# What Happens Internally?

Suppose:

```java
@RequestMapping("/employees")
public String getEmployees() {
    return "Employee List";
}
```

---

Spring Startup:

### Step 1

Scans Controllers.

---

### Step 2

Finds:

```java
@RequestMapping("/employees")
```

---

### Step 3

Registers URL mapping.

---

### Step 4

Stores metadata inside:

```text
RequestMappingHandlerMapping
```

---

When request arrives:

```http
GET /employees
```

Spring:

```text
Find Matching URL
       ↓
Find Controller Method
       ↓
Invoke Method
       ↓
Return Response
```

---

# Class-Level Mapping

Used to define a common base URL.

---

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

}
```

---

All methods inherit:

```text
/employees
```

as base path.

---

Example:

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @RequestMapping("/all")
    public String getAll() {

        return "All Employees";

    }

}
```

---

Final URL:

```http
/employees/all
```

---

# Method-Level Mapping

---

```java
@RestController
public class EmployeeController {

    @RequestMapping("/employees")
    public String getEmployees() {

        return "Employees";

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
Employees
```

---

# Class + Method Mapping

---

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @RequestMapping("/active")
    public String activeEmployees() {

        return "Active Employees";

    }

}
```

---

Final URL:

```http
/employees/active
```

---

# Specifying HTTP Methods

By default:

```java
@RequestMapping
```

accepts:

```text
GET
POST
PUT
DELETE
PATCH
HEAD
OPTIONS
```

---

Example:

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.GET
)
```

---

Only:

```http
GET /employees
```

is allowed.

---

# GET Mapping

---

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.GET
)
public String getEmployees() {

    return "Employee List";

}
```

---

Equivalent modern annotation:

```java
@GetMapping("/employees")
```

---

# POST Mapping

---

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.POST
)
public String createEmployee() {

    return "Employee Created";

}
```

---

Equivalent:

```java
@PostMapping("/employees")
```

---

# PUT Mapping

---

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.PUT
)
```

---

Equivalent:

```java
@PutMapping
```

---

# DELETE Mapping

---

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.DELETE
)
```

---

Equivalent:

```java
@DeleteMapping
```

---

# PATCH Mapping

---

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.PATCH
)
```

---

Equivalent:

```java
@PatchMapping
```

---

# Multiple URLs

A single method can handle multiple URLs.

---

```java
@RequestMapping({
    "/employees",
    "/staff",
    "/workers"
})
```

---

Request:

```http
/employees
/staff
/workers
```

All invoke same method.

---

# Path Variables

---

```java
@RequestMapping("/employees/{id}")
public String getEmployee(
        @PathVariable Long id) {

    return "Employee " + id;

}
```

---

Request:

```http
/employees/101
```

---

Response:

```text
Employee 101
```

---

# Multiple Path Variables

---

```java
@RequestMapping(
    "/department/{deptId}/employee/{empId}"
)
public String getEmployee(
        @PathVariable Long deptId,
        @PathVariable Long empId) {

    return deptId + " " + empId;

}
```

---

Request:

```http
/department/10/employee/100
```

---

Response:

```text
10 100
```

---

# Query Parameters

---

```java
@RequestMapping("/search")
public String search(
        @RequestParam String name) {

    return name;

}
```

---

Request:

```http
/search?name=John
```

---

Response:

```text
John
```

---

# Request Parameters Condition

Only invoke method if parameter exists.

---

```java
@RequestMapping(
    value = "/employees",
    params = "id"
)
```

---

Valid:

```http
/employees?id=100
```

---

Invalid:

```http
/employees
```

---

Method not executed.

---

# Header-Based Mapping

Only execute if header matches.

---

```java
@RequestMapping(
    value = "/employees",
    headers = "version=1"
)
```

---

Request:

```http
GET /employees
version: 1
```

---

Method executes.

---

# Content Type Mapping

Using consumes.

---

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.POST,
    consumes = "application/json"
)
```

---

Only accepts:

```http
Content-Type: application/json
```

---

# Produces

Defines response content type.

---

```java
@RequestMapping(
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

# Example

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.POST,
    consumes = "application/json",
    produces = "application/json"
)
```

---

# Wildcard Mapping

---

```java
@RequestMapping("/employees/*")
```

Matches:

```http
/employees/1
/employees/2
```

---

# Ant Pattern

---

```java
@RequestMapping("/**")
```

Matches:

```text
All URLs
```

---

Usually used in:

```text
Fallback Controllers
Error Handlers
```

---

# RequestMappingHandlerMapping

Important Interview Topic.

Internally Spring stores mappings in:

```java
RequestMappingHandlerMapping
```

---

Example:

```java
@RequestMapping("/employees")
```

Stored as:

```text
URL → Controller Method
```

mapping.

---

# Request Processing Flow

```text
HTTP Request
       │
       ▼
DispatcherServlet
       │
       ▼
RequestMappingHandlerMapping
       │
       ▼
Find Controller Method
       │
       ▼
Invoke Method
       │
       ▼
Return Response
```

---

# @RequestMapping vs @GetMapping

---

## @RequestMapping

```java
@RequestMapping(
    value="/employees",
    method=RequestMethod.GET
)
```

---

## @GetMapping

```java
@GetMapping("/employees")
```

---

Same functionality.

---

Modern Spring prefers:

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```

---

# Common Mistakes

## Mistake 1

Duplicate Mapping

---

```java
@RequestMapping("/employees")
```

in two methods.

---

Result:

```text
Ambiguous Mapping Exception
```

---

# Mistake 2

Wrong HTTP Method

---

```java
@RequestMapping(
    value="/employees",
    method=RequestMethod.POST
)
```

---

Request:

```http
GET /employees
```

---

Result:

```text
405 Method Not Allowed
```

---

# Mistake 3

Missing Path Variable

---

```java
@RequestMapping("/employees/{id}")
```

---

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

# Common Interview Questions

## What is @RequestMapping?

Maps HTTP requests to controller methods.

---

## Can it be used on classes?

Yes.

---

## Can it be used on methods?

Yes.

---

## What is the default HTTP method?

All HTTP methods are allowed unless specified.

---

## What replaces @RequestMapping in modern Spring?

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```

---

## Can @RequestMapping map based on headers?

Yes.

Using:

```java
headers=""
```

---

## Can @RequestMapping map based on content type?

Yes.

Using:

```java
consumes=""
produces=""
```

---

## Which Spring component stores mappings?

```java
RequestMappingHandlerMapping
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/v1/employees")
public class EmployeeController {

    @GetMapping("/{id}")
    public Employee getEmployee(
            @PathVariable Long id) {

        return service.getEmployee(id);

    }

    @PostMapping
    public Employee saveEmployee(
            @RequestBody Employee employee) {

        return service.save(employee);

    }

    @PutMapping("/{id}")
    public Employee updateEmployee(
            @PathVariable Long id,
            @RequestBody Employee employee) {

        return service.update(id, employee);

    }

    @DeleteMapping("/{id}")
    public void deleteEmployee(
            @PathVariable Long id) {

        service.delete(id);

    }

}
```

---

# Key Points To Remember

- Used to map HTTP requests to controller methods.
- Can be applied at class level and method level.
- Supports URL mapping, HTTP methods, headers, parameters, consumes, and produces.
- Internally managed by `RequestMappingHandlerMapping`.
- Supports path variables and query parameters.
- Can map multiple URLs.
- Base annotation behind `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, and `@PatchMapping`.
- One of the most important Spring MVC and Spring Boot web annotations.