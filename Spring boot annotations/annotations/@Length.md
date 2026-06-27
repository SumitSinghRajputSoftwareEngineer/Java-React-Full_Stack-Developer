
## Definition

`@Length` is a **Hibernate Validator-specific** annotation used to validate the minimum and maximum length of a String.

It tells the validation framework:

> The String length must be within the specified range.

Package:

```java
import org.hibernate.validator.constraints.Length;
```

---

# Important Interview Point

`@Length` is:

```text
NOT a Bean Validation Standard Annotation
```

It is provided by:

```text
Hibernate Validator
```

---

Unlike:

```java
@NotNull
@NotBlank
@Size
@Email
```

which belong to the Jakarta Bean Validation specification.

---

# Why Do We Need @Length?

Many applications require text fields to have a specific length range.

Examples:

```text
Username
Password
First Name
Last Name
City Name
Address
Country Name
Product Code
```

---

Example:

Business Rule:

```text
Username length must be between 5 and 20 characters.
```

---

Valid:

```java
sumit123
```

---

Invalid:

```java
abc
```

---

Invalid:

```java
averyveryveryverylongusername
```

---

Solution:

```java
@Length
```

---

# Syntax

```java
@Length(
    min = 5,
    max = 20
)
private String username;
```

---

Meaning:

```text
5 <= length <= 20
```

---

# Basic Example

```java
public class UserRequest {

    @Length(
        min = 5,
        max = 20
    )
    private String username;

}
```

---

Valid:

```java
"sumit"
```

Length:

```text
5
```

---

Valid:

```java
"sumitrajput"
```

---

Invalid:

```java
"abc"
```

Length:

```text
3
```

---

Invalid:

```java
"abcdefghijklmnopqrstuvwxyz"
```

Length:

```text
26
```

---

# Important Interview Question

## Does @Length reject null?

Answer:

```text
NO
```

---

Example:

```java
@Length(
    min = 5,
    max = 20
)
private String username;
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

Most validation annotations treat:

```java
null
```

as valid.

---

If field is mandatory:

```java
@NotNull

@Length(
    min = 5,
    max = 20
)
private String username;
```

---

# Username Example

```java
@Length(
    min = 5,
    max = 20
)
private String username;
```

---

Valid:

```java
"sumit"
```

---

Valid:

```java
"johnsmith"
```

---

Invalid:

```java
"abc"
```

---

Invalid:

```java
"averyveryverylongusername"
```

---

# Password Example

```java
@Length(
    min = 8,
    max = 20
)
private String password;
```

---

Valid:

```java
"Password123"
```

---

Invalid:

```java
"pass"
```

---

Invalid:

```java
"averyveryveryveryverylongpassword"
```

---

# Product Code Example

```java
@Length(
    min = 6,
    max = 10
)
private String productCode;
```

---

Valid:

```java
"ABC123"
```

---

Invalid:

```java
"A1"
```

---

# Custom Message

```java
@Length(
    min = 5,
    max = 20,
    message =
    "Username must be between 5 and 20 characters"
)
private String username;
```

---

Response:

```json
{
    "username":
    "Username must be between 5 and 20 characters"
}
```

---

# Using @Length with @Valid

DTO:

```java
public class UserRequest {

    @Length(
        min = 5,
        max = 20
    )
    private String username;

}
```

---

Controller:

```java
@PostMapping("/users")
public String create(

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
    "username":"ab"
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with Other Validations

Very common.

---

```java
@NotBlank

@Length(
    min = 5,
    max = 20
)
private String username;
```

---

Ensures:

```text
Not Null
Not Empty
Not Blank
Length Between 5 and 20
```

---

# @Length vs @Size

Most Asked Interview Question.

---

## @Length

Package:

```java
org.hibernate.validator.constraints
```

---

Works only with:

```java
String
```

---

Example:

```java
@Length(
    min = 5,
    max = 20
)
```

---

## @Size

Package:

```java
jakarta.validation.constraints
```

---

Works with:

```java
String
Collection
Map
Array
```

---

Example:

```java
@Size(
    min = 5,
    max = 20
)
```

---

Comparison:

| Feature | @Length | @Size |
|----------|----------|--------|
| Standard Bean Validation | ❌ | ✅ |
| String | ✅ | ✅ |
| Collection | ❌ | ✅ |
| Array | ❌ | ✅ |
| Map | ❌ | ✅ |

---

# Which One Should Be Preferred?

Production recommendation:

```java
@Size
```

---

Reason:

```text
Standard Jakarta Validation Annotation
Portable Across Providers
```

---

Most companies prefer:

```java
@Size
```

instead of:

```java
@Length
```

---

# @Length vs @NotBlank

Interview Question.

---

## @Length

Checks:

```text
Length Range
```

---

Valid:

```java
"     "
```

Length:

```text
5
```

Passes validation.

---

## @NotBlank

Checks:

```text
Contains Non-Whitespace Characters
```

---

Invalid:

```java
"     "
```

---

Therefore:

```java
@Length
```

does NOT guarantee meaningful content.

---

Use:

```java
@NotBlank

@Length(
    min = 5,
    max = 20
)
```

---

# Internal Implementation

Hibernate Validator uses:

```java
LengthValidator
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
if(value == null)
    return true;

return value.length() >= min
    && value.length() <= max;
```

---

Equivalent:

```java
min <= value.length() <= max
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
LengthValidator
   │
   ▼
Check String Length
   │
 ┌─Valid────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Length to Reject Null

Wrong:

```java
@Length(
    min = 5,
    max = 20
)
private String username;
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
```

or

```java
@NotBlank
```

---

# Mistake 2

Using @Length for Collections

Wrong:

```java
@Length(min=1,max=10)
private List<String> names;
```

---

Use:

```java
@Size
```

---

# Mistake 3

Thinking @Length Rejects Spaces

Wrong:

```java
"     "
```

Length:

```text
5
```

passes validation.

---

Use:

```java
@NotBlank
```

along with:

```java
@Length
```

---

# Common Interview Questions

## What is @Length?

Validates String length.

---

## Is @Length a standard Bean Validation annotation?

No.

It is Hibernate-specific.

---

## Does @Length reject null?

No.

---

## Difference Between @Length and @Size?

`@Length`

```text
String Only
Hibernate Specific
```

---

`@Size`

```text
String, Collection, Array, Map
Standard Annotation
```

---

## Which annotation is preferred?

```java
@Size
```

in most enterprise applications.

---

## Does @Length reject blank spaces?

No.

Use:

```java
@NotBlank
```

---

# Real-World Production Example

```java
public class UserRegistrationRequest {

    @NotBlank(
        message =
        "Username is required"
    )

    @Length(
        min = 5,
        max = 20,
        message =
        "Username must be between 5 and 20 characters"
    )
    private String username;

}
```

---

Request:

```json
{
    "username":"ab"
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
   "Username must be between 5 and 20 characters"
}
```

---

# Key Points To Remember

- `@Length` validates String length.
- Hibernate Validator specific annotation.
- Not part of Jakarta Bean Validation specification.
- Supports `min` and `max` length.
- Does NOT reject `null`.
- Does NOT reject blank spaces.
- Works only on Strings.
- Prefer `@Size` in enterprise projects.
- Commonly combined with `@NotBlank`.
- Internally uses `LengthValidator`.
- Frequently asked Spring Boot and Hibernate Validator interview annotation.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Length` | String Length Validation |
| `@Size` | Length/Size Validation |
| `@NotBlank` | Reject Blank Text |
| `@NotEmpty` | Reject Empty Values |
| `@NotNull` | Reject Null Values |

---

# Interview Shortcut

### Use `@Length`

```java
@Length(min = 5, max = 20)
private String username;
```

When:

```text
You only want String length validation.
```

### Prefer `@Size`

```java
@Size(min = 5, max = 20)
private String username;
```

When:

```text
You want standard Jakarta Bean Validation.
```

Most enterprise Spring Boot projects prefer `@Size` over `@Length`.