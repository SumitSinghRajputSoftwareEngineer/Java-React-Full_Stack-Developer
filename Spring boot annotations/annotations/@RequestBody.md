
## Definition

`@RequestBody` is a Spring MVC annotation used to bind the **HTTP Request Body** directly to a Java object.

It tells Spring:

> Read the data present inside the request body, convert it into a Java object, and inject it into the controller method parameter.

Package:

```java
import org.springframework.web.bind.annotation.RequestBody;
```

---

# Why Do We Need @RequestBody?

Modern REST APIs exchange data using:

```json
JSON
```

Example Request:

```http
POST /employees
Content-Type: application/json
```

```json
{
  "id": 101,
  "name": "John",
  "salary": 90000
}
```

---

Without `@RequestBody`, Spring does not know that the JSON data should be converted into a Java object.

---

Using:

```java
@RequestBody
```

Spring automatically:

```text
JSON
   ↓
Java Object
```

---

# What Problem Does It Solve?

Suppose client sends:

```json
{
  "name": "John"
}
```

---

How does Spring convert this into:

```java
Employee employee
```

?

Answer:

```java
@RequestBody
```

---

# Basic Syntax

```java
@PostMapping("/employees")
public Employee saveEmployee(
        @RequestBody Employee employee) {

    return employee;

}
```

---

Request:

```json
{
  "id": 101,
  "name": "John"
}
```

---

Spring creates:

```java
Employee employee =
new Employee(101, "John");
```

---

# What Happens Internally?

Request:

```http
POST /employees
```

```json
{
  "id": 101,
  "name": "John"
}
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
RequestMappingHandlerAdapter
      │
      ▼
HttpMessageConverter
      │
      ▼
Jackson ObjectMapper
      │
      ▼
Employee Object
      │
      ▼
Controller Method
```

---

# Simple Example

## Employee Class

```java
public class Employee {

    private Long id;
    private String name;

    // getters setters
}
```

---

## Controller

```java
@RestController
public class EmployeeController {

    @PostMapping("/employees")
    public Employee save(
            @RequestBody Employee employee) {

        return employee;

    }

}
```

---

Request:

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

# How JSON Conversion Happens?

Spring Boot automatically configures:

```java
Jackson ObjectMapper
```

through:

```java
MappingJackson2HttpMessageConverter
```

---

Conversion:

```text
JSON
    ↓
ObjectMapper
    ↓
Employee Object
```

---

Example:

JSON

```json
{
  "id": 101,
  "name": "John"
}
```

---

Java Object

```java
Employee(
    id=101,
    name="John"
)
```

---

# HTTP Message Converters

Very Important Interview Topic.

`@RequestBody` relies on:

```java
HttpMessageConverter
```

---

Common Converters:

| Converter | Purpose |
|------------|----------|
| MappingJackson2HttpMessageConverter | JSON |
| GsonHttpMessageConverter | JSON using Gson |
| StringHttpMessageConverter | Text |
| ByteArrayHttpMessageConverter | Byte Arrays |
| FormHttpMessageConverter | Form Data |
| Jaxb2RootElementHttpMessageConverter | XML |

---

For JSON:

```java
MappingJackson2HttpMessageConverter
```

is most commonly used.

---

# Using @RequestBody with POST

Most common usage.

---

```java
@PostMapping("/employees")
public Employee create(
        @RequestBody Employee employee) {

    return employee;

}
```

---

Request:

```json
{
  "name": "John"
}
```

---

Used for:

```text
Create Resource
```

---

# Using @RequestBody with PUT

---

```java
@PutMapping("/employees/{id}")
public Employee update(
        @PathVariable Long id,
        @RequestBody Employee employee) {

    return employee;

}
```

---

Request:

```json
{
  "name": "John Updated"
}
```

---

Used for:

```text
Full Update
```

---

# Using @RequestBody with PATCH

---

```java
@PatchMapping("/employees/{id}")
public Employee patch(
        @PathVariable Long id,
        @RequestBody EmployeePatchRequest request) {

    return service.patch(id, request);

}
```

---

Request:

```json
{
  "salary": 95000
}
```

---

Used for:

```text
Partial Update
```

---

# Using DTOs with @RequestBody

Production Best Practice.

---

Instead of:

```java
@RequestBody Employee
```

use:

```java
@RequestBody EmployeeRequest
```

---

Example:

```java
public class EmployeeRequest {

    private String name;
    private Double salary;

}
```

---

Controller:

```java
@PostMapping("/employees")
public Employee create(
        @RequestBody EmployeeRequest request) {

}
```

---

Advantages:

```text
Better Security
Better Validation
Loose Coupling
Cleaner APIs
```

---

# Validation with @Valid

Very Important.

---

DTO:

```java
public class EmployeeRequest {

    @NotBlank
    private String name;

    @Positive
    private Double salary;

}
```

---

Controller:

```java
@PostMapping("/employees")
public Employee create(
        @Valid
        @RequestBody EmployeeRequest request) {

    return service.save(request);

}
```

---

Request:

```json
{
  "name": "",
  "salary": -1000
}
```

---

Spring throws:

```java
MethodArgumentNotValidException
```

---

# Nested Object Mapping

JSON:

```json
{
  "name": "John",
  "department": {
      "id": 10,
      "name": "IT"
  }
}
```

---

Java:

```java
public class Employee {

    private String name;
    private Department department;

}
```

---

Spring automatically converts nested objects.

---

# List Mapping

Request:

```json
[
  {
    "name":"John"
  },
  {
    "name":"David"
  }
]
```

---

Controller:

```java
@PostMapping("/employees")
public List<Employee> save(
        @RequestBody List<Employee> employees) {

    return employees;

}
```

---

Spring converts JSON array into:

```java
List<Employee>
```

---

# Map Mapping

Request:

```json
{
  "name":"John",
  "salary":90000
}
```

---

Controller:

```java
@PostMapping("/employees")
public Map<String,Object> save(
        @RequestBody Map<String,Object> map) {

    return map;

}
```

---

Result:

```java
{
   name=John,
   salary=90000
}
```

---

Useful for:

```text
Dynamic Structures
Flexible APIs
```

---

# Required Attribute

By default:

```java
@RequestBody(required = true)
```

---

Meaning:

Request body must be present.

---

Example:

```java
@PostMapping
public void save(
        @RequestBody Employee employee) {

}
```

---

Request:

```http
POST /employees
```

(no body)

---

Exception:

```java
HttpMessageNotReadableException
```

---

Optional Body:

```java
@RequestBody(required = false)
```

---

# @RequestBody vs @RequestParam

## @RequestBody

Reads:

```json
{
  "name":"John"
}
```

from body.

---

## @RequestParam

Reads:

```http
?name=John
```

from query string.

---

Comparison:

| Feature | @RequestBody | @RequestParam |
|----------|-------------|--------------|
| Source | Request Body | Query Parameter |
| Complex Objects | Yes | No |
| JSON Support | Yes | No |
| REST APIs | Common | Limited |

---

# @RequestBody vs @PathVariable

## PathVariable

```http
/employees/101
```

---

Extracts:

```java
101
```

from URL.

---

## RequestBody

```json
{
  "name":"John"
}
```

---

Extracts:

```java
Employee Object
```

from body.

---

# Consumes Attribute

Specify accepted content type.

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

# XML Support

If XML converter exists:

Request:

```xml
<Employee>
   <id>1</id>
   <name>John</name>
</Employee>
```

---

Controller:

```java
@PostMapping("/employees")
public Employee save(
        @RequestBody Employee employee) {

}
```

---

Spring can convert XML too.

---

# Internal Architecture

Most Important Interview Topic.

---

Components involved:

```java
DispatcherServlet
```

↓

```java
RequestMappingHandlerAdapter
```

↓

```java
RequestResponseBodyMethodProcessor
```

↓

```java
HttpMessageConverter
```

↓

```java
Jackson ObjectMapper
```

↓

```java
Controller Method
```

---

# Internal Classes

Very Important.

---

Resolver:

```java
RequestResponseBodyMethodProcessor
```

---

Converter:

```java
MappingJackson2HttpMessageConverter
```

---

Serializer/Deserializer:

```java
ObjectMapper
```

---

# Common Mistakes

## Mistake 1

Forgetting @RequestBody

Wrong:

```java
@PostMapping
public Employee save(
        Employee employee) {

}
```

---

JSON won't bind correctly.

---

Correct:

```java
@RequestBody Employee employee
```

---

# Mistake 2

Using Entity Directly

Bad:

```java
@RequestBody Employee
```

---

Better:

```java
@RequestBody EmployeeRequest
```

---

# Mistake 3

Ignoring Validation

Always use:

```java
@Valid
@RequestBody
```

for user input.

---

# Mistake 4

Sending Invalid JSON

Request:

```json
{
  "name":
}
```

---

Exception:

```java
HttpMessageNotReadableException
```

---

# Common Interview Questions

## What is @RequestBody?

Used to bind HTTP request body to a Java object.

---

## Which component processes @RequestBody?

```java
RequestResponseBodyMethodProcessor
```

---

## Which converter handles JSON?

```java
MappingJackson2HttpMessageConverter
```

---

## Which library converts JSON into Java Objects?

```java
Jackson ObjectMapper
```

---

## Can @RequestBody bind Lists?

Yes.

```java
List<Employee>
```

---

## Can @RequestBody bind Maps?

Yes.

```java
Map<String,Object>
```

---

## Can Validation be applied?

Yes.

Using:

```java
@Valid
@RequestBody
```

---

## Can XML be supported?

Yes.

If XML converters are configured.

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {

    @PostMapping
    public ResponseEntity<EmployeeResponse>
    createEmployee(

            @Valid
            @RequestBody EmployeeRequest request) {

        EmployeeResponse response =
                service.create(request);

        return ResponseEntity
                .status(201)
                .body(response);

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
  "name":"John",
  "salary":90000
}
```

---

Response:

```http
201 Created
```

```json
{
  "id":101,
  "name":"John",
  "salary":90000
}
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
HTTP Request
   │
   ▼
JSON Body
   │
   ▼
DispatcherServlet
   │
   ▼
RequestResponseBodyMethodProcessor
   │
   ▼
HttpMessageConverter
   │
   ▼
ObjectMapper
   │
   ▼
Java Object
   │
   ▼
Controller Method
```

---

# Key Points To Remember

- `@RequestBody` binds HTTP request body to Java objects.
- Primarily used in REST APIs.
- Supports JSON, XML, and other formats through `HttpMessageConverter`.
- JSON conversion is handled by Jackson's `ObjectMapper`.
- Works with POJOs, DTOs, Lists, Maps, and nested objects.
- Validation is commonly applied using `@Valid`.
- Internally processed by `RequestResponseBodyMethodProcessor`.
- Frequently used with `POST`, `PUT`, and `PATCH` requests.
- One of the most important annotations in Spring MVC and Spring Boot.