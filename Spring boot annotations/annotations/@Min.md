
## Definition

`@Min` is a Bean Validation annotation used to ensure that a numeric value is **greater than or equal to a specified minimum value**.

It tells the validation framework:

> The value must be >= the configured minimum value.

Package:

```java
import jakarta.validation.constraints.Min;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Min;
```

---

# Why Do We Need @Min?

Suppose your application has the following rules:

```text
Age must be at least 18
Salary must be at least 10000
Quantity must be at least 1
```

Without validation:

```json
{
    "age": 5
}
```

could be accepted.

---

Using:

```java
@Min
```

prevents invalid values.

---

# Basic Syntax

```java
@Min(18)
private Integer age;
```

Meaning:

```text
Age must be >= 18
```

---

# Supported Types

`@Min` supports:

```java
byte
short
int
long
```

and wrapper types:

```java
Byte
Short
Integer
Long
```

Also supports:

```java
BigInteger
BigDecimal
```

---

Example:

```java
@Min(100)
private Long amount;
```

---

# Basic Example

```java
public class EmployeeRequest {

    @Min(18)
    private Integer age;

}
```

---

Valid:

```json
{
    "age":18
}
```

---

Valid:

```json
{
    "age":25
}
```

---

Invalid:

```json
{
    "age":17
}
```

---

# Validation Logic

Internally:

```java
value >= configuredMinimum
```

---

Example:

```java
@Min(18)
private Integer age;
```

---

Checks:

```java
age >= 18
```

---

# Important Interview Question

Does `@Min` reject null?

Answer:

```text
NO
```

---

Example:

```java
@Min(18)
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

If null should be rejected:

```java
@NotNull
@Min(18)
private Integer age;
```

---

# Age Validation Example

```java
@NotNull

@Min(18)
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
35
```

---

Invalid:

```java
17
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

# Salary Validation Example

```java
@Min(10000)
private Long salary;
```

---

Valid:

```java
10000
```

---

Valid:

```java
25000
```

---

Invalid:

```java
9999
```

---

# Quantity Validation Example

```java
@Min(1)
private Integer quantity;
```

---

Valid:

```java
1
```

---

Valid:

```java
10
```

---

Invalid:

```java
0
```

---

Invalid:

```java
-1
```

---

# BigDecimal Example

```java
@Min(100)
private BigDecimal amount;
```

---

Valid:

```java
150
```

---

Invalid:

```java
99
```

---

# Custom Message

Default:

```text
must be greater than or equal to X
```

---

Custom:

```java
@Min(
    value = 18,
    message = "Age must be at least 18"
)
private Integer age;
```

---

Error:

```json
{
    "message":
    "Age must be at least 18"
}
```

---

# Using @Min with @Valid

DTO:

```java
public class EmployeeRequest {

    @Min(18)
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
    "age":15
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with @Max

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
```

---

Valid:

```java
40
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

# Combining with @NotNull

Production-grade validation.

---

```java
@NotNull

@Min(1)
private Long employeeId;
```

---

Ensures:

```text
Not Null
Greater Than Or Equal To 1
```

---

# Using @Min on Method Parameters

Requires:

```java
@Validated
```

---

Controller:

```java
@RestController

@Validated
public class EmployeeController {

    @GetMapping

    public String find(

            @Min(1)
            @RequestParam
            Integer page) {

        return "success";
    }

}
```

---

Valid:

```http
?page=1
```

---

Invalid:

```http
?page=0
```

---

Throws:

```java
ConstraintViolationException
```

---

# Service Layer Validation

---

```java
@Service

@Validated
public class EmployeeService {

    public void process(

            @Min(100)
            Long amount) {

    }

}
```

---

Call:

```java
process(50L);
```

---

Result:

```java
ConstraintViolationException
```

---

# @Min vs @Positive

Very Important Interview Question.

---

## @Min(1)

```java
@Min(1)
private Integer quantity;
```

---

Valid:

```java
1
10
100
```

---

Invalid:

```java
0
-1
```

---

## @Positive

```java
@Positive
private Integer quantity;
```

---

Valid:

```java
1
10
100
```

---

Invalid:

```java
0
-1
```

---

For integer values:

```java
@Min(1)
```

and

```java
@Positive
```

often behave similarly.

---

Difference:

```java
@Min(100)
```

allows custom minimum values.

---

# @Min vs @DecimalMin

Important.

---

## @Min

Works with:

```java
18
100
1000
```

(integer values)

---

Example:

```java
@Min(18)
private Integer age;
```

---

## @DecimalMin

Supports decimal values.

---

Example:

```java
@DecimalMin("99.99")
private BigDecimal amount;
```

---

Useful for:

```java
Prices
Amounts
Interest Rates
```

---

# Internal Implementation

Hibernate Validator uses:

```java
MinValidatorForInteger
```

---

```java
MinValidatorForLong
```

---

```java
MinValidatorForBigDecimal
```

---

```java
MinValidatorForBigInteger
```

---

depending on the field type.

---

# Internal Validation Logic

Example:

```java
@Min(18)
private Integer age;
```

---

Internally:

```java
return value >= 18;
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
MinValidator
   │
   ▼
value >= min ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Min to Reject Null

Wrong assumption:

```java
@Min(18)
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
@Min(18)
```

---

# Mistake 2

Using @Min for Decimal Validation

Wrong:

```java
@Min(99.99)
```

---

Compilation error.

---

Use:

```java
@DecimalMin("99.99")
```

---

# Mistake 3

Using @Min on Strings

Wrong:

```java
@Min(5)
private String username;
```

---

Not supported.

---

Use:

```java
@Size(min = 5)
```

---

# Common Interview Questions

## What is @Min?

Validates that a numeric value is greater than or equal to a minimum value.

---

## Does @Min reject null?

No.

---

## How do we reject null values?

```java
@NotNull
@Min(...)
```

---

## Which types support @Min?

```java
Integer
Long
Short
Byte
BigInteger
BigDecimal
```

---

## Difference Between @Min and @Positive?

`@Positive` checks:

```java
value > 0
```

`@Min` allows custom minimum values.

---

## Difference Between @Min and @DecimalMin?

`@DecimalMin` supports decimal minimum values.

---

## Can @Min be used on String?

No.

Use:

```java
@Size
```

instead.

---

# Real-World Production Example

```java
public class LoanRequest {

    @NotNull

    @Min(
        value = 18,
        message = "Age must be at least 18"
    )
    private Integer age;

    @NotNull

    @Min(
        value = 10000,
        message = "Loan amount must be at least 10000"
    )
    private Long loanAmount;

}
```

---

Request:

```json
{
    "age":16,
    "loanAmount":5000
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "age":"Age must be at least 18",
    "loanAmount":"Loan amount must be at least 10000"
}
```

---

# Key Points To Remember

- `@Min` validates that a numeric value is greater than or equal to a specified minimum.
- Supports primitive numeric types, wrapper types, `BigInteger`, and `BigDecimal`.
- Does NOT reject null values.
- Use `@NotNull` when null should be rejected.
- Commonly used for age, salary, quantity, IDs, and business limits.
- Can be combined with `@Max` to create ranges.
- Uses type-specific `MinValidator` implementations internally.
- For decimal thresholds, use `@DecimalMin`.
- Frequently used in REST API request validation.
- One of the most commonly asked Bean Validation interview annotations.