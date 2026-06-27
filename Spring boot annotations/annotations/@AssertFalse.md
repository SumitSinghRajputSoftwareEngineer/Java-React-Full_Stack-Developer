
## Definition

`@AssertFalse` is a Bean Validation annotation used to validate that a boolean value is:

```text
false
```

It tells the validation framework:

> The field must contain `false`.

Package:

```java
import jakarta.validation.constraints.AssertFalse;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.AssertFalse;
```

---

# Why Do We Need @AssertFalse?

Some business rules require a flag to explicitly remain disabled.

Examples:

```text
Admin Access Disabled
Account Locked Flag
Deleted Flag
Premium Membership Disabled
Feature Toggle Off
User Not Blacklisted
```

---

Example:

A registration request should not allow:

```text
isAdmin = true
```

during user signup.

---

Valid:

```java
false
```

---

Invalid:

```java
true
```

---

Solution:

```java
@AssertFalse
```

---

# Basic Syntax

```java
@AssertFalse
private boolean admin;
```

---

Meaning:

```text
admin must be false
```

---

# Basic Example

```java
public class UserRegistrationRequest {

    @AssertFalse
    private boolean admin;

}
```

---

Valid:

```json
{
    "admin": false
}
```

---

Invalid:

```json
{
    "admin": true
}
```

---

# Real-World Example

## Prevent Admin Privilege Escalation

```java
public class SignupRequest {

    @AssertFalse(
        message =
        "Admin access cannot be requested"
    )
    private Boolean admin;

}
```

---

Valid:

```java
false
```

---

Invalid:

```java
true
```

---

# Important Interview Question

## Does @AssertFalse reject null?

Depends on the type.

---

### Primitive boolean

```java
private boolean active;
```

Cannot be null.

---

Only:

```java
true
false
```

possible.

---

### Wrapper Boolean

```java
private Boolean active;
```

Can be:

```java
true
false
null
```

---

For:

```java
@AssertFalse
private Boolean active;
```

---

Valid:

```java
false
```

---

Invalid:

```java
true
```

---

Valid:

```java
null
```

---

Important:

```text
@AssertFalse does NOT reject null.
```

---

Use:

```java
@NotNull

@AssertFalse
private Boolean active;
```

if null should be rejected.

---

# Account Lock Example

Business Rule:

```text
New account should not be locked.
```

---

```java
@AssertFalse(
    message =
    "Account cannot be locked during registration"
)
private Boolean accountLocked;
```

---

Valid:

```java
false
```

---

Invalid:

```java
true
```

---

# Blacklist Example

```java
@AssertFalse(
    message =
    "User cannot be blacklisted"
)
private Boolean blacklisted;
```

---

Valid:

```java
false
```

---

Invalid:

```java
true
```

---

# Feature Toggle Example

```java
@AssertFalse(
    message =
    "Feature must remain disabled"
)
private Boolean featureEnabled;
```

---

Valid:

```java
false
```

---

Invalid:

```java
true
```

---

# Custom Validation Message

Default:

```text
must be false
```

---

Custom:

```java
@AssertFalse(
    message =
    "Admin access is not allowed"
)
private Boolean admin;
```

---

Response:

```json
{
    "admin":
    "Admin access is not allowed"
}
```

---

# Using @AssertFalse with @Valid

DTO:

```java
public class SignupRequest {

    @AssertFalse(
        message =
        "Admin access is prohibited"
    )
    private Boolean admin;

}
```

---

Controller:

```java
@PostMapping("/signup")
public String signup(

        @Valid
        @RequestBody
        SignupRequest request) {

    return "success";
}
```

---

Request:

```json
{
    "admin": true
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with @NotNull

Production-grade example:

```java
@NotNull

@AssertFalse(
    message =
    "Account must not be locked"
)
private Boolean accountLocked;
```

---

Ensures:

```text
Not Null
AND
Must Be False
```

---

# Method-Level Validation

Like `@AssertTrue`, it can validate method return values.

---

Example:

```java
@AssertFalse
public boolean hasErrors() {

    return errors.size() > 0;
}
```

---

Valid:

```java
false
```

(No errors)

---

Invalid:

```java
true
```

(Errors exist)

---

# Cross-Field Validation Example

```java
public class UserRequest {

    private String role;

    private Boolean admin;

    @AssertFalse(
        message =
        "Normal users cannot be admin"
    )
    public boolean isInvalidAdminRole() {

        return "USER".equals(role)
               && Boolean.TRUE.equals(admin);
    }
}
```

---

Valid:

```java
role=USER
admin=false
```

---

Invalid:

```java
role=USER
admin=true
```

---

# @AssertFalse vs @AssertTrue

Most Asked Interview Question.

---

## @AssertFalse

Valid:

```java
false
```

---

Invalid:

```java
true
```

---

## @AssertTrue

Valid:

```java
true
```

---

Invalid:

```java
false
```

---

Comparison:

| Value | @AssertTrue | @AssertFalse |
|---------|-------------|--------------|
| true | ✅ | ❌ |
| false | ❌ | ✅ |
| null | ✅* | ✅* |

\* when using `Boolean`

---

# @AssertFalse vs @NotNull

Interview Question.

---

## @NotNull

Checks:

```text
Value Exists
```

---

Valid:

```java
true
```

---

Valid:

```java
false
```

---

Invalid:

```java
null
```

---

## @AssertFalse

Checks:

```text
Value Must Be False
```

---

Valid:

```java
false
```

---

Invalid:

```java
true
```

---

Valid:

```java
null
```

(for `Boolean`)

---

# Internal Implementation

Hibernate Validator uses:

```java
AssertFalseValidator
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
return value == null
       || value == false;
```

---

Meaning:

```java
false -> PASS
null  -> PASS
true  -> FAIL
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
AssertFalseValidator
   │
   ▼
value == false ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting Null Rejection

Wrong:

```java
@AssertFalse
private Boolean active;
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
@NotNull

@AssertFalse
```

---

# Mistake 2

Using Primitive When Null Is Required

Wrong:

```java
private boolean active;
```

---

Cannot represent:

```java
null
```

---

Use:

```java
Boolean
```

when three-state logic is required.

---

# Mistake 3

Using for Complex Business Rules

Wrong:

```java
@AssertFalse
private Boolean invalid;
```

---

Instead:

```java
@AssertFalse
public boolean isBusinessRuleBroken() {
   ...
}
```

---

# Common Interview Questions

## What is @AssertFalse?

Validates that a boolean value is false.

---

## Does @AssertFalse reject true?

Yes.

---

## Does @AssertFalse reject null?

No.

---

## How do you reject null?

```java
@NotNull

@AssertFalse
```

---

## Can @AssertFalse be applied to methods?

Yes.

---

## Difference Between boolean and Boolean?

`boolean`

```java
true / false
```

---

`Boolean`

```java
true / false / null
```

---

## Difference Between @AssertFalse and @NotNull?

`@NotNull` checks existence.

`@AssertFalse` checks value equals false.

---

# Real-World Production Example

```java
public class UserRegistrationRequest {

    @NotNull(
        message =
        "Account lock status is required"
    )

    @AssertFalse(
        message =
        "Account cannot be locked during registration"
    )
    private Boolean accountLocked;

}
```

---

Request:

```json
{
    "accountLocked": true
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "accountLocked":
    "Account cannot be locked during registration"
}
```

---

# Key Points To Remember

- `@AssertFalse` validates that a boolean value is `false`.
- Commonly used for flags that must remain disabled.
- Works with `boolean` and `Boolean`.
- Does NOT reject `null` when used with `Boolean`.
- Combine with `@NotNull` if the value is mandatory.
- Can be applied to fields and methods.
- Useful for cross-field and business-rule validations.
- Internally uses `AssertFalseValidator`.
- Opposite annotation is `@AssertTrue`.
- Frequently asked Spring Boot and Hibernate Validator interview annotation.

---

# Quick Comparison

| Annotation | Valid Value |
|------------|------------|
| `@AssertTrue` | `true` |
| `@AssertFalse` | `false` |
| `@NotNull` | Any non-null value |

---

# Most Common Interview Scenario

```java
public class SignupRequest {

    @NotNull

    @AssertFalse(
        message =
        "Admin access is not allowed"
    )
    private Boolean admin;
}
```

Question:

```text
What happens if client sends admin=true?
```

Answer:

```http
400 BAD REQUEST
```

because:

```java
@AssertFalse
```

requires the value to be:

```java
false
```

only.