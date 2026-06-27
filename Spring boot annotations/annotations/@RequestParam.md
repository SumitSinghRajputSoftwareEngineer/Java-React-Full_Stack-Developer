
## Definition

`@RequestParam` is a Spring MVC annotation used to extract values from the **query parameters**, **form parameters**, or **request parameters** and bind them to method parameters.

It tells Spring:

> Read a parameter from the HTTP request and inject it into the controller method.

Package:

```java
import org.springframework.web.bind.annotation.RequestParam;
```

---

# Why Do We Need @RequestParam?

Consider the URL:

```http
GET /employees?id=101
```

---

The value:

```text
101
```

exists in the query string.

---

To access it inside Java:

```java
@RequestParam
```

is used.

---

# What Problem Does It Solve?

Suppose:

```http
GET /employees?id=101
```

---

How does Spring know:

```text
id = 101
```

inside Java code?

---

Answer:

```java
@RequestParam
```

---

# Basic Syntax

```java
@GetMapping("/employees")
public String getEmployee(
        @RequestParam Long id) {

    return "Employee : " + id;

}
```

---

Request:

```http
GET /employees?id=101
```

---

Response:

```text
Employee : 101
```

---

# What Happens Internally?

Request:

```http
GET /employees?id=101
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
Handler Adapter
      │
      ▼
Extract Query Parameter
      │
      ▼
Convert To Java Type
      │
      ▼
Inject Into Method
```

---

# Query Parameters Explained

URL:

```http
/employees?id=101
```

---

Components:

```text
/employees      -> Path

?               -> Query String Starts

id=101          -> Query Parameter
```

---

Multiple Parameters:

```http
/employees?id=101&name=John
```

---

Parameters:

```text
id = 101
name = John
```

---

# Simple Example

```java
@RestController
public class EmployeeController {

    @GetMapping("/employees")
    public String employee(
            @RequestParam Long id) {

        return "Employee : " + id;

    }

}
```

---

Request:

```http
GET /employees?id=100
```

---

Response:

```text
Employee : 100
```

---

# Parameter Name Matching

Most common usage.

---

```java
@RequestParam Long id
```

---

Spring matches:

```text
id
```

with:

```http
?id=100
```

---

Automatically.

---

# Explicit Parameter Name

---

```java
@GetMapping("/employees")
public String employee(
        @RequestParam("id")
        Long employeeId) {

    return employeeId.toString();

}
```

---

Request:

```http
GET /employees?id=100
```

---

Spring maps:

```text
id → employeeId
```

---

# Multiple Request Parameters

Very common.

---

```java
@GetMapping("/employees")
public String employee(
        @RequestParam Long id,
        @RequestParam String name) {

    return id + " " + name;

}
```

---

Request:

```http
GET /employees?id=101&name=John
```

---

Response:

```text
101 John
```

---

# Optional Parameters

By default:

```java
@RequestParam
```

is mandatory.

---

Example:

```java
@GetMapping("/employees")
public String employee(
        @RequestParam String name) {

    return name;

}
```

---

Request:

```http
/employees
```

---

Exception:

```java
MissingServletRequestParameterException
```

---

# Making Parameters Optional

---

```java
@GetMapping("/employees")
public String employee(
        @RequestParam(
            required = false
        ) String name) {

    return name;

}
```

---

Valid Requests:

```http
/employees
```

or

```http
/employees?name=John
```

---

# Using Default Values

Very common.

---

```java
@GetMapping("/employees")
public String employee(
        @RequestParam(
            defaultValue = "Guest"
        ) String name) {

    return name;

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
Guest
```

---

Request:

```http
GET /employees?name=John
```

---

Response:

```text
John
```

---

# RequestParam with Numbers

---

```java
@GetMapping("/employees")
public String employee(
        @RequestParam Integer age) {

    return age.toString();

}
```

---

Request:

```http
GET /employees?age=25
```

---

Spring automatically converts:

```text
String → Integer
```

---

# Automatic Type Conversion

Spring supports:

---

## String

```java
@RequestParam String name
```

---

## Integer

```java
@RequestParam Integer age
```

---

## Long

```java
@RequestParam Long id
```

---

## Double

```java
@RequestParam Double salary
```

---

## Boolean

```java
@RequestParam Boolean active
```

---

## Enum

```java
@RequestParam Status status
```

---

Request:

```http
?status=ACTIVE
```

---

Spring converts automatically.

---

# RequestParam with List

Very common.

---

Request:

```http
/employees?ids=1,2,3
```

---

Controller:

```java
@GetMapping("/employees")
public List<Long> employee(
        @RequestParam List<Long> ids) {

    return ids;

}
```

---

Result:

```java
[1,2,3]
```

---

# RequestParam with Array

---

```java
@GetMapping("/employees")
public Long[] employee(
        @RequestParam Long[] ids) {

    return ids;

}
```

---

Request:

```http
?ids=1,2,3
```

---

Result:

```java
[1,2,3]
```

---

# RequestParam with Map

Useful for dynamic parameters.

---

```java
@GetMapping("/employees")
public Map<String,String> employee(
        @RequestParam Map<String,String> params) {

    return params;

}
```

---

Request:

```http
/employees?id=1&name=John
```

---

Response:

```json
{
  "id": "1",
  "name": "John"
}
```

---

# Form Submission Example

HTML:

```html
<form action="/employee">
    <input name="name">
</form>
```

---

Controller:

```java
@PostMapping("/employee")
public String save(
        @RequestParam String name) {

    return name;

}
```

---

Form Data:

```text
name=John
```

---

Spring injects:

```java
name = "John"
```

---

# RequestParam vs PathVariable

Very Important Interview Question.

---

## Path Variable

```http
/employees/101
```

---

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

---

Represents:

```text
Search / Filter Criteria
```

---

Code:

```java
@RequestParam Long id
```

---

Comparison:

| Feature | @PathVariable | @RequestParam |
|-----------|--------------|--------------|
| Location | URL Path | Query String |
| Resource ID | Yes | Usually No |
| Optional | Usually No | Yes |
| Filtering | No | Yes |

---

# Real-World REST Examples

## Pagination

---

Request:

```http
GET /employees?page=0&size=10
```

---

Controller:

```java
@GetMapping("/employees")
public Page<Employee> employees(
        @RequestParam int page,
        @RequestParam int size) {

}
```

---

# Sorting

---

Request:

```http
GET /employees?sort=name
```

---

Controller:

```java
@RequestParam String sort
```

---

# Search

---

Request:

```http
GET /employees?name=John
```

---

Controller:

```java
@RequestParam String name
```

---

# Validation with @RequestParam

---

```java
@GetMapping("/employees")
public String employee(
        @Positive
        @RequestParam Long id) {

    return id.toString();

}
```

---

Requires:

```java
@Validated
```

at controller level.

---

```java
@RestController
@Validated
public class EmployeeController {

}
```

---

Invalid Request:

```http
?id=-1
```

---

Exception:

```java
ConstraintViolationException
```

---

# Internal Architecture

Request parameters are resolved by:

```java
RequestParamMethodArgumentResolver
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
RequestParamMethodArgumentResolver
        │
        ▼
Controller Method
```

---

# Common Mistakes

## Mistake 1

Forgetting Required Parameter

---

Controller:

```java
@RequestParam String name
```

---

Request:

```http
/employees
```

---

Exception:

```java
MissingServletRequestParameterException
```

---

# Mistake 2

Wrong Type

Request:

```http
?id=abc
```

---

Method:

```java
@RequestParam Long id
```

---

Exception:

```java
MethodArgumentTypeMismatchException
```

---

# Mistake 3

Using RequestParam for Resource IDs

Less RESTful:

```http
/employees?id=101
```

---

Preferred:

```http
/employees/101
```

with:

```java
@PathVariable
```

---

# Mistake 4

Ignoring Default Values

---

Better:

```java
@RequestParam(
    defaultValue = "0"
)
```

than handling null manually.

---

# Common Interview Questions

## What is @RequestParam?

Used to extract request parameters from HTTP requests.

---

## Is @RequestParam mandatory?

Yes by default.

---

## How to make it optional?

```java
@RequestParam(required = false)
```

---

## How to provide default values?

```java
@RequestParam(
    defaultValue = "Guest"
)
```

---

## Which component resolves @RequestParam?

```java
RequestParamMethodArgumentResolver
```

---

## Difference Between @RequestParam and @PathVariable?

`@PathVariable`

```text
Resource Identification
```

---

`@RequestParam`

```text
Filtering / Search Criteria
```

---

## Can Spring convert types automatically?

Yes.

Examples:

```java
String → Long
String → Integer
String → Enum
String → UUID
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
@Validated
public class EmployeeController {

    @GetMapping
    public List<Employee> employees(
            @RequestParam(
                defaultValue = "0"
            ) int page,

            @RequestParam(
                defaultValue = "10"
            ) int size,

            @RequestParam(
                required = false
            ) String name) {

        return service.search(
                page,
                size,
                name);

    }

}
```

---

Request:

```http
GET /api/employees?page=0&size=20&name=John
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
GET /employees?id=101
   │
   ▼
DispatcherServlet
   │
   ▼
Extract Query Parameters
   │
   ▼
RequestParamMethodArgumentResolver
   │
   ▼
Type Conversion
   │
   ▼
@RequestParam Injection
   │
   ▼
Controller Method
```

---

# Key Points To Remember

- `@RequestParam` extracts query parameters, form parameters, and request parameters.
- Parameters are mandatory by default.
- Can be made optional using `required = false`.
- Supports default values using `defaultValue`.
- Supports automatic type conversion.
- Can bind Lists, Arrays, Maps, and Enums.
- Commonly used for pagination, filtering, and searching.
- Internally resolved by `RequestParamMethodArgumentResolver`.
- Frequently asked Spring MVC and Spring Boot interview annotation.
- Best used for filters and search criteria rather than resource identification.