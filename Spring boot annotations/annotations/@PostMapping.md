
## Definition

`@PostMapping` is a Spring MVC annotation used to map HTTP **POST** requests to a specific controller method.

It tells Spring:

> When an HTTP POST request arrives for a particular URL, execute this method.

Package:

```java
import org.springframework.web.bind.annotation.PostMapping;
```

---

# Why Do We Need @PostMapping?

In REST APIs:

| HTTP Method | Purpose |
|------------|----------|
| GET | Read Data |
| POST | Create Data |
| PUT | Update Entire Resource |
| PATCH | Partial Update |
| DELETE | Delete Resource |

---

POST is primarily used for:

```text
Creating Resources
Submitting Forms
Sending Request Data
Uploading Files
Triggering Business Operations
```

---

Example:

```http
POST /employees
```

means:

```text
Create a New Employee
```

---

Without `@PostMapping`:

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.POST
)
```

---

Spring introduced:

```java
@PostMapping
```

for cleaner code.

---

# What is @PostMapping Internally?

Very Important Interview Question.

Internally:

```java
@PostMapping
```

is a shortcut for:

```java
@RequestMapping(
    method = RequestMethod.POST
)
```

---

Equivalent:

```java
@PostMapping("/employees")
```

and

```java
@RequestMapping(
    value = "/employees",
    method = RequestMethod.POST
)
```

are exactly the same.

---

# Basic Syntax

```java
@PostMapping("/employees")
```

---

Example

```java
@RestController
public class EmployeeController {

    @PostMapping("/employees")
    public String saveEmployee() {

        return "Employee Saved";

    }

}
```

---

Request:

```http
POST /employees
```

---

Response:

```text
Employee Saved
```

---

# What Happens Internally?

Suppose:

```java
@PostMapping("/employees")
```

---

Application Startup:

### Step 1

Spring scans controllers.

---

### Step 2

Finds:

```java
@PostMapping("/employees")
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
HTTP POST Request
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

# Most Common Usage

## Accept Request Body

POST requests usually contain data.

Example Request:

```http
POST /employees
Content-Type: application/json
```

```json
{
  "id": 101,
  "name": "John"
}
```

---

Controller:

```java
@PostMapping("/employees")
public Employee saveEmployee(
        @RequestBody Employee employee) {

    return employee;

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

# How @RequestBody Works with @PostMapping

Request:

```json
{
  "id": 101,
  "name": "John"
}
```

---

Spring:

```text
Receive JSON
       │
       ▼
Jackson ObjectMapper
       │
       ▼
Convert JSON → Java Object
       │
       ▼
Pass Object To Method
```

---

# Example: Create Employee

---

Employee DTO:

```java
public class Employee {

    private Long id;
    private String name;

}
```

---

Controller:

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @PostMapping
    public Employee createEmployee(
            @RequestBody Employee employee) {

        return employee;

    }

}
```

---

Request:

```http
POST /employees
```

```json
{
  "id": 1,
  "name": "John"
}
```

---

Response:

```json
{
  "id": 1,
  "name": "John"
}
```

---

# Using ResponseEntity

Production-grade APIs usually return:

```java
ResponseEntity
```

---

```java
@PostMapping("/employees")
public ResponseEntity<Employee>
saveEmployee(
        @RequestBody Employee employee) {

    return ResponseEntity
            .status(201)
            .body(employee);

}
```

---

Response:

```http
201 Created
```

---

# Why 201 Created?

REST Best Practice:

| Operation | Status |
|------------|---------|
| GET | 200 OK |
| POST Create | 201 Created |
| PUT | 200 OK |
| DELETE | 204 No Content |

---

# Validation with @Valid

Very Common Interview Topic.

---

DTO:

```java
public class Employee {

    @NotBlank
    private String name;

}
```

---

Controller:

```java
@PostMapping("/employees")
public Employee saveEmployee(
        @Valid
        @RequestBody Employee employee) {

    return employee;

}
```

---

Invalid Request:

```json
{
  "name": ""
}
```

---

Spring throws:

```java
MethodArgumentNotValidException
```

---

Usually handled using:

```java
@RestControllerAdvice
```

---

# Class-Level + Method-Level Mapping

---

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @PostMapping
    public Employee save(
            @RequestBody Employee employee) {

        return employee;

    }

}
```

---

Final URL:

```http
POST /employees
```

---

# Path Variable with POST

Possible but less common.

---

```java
@PostMapping("/department/{id}")
public String create(
        @PathVariable Long id) {

    return "Department " + id;

}
```

---

Request:

```http
POST /department/10
```

---

# Query Parameters with POST

---

```java
@PostMapping("/employees")
public String save(
        @RequestParam String name) {

    return name;

}
```

---

Request:

```http
POST /employees?name=John
```

---

Response:

```text
John
```

---

# Consumes Attribute

Defines accepted content type.

---

```java
@PostMapping(
    value = "/employees",
    consumes = "application/json"
)
```

---

Only accepts:

```http
Content-Type: application/json
```

---

# Produces Attribute

Defines response type.

---

```java
@PostMapping(
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

# Complete Example

```java
@PostMapping(
    value = "/employees",
    consumes = "application/json",
    produces = "application/json"
)
public Employee save(
        @RequestBody Employee employee) {

    return employee;

}
```

---

# File Upload with @PostMapping

Very common.

---

```java
@PostMapping("/upload")
public String upload(
        @RequestParam MultipartFile file) {

    return file.getOriginalFilename();

}
```

---

Request:

```http
POST /upload
```

with file.

---

# Multiple URL Mappings

---

```java
@PostMapping({
    "/employees",
    "/staff"
})
```

---

Both URLs invoke same method.

---

# POST vs GET

| Feature | GET | POST |
|----------|------|------|
| Read Data | Yes | No |
| Create Data | No | Yes |
| Request Body | Rare | Common |
| Idempotent | Yes | No |
| Cacheable | Yes | Usually No |

---

# Idempotency

Important Interview Topic.

---

GET:

```http
GET /employees
```

Repeated 100 times:

```text
Same Result
```

---

POST:

```http
POST /employees
```

Repeated 100 times:

```text
May Create 100 Employees
```

---

Therefore:

```text
POST is NOT idempotent
```

---

# Internal Architecture

Mappings stored in:

```java
RequestMappingHandlerMapping
```

---

Request Flow:

```text
POST Request
      │
      ▼
DispatcherServlet
      │
      ▼
Handler Mapping
      │
      ▼
@PostMapping Method
      │
      ▼
Business Logic
      │
      ▼
Response
```

---

# Common Mistakes

## Mistake 1

Forgetting @RequestBody

---

Wrong:

```java
@PostMapping
public Employee save(
        Employee employee) {

}
```

---

JSON may not bind properly.

---

Correct:

```java
@RequestBody Employee employee
```

---

# Mistake 2

Using GET for Create Operations

Wrong:

```java
@GetMapping("/createEmployee")
```

---

Correct:

```java
@PostMapping
```

---

# Mistake 3

Returning 200 Instead of 201

---

Better:

```java
ResponseEntity.status(201)
```

for resource creation.

---

# Mistake 4

Missing Validation

---

Without:

```java
@Valid
```

invalid data enters the system.

---

# Common Interview Questions

## What is @PostMapping?

Maps HTTP POST requests to controller methods.

---

## What is @PostMapping internally?

```java
@RequestMapping(
    method = RequestMethod.POST
)
```

---

## Which HTTP method does it support?

```http
POST
```

only.

---

## What is the primary use case?

```text
Create Resources
```

---

## Can @PostMapping accept JSON?

Yes.

Using:

```java
@RequestBody
```

---

## Which component converts JSON into Java objects?

```java
Jackson ObjectMapper
```

---

## Is POST idempotent?

No.

---

## Which HTTP status should be returned after creation?

```http
201 Created
```

---

## Can @PostMapping upload files?

Yes.

Using:

```java
MultipartFile
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {

    @PostMapping
    public ResponseEntity<Employee>
    createEmployee(
            @Valid
            @RequestBody Employee employee) {

        Employee saved =
                service.save(employee);

        return ResponseEntity
                .status(201)
                .body(saved);

    }

}
```

---

Request:

```http
POST /api/employees
```

```json
{
  "name": "John"
}
```

---

Response:

```http
201 Created
```

```json
{
  "id": 101,
  "name": "John"
}
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
POST Request
   │
   ▼
DispatcherServlet
   │
   ▼
RequestMappingHandlerMapping
   │
   ▼
@PostMapping Method
   │
   ▼
@RequestBody Conversion
   │
   ▼
Business Logic
   │
   ▼
JSON Response
```

---

# Key Points To Remember

- `@PostMapping` handles HTTP POST requests.
- Internally equals `@RequestMapping(method = RequestMethod.POST)`.
- Primarily used for creating resources.
- Commonly used with `@RequestBody`.
- JSON is converted to Java objects using Jackson.
- Supports validation using `@Valid`.
- Usually returns HTTP `201 Created`.
- POST requests are not idempotent.
- Supports file uploads using `MultipartFile`.
- Stored internally in `RequestMappingHandlerMapping`.
- One of the most important Spring Boot REST API annotations.