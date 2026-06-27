
## Definition

`@Size` is a Bean Validation annotation used to validate the **length or size** of:

- Strings
- Collections
- Lists
- Sets
- Maps
- Arrays

It ensures that the size of the value falls within a specified range.

Package:

```java
import jakarta.validation.constraints.Size;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Size;
```

---

# What Problem Does @Size Solve?

Suppose your application requires:

```text
Username: Minimum 5 characters
Password: Minimum 8 characters
Skills List: At least 1 skill
```

Without validation:

```json
{
    "username":"ab",
    "password":"123",
    "skills":[]
}
```

could be accepted.

---

Using:

```java
@Size
```

ensures proper length restrictions.

---

# Basic Syntax

```java
@Size(
    min = 5,
    max = 20
)
private String username;
```

Meaning:

```text
Length must be between 5 and 20 characters.
```

---

# Supported Types

`@Size` supports:

```java
String
Collection
List
Set
Map
Array
```

Examples:

```java
@Size(min = 3, max = 50)
private String name;
```

```java
@Size(min = 1, max = 10)
private List<String> skills;
```

```java
@Size(min = 1)
private String[] tags;
```

---

# String Validation

```java
@Size(
    min = 3,
    max = 10
)
private String username;
```

---

Valid:

```java
"john"
```

Length:

```text
4
```

---

Valid:

```java
"developer"
```

Length:

```text
9
```

---

Invalid:

```java
"ab"
```

Length:

```text
2
```

---

Invalid:

```java
"verylongusername"
```

Length:

```text
16
```

(if max = 10)

---

# Collection Validation

```java
@Size(
    min = 1,
    max = 5
)
private List<String> skills;
```

---

Valid:

```java
["Java"]
```

---

Valid:

```java
["Java","Spring","Docker"]
```

---

Invalid:

```java
[]
```

---

Invalid:

```java
["A","B","C","D","E","F"]
```

---

# Set Validation

```java
@Size(
    min = 1,
    max = 3
)
private Set<String> roles;
```

---

Valid:

```java
["ADMIN"]
```

---

Invalid:

```java
[]
```

---

# Map Validation

```java
@Size(
    min = 1,
    max = 10
)
private Map<String,String> metadata;
```

---

Valid:

```java
{
   "env":"prod"
}
```

---

Invalid:

```java
{}
```

---

# Array Validation

```java
@Size(
    min = 1,
    max = 5
)
private String[] skills;
```

---

Valid:

```java
["Java"]
```

---

Invalid:

```java
[]
```

---

# Important Interview Question

Does `@Size` reject null?

Answer:

```text
NO
```

---

Example:

```java
@Size(min = 5)
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

Because:

```java
@Size
```

only validates size.

It does not check null.

---

If null should be rejected:

```java
@NotNull
@Size(min = 5)
private String username;
```

---

# min Attribute

```java
@Size(min = 5)
private String username;
```

---

Valid:

```java
"hello"
```

Length:

```text
5
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

# max Attribute

```java
@Size(max = 10)
private String username;
```

---

Valid:

```java
"developer"
```

Length:

```text
9
```

---

Invalid:

```java
"verylongusername"
```

Length:

```text
16
```

---

# min + max Together

```java
@Size(
    min = 5,
    max = 20
)
private String username;
```

---

Valid:

```java
"springboot"
```

---

Invalid:

```java
"abc"
```

---

Invalid:

```java
"veryveryverylongusername"
```

---

# Custom Message

Default:

```text
size must be between X and Y
```

---

Custom:

```java
@Size(
    min = 5,
    max = 20,
    message =
    "Username must be between 5 and 20 characters"
)
private String username;
```

---

Error:

```json
{
    "message":
    "Username must be between 5 and 20 characters"
}
```

---

# Using @Size with @NotBlank

Most common real-world combination.

---

```java
@NotBlank
@Size(
    min = 3,
    max = 50
)
private String name;
```

---

Validation:

```text
Must not be blank
AND
Length between 3 and 50
```

---

# Password Validation Example

```java
@NotBlank

@Size(
    min = 8,
    max = 20
)
private String password;
```

---

Valid:

```java
Password123
```

---

Invalid:

```java
123
```

---

# Username Validation Example

```java
@NotBlank

@Size(
    min = 5,
    max = 20
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
ab
```

---

# Collection Example

```java
@NotEmpty

@Size(
    min = 1,
    max = 10
)
private List<String> skills;
```

---

Valid:

```java
["Java","Spring"]
```

---

Invalid:

```java
[]
```

---

# Nested Validation Example

```java
@Valid

@Size(min = 1)
private List<Address> addresses;
```

---

Checks:

1. List size >= 1
2. Every Address is valid

---

# @Size vs @NotEmpty

Important Interview Question.

---

## @NotEmpty

Checks:

```java
value != null
AND
size > 0
```

---

Cannot specify:

```java
Minimum = 5
Maximum = 20
```

---

## @Size

Checks:

```java
min <= size <= max
```

---

Can specify exact ranges.

---

Comparison:

| Feature | @NotEmpty | @Size |
|----------|-----------|--------|
| Null Check | Yes | No |
| Empty Check | Yes | Yes (if min > 0) |
| Min Length | No | Yes |
| Max Length | No | Yes |

---

# @Size vs @Length

Common Interview Question.

---

## @Size

Standard Bean Validation.

```java
@Size
```

---

Portable across validation providers.

---

## @Length

Hibernate-specific.

```java
@Length
```

Package:

```java
org.hibernate.validator.constraints.Length
```

---

Recommended:

```java
@Size
```

---

# Internal Implementation

For Strings:

```java
SizeValidatorForCharSequence
```

---

For Collections:

```java
SizeValidatorForCollection
```

---

For Maps:

```java
SizeValidatorForMap
```

---

For Arrays:

```java
SizeValidatorForArray
```

---

# Internal Validation Logic

String:

```java
min <= value.length() <= max
```

---

Collection:

```java
min <= collection.size() <= max
```

---

Array:

```java
min <= array.length <= max
```

---

# Internal Architecture

Flow:

```text
Request
   │
   ▼
DTO Created
   │
   ▼
@Valid / @Validated
   │
   ▼
Hibernate Validator
   │
   ▼
SizeValidator
   │
   ▼
min <= size <= max ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Size to Reject Null

Wrong:

```java
@Size(min = 5)
private String name;
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
@Size(min = 5)
```

---

# Mistake 2

Using @Size on Numeric Types

Wrong:

```java
@Size(min = 1)
private Integer age;
```

---

Not supported.

---

Use:

```java
@Min
@Max
```

instead.

---

# Mistake 3

Using Only @Size for User Input

Wrong:

```java
@Size(min = 3)
private String username;
```

---

Allows:

```java
"   "
```

---

Better:

```java
@NotBlank
@Size(min = 3)
```

---

# Common Interview Questions

## What is @Size?

Validates length or size within a range.

---

## Does @Size reject null?

No.

---

## Which types support @Size?

```java
String
Collection
Set
List
Map
Array
```

---

## Can @Size be applied to Integer?

No.

Use:

```java
@Min
@Max
```

---

## Difference Between @Size and @NotEmpty?

`@NotEmpty` only checks:

```java
size > 0
```

while `@Size` supports ranges.

---

## Difference Between @Size and @Length?

`@Size` is standard Bean Validation.

`@Length` is Hibernate-specific.

---

# Real-World Production Example

```java
public class RegistrationRequest {

    @NotBlank

    @Size(
        min = 5,
        max = 20,
        message =
        "Username must be between 5 and 20 characters"
    )
    private String username;

    @NotBlank

    @Size(
        min = 8,
        max = 30,
        message =
        "Password must be between 8 and 30 characters"
    )
    private String password;

}
```

---

Request:

```json
{
    "username":"ab",
    "password":"123"
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
    "Username must be between 5 and 20 characters",

    "password":
    "Password must be between 8 and 30 characters"
}
```

---

# Key Points To Remember

- `@Size` validates length/size ranges.
- Supports Strings, Collections, Maps, Sets, and Arrays.
- Does NOT validate null values.
- Use `@NotNull`, `@NotEmpty`, or `@NotBlank` when null should be rejected.
- Supports `min` and `max` attributes.
- Commonly used for usernames, passwords, lists, arrays, and collections.
- Cannot be applied to numeric types.
- Uses specialized `SizeValidator` implementations internally.
- Part of the Bean Validation specification.
- Frequently asked Spring Boot and Hibernate Validator interview topic.