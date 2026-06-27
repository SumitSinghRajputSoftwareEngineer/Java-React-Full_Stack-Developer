
## Definition

`@NotNull` is a Bean Validation annotation used to ensure that a field, method parameter, or return value is **not null**.

It tells the validation framework:

> This value must not be `null`.

Package:

```java
import jakarta.validation.constraints.NotNull;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.NotNull;
```

---

# Why Do We Need @NotNull?

Suppose a user sends:

```json
{
    "name": null
}
```

Without validation:

```java
employeeService.save(name);
```

may result in:

```java
NullPointerException
```

or invalid data stored in the database.

---

Using:

```java
@NotNull
```

prevents this.

---

# Basic Example

```java
public class EmployeeRequest {

    @NotNull
    private String name;

}
```

---

Valid Request:

```json
{
    "name":"John"
}
```

---

Validation Result:

```text
PASS
```

---

Invalid Request:

```json
{
    "name":null
}
```

---

Validation Result:

```text
FAIL
```

---

# What Exactly Does @NotNull Validate?

`@NotNull` only checks:

```java
value != null
```

---

Internally:

```java
if(value == null) {
    validationFailed();
}
```

---

Nothing else is checked.

---

# Important Interview Question

Many developers confuse:

```java
@NotNull
@NotEmpty
@NotBlank
```

They are different.

---

# @NotNull Allows Empty Strings

DTO:

```java
public class EmployeeRequest {

    @NotNull
    private String name;

}
```

---

Valid:

```json
{
    "name":""
}
```

---

Why?

Because:

```java
""
```

is not null.

---

Validation passes.

---

# @NotNull Allows Blank Strings

Valid:

```json
{
    "name":"     "
}
```

---

Because:

```java
"     "
```

is still not null.

---

Validation passes.

---

# @NotNull Allows Empty Collections

```java
@NotNull
private List<String> skills;
```

---

Valid:

```java
skills = []
```

---

Why?

Because:

```java
[]
```

is not null.

---

Validation passes.

---

# Example with Integer

```java
public class EmployeeRequest {

    @NotNull
    private Integer age;

}
```

---

Valid:

```json
{
    "age":25
}
```

---

Invalid:

```json
{
    "age":null
}
```

---

# Example with Long

```java
@NotNull
private Long employeeId;
```

---

Valid:

```java
100L
```

---

Invalid:

```java
null
```

---

# Example with LocalDate

```java
@NotNull
private LocalDate joiningDate;
```

---

Valid:

```java
2026-01-01
```

---

Invalid:

```java
null
```

---

# Example with List

```java
@NotNull
private List<String> skills;
```

---

Valid:

```java
["Java","Spring"]
```

---

Also Valid:

```java
[]
```

---

Invalid:

```java
null
```

---

# Example with Map

```java
@NotNull
private Map<String,String> metadata;
```

---

Valid:

```java
{}
```

---

Invalid:

```java
null
```

---

# Using Custom Message

Default message:

```text
must not be null
```

---

Custom:

```java
@NotNull(
    message = "Employee ID is required"
)
private Long employeeId;
```

---

Error:

```json
{
    "message":
    "Employee ID is required"
}
```

---

# Using @NotNull with @Valid

DTO:

```java
public class EmployeeRequest {

    @NotNull
    private String name;

}
```

---

Controller:

```java
@PostMapping("/employees")
public String create(

        @Valid
        @RequestBody
        EmployeeRequest request) {

    return "saved";

}
```

---

Request:

```json
{
    "name":null
}
```

---

Result:

```http
400 BAD REQUEST
```

---

# Using @NotNull on Method Parameters

Requires:

```java
@Validated
```

---

Controller:

```java
@RestController

@Validated
public class EmployeeController {

    @GetMapping("/{id}")
    public String find(

            @NotNull
            @PathVariable
            Long id) {

        return "Employee";

    }

}
```

---

Valid:

```http
/employees/10
```

---

Invalid:

```java
null
```

(if parameter is nullable)

---

# Using @NotNull on Service Methods

---

```java
@Service

@Validated
public class EmployeeService {

    public void save(

            @NotNull
            String name) {

    }

}
```

---

Call:

```java
save(null);
```

---

Result:

```java
ConstraintViolationException
```

---

# Using @NotNull on Return Values

---

```java
@Service

@Validated
public class EmployeeService {

    @NotNull
    public Employee findEmployee() {

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

# Wrapper vs Primitive Types

Very Important Interview Question.

---

## Wrapper Type

```java
@NotNull
private Integer age;
```

Works.

---

Because:

```java
Integer
```

can be:

```java
null
```

---

## Primitive Type

```java
@NotNull
private int age;
```

Meaningless.

---

Why?

Because:

```java
int
```

can never be:

```java
null
```

---

Invalid usage:

```java
@NotNull
private int age;
```

---

Validation is useless.

---

# Common Real-World Example

DTO:

```java
public class EmployeeRequest {

    @NotNull(
        message = "Employee ID required"
    )
    private Long employeeId;

    @NotNull(
        message = "Department ID required"
    )
    private Long departmentId;

}
```

---

Request:

```json
{
    "employeeId":null,
    "departmentId":10
}
```

---

Validation Error:

```json
{
    "message":
    "Employee ID required"
}
```

---

# @NotNull vs @NotEmpty

---

## @NotNull

Checks:

```java
value != null
```

---

Allows:

```java
""
[]
{}
```

---

## @NotEmpty

Checks:

```java
value != null
AND
size > 0
```

---

Rejects:

```java
""
[]
{}
```

---

Comparison:

| Value | @NotNull | @NotEmpty |
|---------|-----------|------------|
| null | ❌ | ❌ |
| "" | ✅ | ❌ |
| [] | ✅ | ❌ |
| {} | ✅ | ❌ |

---

# @NotNull vs @NotBlank

---

## @NotNull

Allows:

```java
""
"   "
```

---

## @NotBlank

Rejects:

```java
null
""
"   "
```

---

Comparison:

| Value | @NotNull | @NotBlank |
|---------|-----------|-----------|
| null | ❌ | ❌ |
| "" | ✅ | ❌ |
| "   " | ✅ | ❌ |
| "John" | ✅ | ✅ |

---

# Internal Implementation

Validator:

```java
NotNullValidator
```

---

Logic:

```java
return value != null;
```

---

Extremely lightweight validation.

---

# Internal Architecture

Flow:

```text
Request
   │
   ▼
DTO Creation
   │
   ▼
@Valid / @Validated
   │
   ▼
Hibernate Validator
   │
   ▼
NotNullValidator
   │
   ▼
value != null ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Using @NotNull for Strings

Wrong:

```java
@NotNull
private String name;
```

---

Allows:

```java
""
```

---

Better:

```java
@NotBlank
```

---

# Mistake 2

Using @NotNull on Primitive Types

Wrong:

```java
@NotNull
private int age;
```

---

Primitive types cannot be null.

---

# Mistake 3

Expecting Collection Size Validation

Wrong:

```java
@NotNull
private List<String> skills;
```

---

Allows:

```java
[]
```

---

Use:

```java
@NotEmpty
```

instead.

---

# Common Interview Questions

## What is @NotNull?

Ensures a value is not null.

---

## Does @NotNull reject empty strings?

No.

```java
""
```

passes validation.

---

## Does @NotNull reject blank strings?

No.

```java
"   "
```

passes validation.

---

## Can @NotNull be applied to collections?

Yes.

But it only checks:

```java
collection != null
```

---

## Can @NotNull be applied to primitive types?

Technically yes, but it is useless.

---

## Which validator processes @NotNull?

```java
NotNullValidator
```

---

## Difference Between @NotNull and @NotBlank?

`@NotBlank` rejects:

```java
null
""
"   "
```

while `@NotNull` only rejects:

```java
null
```

---

# Real-World Example

```java
public class EmployeeRequest {

    @NotNull(
        message = "Employee ID required"
    )
    private Long employeeId;

    @NotNull(
        message = "Joining Date required"
    )
    private LocalDate joiningDate;

}
```

---

Request:

```json
{
    "employeeId":null,
    "joiningDate":"2026-01-01"
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "message":
    "Employee ID required"
}
```

---

# Key Points To Remember

- `@NotNull` validates only that a value is not `null`.
- It does NOT validate emptiness.
- It does NOT validate blank strings.
- Works with wrapper types, objects, collections, maps, and dates.
- Should not be used on primitive types.
- Uses `NotNullValidator` internally.
- Frequently combined with `@Valid` and `@Validated`.
- Commonly used for IDs, dates, references, and mandatory object fields.
- One of the most fundamental Bean Validation annotations.
- Very common Spring Boot and interview question.