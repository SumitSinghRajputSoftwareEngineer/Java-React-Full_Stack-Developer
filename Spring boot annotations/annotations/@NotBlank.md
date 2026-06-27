# @NotBlank

## Definition

`@NotBlank` is a Bean Validation annotation used to validate **String fields**.

It ensures that a String is:

1. Not `null`
2. Not empty (`""`)
3. Not only whitespace (`"   "`)

It tells the validation framework:

> This String must contain at least one non-whitespace character.

Package:

```java
import jakarta.validation.constraints.NotBlank;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.NotBlank;
```

---

# Why Do We Need @NotBlank?

Suppose a user submits:

```json
{
    "name":""
}
```

or

```json
{
    "name":"     "
}
```

---

Using:

```java
@NotNull
```

both values pass validation because:

```java
"" != null
"     " != null
```

---

Using:

```java
@NotEmpty
```

the blank-space value still passes:

```java
"     "
```

because length is greater than zero.

---

Business requirement:

```text
Name should contain actual text.
```

---

Solution:

```java
@NotBlank
```

---

# What Exactly Does @NotBlank Validate?

Internally:

```java
value != null
AND
value.trim().length() > 0
```

---

Equivalent logic:

```java
if(value == null) {
    fail;
}

if(value.trim().length() == 0) {
    fail;
}
```

---

# Supported Types

`@NotBlank` only supports:

```java
CharSequence
```

Examples:

```java
String
StringBuilder
StringBuffer
```

---

Most common:

```java
@NotBlank
private String name;
```

---

# Basic Example

```java
public class EmployeeRequest {

    @NotBlank
    private String name;

}
```

---

Valid:

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

Invalid:

```json
{
    "name":""
}
```

---

Validation Result:

```text
FAIL
```

---

Invalid:

```json
{
    "name":"     "
}
```

---

Validation Result:

```text
FAIL
```

---

Invalid:

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

# String Validation Examples

## Valid

```java
"John"
```

---

```java
"A"
```

---

```java
"Java Developer"
```

---

## Invalid

```java
null
```

---

```java
""
```

---

```java
" "
```

---

```java
"     "
```

---

```java
"\t"
```

---

```java
"\n"
```

---

# Why Does @NotBlank Reject Spaces?

Example:

```java
"     "
```

---

Internally:

```java
value.trim()
```

becomes:

```java
""
```

---

Length:

```java
0
```

---

Validation fails.

---

# Custom Message

Default message:

```text
must not be blank
```

---

Custom:

```java
@NotBlank(
    message = "Name is required"
)
private String name;
```

---

Error Response:

```json
{
    "message":
    "Name is required"
}
```

---

# Using @NotBlank with @Valid

DTO:

```java
public class EmployeeRequest {

    @NotBlank
    private String name;

}
```

---

Controller:

```java
@PostMapping("/employees")
public String save(

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
    "name":"   "
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Using Multiple Validations

Example:

```java
@NotBlank
@Size(
    min = 3,
    max = 50
)
private String name;
```

---

Valid:

```java
"John"
```

---

Invalid:

```java
" "
```

---

Invalid:

```java
"AB"
```

---

# Real-World Example

```java
public class EmployeeRequest {

    @NotBlank(
        message = "Employee name required"
    )
    private String name;

    @NotBlank(
        message = "Department required"
    )
    private String department;

}
```

---

Request:

```json
{
    "name":"   ",
    "department":""
}
```

---

Validation Errors:

```json
{
    "name":"Employee name required",
    "department":"Department required"
}
```

---

# @NotBlank vs @NotNull

Very Important Interview Question.

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
"   "
```

---

## @NotBlank

Checks:

```java
value != null
AND
trim().length() > 0
```

---

Rejects:

```java
null
""
"   "
```

---

Comparison:

| Value | @NotNull | @NotBlank |
|---------|----------|------------|
| null | ❌ | ❌ |
| "" | ✅ | ❌ |
| "   " | ✅ | ❌ |
| "John" | ✅ | ✅ |

---

# @NotBlank vs @NotEmpty

Most Frequently Asked Interview Question.

---

## @NotEmpty

Checks:

```java
value != null
AND
length > 0
```

---

Allows:

```java
"   "
```

---

## @NotBlank

Checks:

```java
value != null
AND
trim().length() > 0
```

---

Rejects:

```java
"   "
```

---

Comparison:

| Value | @NotEmpty | @NotBlank |
|---------|-----------|------------|
| null | ❌ | ❌ |
| "" | ❌ | ❌ |
| "   " | ✅ | ❌ |
| "John" | ✅ | ✅ |

---

# Complete Comparison

| Value | @NotNull | @NotEmpty | @NotBlank |
|---------|----------|-----------|-----------|
| null | ❌ | ❌ | ❌ |
| "" | ✅ | ❌ | ❌ |
| "   " | ✅ | ✅ | ❌ |
| "John" | ✅ | ✅ | ✅ |

---

# Internal Implementation

Hibernate Validator uses:

```java
NotBlankValidator
```

---

Validation Logic:

```java
return value != null &&
       value.toString()
            .trim()
            .length() > 0;
```

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
NotBlankValidator
   │
   ▼
trim().length() > 0 ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Combining with Other Validations

## With @Size

```java
@NotBlank
@Size(
    min = 5,
    max = 50
)
private String username;
```

---

## With @Pattern

```java
@NotBlank
@Pattern(
    regexp = "^[A-Za-z ]+$"
)
private String name;
```

---

## With @Email

```java
@NotBlank
@Email
private String email;
```

---

# Common Mistakes

## Mistake 1

Using @NotNull for User Input

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

and

```java
"   "
```

---

Better:

```java
@NotBlank
```

---

# Mistake 2

Using @NotBlank on Collections

Wrong:

```java
@NotBlank
private List<String> skills;
```

---

Compilation/Validation error.

---

Use:

```java
@NotEmpty
```

instead.

---

# Mistake 3

Using @NotBlank on Numbers

Wrong:

```java
@NotBlank
private Integer age;
```

---

Use:

```java
@NotNull
```

or

```java
@Min
```

---

# Common Interview Questions

## What is @NotBlank?

Validates that a String is not null, not empty, and not whitespace-only.

---

## Does @NotBlank reject null?

Yes.

---

## Does @NotBlank reject empty strings?

Yes.

---

## Does @NotBlank reject blank spaces?

Yes.

Example:

```java
"     "
```

fails validation.

---

## Can @NotBlank be used on Collections?

No.

Use:

```java
@NotEmpty
```

---

## Which validator processes @NotBlank?

```java
NotBlankValidator
```

---

## Difference Between @NotBlank and @NotEmpty?

`@NotBlank` trims whitespace before checking length.

---

## Which validation should be used for names?

Usually:

```java
@NotBlank
```

---

# Real-World Production Example

```java
public class RegistrationRequest {

    @NotBlank(
        message = "Username required"
    )
    private String username;

    @NotBlank(
        message = "Email required"
    )
    @Email
    private String email;

    @NotBlank(
        message = "Password required"
    )
    private String password;

}
```

---

Request:

```json
{
    "username":"   ",
    "email":"",
    "password":""
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "username":"Username required",
    "email":"Email required",
    "password":"Password required"
}
```

---

# Key Points To Remember

- `@NotBlank` is designed specifically for Strings (`CharSequence`).
- Ensures a value is not `null`, not empty, and not whitespace-only.
- Stronger than both `@NotNull` and `@NotEmpty` for text input.
- Uses `trim()` internally before checking length.
- Commonly used for names, usernames, passwords, emails, addresses, and user-entered text.
- Cannot be applied to collections, arrays, maps, or numeric types.
- Uses Hibernate Validator's `NotBlankValidator`.
- Frequently combined with `@Size`, `@Pattern`, and `@Email`.
- One of the most commonly used validation annotations in Spring Boot REST APIs.
- Extremely common Spring Boot interview question.