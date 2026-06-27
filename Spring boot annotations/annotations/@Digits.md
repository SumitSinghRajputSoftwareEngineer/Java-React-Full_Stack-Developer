
## Definition

`@Digits` is a Bean Validation annotation used to validate the **number of digits** allowed in a numeric value.

It tells the validation framework:

> The number can contain a specified maximum number of integer digits and fractional (decimal) digits.

Package:

```java
import jakarta.validation.constraints.Digits;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Digits;
```

---

# Why Do We Need @Digits?

Many business applications have strict rules about numeric precision.

Examples:

```text
Price
Salary
Tax Amount
Interest Rate
Product Weight
Invoice Amount
Bank Balance
```

---

Example:

A business rule says:

```text
Maximum 10 digits before decimal
Maximum 2 digits after decimal
```

Valid:

```java
1234567890.99
```

---

Invalid:

```java
12345678901.99
```

(11 integer digits)

---

Invalid:

```java
1234567890.999
```

(3 decimal digits)

---

Solution:

```java
@Digits
```

---

# Syntax

```java
@Digits(
    integer = x,
    fraction = y
)
```

Where:

```text
integer  = Maximum digits before decimal point
fraction = Maximum digits after decimal point
```

---

# Basic Example

```java
@Digits(
    integer = 10,
    fraction = 2
)
private BigDecimal amount;
```

Meaning:

```text
Maximum 10 integer digits
Maximum 2 decimal digits
```

---

# Understanding integer and fraction

Example:

```java
12345.67
```

---

Integer Part:

```java
12345
```

Digits:

```text
5
```

---

Fraction Part:

```java
67
```

Digits:

```text
2
```

---

# Example 1

```java
@Digits(
    integer = 5,
    fraction = 2
)
private BigDecimal amount;
```

---

Valid:

```java
12345.67
```

Integer digits:

```text
5
```

Fraction digits:

```text
2
```

---

Valid:

```java
99999.99
```

---

Invalid:

```java
123456.78
```

Integer digits:

```text
6
```

---

Invalid:

```java
12345.678
```

Fraction digits:

```text
3
```

---

# Important Interview Question

## Does @Digits reject null?

Answer:

```text
NO
```

---

Example:

```java
@Digits(
    integer = 5,
    fraction = 2
)
private BigDecimal amount;
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
@NotNull

@Digits(
    integer = 5,
    fraction = 2
)
private BigDecimal amount;
```

---

# Price Validation Example

Business Rule:

```text
Maximum 8 digits before decimal
Maximum 2 digits after decimal
```

---

```java
@Digits(
    integer = 8,
    fraction = 2
)
private BigDecimal price;
```

---

Valid:

```java
99999999.99
```

---

Invalid:

```java
100000000.99
```

(9 integer digits)

---

Invalid:

```java
99999999.999
```

(3 decimal digits)

---

# Salary Validation Example

```java
@Digits(
    integer = 7,
    fraction = 2
)
private BigDecimal salary;
```

---

Valid:

```java
9999999.99
```

---

Invalid:

```java
10000000.00
```

---

# Tax Percentage Example

```java
@Digits(
    integer = 2,
    fraction = 2
)
private BigDecimal taxPercentage;
```

---

Valid:

```java
18.50
```

---

Valid:

```java
99.99
```

---

Invalid:

```java
100.00
```

---

# Weight Validation Example

```java
@Digits(
    integer = 3,
    fraction = 3
)
private BigDecimal weight;
```

---

Valid:

```java
999.999
```

---

Invalid:

```java
1000.999
```

---

Invalid:

```java
999.9999
```

---

# Custom Message

Default:

```text
numeric value out of bounds
```

---

Custom:

```java
@Digits(
    integer = 10,
    fraction = 2,
    message = "Amount can contain max 10 digits and 2 decimal places"
)
private BigDecimal amount;
```

---

Response:

```json
{
    "message":
    "Amount can contain max 10 digits and 2 decimal places"
}
```

---

# Using @Digits with @Valid

DTO:

```java
public class PaymentRequest {

    @Digits(
        integer = 10,
        fraction = 2
    )
    private BigDecimal amount;

}
```

---

Controller:

```java
@PostMapping("/payment")
public String process(

        @Valid
        @RequestBody
        PaymentRequest request) {

    return "success";
}
```

---

Request:

```json
{
    "amount":12345678901.99
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with Other Validations

Very common in production.

---

```java
@NotNull

@Positive

@Digits(
    integer = 10,
    fraction = 2
)
private BigDecimal amount;
```

---

Ensures:

```text
Amount Exists
Amount > 0
Maximum 10 Integer Digits
Maximum 2 Decimal Digits
```

---

# @Digits vs @Size

Interview Question.

---

## @Digits

Validates:

```text
Numeric Digits
```

---

Example:

```java
@Digits(
    integer = 5,
    fraction = 2
)
```

---

Checks:

```java
12345.67
```

---

## @Size

Validates:

```text
Length
```

---

Example:

```java
@Size(max = 10)
```

---

Checks:

```text
String Length
Collection Size
Array Size
```

---

Cannot validate:

```text
Integer Digits
Decimal Digits
```

---

# @Digits vs @DecimalMax

Interview Question.

---

## @Digits

Checks:

```text
Number of Digits
```

---

Example:

```java
@Digits(
    integer = 5,
    fraction = 2
)
```

---

## @DecimalMax

Checks:

```text
Maximum Numeric Value
```

---

Example:

```java
@DecimalMax("99999.99")
```

---

# Difference

```java
12345.678
```

---

With:

```java
@Digits(integer = 5, fraction = 2)
```

Fails.

---

With:

```java
@DecimalMax("99999.99")
```

May still pass based on value comparison.

---

# Supported Types

Most commonly:

```java
BigDecimal
BigInteger
```

---

Also supports:

```java
Byte
Short
Integer
Long
Float
Double
String
```

---

# String Support

Many developers don't know this.

---

Example:

```java
@Digits(
    integer = 5,
    fraction = 2
)
private String amount;
```

---

Valid:

```java
"12345.67"
```

---

Invalid:

```java
"123456.67"
```

---

# Internal Implementation

Hibernate Validator uses:

```java
DigitsValidatorForBigDecimal
```

---

```java
DigitsValidatorForBigInteger
```

---

```java
DigitsValidatorForCharSequence
```

---

depending on field type.

---

# Internal Validation Logic

Example:

```java
@Digits(
    integer = 5,
    fraction = 2
)
```

Internally:

```java
integerDigits <= 5
&&
fractionDigits <= 2
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
DigitsValidator
   │
   ▼
Check Integer Digits
Check Fraction Digits
   │
 ┌─Valid────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Digits to Reject Null

Wrong:

```java
@Digits(
    integer = 5,
    fraction = 2
)
private BigDecimal amount;
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

@Digits(...)
```

---

# Mistake 2

Thinking @Digits Controls Value Range

Wrong:

```java
@Digits(
    integer = 5,
    fraction = 2
)
```

---

Does NOT mean:

```java
value <= 99999.99
```

It validates digit count, not business limits.

---

Use:

```java
@DecimalMax
```

for maximum value validation.

---

# Mistake 3

Using Only @Digits for Money

Wrong:

```java
@Digits(
    integer = 10,
    fraction = 2
)
private BigDecimal amount;
```

---

Allows:

```java
-999.99
```

---

Combine with:

```java
@Positive
```

or

```java
@PositiveOrZero
```

---

# Common Interview Questions

## What is @Digits?

Validates maximum integer and decimal digits in a numeric value.

---

## What does integer mean?

Maximum digits before decimal point.

---

## What does fraction mean?

Maximum digits after decimal point.

---

## Does @Digits reject null?

No.

---

## Difference Between @Digits and @Size?

`@Digits` validates numeric precision.

`@Size` validates length.

---

## Difference Between @Digits and @DecimalMax?

`@Digits` validates digit count.

`@DecimalMax` validates numeric value.

---

## Can @Digits be used with BigDecimal?

Yes.

Most common usage.

---

## Can @Digits be used with String?

Yes.

As long as the String represents a numeric value.

---

# Real-World Production Example

```java
public class PaymentRequest {

    @NotNull(
        message = "Amount is required"
    )

    @Positive(
        message = "Amount must be positive"
    )

    @Digits(
        integer = 10,
        fraction = 2,
        message =
        "Amount can have maximum 10 digits and 2 decimal places"
    )
    private BigDecimal amount;

}
```

---

Request:

```json
{
    "amount":12345678901.999
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "amount":
    "Amount can have maximum 10 digits and 2 decimal places"
}
```

---

# Key Points To Remember

- `@Digits` validates numeric precision and scale.
- `integer` = maximum digits before decimal point.
- `fraction` = maximum digits after decimal point.
- Does NOT reject null values.
- Use `@NotNull` if the value is mandatory.
- Frequently used with `BigDecimal`.
- Commonly used for money, salary, tax, percentage, and financial calculations.
- Does NOT validate minimum or maximum value.
- Often combined with `@Positive`, `@DecimalMin`, and `@DecimalMax`.
- Uses type-specific `DigitsValidator` implementations internally.
- Frequently asked Spring Boot and Bean Validation interview annotation.