
## Definition

`@AssertTrue` is a Bean Validation annotation used to validate that a boolean value is:

```text
true
```

It tells the validation framework:

> The field must contain `true`.

Package:

```java
import jakarta.validation.constraints.AssertTrue;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.AssertTrue;
```

---

# Why Do We Need @AssertTrue?

Many business rules require explicit user consent or acceptance.

Examples:

```text
Terms & Conditions Accepted
Privacy Policy Accepted
Age Confirmation
Email Verification Confirmation
User Agreement Accepted
Declaration Accepted
```

---

Example:

A registration form requires:

```text
User must accept Terms & Conditions.
```

---

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

Solution:

```java
@AssertTrue
```

---

# Basic Syntax

```java
@AssertTrue
private boolean accepted;
```

---

Meaning:

```text
accepted must be true
```

---

# Basic Example

```java
public class RegistrationRequest {

    @AssertTrue
    private boolean termsAccepted;

}
```

---

Valid:

```json
{
    "termsAccepted": true
}
```

---

Invalid:

```json
{
    "termsAccepted": false
}
```

---

# Most Common Real-World Example

## Terms & Conditions

```java
@AssertTrue(
    message =
    "You must accept Terms and Conditions"
)
private boolean termsAccepted;
```

---

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

# Important Interview Question

## Does @AssertTrue reject null?

Depends on the type.

---

### Primitive boolean

```java
private boolean accepted;
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
private Boolean accepted;
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
@AssertTrue
private Boolean accepted;
```

---

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

Valid:

```java
null
```

---

Important:

```text
@AssertTrue does NOT reject null.
```

---

Use:

```java
@NotNull

@AssertTrue
private Boolean accepted;
```

if null should be rejected.

---

# Age Confirmation Example

Business Rule:

```text
User confirms age >= 18.
```

---

```java
@AssertTrue(
    message =
    "Age confirmation is required"
)
private Boolean ageConfirmed;
```

---

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

# Privacy Policy Acceptance Example

```java
@AssertTrue(
    message =
    "Privacy policy must be accepted"
)
private Boolean privacyAccepted;
```

---

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

# Declaration Acceptance Example

```java
@AssertTrue(
    message =
    "Declaration must be accepted"
)
private Boolean declarationAccepted;
```

---

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

# Custom Validation Message

Default:

```text
must be true
```

---

Custom:

```java
@AssertTrue(
    message =
    "Terms and Conditions must be accepted"
)
private Boolean accepted;
```

---

Response:

```json
{
    "accepted":
    "Terms and Conditions must be accepted"
}
```

---

# Using @AssertTrue with @Valid

DTO:

```java
public class RegistrationRequest {

    @AssertTrue(
        message =
        "Accept Terms and Conditions"
    )
    private Boolean termsAccepted;

}
```

---

Controller:

```java
@PostMapping("/register")
public String register(

        @Valid
        @RequestBody
        RegistrationRequest request) {

    return "success";
}
```

---

Request:

```json
{
    "termsAccepted": false
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

@AssertTrue(
    message =
    "Terms must be accepted"
)
private Boolean termsAccepted;
```

---

Ensures:

```text
Not Null
AND
Must Be True
```

---

# Method-Level Validation

Few developers know this.

`@AssertTrue` can also validate method return values.

---

Example:

```java
@AssertTrue
public boolean isValidAge() {

    return age >= 18;
}
```

---

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

Used in:

```text
Cross-field Validation
Business Rule Validation
Derived Validation
```

---

# Example: Cross Field Validation

```java
public class UserRequest {

    private Integer age;

    private Boolean parentConsent;

    @AssertTrue(
        message =
        "Minor must provide parent consent"
    )
    public boolean isValid() {

        if(age < 18) {
            return Boolean.TRUE.equals(parentConsent);
        }

        return true;
    }
}
```

---

Valid:

```java
age = 15
parentConsent = true
```

---

Invalid:

```java
age = 15
parentConsent = false
```

---

# @AssertTrue vs @NotNull

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

## @AssertTrue

Checks:

```text
Value Must Be True
```

---

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

Valid:

```java
null
```

(when using Boolean)

---

# @AssertTrue vs @AssertFalse

Interview Question.

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

# Internal Implementation

Hibernate Validator uses:

```java
AssertTrueValidator
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
       || value == true;
```

---

Important:

```java
null
```

passes validation.

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
AssertTrueValidator
   │
   ▼
value == true ?
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
@AssertTrue
private Boolean accepted;
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

@AssertTrue
```

---

# Mistake 2

Using Primitive When Null Is Needed

Wrong:

```java
private boolean accepted;
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

when three states are required.

---

# Mistake 3

Using @AssertTrue for Complex Rules

Wrong:

```java
@AssertTrue
private Boolean valid;
```

---

Instead:

```java
@AssertTrue
public boolean isBusinessRuleValid() {
    ...
}
```

---

# Common Interview Questions

## What is @AssertTrue?

Validates that a boolean value is true.

---

## Does @AssertTrue reject false?

Yes.

---

## Does @AssertTrue reject null?

No.

---

## How do you reject null?

```java
@NotNull

@AssertTrue
```

---

## Can @AssertTrue be used on methods?

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

## Difference Between @AssertTrue and @NotNull?

`@NotNull` checks existence.

`@AssertTrue` checks value equals true.

---

# Real-World Production Example

```java
public class RegistrationRequest {

    @NotNull(
        message =
        "Terms acceptance is required"
    )

    @AssertTrue(
        message =
        "You must accept Terms and Conditions"
    )
    private Boolean termsAccepted;

}
```

---

Request:

```json
{
    "termsAccepted": false
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "termsAccepted":
    "You must accept Terms and Conditions"
}
```

---

# Key Points To Remember

- `@AssertTrue` validates that a boolean value is `true`.
- Commonly used for terms acceptance, declarations, privacy policies, and confirmations.
- Works with `boolean` and `Boolean`.
- Does NOT reject `null` when used with `Boolean`.
- Combine with `@NotNull` if the field is mandatory.
- Can be applied to fields and methods.
- Useful for cross-field business validations.
- Internally uses `AssertTrueValidator`.
- Opposite annotation is `@AssertFalse`.
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

    @AssertTrue(
        message =
        "Terms and Conditions must be accepted"
    )
    private Boolean termsAccepted;
}
```

Question:

```text
What happens if the client sends false?
```

Answer:

```http
400 BAD REQUEST
```

because:

```java
@AssertTrue
```

requires the value to be:

```java
true
```

only.