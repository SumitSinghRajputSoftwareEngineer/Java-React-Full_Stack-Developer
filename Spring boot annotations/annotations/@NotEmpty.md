
## Definition

`@NotEmpty` is a Bean Validation annotation used to ensure that a value is:

1. Not `null`
2. Not empty

It tells the validation framework:

> This value must contain at least one element or character.

Package:

```java
import jakarta.validation.constraints.NotEmpty;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.NotEmpty;
```

---

# Why Do We Need @NotEmpty?

Suppose a user submits:

```json
{
    "skills":[]
}
```

or

```json
{
    "name":""
}
```

---

Using:

```java
@NotNull
```

would pass validation because:

```java
"" != null
[] != null
```

---

But business rules may require actual content.

---

Solution:

```java
@NotEmpty
```

---

# What Exactly Does @NotEmpty Validate?

Internally:

```java
value != null
AND
value.size() > 0
```

or

```java
value.length() > 0
```

depending on type.

---

# Supported Types

`@NotEmpty` supports:

```java
String
Collection
List
Set
Map
Array
```

---

Examples:

```java
@NotEmpty
private String name;
```

```java
@NotEmpty
private List<String> skills;
```

```java
@NotEmpty
private Set<String> permissions;
```

```java
@NotEmpty
private Map<String,String> metadata;
```

```java
@NotEmpty
private String[] tags;
```

---

# Basic Example

```java
public class EmployeeRequest {

    @NotEmpty
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
    "name":null
}
```

---

Validation Result:

```text
FAIL
```

---

# String Validation

## Valid

```java
"John"
```

---

```java
"A"
```

---

## Invalid

```java
""
```

---

```java
null
```

---

# Important Interview Question

`@NotEmpty` does NOT trim spaces.

---

Valid:

```java
"     "
```

---

Why?

Length is:

```java
5
```

---

Therefore validation passes.

---

If spaces should be rejected:

```java
@NotBlank
```

must be used.

---

# Collection Validation

Example:

```java
@NotEmpty
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
["Java","Spring"]
```

---

Invalid:

```java
[]
```

---

Invalid:

```java
null
```

---

# Set Validation

```java
@NotEmpty
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
@NotEmpty
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

Invalid:

```java
null
```

---

# Array Validation

```java
@NotEmpty
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

Invalid:

```java
null
```

---

# Custom Message

Default message:

```text
must not be empty
```

---

Custom:

```java
@NotEmpty(
    message = "Skills cannot be empty"
)
private List<String> skills;
```

---

Error:

```json
{
    "message":
    "Skills cannot be empty"
}
```

---

# Using @NotEmpty with @Valid

DTO:

```java
public class EmployeeRequest {

    @NotEmpty
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
    "name":""
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Nested Validation Example

```java
public class EmployeeRequest {

    @Valid

    @NotEmpty
    private List<Address> addresses;

}
```

---

Valid:

```json
{
    "addresses":[
       {
         "city":"Delhi"
       }
    ]
}
```

---

Invalid:

```json
{
    "addresses":[]
}
```

---

# Difference Between Null and Empty

Very Important.

---

## Null

```java
String name = null;
```

Means:

```text
Object does not exist
```

---

## Empty

```java
String name = "";
```

Means:

```text
Object exists
But contains no value
```

---

`@NotEmpty` rejects both.

---

# @NotEmpty vs @NotNull

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
|---------|----------|------------|
| null | ❌ | ❌ |
| "" | ✅ | ❌ |
| [] | ✅ | ❌ |
| {} | ✅ | ❌ |
| "John" | ✅ | ✅ |

---

# @NotEmpty vs @NotBlank

Very Important Interview Question.

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

because length is greater than 0.

---

## @NotBlank

Checks:

```java
trim(value).length > 0
```

---

Rejects:

```java
""
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

# Internal Implementation

Hibernate Validator provides:

```java
NotEmptyValidatorForCharSequence
```

for Strings.

---

For Collections:

```java
NotEmptyValidatorForCollection
```

---

For Maps:

```java
NotEmptyValidatorForMap
```

---

For Arrays:

```java
NotEmptyValidatorForArray
```

---

# Internal Validation Logic

String:

```java
value != null &&
value.length() > 0
```

---

Collection:

```java
collection != null &&
collection.size() > 0
```

---

Map:

```java
map != null &&
map.size() > 0
```

---

Array:

```java
array != null &&
array.length > 0
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
NotEmptyValidator
   │
   ▼
size > 0 ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Using @NotEmpty for Spaces

Wrong assumption:

```java
@NotEmpty
private String name;
```

rejects:

```java
"   "
```

---

It does NOT.

---

Use:

```java
@NotBlank
```

---

# Mistake 2

Using @NotNull Instead

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

---

# Mistake 3

Expecting Element Validation

Wrong:

```java
@NotEmpty
private List<Address> addresses;
```

---

Only validates list size.

---

To validate elements:

```java
@Valid
@NotEmpty
private List<Address> addresses;
```

---

# Common Interview Questions

## What is @NotEmpty?

Ensures a value is not null and not empty.

---

## Does @NotEmpty reject null?

Yes.

---

## Does @NotEmpty reject empty strings?

Yes.

---

## Does @NotEmpty reject empty collections?

Yes.

---

## Does @NotEmpty reject blank strings?

No.

Example:

```java
"   "
```

passes validation.

---

## Which validator processes @NotEmpty?

Examples:

```java
NotEmptyValidatorForCollection
NotEmptyValidatorForCharSequence
NotEmptyValidatorForMap
NotEmptyValidatorForArray
```

---

## Difference Between @NotNull and @NotEmpty?

`@NotEmpty` additionally checks size/length > 0.

---

## Difference Between @NotEmpty and @NotBlank?

`@NotBlank` trims whitespace and rejects blank strings.

---

# Real-World Example

```java
public class EmployeeRequest {

    @NotEmpty(
        message = "Name cannot be empty"
    )
    private String name;

    @NotEmpty(
        message = "At least one skill required"
    )
    private List<String> skills;

}
```

---

Request:

```json
{
    "name":"",
    "skills":[]
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "name":
    "Name cannot be empty",

    "skills":
    "At least one skill required"
}
```

---

# Key Points To Remember

- `@NotEmpty` validates that a value is not null and not empty.
- Supports Strings, Collections, Maps, Arrays, and Sets.
- Rejects `null`, `""`, `[]`, `{}`.
- Does NOT reject `"   "` (blank spaces).
- For blank-space validation use `@NotBlank`.
- Stronger than `@NotNull`.
- Frequently used for Lists, Sets, Arrays, and user input strings.
- Uses specialized validators internally depending on the field type.
- Commonly combined with `@Valid` and `@Validated`.
- One of the most frequently asked Bean Validation interview annotations.