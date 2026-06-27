
## Definition

`@PathVariable` is a Spring MVC annotation used to extract values from the **URI path (URL path)** and bind them to method parameters.

It tells Spring:

> Take the value present in the URL path and inject it into the controller method parameter.

Package:

```java
import org.springframework.web.bind.annotation.PathVariable;
```

---

# Why Do We Need @PathVariable?

Suppose we want to fetch employee details.

Without Path Variable:

```http
GET /employee?id=101
```

Using query parameters.

---

REST APIs usually prefer:

```http
GET /employees/101
```

where:

```text
101 = Employee ID
```

---

To capture this value:

```java
@PathVariable
```

is used.

---

# What Problem Does It Solve?

Consider:

```http
GET /employees/101
```

---

How will Spring know:

```text
101 = id
```

inside Java code?

---

Answer:

```java
@PathVariable
```

---

# Basic Syntax

```java
@GetMapping("/employees/{id}")
public Employee getEmployee(
        @PathVariable Long id) {

}
```

---

URL:

```http
GET /employees/101
```

---

Spring automatically injects:

```java
id = 101
```

---

# What Happens Internally?

Controller:

```java
@GetMapping("/employees/{id}")
```

---

Request:

```http
GET /employees/101
```

---

Spring Flow:

```text
Request Received
        │
        ▼
DispatcherServlet
        │
        ▼
RequestMappingHandlerMapping
        │
        ▼
Match URL Pattern
        │
        ▼
Extract Path Variable
        │
        ▼
Convert To Java Type
        │
        ▼
Invoke Method
```

---

# Simple Example

```java
@RestController
public class EmployeeController {

    @GetMapping("/employees/{id}")
    public String getEmployee(
            @PathVariable Long id) {

        return "Employee : " + id;

    }

}
```

---

Request:

```http
GET /employees/100
```

---

Response:

```text
Employee : 100
```

---

# Internal URL Pattern Matching

Pattern:

```java
/employees/{id}
```

---

Request:

```http
/employees/101
```

---

Spring extracts:

```java
id = 101
```

---

Think of:

```text
{id}
```

as a placeholder.

---

# Named Path Variables

---

```java
@GetMapping("/employees/{employeeId}")
public String getEmployee(
        @PathVariable("employeeId")
        Long id) {

    return "Employee : " + id;

}
```

---

URL:

```http
/employees/101
```

---

Spring maps:

```text
employeeId → id
```

---

# Parameter Name Matching

Most common approach.

---

```java
@GetMapping("/employees/{id}")
public String getEmployee(
        @PathVariable Long id) {

}
```

---

Spring automatically matches:

```text
{id}
```

with:

```java
Long id
```

---

# Multiple Path Variables

Very common.

---

```java
@GetMapping(
    "/departments/{deptId}/employees/{empId}"
)
public String details(
        @PathVariable Long deptId,
        @PathVariable Long empId) {

    return deptId + " - " + empId;

}
```

---

Request:

```http
GET /departments/10/employees/101
```

---

Spring injects:

```java
deptId = 10
empId = 101
```

---

Response:

```text
10 - 101
```

---

# Path Variable with Objects

---

Controller:

```java
@GetMapping("/employees/{id}")
public Employee getEmployee(
        @PathVariable Long id) {

    return service.findById(id);

}
```

---

Request:

```http
GET /employees/101
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

# Automatic Type Conversion

Spring automatically converts.

---

URL:

```http
/employees/101
```

---

Method:

```java
@PathVariable Long id
```

---

Conversion:

```text
String → Long
```

performed automatically.

---

# Supported Types

---

## String

```java
@PathVariable String id
```

---

## Integer

```java
@PathVariable Integer id
```

---

## Long

```java
@PathVariable Long id
```

---

## Double

```java
@PathVariable Double amount
```

---

## UUID

```java
@PathVariable UUID id
```

---

Example:

```http
/users/550e8400-e29b-41d4-a716-446655440000
```

---

Spring converts automatically.

---

# Using Map<String,String>

Useful for dynamic paths.

---

```java
@GetMapping("/employees/{id}/{name}")
public Map<String,String> test(
        @PathVariable
        Map<String,String> values) {

    return values;

}
```

---

Request:

```http
/employees/101/john
```

---

Response:

```json
{
  "id": "101",
  "name": "john"
}
```

---

# Optional Path Variable

Not commonly used.

---

```java
@GetMapping({
    "/employees",
    "/employees/{id}"
})
public String employee(
        @PathVariable(
            required = false
        ) Long id) {

    return String.valueOf(id);

}
```

---

Requests:

```http
/employees
```

or

```http
/employees/101
```

---

# Path Variable vs Request Parameter

Very Important Interview Question.

---

## Path Variable

```http
/employees/101
```

Represents:

```text
Specific Resource
```

---

Code:

```java
@PathVariable Long id
```

---

## Request Parameter

```http
/employees?id=101
```

Represents:

```text
Filtering/Search Criteria
```

---

Code:

```java
@RequestParam Long id
```

---

Comparison:

| Feature | @PathVariable | @RequestParam |
|-----------|-------------|--------------|
| URL Location | Path | Query String |
| Mandatory | Usually Yes | Can Be Optional |
| Resource Identification | Yes | Usually No |
| REST Friendly | Yes | Less |

---

# Real REST API Examples

## Get Employee

```http
GET /employees/101
```

---

```java
@GetMapping("/employees/{id}")
```

---

## Delete Employee

```http
DELETE /employees/101
```

---

```java
@DeleteMapping("/employees/{id}")
```

---

## Update Employee

```http
PUT /employees/101
```

---

```java
@PutMapping("/employees/{id}")
```

---

## Partial Update

```http
PATCH /employees/101
```

---

```java
@PatchMapping("/employees/{id}")
```

---

# Validation with @PathVariable

---

```java
@GetMapping("/{id}")
public Employee getEmployee(
        @Positive
        @PathVariable Long id) {

    return service.findById(id);

}
```

---

Request:

```http
/employees/-1
```

---

Validation Exception:

```java
ConstraintViolationException
```

---

Requires:

```java
@Validated
```

at controller level.

---

Example:

```java
@RestController
@Validated
public class EmployeeController {

}
```

---

# Class-Level + Method-Level Mapping

---

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @GetMapping("/{id}")
    public String getEmployee(
            @PathVariable Long id) {

        return "Employee";

    }

}
```

---

Final URL:

```http
/employees/101
```

---

# Internal Architecture

Path variables are resolved by:

```java
PathVariableMethodArgumentResolver
```

---

Spring Flow:

```text
DispatcherServlet
        │
        ▼
RequestMappingHandlerMapping
        │
        ▼
Handler Adapter
        │
        ▼
PathVariableMethodArgumentResolver
        │
        ▼
Controller Method
```

---

# Common Mistakes

## Mistake 1

Variable Name Mismatch

Wrong:

```java
@GetMapping("/employees/{id}")
public String get(
        @PathVariable Long employeeId) {

}
```

---

Spring cannot match:

```text
{id}
```

with:

```java
employeeId
```

---

Correct:

```java
@PathVariable("id")
Long employeeId
```

---

# Mistake 2

Wrong Data Type

URL:

```http
/employees/abc
```

---

Method:

```java
@PathVariable Long id
```

---

Exception:

```java
MethodArgumentTypeMismatchException
```

---

# Mistake 3

Missing Path Variable

Controller:

```java
@GetMapping("/employees/{id}")
```

---

Request:

```http
/employees
```

---

Result:

```http
404 Not Found
```

---

# Mistake 4

Using RequestParam Instead

Wrong:

```java
@GetMapping("/employees/{id}")
public String get(
        @RequestParam Long id) {

}
```

---

Should use:

```java
@PathVariable
```

---

# Common Interview Questions

## What is @PathVariable?

Used to extract values from URL path and bind them to method parameters.

---

## Which component resolves @PathVariable?

```java
PathVariableMethodArgumentResolver
```

---

## Can Spring automatically convert types?

Yes.

Examples:

```java
String → Long
String → Integer
String → UUID
```

---

## Difference Between @PathVariable and @RequestParam?

`@PathVariable`

```text
Resource Identification
```

`@RequestParam`

```text
Filtering/Search Parameters
```

---

## Can Multiple Path Variables Be Used?

Yes.

---

```java
@PathVariable Long deptId
@PathVariable Long empId
```

---

## Can Validation Be Applied?

Yes.

Using:

```java
@Positive
@Min
@Max
```

with:

```java
@Validated
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
@Validated
public class EmployeeController {

    @GetMapping("/{id}")
    public ResponseEntity<Employee>
    getEmployee(
            @Positive
            @PathVariable Long id) {

        Employee employee =
                service.findById(id);

        return ResponseEntity.ok(employee);

    }

}
```

---

Request:

```http
GET /api/employees/101
```

---

Response:

```json
{
  "id": 101,
  "name": "John",
  "salary": 90000
}
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
GET /employees/101
   │
   ▼
DispatcherServlet
   │
   ▼
RequestMappingHandlerMapping
   │
   ▼
Extract {id}
   │
   ▼
Convert To Long
   │
   ▼
@PathVariable Injection
   │
   ▼
Controller Method
   │
   ▼
Response
```

---

# Key Points To Remember

- `@PathVariable` extracts values from URL paths.
- Used for resource identification in REST APIs.
- Supports automatic type conversion.
- Can bind single or multiple path variables.
- Can be validated using Bean Validation annotations.
- Internally resolved by `PathVariableMethodArgumentResolver`.
- Frequently used with `@GetMapping`, `@PutMapping`, `@PatchMapping`, and `@DeleteMapping`.
- More RESTful than query parameters for identifying resources.
- One of the most commonly asked Spring MVC interview annotations.