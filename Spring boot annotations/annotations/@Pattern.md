
## Definition

`@Pattern` is a Bean Validation annotation used to validate a String against a **Regular Expression (Regex)**.

It tells the validation framework:

> The String value must match the specified regular expression.

Package:

```java
import jakarta.validation.constraints.Pattern;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Pattern;
```

---

# Why Do We Need @Pattern?

Many business requirements cannot be validated using built-in annotations.

Examples:

```text
Mobile Number Format
PAN Card Format
Aadhaar Number Format
Password Rules
Employee ID Format
Company Email Format
Username Format
```

---

Example:

```json
{
    "mobile":"123"
}
```

should be rejected.

---

Business Rule:

```text
Mobile number must contain exactly 10 digits.
```

---

Solution:

```java
@Pattern
```

---

# What is Regex?

Regex = Regular Expression

A pattern used to match text.

Example:

```java
^[0-9]{10}$
```

Meaning:

```text
Exactly 10 digits
```

---

Examples:

```java
9876543210
```

Valid

---

```java
12345
```

Invalid

---

# Basic Syntax

```java
@Pattern(
    regexp = "regex"
)
private String field;
```

---

Example:

```java
@Pattern(
    regexp = "^[0-9]{10}$"
)
private String mobile;
```

---

# Important Interview Question

Does @Pattern reject null?

Answer:

```text
NO
```

---

Example:

```java
@Pattern(
    regexp = "^[0-9]{10}$"
)
private String mobile;
```

---

Value:

```java
null
```

Validation:

```text
PASS
```

---

Why?

Bean Validation treats:

```java
null
```

as valid.

---

If value is mandatory:

```java
@NotBlank

@Pattern(
    regexp = "^[0-9]{10}$"
)
private String mobile;
```

---

# Mobile Number Validation

Business Rule:

```text
Exactly 10 digits
```

---

```java
@Pattern(
    regexp = "^[0-9]{10}$",
    message = "Mobile number must contain exactly 10 digits"
)
private String mobile;
```

---

Valid:

```java
9876543210
```

---

Invalid:

```java
12345
```

---

Invalid:

```java
abcd123456
```

---

# Username Validation

Business Rule:

```text
Only letters and numbers
Minimum 5 characters
Maximum 20 characters
```

---

Regex:

```java
^[A-Za-z0-9]{5,20}$
```

---

Annotation:

```java
@Pattern(
    regexp = "^[A-Za-z0-9]{5,20}$"
)
private String username;
```

---

Valid:

```java
john123
```

---

Invalid:

```java
john@
```

---

# Password Validation

Business Rule:

```text
At least one uppercase
At least one lowercase
At least one digit
At least one special character
Minimum 8 characters
```

---

Regex:

```java
^(?=.*[a-z])
(?=.*[A-Z])
(?=.*\\d)
(?=.*[@#$%^&+=!])
.{8,}$
```

---

Annotation:

```java
@Pattern(
    regexp =
    "^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)(?=.*[@#$%^&+=!]).{8,}$",

    message =
    "Password must contain uppercase, lowercase, digit and special character"
)
private String password;
```

---

Valid:

```java
Spring@123
```

---

Invalid:

```java
spring123
```

---

# Email Domain Restriction

Business Rule:

```text
Only Wipro Email IDs Allowed
```

---

```java
@Email

@Pattern(
    regexp =
    "^[A-Za-z0-9._%+-]+@wipro\\.com$",

    message =
    "Only Wipro email addresses are allowed"
)
private String email;
```

---

Valid:

```java
john@wipro.com
```

---

Invalid:

```java
john@gmail.com
```

---

# PAN Card Validation

Indian PAN Format:

```text
ABCDE1234F
```

---

Regex:

```java
^[A-Z]{5}[0-9]{4}[A-Z]{1}$
```

---

```java
@Pattern(
    regexp =
    "^[A-Z]{5}[0-9]{4}[A-Z]{1}$"
)
private String panNumber;
```

---

Valid:

```java
ABCDE1234F
```

---

Invalid:

```java
ABC12345F
```

---

# Aadhaar Validation

Business Rule:

```text
Exactly 12 digits
```

---

```java
@Pattern(
    regexp = "^[0-9]{12}$"
)
private String aadhaarNumber;
```

---

Valid:

```java
123456789012
```

---

Invalid:

```java
12345
```

---

# Employee ID Validation

Business Rule:

```text
EMP followed by 4 digits
```

Examples:

```text
EMP1234
```

---

Regex:

```java
^EMP[0-9]{4}$
```

---

```java
@Pattern(
    regexp = "^EMP[0-9]{4}$"
)
private String employeeId;
```

---

Valid:

```java
EMP1234
```

---

Invalid:

```java
1234EMP
```

---

# Common Regex Symbols

## ^

Beginning of String

```java
^ABC
```

Must start with:

```text
ABC
```

---

## $

End of String

```java
XYZ$
```

Must end with:

```text
XYZ
```

---

## []

Character Set

```java
[A-Z]
```

Uppercase letters

---

```java
[0-9]
```

Digits

---

## +

One or More

```java
[0-9]+
```

---

## *

Zero or More

```java
[0-9]*
```

---

## ?

Optional

```java
Mr?
```

---

## {n}

Exactly n Times

```java
[0-9]{10}
```

Exactly 10 digits

---

## {min,max}

Range

```java
[A-Z]{5,10}
```

---

# Custom Message

```java
@Pattern(
    regexp = "^[0-9]{10}$",

    message =
    "Mobile number must contain exactly 10 digits"
)
private String mobile;
```

---

Response:

```json
{
    "mobile":
    "Mobile number must contain exactly 10 digits"
}
```

---

# Using @Pattern with @Valid

DTO:

```java
public class UserRequest {

    @Pattern(
        regexp = "^[0-9]{10}$"
    )
    private String mobile;

}
```

---

Controller:

```java
@PostMapping("/users")
public String save(

        @Valid
        @RequestBody
        UserRequest request) {

    return "saved";
}
```

---

Request:

```json
{
    "mobile":"123"
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Using Multiple Validations

Very common.

---

```java
@NotBlank

@Pattern(
    regexp = "^[0-9]{10}$"
)
private String mobile;
```

---

Ensures:

```text
Not Null
Not Empty
Valid Mobile Format
```

---

# Internal Implementation

Hibernate Validator uses:

```java
PatternValidator
```

---

Package:

```java
org.hibernate.validator.internal.constraintvalidators
```

---

# Internal Validation Logic

Simplified:

```java
Pattern.compile(regex)
       .matcher(value)
       .matches()
```

---

# Internal Architecture

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
PatternValidator
   │
   ▼
Regex Match ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Pattern to Reject Null

Wrong:

```java
@Pattern(...)
private String mobile;
```

---

Value:

```java
null
```

passes validation.

---

Use:

```java
@NotBlank

@Pattern(...)
```

---

# Mistake 2

Using @Pattern on Integer

Wrong:

```java
@Pattern(...)
private Integer mobile;
```

---

Not supported.

---

Use:

```java
String
```

---

# Mistake 3

Writing Wrong Escape Characters

Wrong:

```java
"\d"
```

---

Correct:

```java
"\\d"
```

Because Java String itself uses:

```java
\
```

as escape character.

---

# @Pattern vs @Email

Interview Question.

---

## @Email

Built-in Email Validation

```java
@Email
```

---

## @Pattern

Custom Regex Validation

```java
@Pattern
```

---

Usually:

```java
@Email

@Pattern(...)
```

are used together.

---

# @Pattern vs @Size

Interview Question.

---

## @Size

Checks:

```text
Length Only
```

---

Example:

```java
@Size(min=10,max=10)
```

---

Allows:

```java
abcdefghij
```

---

## @Pattern

Checks:

```text
Exact Format
```

---

Example:

```java
@Pattern(
    regexp="^[0-9]{10}$"
)
```

---

Allows:

```java
9876543210
```

---

Rejects:

```java
abcdefghij
```

---

# Common Interview Questions

## What is @Pattern?

Validates a String using a regular expression.

---

## Does @Pattern reject null?

No.

---

## Which validator processes @Pattern?

```java
PatternValidator
```

---

## Can @Pattern be used on Integer?

No.

Only:

```java
String
CharSequence
```

---

## Difference Between @Pattern and @Size?

`@Size` validates length.

`@Pattern` validates structure and format.

---

## Why is \\d used instead of \d?

Because Java Strings require escaping:

```java
"\\d"
```

---

# Real-World Production Example

```java
public class RegistrationRequest {

    @NotBlank

    @Pattern(
        regexp =
        "^[A-Za-z0-9]{5,20}$",

        message =
        "Username must contain only letters and numbers"
    )
    private String username;

    @NotBlank

    @Pattern(
        regexp =
        "^[0-9]{10}$",

        message =
        "Invalid mobile number"
    )
    private String mobile;

}
```

---

Request:

```json
{
    "username":"john@123",
    "mobile":"123"
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "username":
    "Username must contain only letters and numbers",

    "mobile":
    "Invalid mobile number"
}
```

---

# Key Points To Remember

- `@Pattern` validates Strings using Regular Expressions (Regex).
- Supports only `CharSequence` types such as `String`.
- Does NOT reject null values.
- Usually combined with `@NotBlank`.
- Used for mobile numbers, PAN, Aadhaar, passwords, usernames, employee IDs, and company email validation.
- Uses Hibernate Validator's `PatternValidator`.
- Internally uses Java Regex APIs (`Pattern.compile()`).
- More powerful than `@Size` because it validates structure, not just length.
- Frequently asked Spring Boot and Hibernate Validator interview annotation.
- One of the most important validation annotations in enterprise applications.

---

# Most Asked Interview Regex Examples

## Mobile Number

```java
^[0-9]{10}$
```

---

## PAN Card

```java
^[A-Z]{5}[0-9]{4}[A-Z]$
```

---

## Aadhaar

```java
^[0-9]{12}$
```

---

## Employee ID

```java
^EMP[0-9]{4}$
```

---

## Username

```java
^[A-Za-z0-9]{5,20}$
```

---

## Strong Password

```java
^(?=.*[a-z])
(?=.*[A-Z])
(?=.*\\d)
(?=.*[@#$%^&+=!])
.{8,}$
```

These regex patterns are extremely common in Spring Boot interview questions and production applications.