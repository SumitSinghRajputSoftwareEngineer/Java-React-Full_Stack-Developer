
## Definition

`@Valid` is a Bean Validation annotation used to trigger validation of an object before Spring processes it.

It tells Spring:

> Validate this object using all validation constraints defined on its fields.

Package:

```java
import jakarta.validation.Valid;
```

Prior to Spring Boot 3 / Jakarta EE:

```java
import javax.validation.Valid;
```

---

# What Problem Does @Valid Solve?

Suppose a client sends:

```json
{
  "name":"",
  "email":"abc",
  "age":-5
}
```

Without validation:

```java
@PostMapping("/users")
public User createUser(
        @RequestBody User user) {

    return userService.save(user);

}
```

Spring accepts:

```text
name = ""
email = abc
age = -5
```

which may corrupt data.

---

We need validation.

---

Solution:

```java
@Valid
```

---

# Bean Validation (JSR-303 / JSR-380)

`@Valid` itself does not contain validation rules.

Instead, it triggers validation annotations such as:

```java
@NotNull
@NotBlank
@NotEmpty
@Email
@Min
@Max
@Size
@Pattern
@Positive
@Negative
@Past
@Future
```

---

Example:

```java
public class UserRequest {

    @NotBlank
    private String name;

    @Email
    private String email;

    @Min(18)
    private int age;

}
```

---

# Basic Example

DTO:

```java
public class EmployeeRequest {

    @NotBlank
    private String name;

    @Email
    private String email;

}
```

---

Controller:

```java
@PostMapping("/employees")
public String createEmployee(

        @Valid
        @RequestBody EmployeeRequest request) {

    return "Success";

}
```

---

Valid Request:

```json
{
  "name":"John",
  "email":"john@gmail.com"
}
```

---

Response:

```http
200 OK
```

---

Invalid Request:

```json
{
  "name":"",
  "email":"wrong-email"
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# How @Valid Works Internally

Request:

```json
{
  "name":"",
  "email":"abc"
}
```

---

Flow:

```text
Request
   │
   ▼
JSON → DTO Conversion
   │
   ▼
@Valid Detected
   │
   ▼
Validator Invoked
   │
   ▼
Constraint Checks
   │
   ▼
Validation Errors?
   │
 ┌─Yes─────────────┐
 ▼                 ▼
Exception      Controller Method
Thrown          Executes
```

---

# Required Dependency

Spring Boot Starter Web does NOT automatically enable validation.

You need:

```xml
<dependency>
    <groupId>
        org.springframework.boot
    </groupId>

    <artifactId>
        spring-boot-starter-validation
    </artifactId>
</dependency>
```

---

Without it:

```java
@Valid
```

will not work properly.

---

# Common Validation Annotations Used with @Valid

## @NotNull

Field cannot be:

```java
null
```

---

Example:

```java
@NotNull
private String name;
```

---

Valid:

```java
"name":"John"
```

---

Invalid:

```java
"name":null
```

---

# @NotEmpty

Cannot be:

```java
null
""
[]
{}
```

---

Example:

```java
@NotEmpty
private String name;
```

---

# @NotBlank

Most commonly used for Strings.

Rejects:

```java
null
""
"   "
```

---

Example:

```java
@NotBlank
private String name;
```

---

# @Email

Validates email format.

---

```java
@Email
private String email;
```

---

Valid:

```java
john@gmail.com
```

---

Invalid:

```java
john
```

---

# @Size

String / Collection size validation.

---

```java
@Size(
    min = 3,
    max = 20
)
private String username;
```

---

# @Min

---

```java
@Min(18)
private int age;
```

---

Valid:

```java
18
19
20
```

---

Invalid:

```java
17
```

---

# @Max

---

```java
@Max(60)
private int age;
```

---

# @Positive

---

```java
@Positive
private Double salary;
```

---

Valid:

```java
100
5000
```

---

Invalid:

```java
0
-1
```

---

# @Negative

---

```java
@Negative
private Integer balance;
```

---

Valid:

```java
-10
```

---

# @Pattern

Regex validation.

---

```java
@Pattern(
        regexp = "[A-Z]{3}[0-9]{3}"
)
private String code;
```

---

Valid:

```java
ABC123
```

---

Invalid:

```java
abc123
```

---

# Using Custom Messages

Default messages are ugly.

---

```java
@NotBlank(
        message =
        "Name cannot be empty"
)
private String name;
```

---

Error:

```json
{
  "message":
  "Name cannot be empty"
}
```

---

# Validation with @RequestBody

Most common usage.

---

```java
@PostMapping
public Employee save(

        @Valid
        @RequestBody
        EmployeeRequest request) {

}
```

---

Spring validates JSON request.

---

# Validation with @ModelAttribute

Traditional MVC.

---

```java
@PostMapping
public String save(

        @Valid
        @ModelAttribute
        EmployeeRequest request) {

}
```

---

Used in:

```text
Thymeleaf
JSP
Forms
```

---

# Validation with @RequestPart

Multipart APIs.

---

```java
@PostMapping
public String upload(

        @Valid
        @RequestPart
        EmployeeRequest request) {

}
```

---

# Nested Validation

Very Important Interview Topic.

---

Employee DTO:

```java
public class EmployeeRequest {

    @Valid
    private Address address;

}
```

---

Address DTO:

```java
public class Address {

    @NotBlank
    private String city;

}
```

---

Request:

```json
{
  "address":{
      "city":""
  }
}
```

---

Validation fails.

---

Without:

```java
@Valid
```

on nested object,

Spring will NOT validate it.

---

# Collection Validation

---

```java
public class EmployeeRequest {

    @Valid
    private List<Address> addresses;

}
```

---

Each Address gets validated.

---

# Using BindingResult

MVC applications.

---

```java
@PostMapping
public String save(

        @Valid
        @ModelAttribute
        EmployeeRequest request,

        BindingResult result) {

    if(result.hasErrors()) {
        return "form";
    }

    return "success";
}
```

---

# Validation Groups

Advanced Feature.

---

Create Groups:

```java
public interface CreateGroup {}
public interface UpdateGroup {}
```

---

DTO:

```java
@NotNull(
    groups = UpdateGroup.class
)
private Long id;
```

---

Validation:

```java
@Validated(UpdateGroup.class)
```

---

Useful for:

```text
Create APIs
Update APIs
Different Validation Rules
```

---

# @Valid vs @Validated

Very Important Interview Question.

---

## @Valid

From:

```java
jakarta.validation
```

---

Supports:

```text
Bean Validation
Nested Validation
```

---

Does NOT support:

```text
Validation Groups
```

directly.

---

## @Validated

From Spring.

```java
org.springframework.validation.annotation.Validated
```

---

Supports:

```text
Validation Groups
Method Validation
```

---

Comparison:

| Feature | @Valid | @Validated |
|----------|---------|------------|
| Bean Validation | Yes | Yes |
| Nested Validation | Yes | Yes |
| Validation Groups | No | Yes |
| Method Validation | No | Yes |

---

# Validation Exceptions

## RequestBody Validation

Throws:

```java
MethodArgumentNotValidException
```

---

Example:

```java
@Valid
@RequestBody
```

---

## ModelAttribute Validation

Throws:

```java
BindException
```

---

Example:

```java
@Valid
@ModelAttribute
```

---

# Global Validation Handling

---

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(
        MethodArgumentNotValidException.class
    )
    public ResponseEntity<?> handle(
            MethodArgumentNotValidException ex) {

        return ResponseEntity
                .badRequest()
                .body(
                    ex.getMessage()
                );
    }
}
```

---

# Internal Architecture

Very Important.

---

Spring uses:

```java
LocalValidatorFactoryBean
```

which delegates to:

```java
Hibernate Validator
```

(the default implementation).

---

Flow:

```text
Request
   │
   ▼
Jackson Converts JSON
   │
   ▼
@Valid Detected
   │
   ▼
LocalValidatorFactoryBean
   │
   ▼
Hibernate Validator
   │
   ▼
Constraint Validation
   │
   ▼
Pass / Fail
```

---

# Internal Classes

Validator:

```java
LocalValidatorFactoryBean
```

---

Implementation:

```java
Hibernate Validator
```

---

Exceptions:

```java
MethodArgumentNotValidException
BindException
ConstraintViolationException
```

---

# Common Mistakes

## Mistake 1

Forgetting Validation Dependency

Missing:

```xml
spring-boot-starter-validation
```

---

Validation won't execute.

---

# Mistake 2

Forgetting @Valid

Wrong:

```java
@RequestBody EmployeeRequest request
```

---

Validation annotations ignored.

---

# Mistake 3

Missing @Valid on Nested Objects

Wrong:

```java
private Address address;
```

---

Correct:

```java
@Valid
private Address address;
```

---

# Mistake 4

Using @NotNull for String Fields

Better:

```java
@NotBlank
```

---

because:

```java
@NotNull
```

allows:

```java
""
```

---

# Common Interview Questions

## What is @Valid?

Triggers Bean Validation.

---

## Does @Valid perform validation itself?

No.

It triggers validation annotations.

---

## Which implementation performs validation?

```java
Hibernate Validator
```

---

## Which exception is thrown for RequestBody validation?

```java
MethodArgumentNotValidException
```

---

## Difference Between @Valid and @Validated?

`@Validated` supports groups and method validation.

---

## Can @Valid validate nested objects?

Yes.

Need:

```java
@Valid
```

on nested fields.

---

## Which bean handles validation in Spring?

```java
LocalValidatorFactoryBean
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @PostMapping
    public ResponseEntity<String> save(

            @Valid
            @RequestBody
            EmployeeRequest request) {

        return ResponseEntity.ok(
                "Employee Saved"
        );

    }

}
```

---

DTO:

```java
public class EmployeeRequest {

    @NotBlank(
        message = "Name required"
    )
    private String name;

    @Email(
        message = "Invalid email"
    )
    private String email;

    @Min(
        value = 18,
        message = "Age must be 18+"
    )
    private int age;

}
```

---

# Internal Flow Diagram

```text
HTTP Request
      │
      ▼
JSON → DTO
      │
      ▼
@Valid Found
      │
      ▼
LocalValidatorFactoryBean
      │
      ▼
Hibernate Validator
      │
      ▼
Constraint Checks
      │
      ▼
Pass / Fail
      │
      ▼
Controller / Exception
```

---

# Key Points To Remember

- `@Valid` triggers Bean Validation.
- It works together with annotations like `@NotBlank`, `@Email`, `@Min`, and `@Size`.
- It is commonly used with `@RequestBody`, `@ModelAttribute`, and `@RequestPart`.
- Supports nested object validation.
- Uses `LocalValidatorFactoryBean` internally.
- Hibernate Validator is the default implementation.
- Validation failures usually result in `MethodArgumentNotValidException`.
- Requires `spring-boot-starter-validation`.
- `@Validated` is an advanced alternative supporting validation groups.
- One of the most important annotations in Spring Boot REST API development and interviews.