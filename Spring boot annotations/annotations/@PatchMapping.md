
## Definition

`@PatchMapping` is a Spring MVC annotation used to map HTTP **PATCH** requests to a controller method.

It tells Spring:

> When an HTTP PATCH request arrives for a specific URL, execute this method.

Package:

```java
import org.springframework.web.bind.annotation.PatchMapping;
```

---

# Why Do We Need @PatchMapping?

In REST APIs:

| HTTP Method | Purpose |
|------------|----------|
| GET | Read Data |
| POST | Create Data |
| PUT | Full Update |
| PATCH | Partial Update |
| DELETE | Delete Resource |

---

PATCH is used when:

```text
Only a few fields of an existing resource need to be updated.
```

Example:

```http
PATCH /employees/101
```

means:

```text
Modify selected fields of Employee 101.
```

---

# Real-Life Example

Suppose Employee exists:

```json
{
  "id": 101,
  "name": "John",
  "salary": 50000,
  "department": "IT"
}
```

---

User wants to update only salary.

Request:

```http
PATCH /employees/101
```

```json
{
  "salary": 70000
}
```

---

Result:

```json
{
  "id": 101,
  "name": "John",
  "salary": 70000,
  "department": "IT"
}
```

---

Only one field changed.

---

# What is @PatchMapping Internally?

Very Important Interview Question.

Internally:

```java
@PatchMapping
```

is a shortcut for:

```java
@RequestMapping(
    method = RequestMethod.PATCH
)
```

---

Equivalent:

```java
@PatchMapping("/employees/{id}")
```

and

```java
@RequestMapping(
    value = "/employees/{id}",
    method = RequestMethod.PATCH
)
```

are exactly the same.

---

# Basic Syntax

```java
@PatchMapping("/employees/{id}")
```

---

Example

```java
@RestController
public class EmployeeController {

    @PatchMapping("/employees/{id}")
    public String updateEmployee(
            @PathVariable Long id) {

        return "Employee Updated";

    }

}
```

---

Request:

```http
PATCH /employees/101
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
@PatchMapping("/employees/{id}")
```

---

Application Startup:

### Step 1

Spring scans controllers.

---

### Step 2

Finds:

```java
@PatchMapping("/employees/{id}")
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
HTTP PATCH Request
        │
        ▼
DispatcherServlet
        │
        ▼
RequestMappingHandlerMapping
        │
        ▼
Controller Method
        │
        ▼
Business Logic
        │
        ▼
Response
```

---

# Most Common Usage

## Partial Update

---

Controller:

```java
@PatchMapping("/employees/{id}")
public Employee updateSalary(
        @PathVariable Long id,
        @RequestBody Employee employee) {

    return employee;

}
```

---

Request:

```json
{
  "salary": 70000
}
```

---

Response:

```json
{
  "salary": 70000
}
```

---

# PATCH vs PUT

Most Important Interview Topic.

---

## PUT

Represents:

```text
Full Resource Replacement
```

---

Example:

```json
{
  "id": 101,
  "name": "John",
  "salary": 70000,
  "department": "HR"
}
```

Entire object supplied.

---

## PATCH

Represents:

```text
Partial Resource Update
```

---

Example:

```json
{
  "salary": 70000
}
```

Only changed fields supplied.

---

Comparison:

| Feature | PUT | PATCH |
|----------|------|--------|
| Full Update | Yes | No |
| Partial Update | No | Yes |
| Entire Object Sent | Yes | No |
| Bandwidth Usage | Higher | Lower |

---

# Real Production Example

Employee:

```java
@Entity
public class Employee {

    private Long id;
    private String name;
    private Double salary;
    private String department;

}
```

---

PATCH Request:

```json
{
  "salary": 90000
}
```

---

Service:

```java
public Employee updateSalary(
        Long id,
        Double salary) {

    Employee employee =
            repository.findById(id)
                      .orElseThrow();

    employee.setSalary(salary);

    return repository.save(employee);

}
```

---

Only salary updated.

---

# Using Map for Dynamic Updates

Very common.

---

Controller:

```java
@PatchMapping("/{id}")
public Employee update(
        @PathVariable Long id,
        @RequestBody Map<String,Object> fields) {

    return service.update(id, fields);

}
```

---

Request:

```json
{
  "salary": 90000,
  "department": "HR"
}
```

---

Map contains:

```java
salary -> 90000
department -> HR
```

---

# Using JsonNode

Another production approach.

---

```java
@PatchMapping("/{id}")
public Employee update(
        @PathVariable Long id,
        @RequestBody JsonNode node) {

    return service.update(id, node);

}
```

---

Useful for:

```text
Dynamic Fields
Unknown Structures
Flexible APIs
```

---

# Using DTO for Partial Updates

Preferred approach.

---

DTO:

```java
public class EmployeePatchRequest {

    private String name;
    private Double salary;

}
```

---

Controller:

```java
@PatchMapping("/{id}")
public Employee patch(
        @PathVariable Long id,
        @RequestBody EmployeePatchRequest request) {

    return service.patch(id, request);

}
```

---

Safer than:

```java
Map<String,Object>
```

---

# Using ResponseEntity

Production APIs typically use:

```java
ResponseEntity
```

---

```java
@PatchMapping("/{id}")
public ResponseEntity<Employee>
patchEmployee(
        @PathVariable Long id,
        @RequestBody EmployeePatchRequest request) {

    Employee updated =
            service.patch(id, request);

    return ResponseEntity.ok(updated);

}
```

---

Response:

```http
200 OK
```

---

# Validation with PATCH

Possible but requires caution.

---

DTO:

```java
public class EmployeePatchRequest {

    @Size(min = 3)
    private String name;

}
```

---

Controller:

```java
@PatchMapping("/{id}")
public Employee patch(
        @Valid
        @RequestBody EmployeePatchRequest request) {

    return service.patch(request);

}
```

---

# Idempotency

Important Interview Topic.

---

### PUT

Always idempotent.

---

### PATCH

Depends on implementation.

---

Example 1:

```json
{
  "salary": 70000
}
```

Running multiple times:

```text
Same Result
```

---

Idempotent.

---

Example 2:

```json
{
  "salaryIncrement": 1000
}
```

Run 5 times:

```text
Salary keeps increasing
```

---

Not idempotent.

---

Therefore:

```text
PATCH may or may not be idempotent.
```

---

# Consumes Attribute

Specify accepted content type.

---

```java
@PatchMapping(
    value = "/employees/{id}",
    consumes = "application/json"
)
```

---

# Produces Attribute

Specify response type.

---

```java
@PatchMapping(
    value = "/employees/{id}",
    produces = "application/json"
)
```

---

# Class-Level + Method-Level Mapping

---

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @PatchMapping("/{id}")
    public Employee patch(
            @PathVariable Long id,
            @RequestBody EmployeePatchRequest request) {

        return service.patch(id, request);

    }

}
```

---

Final URL:

```http
PATCH /employees/101
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
PATCH Request
      │
      ▼
DispatcherServlet
      │
      ▼
Handler Mapping
      │
      ▼
@PatchMapping Method
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

Using PUT for Partial Updates

Wrong:

```java
@PutMapping
```

for changing one field.

---

Better:

```java
@PatchMapping
```

---

# Mistake 2

Overwriting Existing Data

---

Bad implementation:

```java
employee.setName(dto.getName());
```

when:

```java
dto.getName() == null
```

---

Can accidentally erase data.

---

Always check:

```java
if(dto.getName() != null)
```

---

# Mistake 3

Using Entity Directly

Bad:

```java
@RequestBody Employee
```

---

Preferred:

```java
@RequestBody EmployeePatchRequest
```

---

# Mistake 4

Ignoring Validation

---

Validate incoming fields whenever possible.

---

# Common Interview Questions

## What is @PatchMapping?

Maps HTTP PATCH requests to controller methods.

---

## What is @PatchMapping internally?

```java
@RequestMapping(
    method = RequestMethod.PATCH
)
```

---

## What is the primary use case?

```text
Partial Resource Update
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

## Is PATCH idempotent?

Depends on implementation.

---

## Can PATCH accept JSON?

Yes.

Using:

```java
@RequestBody
```

---

## Can PATCH use DTOs?

Yes.

Recommended approach.

---

## Which component converts JSON into Java Objects?

```java
Jackson ObjectMapper
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {

    @PatchMapping("/{id}")
    public ResponseEntity<Employee>
    patchEmployee(
            @PathVariable Long id,
            @RequestBody EmployeePatchRequest request) {

        Employee updated =
                service.patch(id, request);

        return ResponseEntity.ok(updated);

    }

}
```

---

Request:

```http
PATCH /api/employees/101
```

```json
{
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
  "name": "John",
  "salary": 90000,
  "department": "IT"
}
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
PATCH Request
   │
   ▼
DispatcherServlet
   │
   ▼
RequestMappingHandlerMapping
   │
   ▼
@PatchMapping Method
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

- `@PatchMapping` handles HTTP PATCH requests.
- Internally equals `@RequestMapping(method = RequestMethod.PATCH)`.
- Primarily used for partial resource updates.
- Commonly used with `@RequestBody`.
- Usually implemented using DTOs, Maps, or JsonNode.
- PATCH reduces payload size compared to PUT.
- PATCH may or may not be idempotent.
- JSON conversion is handled by Jackson.
- Stored internally in `RequestMappingHandlerMapping`.
- Frequently asked in Spring Boot and REST API interviews.
- Preferred when only a few fields need modification.