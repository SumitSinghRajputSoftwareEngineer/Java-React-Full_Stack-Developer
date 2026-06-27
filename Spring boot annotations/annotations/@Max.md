
## Definition

`@Max` is a Bean Validation annotation used to ensure that a numeric value is **less than or equal to a specified maximum value**.

It tells the validation framework:

> The value must be <= the configured maximum value.

Package:

```java
import jakarta.validation.constraints.Max;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Max;
```

---

# Why Do We Need @Max?

Suppose business requirements are:

```text
Age cannot exceed 60
Rating cannot exceed 5
Discount cannot exceed 100
Monthly Leave cannot exceed 30
```

Without validation:

```json
{
    "rating": 100
}
```

could be accepted.

---

Using:

```java
@Max
```

prevents values larger than the allowed limit.

---

# Basic Syntax

```java
@Max(60)
private Integer age;
```

Meaning:

```text
Age must be <= 60
```

---

# Supported Types

`@Max` supports:

```java
byte
short
int
long
```

Wrapper types:

```java
Byte
Short
Integer
Long
```

Also:

```java
BigInteger
BigDecimal
```

---

Example:

```java
@Max(100000)
private Long salary;
```

---

# Basic Example

```java
public class EmployeeRequest {

    @Max(60)
    private Integer age;

}
```

---

Valid:

```json
{
    "age":60
}
```

---

Valid:

```json
{
    "age":35
}
```

---

Invalid:

```json
{
    "age":61
}
```

---

# Validation Logic

Internally:

```java
value <= configuredMaximum
```

---

Example:

```java
@Max(60)
private Integer age;
```

Checks:

```java
age <= 60
```

---

# Important Interview Question

Does `@Max` reject null?

Answer:

```text
NO
```

---

Example:

```java
@Max(60)
private Integer age;
```

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

as valid unless:

```java
@NotNull
```

is specified.

---

If null should fail:

```java
@NotNull
@Max(60)
private Integer age;
```

---

# Age Validation Example

```java
@NotNull

@Max(60)
private Integer age;
```

---

Valid:

```java
18
```

---

Valid:

```java
60
```

---

Invalid:

```java
61
```

---

Invalid:

```java
null
```

because of:

```java
@NotNull
```

---

# Rating Validation Example

```java
@Max(5)
private Integer rating;
```

---

Valid:

```java
1
2
3
4
5
```

---

Invalid:

```java
6
```

---

# Discount Validation Example

```java
@Max(100)
private Integer discountPercentage;
```

---

Valid:

```java
50
100
```

---

Invalid:

```java
101
```

---

# Salary Validation Example

```java
@Max(100000)
private Long salary;
```

---

Valid:

```java
75000
```

---

Invalid:

```java
150000
```

---

# BigDecimal Example

```java
@Max(1000)
private BigDecimal amount;
```

---

Valid:

```java
500
```

---

Invalid:

```java
1500
```

---

# Custom Message

Default:

```text
must be less than or equal to X
```

---

Custom:

```java
@Max(
    value = 60,
    message = "Age cannot exceed 60"
)
private Integer age;
```

---

Error:

```json
{
    "message":
    "Age cannot exceed 60"
}
```

---

# Combining @Min and @Max

Very common.

---

```java
@Min(18)

@Max(60)
private Integer age;
```

---

Valid:

```java
18
25
60
```

---

Invalid:

```java
17
61
```

---

Creates range validation:

```text
18 <= age <= 60
```

---

# Using @Max with @Valid

DTO:

```java
public class EmployeeRequest {

    @Max(60)
    private Integer age;

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
    "age":75
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Method Parameter Validation

Requires:

```java
@Validated
```

---

Example:

```java
@RestController

@Validated
public class EmployeeController {

    @GetMapping

    public String getEmployees(

            @Max(100)
            @RequestParam
            Integer size) {

        return "success";
    }

}
```

---

Valid:

```http
?size=50
```

---

Invalid:

```http
?size=500
```

---

Throws:

```java
ConstraintViolationException
```

---

# Service Layer Validation

```java
@Service

@Validated
public class SalaryService {

    public void process(

            @Max(100000)
            Long salary) {

    }

}
```

---

Call:

```java
process(200000L);
```

---

Result:

```java
ConstraintViolationException
```

---

# @Max vs @Negative

Interview Question.

---

## @Max(0)

```java
@Max(0)
private Integer balance;
```

---

Allows:

```java
0
-1
-100
```

---

Rejects:

```java
1
```

---

## @Negative

```java
@Negative
private Integer balance;
```

---

Allows:

```java
-1
-100
```

---

Rejects:

```java
0
1
```

---

Difference:

```java
@Max
```

defines a custom upper limit.

---

# @Max vs @DecimalMax

Important.

---

## @Max

Supports integer maximum values.

Example:

```java
@Max(100)
private Integer quantity;
```

---

## @DecimalMax

Supports decimal values.

Example:

```java
@DecimalMax("99.99")
private BigDecimal price;
```

---

Useful for:

```text
Price
Interest Rate
Tax
Discount
```

---

# Internal Implementation

Hibernate Validator provides:

```java
MaxValidatorForInteger
```

---

```java
MaxValidatorForLong
```

---

```java
MaxValidatorForBigDecimal
```

---

```java
MaxValidatorForBigInteger
```

---

depending on field type.

---

# Internal Validation Logic

Example:

```java
@Max(60)
private Integer age;
```

Internally:

```java
return value <= 60;
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
MaxValidator
   │
   ▼
value <= max ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Max to Reject Null

Wrong assumption:

```java
@Max(60)
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
@Max(60)
```

---

# Mistake 2

Using @Max for Decimal Values

Wrong:

```java
@Max(99.99)
```

---

Compilation error.

---

Use:

```java
@DecimalMax("99.99")
```

---

# Mistake 3

Using @Max on String

Wrong:

```java
@Max(10)
private String username;
```

---

Not supported.

---

Use:

```java
@Size(max = 10)
```

---

# Common Interview Questions

## What is @Max?

Validates that a numeric value is less than or equal to a specified maximum.

---

## Does @Max reject null?

No.

---

## How do we reject null values?

```java
@NotNull
@Max(...)
```

---

## Which types support @Max?

```java
Integer
Long
Short
Byte
BigInteger
BigDecimal
```

---

## Difference Between @Max and @DecimalMax?

`@DecimalMax` supports decimal values.

---

## Can @Max be applied to String?

No.

Use:

```java
@Size(max = ...)
```

---

## Can @Max be combined with @Min?

Yes.

Very common for range validation.

---

# Real-World Production Example

```java
public class EmployeeRequest {

    @NotNull

    @Min(
        value = 18,
        message = "Age must be at least 18"
    )

    @Max(
        value = 60,
        message = "Age cannot exceed 60"
    )
    private Integer age;

}
```

---

Request:

```json
{
    "age":65
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "age":"Age cannot exceed 60"
}
```

---

# Key Points To Remember

- `@Max` validates that a numeric value is less than or equal to a specified maximum.
- Supports primitive numeric types, wrapper types, `BigInteger`, and `BigDecimal`.
- Does NOT reject null values.
- Use `@NotNull` when null should fail validation.
- Commonly used for age limits, ratings, discounts, quantities, and business constraints.
- Often combined with `@Min` to create value ranges.
- Uses type-specific `MaxValidator` implementations internally.
- For decimal thresholds use `@DecimalMax`.
- Cannot be applied to Strings.
- One of the most frequently asked Bean Validation interview annotations.