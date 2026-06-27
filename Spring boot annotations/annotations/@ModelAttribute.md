
## Definition

`@ModelAttribute` is a Spring MVC annotation used to:

1. Bind request parameters to a Java object.
2. Add data to the Model for view rendering.
3. Execute common model preparation methods before controller methods.

It tells Spring:

> Create an object, populate it using request parameters, and make it available to the controller and optionally to the view.

Package:

```java
import org.springframework.web.bind.annotation.ModelAttribute;
```

---

# Why is @ModelAttribute Important?

`@ModelAttribute` is one of the oldest and most important Spring MVC annotations.

It is heavily used in:

```text
Spring MVC
Server Side Rendering
Form Handling
Thymeleaf
JSP
Web Applications
```

---

Even though modern REST APIs mostly use:

```java
@RequestBody
```

you will still encounter `@ModelAttribute` in:

```text
Legacy Applications
Spring MVC Projects
Form-Based Applications
Interview Questions
```

---

# Three Major Uses of @ModelAttribute

## 1. Form Data Binding

```java
@ModelAttribute Employee employee
```

---

## 2. Add Data To Model

```java
@ModelAttribute("company")
public String company() {
    return "OpenAI";
}
```

---

## 3. Common Data Initialization

Executed before controller methods.

---

# Understanding the Problem

Suppose browser sends:

```http
POST /employees

name=John
salary=90000
```

---

Without Spring:

```java
String name =
request.getParameter("name");

String salary =
request.getParameter("salary");
```

---

Manually mapping fields becomes tedious.

---

Spring solves this using:

```java
@ModelAttribute
```

---

# What Happens Internally?

Request:

```http
POST /employees

name=John
salary=90000
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
Create Object
      │
      ▼
Populate Fields
      │
      ▼
Data Binding
      │
      ▼
Controller Method
```

---

# Use Case 1: Form Binding

## Employee DTO

```java
public class Employee {

    private String name;
    private Double salary;

    // getters setters
}
```

---

## Controller

```java
@PostMapping("/employees")
public String save(

        @ModelAttribute
        Employee employee) {

    return employee.getName();

}
```

---

Request:

```http
POST /employees

name=John
salary=90000
```

---

Spring creates:

```java
Employee employee =
new Employee();

employee.setName("John");
employee.setSalary(90000);
```

---

# Explicit Naming

---

```java
@PostMapping("/employees")
public String save(

        @ModelAttribute("employee")
        Employee employee) {

    return employee.getName();

}
```

---

Model Attribute Name:

```text
employee
```

---

# How Binding Works

Suppose DTO:

```java
public class Employee {

    private String name;
    private Double salary;

}
```

---

Request:

```http
name=John
salary=90000
```

---

Spring automatically maps:

```text
name
  ↓
employee.name

salary
  ↓
employee.salary
```

---

# Nested Object Binding

Very Important Interview Topic.

---

DTO:

```java
public class Employee {

    private Department department;

}
```

---

Department:

```java
public class Department {

    private String name;

}
```

---

Request:

```http
department.name=IT
```

---

Result:

```java
employee.getDepartment()
        .getName();

=> IT
```

---

Spring automatically creates nested objects.

---

# Collection Binding

DTO:

```java
public class Employee {

    private List<String> skills;

}
```

---

Request:

```http
skills[0]=Java
skills[1]=Spring
skills[2]=Docker
```

---

Result:

```java
[
  Java,
  Spring,
  Docker
]
```

---

# Validation with @Valid

Very Common.

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
public String save(

        @Valid
        @ModelAttribute
        EmployeeRequest request) {

    return "saved";

}
```

---

Invalid Request:

```http
name=
salary=-100
```

---

Exception:

```java
MethodArgumentNotValidException
```

or

```java
BindException
```

depending on scenario.

---

# Using BindingResult

Classic Spring MVC Pattern.

---

```java
@PostMapping("/employees")
public String save(

        @Valid
        @ModelAttribute
        EmployeeRequest request,

        BindingResult result) {

    if(result.hasErrors()) {
        return "employee-form";
    }

    return "success";
}
```

---

Very common in:

```text
JSP
Thymeleaf
Spring MVC
```

---

# Use Case 2: Add Data To Model

Controller:

```java
@Controller
public class EmployeeController {

    @ModelAttribute("company")
    public String company() {

        return "OpenAI";

    }

}
```

---

Before every request:

```java
company = "OpenAI"
```

is added to Model.

---

Available in view:

```html
${company}
```

---

# Use Case 3: Common Model Preparation

Very Important.

---

```java
@Controller
public class EmployeeController {

    @ModelAttribute("departments")
    public List<String> departments() {

        return List.of(
            "IT",
            "HR",
            "Finance"
        );

    }

}
```

---

Every request gets:

```java
departments
```

inside model.

---

Useful for:

```text
Dropdown Lists
Reference Data
Master Data
Menus
```

---

# Method-Level @ModelAttribute

Executed before request handler methods.

---

```java
@Controller
public class EmployeeController {

    @ModelAttribute
    public void initialize() {

        System.out.println(
            "Executed First"
        );

    }

    @GetMapping("/employees")
    public String employees() {

        return "employees";

    }

}
```

---

Execution Order:

```text
@ModelAttribute Method
       ↓
Controller Method
```

---

# Parameter-Level @ModelAttribute

Most common usage.

---

```java
@PostMapping("/employees")
public String save(

        @ModelAttribute
        Employee employee) {

}
```

---

Creates and populates object.

---

# @ModelAttribute vs @RequestBody

Most Important Interview Question.

---

## @ModelAttribute

Reads:

```text
Form Data
Query Parameters
URL Parameters
```

---

Example:

```http
name=John
salary=90000
```

---

## @RequestBody

Reads:

```json
{
  "name":"John",
  "salary":90000
}
```

---

Comparison:

| Feature | @ModelAttribute | @RequestBody |
|----------|---------------|-------------|
| Form Data | Yes | No |
| Query Params | Yes | No |
| JSON | No | Yes |
| Multipart Forms | Yes | No |
| Traditional MVC | Yes | No |
| REST APIs | Rare | Common |

---

# @ModelAttribute vs @RequestParam

---

## RequestParam

Single Value

```java
@RequestParam String name
```

---

## ModelAttribute

Entire Object

```java
@ModelAttribute Employee employee
```

---

Comparison:

| Feature | @RequestParam | @ModelAttribute |
|----------|--------------|----------------|
| Single Field | Yes | No |
| Object Binding | No | Yes |
| Complex DTO | No | Yes |

---

# @ModelAttribute vs @RequestPart

| Feature | @ModelAttribute | @RequestPart |
|----------|----------------|-------------|
| Form Data | Yes | Yes |
| File Upload | Limited | Yes |
| JSON Part Conversion | No | Yes |
| Multipart DTO | Limited | Yes |

---

# Internal Architecture

Very Important.

---

Spring uses:

```java
ModelAttributeMethodProcessor
```

to process:

```java
@ModelAttribute
```

---

Flow:

```text
DispatcherServlet
      │
      ▼
ModelAttributeMethodProcessor
      │
      ▼
Create Object
      │
      ▼
WebDataBinder
      │
      ▼
Populate Fields
      │
      ▼
Controller Method
```

---

# Internal Components

## Resolver

```java
ModelAttributeMethodProcessor
```

---

## Binder

```java
WebDataBinder
```

---

## Binding Result

```java
BindingResult
```

---

## Validation

```java
LocalValidatorFactoryBean
```

---

# Common Mistakes

## Mistake 1

Using @ModelAttribute for JSON

Wrong:

```java
@PostMapping
public void save(

        @ModelAttribute
        Employee employee) {

}
```

---

Request:

```json
{
  "name":"John"
}
```

---

Use:

```java
@RequestBody
```

instead.

---

# Mistake 2

Missing Getters and Setters

Binding requires:

```java
Setter Methods
```

---

Without setters:

```java
Fields Not Populated
```

---

# Mistake 3

Ignoring BindingResult

Wrong:

```java
@Valid
@ModelAttribute
Employee employee
```

---

Better:

```java
BindingResult result
```

---

# Mistake 4

Expecting Request Body Parsing

`@ModelAttribute` does NOT parse:

```json
JSON
```

---

Use:

```java
@RequestBody
```

for JSON.

---

# Common Interview Questions

## What is @ModelAttribute?

Used for data binding and model population.

---

## Which component processes @ModelAttribute?

```java
ModelAttributeMethodProcessor
```

---

## Can it bind complex objects?

Yes.

---

## Can it bind nested objects?

Yes.

---

## Can it bind collections?

Yes.

---

## Can Validation be applied?

Yes.

Using:

```java
@Valid
```

---

## Difference Between @ModelAttribute and @RequestBody?

`@ModelAttribute`

```text
Form Data
```

---

`@RequestBody`

```text
JSON/XML
```

---

## When are method-level @ModelAttribute methods executed?

Before controller request handler methods.

---

# Real-World Example

```java
@Controller
@RequestMapping("/employees")
public class EmployeeController {

    @ModelAttribute("departments")
    public List<String> departments() {

        return List.of(
                "IT",
                "HR",
                "Finance"
        );

    }

    @PostMapping
    public String save(

            @Valid
            @ModelAttribute
            EmployeeRequest request,

            BindingResult result) {

        if(result.hasErrors()) {
            return "employee-form";
        }

        return "success";

    }

}
```

---

# Internal Flow Diagram

```text
HTTP Request
      │
      ▼
DispatcherServlet
      │
      ▼
ModelAttributeMethodProcessor
      │
      ▼
Create DTO Object
      │
      ▼
WebDataBinder
      │
      ▼
Populate Fields
      │
      ▼
Validation
      │
      ▼
Controller Method
```

---

# Key Points To Remember

- `@ModelAttribute` binds request parameters to Java objects.
- Can also add data into the Spring Model.
- Supports nested object binding.
- Supports collection binding.
- Commonly used in traditional Spring MVC applications.
- Frequently used with Thymeleaf and JSP forms.
- Supports validation using `@Valid`.
- Internally processed by `ModelAttributeMethodProcessor`.
- Uses `WebDataBinder` for data binding.
- One of the most important Spring MVC interview annotations.
- Mostly replaced by `@RequestBody` in modern REST APIs but still widely used in MVC applications.