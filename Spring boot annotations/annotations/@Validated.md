
## Definition

`@Validated` is a Spring-specific validation annotation used to trigger Bean Validation and support advanced validation features such as:

- Validation Groups
- Method Parameter Validation
- Return Value Validation
- Class-Level Validation

It tells Spring:

> Validate this object, method parameter, or method return value using Bean Validation rules.

Package:

```java
import org.springframework.validation.annotation.Validated;
```

---

# Why Do We Need @Validated?

Most developers know:

```java
@Valid
```

---

Example:

```java
@PostMapping
public void save(
        @Valid
        @RequestBody EmployeeRequest request) {

}
```

---

This works for simple validation.

However, `@Valid` has limitations:

❌ No Validation Groups

❌ No Method-Level Validation

❌ No Return Value Validation

❌ No Service Layer Validation

---

To solve these limitations, Spring provides:

```java
@Validated
```

---

# @Validated vs @Valid

Most Important Interview Question.

---

## @Valid

Provided by:

```java
jakarta.validation.Valid
```

---

Supports:

```text
Bean Validation
Nested Validation
```

---

Does NOT directly support:

```text
Validation Groups
Method Validation
```

---

## @Validated

Provided by Spring:

```java
org.springframework.validation.annotation.Validated
```

---

Supports:

```text
Bean Validation
Validation Groups
Method Validation
Return Value Validation
Class Validation
```

---

Comparison:

| Feature | @Valid | @Validated |
|----------|---------|------------|
| Bean Validation | Yes | Yes |
| Nested Validation | Yes | Yes |
| Validation Groups | No | Yes |
| Method Validation | No | Yes |
| Return Value Validation | No | Yes |
| Service Layer Validation | No | Yes |

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
public String save(

        @Validated
        @RequestBody
        EmployeeRequest request) {

    return "Success";

}
```

---

Works similarly to:

```java
@Valid
```

for simple cases.

---

# Validation Groups

This is the biggest reason to use `@Validated`.

---

# Problem Statement

Suppose:

```text
Create Employee API
Update Employee API
```

---

For Create:

```java
id should NOT be required
```

---

For Update:

```java
id MUST be required
```

---

How do we handle this?

---

Solution:

```java
Validation Groups
```

---

# Step 1: Create Marker Interfaces

```java
public interface CreateGroup {
}
```

---

```java
public interface UpdateGroup {
}
```

---

# Step 2: Apply Groups

DTO:

```java
public class EmployeeRequest {

    @NotNull(
        groups = UpdateGroup.class
    )
    private Long id;

    @NotBlank(
        groups = {
            CreateGroup.class,
            UpdateGroup.class
        }
    )
    private String name;

}
```

---

# Step 3: Use @Validated

Create API:

```java
@PostMapping

public String create(

        @Validated(
            CreateGroup.class
        )
        @RequestBody
        EmployeeRequest request) {

    return "created";

}
```

---

Update API:

```java
@PutMapping

public String update(

        @Validated(
            UpdateGroup.class
        )
        @RequestBody
        EmployeeRequest request) {

    return "updated";

}
```

---

Now:

### Create Request

```json
{
  "name":"John"
}
```

Valid.

---

### Update Request

```json
{
  "name":"John"
}
```

Invalid.

Because:

```java
id
```

is required for:

```java
UpdateGroup
```

---

# Method Parameter Validation

Very Important Interview Topic.

---

# Problem

Without validation:

```java
@GetMapping("/{id}")
public Employee getEmployee(
        @PathVariable Long id) {

}
```

---

User can send:

```http
/employees/-100
```

---

We want:

```java
id > 0
```

---

# Solution

Class:

```java
@RestController

@Validated
public class EmployeeController {

}
```

---

Method:

```java
@GetMapping("/{id}")
public Employee getEmployee(

        @Positive
        @PathVariable
        Long id) {

}
```

---

Valid:

```http
/employees/10
```

---

Invalid:

```http
/employees/-10
```

---

Throws:

```java
ConstraintViolationException
```

---

# Why Class-Level @Validated Is Required?

Without:

```java
@Validated
```

Spring will NOT perform method validation.

---

Wrong:

```java
@RestController
public class EmployeeController {

}
```

---

Correct:

```java
@RestController
@Validated
public class EmployeeController {

}
```

---

# Request Parameter Validation

---

```java
@RestController
@Validated
public class EmployeeController {

    @GetMapping

    public String find(

            @Min(1)
            @RequestParam
            Long page) {

        return "success";

    }

}
```

---

Request:

```http
?page=0
```

---

Result:

```java
ConstraintViolationException
```

---

# Return Value Validation

Rare but Important.

---

```java
@Validated
@Service
public class EmployeeService {

    @NotNull
    public Employee getEmployee() {

        return null;

    }

}
```

---

Validation Failure:

```java
ConstraintViolationException
```

---

Because:

```java
@NotNull
```

was violated.

---

# Service Layer Validation

Very common in Enterprise Applications.

---

```java
@Service

@Validated
public class EmployeeService {

    public void createEmployee(

            @NotBlank
            String name) {

    }

}
```

---

Call:

```java
service.createEmployee("");
```

---

Result:

```java
ConstraintViolationException
```

---

Even without Controller.

---

# Nested Validation

Just like:

```java
@Valid
```

---

DTO:

```java
public class EmployeeRequest {

    @Valid
    private Address address;

}
```

---

Address:

```java
public class Address {

    @NotBlank
    private String city;

}
```

---

Validation works recursively.

---

# Multiple Validation Groups

---

```java
@Validated({
        CreateGroup.class,
        CommonGroup.class
})
```

---

Multiple groups can be executed.

---

# Group Sequence

Advanced Feature.

---

```java
@GroupSequence({
        BasicGroup.class,
        AdvancedGroup.class
})
public interface ValidationOrder {
}
```

---

Validation order:

```text
BasicGroup
      ↓
AdvancedGroup
```

---

If first group fails:

```text
Second group skipped
```

---

# Validation Exceptions

## RequestBody Validation

Usually:

```java
MethodArgumentNotValidException
```

---

## Method Validation

Usually:

```java
ConstraintViolationException
```

---

Example:

```java
@Min
@Max
@Positive
```

on method parameters.

---

# Internal Architecture

Very Important Interview Topic.

---

Spring creates:

```java
MethodValidationPostProcessor
```

---

This creates:

```java
AOP Proxy
```

around bean.

---

Flow:

```text
Method Call
     │
     ▼
AOP Proxy
     │
     ▼
Validation Check
     │
     ▼
Constraint Validation
     │
 ┌──Pass───────┐
 ▼             ▼
Method      Exception
Executes    Thrown
```

---

# Internal Components

Main Processor:

```java
MethodValidationPostProcessor
```

---

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

Exception:

```java
ConstraintViolationException
```

---

# Method Validation Example

---

```java
@Service

@Validated
public class PaymentService {

    public void process(

            @Positive
            Double amount) {

    }

}
```

---

Valid:

```java
process(100.0);
```

---

Invalid:

```java
process(-100.0);
```

---

Throws:

```java
ConstraintViolationException
```

---

# Common Mistakes

## Mistake 1

Forgetting @Validated

Wrong:

```java
@RestController
public class EmployeeController {

}
```

---

Method validation won't work.

---

# Mistake 2

Using @Valid for Validation Groups

Wrong:

```java
@Valid
```

---

Validation groups require:

```java
@Validated(Group.class)
```

---

# Mistake 3

Missing Validation Dependency

Need:

```xml
spring-boot-starter-validation
```

---

# Mistake 4

Expecting Method Validation Without Proxy

Internal validation uses:

```java
AOP Proxy
```

---

Self-invocation won't trigger validation.

---

Example:

```java
this.process(-10);
```

may bypass validation.

---

# @Validated vs @Valid Summary

| Feature | @Valid | @Validated |
|----------|---------|------------|
| Bean Validation | Yes | Yes |
| Nested Validation | Yes | Yes |
| Validation Groups | No | Yes |
| Method Validation | No | Yes |
| Return Validation | No | Yes |
| Service Validation | No | Yes |
| Spring Specific | No | Yes |

---

# Common Interview Questions

## What is @Validated?

Spring's advanced validation annotation.

---

## Difference Between @Valid and @Validated?

`@Validated` supports:

```text
Validation Groups
Method Validation
```

---

## Which exception is thrown during method validation?

```java
ConstraintViolationException
```

---

## Which component enables method validation?

```java
MethodValidationPostProcessor
```

---

## Does @Validated use AOP?

Yes.

Spring creates a validation proxy.

---

## Can @Validated be used on Service Layer?

Yes.

Very common.

---

## Can @Validated validate method parameters?

Yes.

Example:

```java
@Positive
Long id
```

---

# Real-World Example

```java
@RestController

@RequestMapping("/employees")

@Validated
public class EmployeeController {

    @GetMapping("/{id}")
    public Employee findEmployee(

            @Positive
            @PathVariable
            Long id) {

        return service.findById(id);

    }

}
```

---

Request:

```http
GET /employees/-10
```

---

Response:

```http
400 BAD REQUEST
```

---

Exception:

```java
ConstraintViolationException
```

---

# Internal Flow Diagram

```text
Request
   │
   ▼
Controller / Service
   │
   ▼
@Validated Found
   │
   ▼
MethodValidationPostProcessor
   │
   ▼
AOP Proxy
   │
   ▼
Hibernate Validator
   │
   ▼
Pass / Fail
   │
   ▼
Method / Exception
```

---

# Key Points To Remember

- `@Validated` is Spring's advanced validation annotation.
- Supports everything `@Valid` supports plus Validation Groups.
- Required for method parameter validation.
- Required for service-layer validation.
- Uses `MethodValidationPostProcessor` internally.
- Works through Spring AOP proxies.
- Validation group support is the biggest difference from `@Valid`.
- Method validation failures usually throw `ConstraintViolationException`.
- Uses `Hibernate Validator` as the default validation implementation.
- Frequently asked in Spring Boot, Spring MVC, and Microservices interviews.