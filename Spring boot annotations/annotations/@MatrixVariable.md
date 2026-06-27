
## Definition

`@MatrixVariable` is a Spring MVC annotation used to extract variables embedded within a URI path segment using **matrix URI notation**.

It tells Spring:

> Read values from matrix parameters inside the URL path and inject them into controller method parameters.

Package:

```java
import org.springframework.web.bind.annotation.MatrixVariable;
```

---

# What is a Matrix Variable?

Most developers know URLs like:

```http
/employees?id=101
```

This uses:

```java
@RequestParam
```

---

Matrix Variables use a different syntax:

```http
/employees;id=101
```

Notice:

```text
;
```

instead of:

```text
?
```

---

Multiple values:

```http
/employees;id=101;name=John
```

---

Here:

```text
id=101
name=John
```

are Matrix Variables.

---

# Why Does @MatrixVariable Exist?

Matrix Variables come from:

```text
RFC 3986 URI Specification
```

and were introduced to support:

```text
Hierarchical URI Parameters
```

inside path segments.

---

Example:

```http
/cars;color=red;year=2025
```

instead of:

```http
/cars?color=red&year=2025
```

---

# Important Reality Check

In modern REST APIs:

```java
@MatrixVariable
```

is rarely used.

---

Most APIs use:

```java
@PathVariable
@RequestParam
```

instead.

---

However:

```text
Interview Questions
Spring MVC Internals
Legacy Applications
```

may still use it.

---

# Why Is It Called Matrix Variable?

Because parameters are attached directly to a path segment.

Example:

```http
/employees;id=101;name=John
```

---

Structure:

```text
Path Segment
      │
      ▼
employees
      │
      ▼
id=101
name=John
```

---

# Basic Syntax

```java
@GetMapping("/employees/{filter}")
public String getEmployee(

        @MatrixVariable
        Long id) {

    return "Employee : " + id;

}
```

---

Request:

```http
/employees/data;id=101
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
/employees/data;id=101
```

---

Spring Flow:

```text
DispatcherServlet
      │
      ▼
RequestMappingHandlerAdapter
      │
      ▼
MatrixVariableMethodArgumentResolver
      │
      ▼
Extract Matrix Parameters
      │
      ▼
Type Conversion
      │
      ▼
Controller Method
```

---

# Enabling Matrix Variables

Very Important.

By default Spring Boot strips:

```text
;content
```

from URLs.

---

Therefore Matrix Variables won't work automatically.

---

You must enable them.

---

## Spring MVC Configuration

```java
@Configuration
public class WebConfig
        implements WebMvcConfigurer {

    @Override
    public void configurePathMatch(
            PathMatchConfigurer configurer) {

        UrlPathHelper helper =
                new UrlPathHelper();

        helper.setRemoveSemicolonContent(
                false);

        configurer.setUrlPathHelper(
                helper);
    }
}
```

---

Without this:

```java
@MatrixVariable
```

will not work.

---

# Simple Example

Controller:

```java
@RestController
public class EmployeeController {

    @GetMapping("/employees/{path}")
    public String employee(

            @MatrixVariable
            Long id) {

        return "Employee : " + id;

    }

}
```

---

Request:

```http
/employees/test;id=101
```

---

Response:

```text
Employee : 101
```

---

# Named Matrix Variable

---

```java
@GetMapping("/employees/{path}")
public String employee(

        @MatrixVariable("id")
        Long employeeId) {

    return employeeId.toString();

}
```

---

Request:

```http
/employees/test;id=101
```

---

Result:

```java
employeeId = 101
```

---

# Multiple Matrix Variables

---

Controller:

```java
@GetMapping("/employees/{path}")
public String employee(

        @MatrixVariable Long id,

        @MatrixVariable String name) {

    return id + " " + name;

}
```

---

Request:

```http
/employees/test;id=101;name=John
```

---

Response:

```text
101 John
```

---

# Matrix Variables with Multiple Path Segments

Example:

```http
/departments;deptId=10/employees;empId=101
```

---

Controller:

```java
@GetMapping(
"/departments/{dept}/employees/{emp}"
)
public String test(

        @MatrixVariable
        Long deptId,

        @MatrixVariable
        Long empId) {

    return deptId + " " + empId;

}
```

---

Response:

```text
10 101
```

---

# Ambiguous Matrix Variables

Consider:

```http
/departments;id=10/employees;id=101
```

---

Which id should Spring use?

---

To resolve ambiguity:

```java
@MatrixVariable(
    pathVar = "dept"
)
Long id
```

---

# Using pathVar Attribute

Controller:

```java
@GetMapping(
"/departments/{dept}/employees/{emp}"
)
public String test(

        @MatrixVariable(
            value = "id",
            pathVar = "dept"
        )
        Long deptId,

        @MatrixVariable(
            value = "id",
            pathVar = "emp"
        )
        Long empId) {

    return deptId + " " + empId;

}
```

---

Request:

```http
/departments;id=10/employees;id=101
```

---

Result:

```java
deptId = 10
empId = 101
```

---

# Optional Matrix Variables

By default:

```java
@MatrixVariable
```

is required.

---

Missing variable causes:

```java
MissingMatrixVariableException
```

---

Optional:

```java
@GetMapping("/employees/{path}")
public String employee(

        @MatrixVariable(
            value = "id",
            required = false
        )
        Long id) {

    return String.valueOf(id);

}
```

---

# Default Values

---

```java
@GetMapping("/employees/{path}")
public String employee(

        @MatrixVariable(
            value = "id",
            defaultValue = "0"
        )
        Long id) {

    return String.valueOf(id);

}
```

---

Request:

```http
/employees/test
```

---

Response:

```text
0
```

---

# Multiple Values

Matrix Variables support repeated values.

---

Request:

```http
/employees/test;role=ADMIN;role=USER
```

---

Controller:

```java
@GetMapping("/employees/{path}")
public List<String> employee(

        @MatrixVariable
        List<String> role) {

    return role;

}
```

---

Result:

```java
["ADMIN","USER"]
```

---

# Matrix Variable vs RequestParam

Very Important Interview Question.

---

## RequestParam

```http
/employees?id=101
```

---

Uses:

```text
?
&
```

---

Code:

```java
@RequestParam
```

---

## MatrixVariable

```http
/employees;id=101
```

---

Uses:

```text
;
```

---

Code:

```java
@MatrixVariable
```

---

Comparison:

| Feature | @RequestParam | @MatrixVariable |
|----------|--------------|----------------|
| Separator | ? | ; |
| Common Usage | Very Common | Rare |
| REST APIs | Standard | Rare |
| Browser Support | Excellent | Limited |

---

# MatrixVariable vs PathVariable

---

Path Variable:

```http
/employees/101
```

---

Code:

```java
@PathVariable
```

---

Matrix Variable:

```http
/employees;id=101
```

---

Code:

```java
@MatrixVariable
```

---

Comparison:

| Feature | @PathVariable | @MatrixVariable |
|----------|--------------|----------------|
| Part Of Path | Yes | Metadata On Path |
| Common | Very Common | Rare |
| REST Friendly | Yes | Less Common |

---

# Internal Architecture

Matrix variables are resolved by:

```java
MatrixVariableMethodArgumentResolver
```

---

Flow:

```text
DispatcherServlet
       │
       ▼
RequestMappingHandlerAdapter
       │
       ▼
MatrixVariableMethodArgumentResolver
       │
       ▼
Extract Matrix Variables
       │
       ▼
Type Conversion
       │
       ▼
Controller Method
```

---

# Internal Classes

Resolver:

```java
MatrixVariableMethodArgumentResolver
```

---

Parent:

```java
AbstractNamedValueMethodArgumentResolver
```

---

Used by:

```java
RequestMappingHandlerAdapter
```

---

# Common Mistakes

## Mistake 1

Not Enabling Matrix Variables

Without:

```java
setRemoveSemicolonContent(false)
```

---

Spring removes:

```text
;id=101
```

automatically.

---

Result:

```text
Variable Not Found
```

---

# Mistake 2

Confusing with RequestParam

Wrong:

```http
/employees?id=101
```

with:

```java
@MatrixVariable
```

---

Should be:

```http
/employees;id=101
```

---

# Mistake 3

Duplicate Variable Names

Request:

```http
/dept;id=10/emp;id=101
```

---

Need:

```java
pathVar
```

attribute.

---

# Common Interview Questions

## What is @MatrixVariable?

Used to extract matrix parameters from URI path segments.

---

## Which resolver processes @MatrixVariable?

```java
MatrixVariableMethodArgumentResolver
```

---

## Is it enabled by default?

No.

Requires:

```java
setRemoveSemicolonContent(false)
```

---

## Difference Between RequestParam and MatrixVariable?

RequestParam:

```http
?id=101
```

---

MatrixVariable:

```http
;id=101
```

---

## Can Multiple Values Be Bound?

Yes.

Example:

```java
List<String>
```

---

## Is It Commonly Used?

No.

Mostly:

```java
@PathVariable
@RequestParam
```

are preferred.

---

# Real-World Example

```java
@RestController
@RequestMapping("/products")
public class ProductController {

    @GetMapping("/{segment}")
    public String product(

            @MatrixVariable(
                value = "category",
                defaultValue = "general"
            )
            String category,

            @MatrixVariable(
                value = "brand",
                required = false
            )
            String brand) {

        return category + " " + brand;

    }

}
```

---

Request:

```http
/products/data;category=laptop;brand=lenovo
```

---

Response:

```text
laptop lenovo
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
/employees/test;id=101
   │
   ▼
DispatcherServlet
   │
   ▼
MatrixVariableMethodArgumentResolver
   │
   ▼
Extract id=101
   │
   ▼
Type Conversion
   │
   ▼
Controller Method
```

---

# Key Points To Remember

- `@MatrixVariable` extracts matrix parameters from URI path segments.
- Uses `;` instead of `?`.
- Not enabled by default in Spring Boot.
- Requires `setRemoveSemicolonContent(false)`.
- Supports automatic type conversion.
- Supports Lists and repeated values.
- Internally resolved by `MatrixVariableMethodArgumentResolver`.
- Rarely used in modern REST APIs.
- Frequently asked in Spring MVC interview discussions because it is less common.
- Most applications prefer `@RequestParam` and `@PathVariable`.