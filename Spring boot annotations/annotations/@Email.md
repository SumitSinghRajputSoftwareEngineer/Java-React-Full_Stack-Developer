
## Definition

`@Email` is a Bean Validation annotation used to validate whether a String contains a **valid email address format**.

It tells the validation framework:

> The value must match a valid email pattern.

Package:

```java
import jakarta.validation.constraints.Email;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Email;
```

---

# Why Do We Need @Email?

Suppose users register using:

```text
Email Address
```

Without validation:

```json
{
    "email":"abc"
}
```

or

```json
{
    "email":"sumit@"
}
```

or

```json
{
    "email":"gmail.com"
}
```

could be accepted.

---

These are invalid email formats.

---

Solution:

```java
@Email
```

---

# What Does @Email Validate?

It validates email structure.

Typical format:

```text
local-part@domain
```

Example:

```text
john@gmail.com
```

---

Structure:

```text
john      -> local part
@
gmail.com -> domain
```

---

# Basic Syntax

```java
@Email
private String email;
```

---

# Basic Example

```java
public class UserRequest {

    @Email
    private String email;

}
```

---

Valid:

```json
{
    "email":"john@gmail.com"
}
```

---

Valid:

```json
{
    "email":"test.user@yahoo.com"
}
```

---

Invalid:

```json
{
    "email":"john"
}
```

---

Invalid:

```json
{
    "email":"john@"
}
```

---

Invalid:

```json
{
    "email":"gmail.com"
}
```

---

# Important Interview Question

Does @Email reject null?

Answer:

```text
NO
```

---

Example:

```java
@Email
private String email;
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

Because Bean Validation treats:

```java
null
```

as valid.

---

If email is mandatory:

```java
@NotBlank

@Email
private String email;
```

---

# Common Valid Email Examples

```java
john@gmail.com
```

---

```java
sumit.rajput@yahoo.com
```

---

```java
test123@hotmail.com
```

---

```java
admin@company.org
```

---

```java
support@my-domain.com
```

---

```java
user_123@gmail.com
```

---

# Invalid Email Examples

```java
john
```

Missing:

```text
@
```

---

```java
john@
```

Missing:

```text
domain
```

---

```java
@gmail.com
```

Missing:

```text
local part
```

---

```java
gmail.com
```

Missing:

```text
@
```

---

```java
john@gmail
```

Missing:

```text
top-level domain
```

---

# Real Production Example

```java
public class RegistrationRequest {

    @NotBlank

    @Email
    private String email;

}
```

---

Valid:

```json
{
    "email":"user@gmail.com"
}
```

---

Invalid:

```json
{
    "email":"abc"
}
```

---

# Using Custom Message

Default:

```text
must be a well-formed email address
```

---

Custom:

```java
@Email(
    message = "Please enter a valid email address"
)
private String email;
```

---

Response:

```json
{
    "message":
    "Please enter a valid email address"
}
```

---

# Most Common Real-World Combination

```java
@NotBlank

@Email
private String email;
```

---

Why?

`@Email` alone allows:

```java
null
```

and

```java
""
```

---

Adding:

```java
@NotBlank
```

ensures:

```text
Not Null
Not Empty
Not Spaces
Valid Email Format
```

---

# Validation Flow Example

DTO:

```java
public class UserRequest {

    @NotBlank

    @Email
    private String email;

}
```

---

Controller:

```java
@PostMapping("/register")
public String register(

        @Valid
        @RequestBody
        UserRequest request) {

    return "Success";
}
```

---

Request:

```json
{
    "email":"abc"
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with @Size

Sometimes required.

```java
@NotBlank

@Email

@Size(max = 100)
private String email;
```

---

Validation:

```text
Valid Email
Maximum 100 Characters
```

---

# Using @Email with Regex

Many companies enforce domain restrictions.

Example:

```java
@Email

@Pattern(
    regexp = ".*@company\\.com$"
)
private String email;
```

---

Valid:

```java
john@company.com
```

---

Invalid:

```java
john@gmail.com
```

---

# Corporate Email Validation Example

```java
@NotBlank

@Email

@Pattern(
    regexp = "^[A-Za-z0-9._%+-]+@wipro\\.com$",
    message = "Only Wipro email allowed"
)
private String email;
```

---

Valid:

```java
employee@wipro.com
```

---

Invalid:

```java
employee@gmail.com
```

---

# Internal Implementation

Hibernate Validator uses:

```java
EmailValidator
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
emailPattern.matcher(value).matches()
```

---

Checks:

```text
Local Part Exists
@
Domain Exists
Valid Email Structure
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
EmailValidator
   │
   ▼
Regex Validation
   │
 ┌─Valid────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Using Only @Email

Wrong:

```java
@Email
private String email;
```

---

Allows:

```java
null
```

---

Use:

```java
@NotBlank

@Email
```

---

# Mistake 2

Using @NotBlank Only

Wrong:

```java
@NotBlank
private String email;
```

---

Allows:

```java
abcd
```

---

Because:

```text
NotBlank ≠ Email Validation
```

---

Use:

```java
@NotBlank

@Email
```

---

# Mistake 3

Using @Email on Non-String Fields

Wrong:

```java
@Email
private Integer email;
```

---

Not supported.

---

Use:

```java
String
```

---

# @Email vs @Pattern

Interview Question.

---

## @Email

```java
@Email
```

Checks:

```text
Standard Email Format
```

---

## @Pattern

```java
@Pattern
```

Checks:

```text
Custom Regex
```

---

Often combined:

```java
@Email

@Pattern(...)
```

---

# Common Interview Questions

## What is @Email?

Validates whether a String follows a valid email format.

---

## Does @Email reject null?

No.

---

## How do we reject null emails?

```java
@NotBlank

@Email
```

---

## Can @Email validate domain restrictions?

Not directly.

Use:

```java
@Pattern
```

along with:

```java
@Email
```

---

## Which validator processes @Email?

```java
EmailValidator
```

---

## Can @Email be used on Integer?

No.

Only String/CharSequence types.

---

# Real-World Production Example

```java
public class RegistrationRequest {

    @NotBlank(
        message = "Email is required"
    )

    @Email(
        message = "Invalid email format"
    )

    @Size(
        max = 100,
        message = "Email cannot exceed 100 characters"
    )
    private String email;

}
```

---

Request:

```json
{
    "email":"abc"
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "email":"Invalid email format"
}
```

---

# Key Points To Remember

- `@Email` validates email format.
- Works only on String/CharSequence types.
- Does NOT reject null values.
- Usually combined with `@NotBlank`.
- Uses Hibernate Validator's `EmailValidator`.
- Can be combined with `@Pattern` for company-specific email restrictions.
- Can be combined with `@Size` for length limits.
- Commonly used in Registration APIs, Login APIs, User Management APIs, and Profile APIs.
- Validation is format-based, not existence-based.
- One of the most commonly used validation annotations in Spring Boot applications.

---

# Extremely Important Interview Point

`@Email` only checks the **format**.

It does NOT verify:

```text
Mailbox Exists
Domain Exists
User Exists
Email Can Receive Messages
```

Example:

```java
fakeuser123456@gmail.com
```

passes:

```java
@Email
```

because the format is valid.

---

To verify actual email existence, you need:

```text
OTP Verification
Email Verification Link
SMTP Verification
Third-Party Email Verification APIs
```

outside Bean Validation.