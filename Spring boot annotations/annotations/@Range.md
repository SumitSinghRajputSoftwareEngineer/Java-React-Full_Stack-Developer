
## Definition

`@Range` is a **Hibernate Validator-specific** annotation used to validate that a numeric value falls within a specified minimum and maximum range.

It tells the validation framework:

> The value must be between the configured minimum and maximum values (inclusive).

Package:

```java
import org.hibernate.validator.constraints.Range;
```

---

# Important Interview Point

`@Range` is:

```text
NOT a Jakarta Bean Validation Standard Annotation
```

It is provided by:

```text
Hibernate Validator
```

---

Unlike:

```java
@Min
@Max
@NotNull
@Size
@Email
```

which are standard Bean Validation annotations.

---

# Why Do We Need @Range?

Many business rules require values to stay within a fixed range.

Examples:

```text
Age
Marks
Rating
Experience
Working Hours
Discount Percentage
Temperature
Quantity
```

---

Example:

Business Rule:

```text
Age must be between 18 and 60.
```

---

Valid:

```java
25
```

---

Valid:

```java
60
```

---

Invalid:

```java
15
```

---

Invalid:

```java
70
```

---

Solution:

```java
@Range
```

---

# Syntax

```java
@Range(
    min = 18,
    max = 60
)
private Integer age;
```

---

Meaning:

```text
18 <= age <= 60
```

---

# Basic Example

```java
public class EmployeeRequest {

    @Range(
        min = 18,
        max = 60
    )
    private Integer age;

}
```

---

Valid:

```java
18
```

---

Valid:

```java
35
```

---

Valid:

```java
60
```

---

Invalid:

```java
17
```

---

Invalid:

```java
61
```

---

# Important Interview Question

## Does @Range reject null?

Answer:

```text
NO
```

---

Example:

```java
@Range(
    min = 18,
    max = 60
)
private Integer age;
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

@Range(
    min = 18,
    max = 60
)
private Integer age;
```

---

# Age Validation Example

```java
@Range(
    min = 18,
    max = 60
)
private Integer age;
```

---

Valid:

```java
25
```

---

Valid:

```java
60
```

---

Invalid:

```java
15
```

---

Invalid:

```java
70
```

---

# Rating Example

Business Rule:

```text
Rating must be between 1 and 5.
```

---

```java
@Range(
    min = 1,
    max = 5
)
private Integer rating;
```

---

Valid:

```java
1
```

---

Valid:

```java
3
```

---

Valid:

```java
5
```

---

Invalid:

```java
0
```

---

Invalid:

```java
6
```

---

# Discount Example

```java
@Range(
    min = 0,
    max = 100
)
private Integer discountPercentage;
```

---

Valid:

```java
0
```

---

Valid:

```java
50
```

---

Valid:

```java
100
```

---

Invalid:

```java
101
```

---

# Experience Example

```java
@Range(
    min = 0,
    max = 40
)
private Integer yearsOfExperience;
```

---

Valid:

```java
10
```

---

Invalid:

```java
45
```

---

# Custom Message

```java
@Range(
    min = 18,
    max = 60,
    message =
    "Age must be between 18 and 60"
)
private Integer age;
```

---

Response:

```json
{
    "age":
    "Age must be between 18 and 60"
}
```

---

# Using @Range with @Valid

DTO:

```java
public class EmployeeRequest {

    @Range(
        min = 18,
        max = 60
    )
    private Integer age;

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
    "age":15
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with Other Validations

Common usage:

```java
@NotNull

@Range(
    min = 18,
    max = 60
)
private Integer age;
```

---

Ensures:

```text
Age Exists
AND
Age Between 18 and 60
```

---

# @Range vs @Min + @Max

Most Asked Interview Question.

---

## Using @Range

```java
@Range(
    min = 18,
    max = 60
)
private Integer age;
```

---

## Using Standard Annotations

```java
@Min(18)

@Max(60)
private Integer age;
```

---

Both achieve:

```text
18 <= age <= 60
```

---

# Which Should Be Preferred?

Production recommendation:

```java
@Min + @Max
```

---

Reason:

```text
Standard Jakarta Bean Validation
Portable Across Providers
```

---

Most enterprise projects prefer:

```java
@Min
@Max
```

instead of:

```java
@Range
```

---

# @Range vs @DecimalMin + @DecimalMax

Interview Question.

---

## @Range

Works mainly with:

```java
Integer
Long
Short
Byte
```

---

Example:

```java
@Range(
    min = 1,
    max = 100
)
```

---

## @DecimalMin + @DecimalMax

Supports:

```java
BigDecimal
Decimal Values
```

---

Example:

```java
@DecimalMin("1.50")

@DecimalMax("99.99")
```

---

# Supported Types

Commonly:

```java
Byte
Short
Integer
Long
```

---

Also supports:

```java
BigInteger
BigDecimal
String
```

(when value can be interpreted numerically)

---

# Internal Implementation

Hibernate Validator uses:

```java
RangeValidator
```

---

Internally it behaves similarly to:

```java
@Min

@Max
```

combined together.

---

# Internal Validation Logic

Simplified:

```java
if(value == null)
    return true;

return value >= min
    &&
       value <= max;
```

---

Equivalent:

```java
min <= value <= max
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
RangeValidator
   │
   ▼
Check Min & Max
   │
 ┌─Valid────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Range to Reject Null

Wrong:

```java
@Range(
    min = 18,
    max = 60
)
private Integer age;
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

---

# Mistake 2

Using @Range for Decimal Precision

Wrong:

```java
@Range(
    min = 1,
    max = 100
)
private BigDecimal amount;
```

---

For precise decimal business rules prefer:

```java
@DecimalMin

@DecimalMax
```

---

# Mistake 3

Thinking @Range Is Standard

Wrong assumption:

```java
@Range
```

belongs to Jakarta Validation.

---

Actually:

```java
org.hibernate.validator.constraints.Range
```

is Hibernate-specific.

---

# Common Interview Questions

## What is @Range?

Validates that a number falls between a minimum and maximum value.

---

## Is @Range a standard Bean Validation annotation?

No.

It is Hibernate-specific.

---

## Does @Range reject null?

No.

---

## Difference Between @Range and @Min/@Max?

`@Range`

```text
Single Annotation
Hibernate Specific
```

---

`@Min + @Max`

```text
Standard Jakarta Validation
```

---

## Which should be preferred?

```java
@Min + @Max
```

in most enterprise applications.

---

## Does @Range include min and max values?

Yes.

Both limits are inclusive.

---

# Real-World Production Example

```java
public class EmployeeRequest {

    @NotNull(
        message =
        "Age is required"
    )

    @Range(
        min = 18,
        max = 60,
        message =
        "Age must be between 18 and 60"
    )
    private Integer age;

}
```

---

Request:

```json
{
    "age":70
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
   "age":
   "Age must be between 18 and 60"
}
```

---

# Key Points To Remember

- `@Range` validates that a numeric value lies within a range.
- Hibernate Validator-specific annotation.
- Not part of Jakarta Bean Validation.
- Equivalent to combining `@Min` and `@Max`.
- Both min and max are inclusive.
- Does NOT reject null values.
- Use `@NotNull` if the field is mandatory.
- Commonly used for age, rating, marks, discount, and quantity validations.
- Prefer `@Min` + `@Max` in enterprise applications.
- Internally uses `RangeValidator`.
- Frequently asked Spring Boot and Hibernate Validator interview annotation.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Range` | Min + Max Validation |
| `@Min` | Minimum Value |
| `@Max` | Maximum Value |
| `@DecimalMin` | Minimum Decimal Value |
| `@DecimalMax` | Maximum Decimal Value |

---

# Interview Shortcut

### Hibernate-Specific

```java
@Range(
    min = 18,
    max = 60
)
private Integer age;
```

### Standard Jakarta Validation

```java
@Min(18)

@Max(60)
private Integer age;
```

Most enterprise Spring Boot projects prefer the second approach because it follows the Bean Validation specification.