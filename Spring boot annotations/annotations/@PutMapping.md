
## Definition

`@PutMapping` is a Spring MVC annotation used to map HTTP **PUT** requests to a controller method.

It tells Spring:

> When an HTTP PUT request arrives for a specific URL, execute this method.

Package:

```java
import org.springframework.web.bind.annotation.PutMapping;
```

---

# Why Do We Need @PutMapping?

In REST APIs:

| HTTP Method | Purpose |
|------------|----------|
| GET | Read Data |
| POST | Create Data |
| PUT | Update Entire Resource |
| PATCH | Partial Update Resource |
| DELETE | Delete Resource |

---

PUT is used when:

```text
An existing resource must be completely updated.
```

Example:

```http
PUT /employees/101
```

means:

```text
Replace the Employee with ID 101 using the new data provided.
```

---

# What is @PutMapping Internally?

Very Important Interview Question.

Internally:

```java
@PutMapping
```

is a shortcut for:

```java
@RequestMapping(
    method = RequestMethod.PUT
)
```

---

Equivalent:

```java
@PutMapping("/employees/{id}")
```

and

```java
@RequestMapping(
    value = "/employees/{id}",
    method = RequestMethod.PUT
)
```

are exactly the same.

---

# Basic Syntax

```java
@PutMapping("/employees/{id}")
```

---

Example

```java
@RestController
public class EmployeeController {

    @PutMapping("/employees/{id}")
    public String updateEmployee(
            @PathVariable Long id) {

        return "Employee Updated";

    }

}
```

---

Request:

```http
PUT /employees/101
```

---

Response:

```text
Employee Updated
```

---

# What Happens Internally?

Suppose:

```java
@PutMapping("/employees/{id}")
```

---

Application Startup:

### Step 1

Spring scans controllers.

---

### Step 2

Finds:

```java
@PutMapping("/employees/{id}")
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
HTTP PUT Request
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
Execute Method
       │
       ▼
Return Response
```

---

# Most Common Usage

## Update Existing Resource

---

Request:

```http
PUT /employees/101
```

```json
{
  "name": "John Updated",
  "salary": 90000
}
```

---

Controller:

```java
@PutMapping("/employees/{id}")
public Employee updateEmployee(
        @PathVariable Long id,
        @RequestBody Employee employee) {

    employee.setId(id);

    return employee;

}
```

---

Response:

```json
{
  "id": 101,
  "name": "John Updated",
  "salary": 90000
}
```

---

# Complete Resource Replacement

Very Important Concept.

PUT means:

```text
Replace Entire Resource
```

---

Suppose Existing Employee:

```json
{
  "id": 101,
  "name": "John",
  "salary": 50000,
  "department": "IT"
}
```

---

PUT Request:

```json
{
  "id": 101,
  "name": "David",
  "salary": 80000,
  "department": "HR"
}
```

---

After PUT:

```json
{
  "id": 101,
  "name": "David",
  "salary": 80000,
  "department": "HR"
}
```

Entire resource is replaced.

---

# PUT with @RequestBody

Most common usage.

---

```java
@PutMapping("/employees/{id}")
public Employee update(
        @PathVariable Long id,
        @RequestBody Employee employee) {

    return service.update(id, employee);

}
```

---

Request Body:

```json
{
  "name": "John Updated"
}
```

---

Spring:

```text
JSON
   │
   ▼
Jackson ObjectMapper
   │
   ▼
Employee Object
```

---

# Using ResponseEntity

Production APIs generally use:

```java
ResponseEntity
```

---

```java
@PutMapping("/employees/{id}")
public ResponseEntity<Employee>
updateEmployee(
        @PathVariable Long id,
        @RequestBody Employee employee) {

    Employee updated =
            service.update(id, employee);

    return ResponseEntity.ok(updated);

}
```

---

Response:

```http
200 OK
```

---

# Handling Resource Not Found

---

```java
@PutMapping("/employees/{id}")
public ResponseEntity<Employee>
updateEmployee(
        @PathVariable Long id,
        @RequestBody Employee employee) {

    Employee updated =
            service.update(id, employee);

    if(updated == null) {

        return ResponseEntity
                .notFound()
                .build();

    }

    return ResponseEntity.ok(updated);

}
```

---

Response:

```http
404 Not Found
```

---

# Validation with @Valid

Very Important.

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
@PutMapping("/employees/{id}")
public Employee update(
        @PathVariable Long id,
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

    @PutMapping("/{id}")
    public Employee update(
            @PathVariable Long id,
            @RequestBody Employee employee) {

        return employee;

    }

}
```

---

Final URL:

```http
PUT /employees/101
```

---

# Consumes Attribute

Specify accepted content type.

---

```java
@PutMapping(
    value = "/employees/{id}",
    consumes = "application/json"
)
```

---

Accepts:

```http
Content-Type: application/json
```

---

# Produces Attribute

Specify response type.

---

```java
@PutMapping(
    value = "/employees/{id}",
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
@PutMapping(
    value = "/employees/{id}",
    consumes = "application/json",
    produces = "application/json"
)
public Employee update(
        @PathVariable Long id,
        @RequestBody Employee employee) {

    return employee;

}
```

---

# PUT vs POST

Very Common Interview Question.

| Feature | POST | PUT |
|----------|------|------|
| Purpose | Create | Update |
| Idempotent | No | Yes |
| Resource Exists | Not Required | Usually Yes |
| Repeat Request | Creates Multiple Resources | Same Result |

---

Example:

POST:

```http
POST /employees
```

Run 5 times:

```text
5 Employees Created
```

---

PUT:

```http
PUT /employees/101
```

Run 5 times:

```text
Employee 101 Updated
Same Final State
```

---

# PUT is Idempotent

Very Important.

Definition:

```text
Multiple identical PUT requests
produce the same final result.
```

---

Example:

```http
PUT /employees/101
```

```json
{
  "name": "John"
}
```

Run:

```text
1 Time
10 Times
100 Times
```

Result remains:

```json
{
  "name": "John"
}
```

---

Therefore:

```text
PUT is Idempotent
```

---

# PUT vs PATCH

Another Favorite Interview Question.

| Feature | PUT | PATCH |
|----------|------|------|
| Update Type | Full Update | Partial Update |
| Entire Resource Sent | Yes | No |
| Idempotent | Yes | Usually Yes |

---

PUT:

```json
{
  "name": "John",
  "salary": 50000,
  "department": "IT"
}
```

---

PATCH:

```json
{
  "salary": 70000
}
```

---

PATCH modifies only provided fields.

---

# Internal Architecture

Spring stores mappings inside:

```java
RequestMappingHandlerMapping
```

---

Request Flow:

```text
PUT Request
      │
      ▼
DispatcherServlet
      │
      ▼
Handler Mapping
      │
      ▼
@PutMapping Method
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

Using POST Instead of PUT

Wrong:

```java
@PostMapping("/employees/{id}")
```

for updates.

---

Correct:

```java
@PutMapping("/employees/{id}")
```

---

# Mistake 2

Ignoring Idempotency

PUT should be designed to be:

```text
Idempotent
```

---

# Mistake 3

Updating Without Validation

---

Always validate:

```java
@Valid
@RequestBody
```

---

# Mistake 4

Not Returning 404

If resource doesn't exist:

```http
404 Not Found
```

is generally preferred.

---

# Common Interview Questions

## What is @PutMapping?

Maps HTTP PUT requests to controller methods.

---

## What is @PutMapping internally?

```java
@RequestMapping(
    method = RequestMethod.PUT
)
```

---

## What is the primary use case?

```text
Update Existing Resource
```

---

## Is PUT idempotent?

Yes.

---

## What does PUT mean in REST?

```text
Complete Resource Replacement
```

---

## Can @PutMapping accept JSON?

Yes.

Using:

```java
@RequestBody
```

---

## Which component converts JSON into Java Objects?

```java
Jackson ObjectMapper
```

---

## Difference Between PUT and PATCH?

PUT:

```text
Full Update
```

PATCH:

```text
Partial Update
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {

    @PutMapping("/{id}")
    public ResponseEntity<Employee>
    updateEmployee(
            @PathVariable Long id,
            @Valid
            @RequestBody Employee employee) {

        Employee updated =
                service.update(id, employee);

        return ResponseEntity.ok(updated);

    }

}
```

---

Request:

```http
PUT /api/employees/101
```

```json
{
  "name": "John Updated",
  "salary": 90000
}
```

---

Response:

```http
200 OK
```

```json
{
  "id": 101,
  "name": "John Updated",
  "salary": 90000
}
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
PUT Request
   │
   ▼
DispatcherServlet
   │
   ▼
RequestMappingHandlerMapping
   │
   ▼
@PutMapping Method
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

- `@PutMapping` handles HTTP PUT requests.
- Internally equals `@RequestMapping(method = RequestMethod.PUT)`.
- Primarily used for updating existing resources.
- Usually represents full resource replacement.
- Commonly used with `@RequestBody`.
- Supports validation using `@Valid`.
- PUT requests are idempotent.
- Usually returns `200 OK` or `204 No Content`.
- JSON conversion is handled by Jackson.
- Stored internally in `RequestMappingHandlerMapping`.
- One of the most important REST API annotations in Spring Boot.