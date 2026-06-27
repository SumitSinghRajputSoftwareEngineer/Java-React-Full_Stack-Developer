
## Definition

`@DecimalMax` is a Bean Validation annotation used to validate that a numeric value is **less than or equal to** a specified maximum decimal value.

It tells the validation framework:

> The number must not be greater than the configured maximum value.

Package:

```java
import jakarta.validation.constraints.DecimalMax;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.DecimalMax;
```

---

# Why Do We Need @DecimalMax?

Many business rules require an upper limit.

Examples:

```text
Maximum Product Price
Maximum Discount Percentage
Maximum Loan Amount
Maximum Tax Rate
Maximum Order Quantity
Maximum Interest Rate
```

---

Example:

Business Rule:

```text
Maximum discount = 50%
```

---

Valid:

```java
50
```

---

Valid:

```java
25
```

---

Invalid:

```java
60
```

---

Solution:

```java
@DecimalMax
```

---

# Syntax

```java
@DecimalMax("1000.00")
private BigDecimal amount;
```

---

Notice:

```java
"1000.00"
```

is a String.

This avoids floating-point precision problems.

---

# Basic Example

```java
@DecimalMax("1000.00")
private BigDecimal amount;
```

Meaning:

```text
amount <= 1000.00
```

---

Valid:

```java
1000.00
```

---

Valid:

```java
999.99
```

---

Invalid:

```java
1000.01
```

---

# Important Interview Question

## Why is value a String?

Wrong:

```java
@DecimalMax(1000.00)
```

---

Correct:

```java
@DecimalMax("1000.00")
```

---

Reason:

```text
Avoid floating-point precision issues.
```

---

# Important Interview Question

## Does @DecimalMax reject null?

Answer:

```text
NO
```

---

Example:

```java
@DecimalMax("1000")
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

@DecimalMax("1000")
private BigDecimal amount;
```

---

# Product Price Example

```java
@DecimalMax(
    value = "100000.00",
    message = "Price cannot exceed 100000"
)
private BigDecimal price;
```

---

Valid:

```java
99999.99
```

---

Valid:

```java
100000.00
```

---

Invalid:

```java
100000.01
```

---

# Discount Percentage Example

```java
@DecimalMax("50")
private BigDecimal discountPercentage;
```

---

Valid:

```java
50
```

---

Valid:

```java
25
```

---

Invalid:

```java
60
```

---

# Interest Rate Example

```java
@DecimalMax("25.00")
private BigDecimal interestRate;
```

---

Valid:

```java
15.50
```

---

Valid:

```java
25.00
```

---

Invalid:

```java
25.01
```

---

# Loan Amount Example

```java
@DecimalMax("10000000")
private BigDecimal loanAmount;
```

---

Valid:

```java
5000000
```

---

Valid:

```java
10000000
```

---

Invalid:

```java
10000001
```

---

# Inclusive Attribute

Default:

```java
inclusive = true
```

Meaning:

```text
value <= maximum
```

---

Example:

```java
@DecimalMax("100")
```

Equivalent:

```java
@DecimalMax(
    value = "100",
    inclusive = true
)
```

---

Valid:

```java
100
```

---

Valid:

```java
99
```

---

Invalid:

```java
101
```

---

# Exclusive Maximum

Use:

```java
inclusive = false
```

---

Example:

```java
@DecimalMax(
    value = "100",
    inclusive = false
)
private BigDecimal amount;
```

---

Meaning:

```text
amount < 100
```

---

Valid:

```java
99
```

---

Invalid:

```java
100
```

---

Invalid:

```java
101
```

---

# Custom Message

```java
@DecimalMax(
    value = "1000.00",
    message = "Amount cannot exceed 1000.00"
)
private BigDecimal amount;
```

---

Response:

```json
{
    "message":
    "Amount cannot exceed 1000.00"
}
```

---

# Using @DecimalMax with @Valid

DTO:

```java
public class PaymentRequest {

    @DecimalMax("1000.00")
    private BigDecimal amount;

}
```

---

Controller:

```java
@PostMapping("/payments")
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
    "amount":2000
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

@Digits(
    integer = 10,
    fraction = 2
)

@DecimalMin("1.00")

@DecimalMax("100000.00")

private BigDecimal price;
```

---

Ensures:

```text
Price Exists
Valid Precision
Minimum Value = 1.00
Maximum Value = 100000.00
```

---

# @DecimalMax vs @Max

Most Asked Interview Question.

---

## @Max

Works primarily with:

```java
long values
```

---

Example:

```java
@Max(100)
```

---

Not ideal for:

```java
100.50
```

---

## @DecimalMax

Supports:

```java
Decimal Values
BigDecimal
```

---

Example:

```java
@DecimalMax("100.50")
```

---

Comparison:

| Feature | @Max | @DecimalMax |
|----------|------|------------|
| Integer Values | ✅ | ✅ |
| Decimal Values | ❌ | ✅ |
| BigDecimal | Limited | ✅ |

---

# @DecimalMax vs @Digits

Interview Question.

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

## @Digits

Checks:

```text
Digit Count
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

Example Value:

```java
12345.678
```

---

`@Digits`

```text
FAIL
```

because fraction digits = 3

---

`@DecimalMax`

```text
PASS
```

because value is less than 99999.99

---

# Supported Types

Commonly:

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

Example:

```java
@DecimalMax("100")
private String amount;
```

---

Valid:

```java
"99"
```

---

Invalid:

```java
"101"
```

---

# Internal Implementation

Hibernate Validator uses:

```java
DecimalMaxValidatorForBigDecimal
```

---

```java
DecimalMaxValidatorForBigInteger
```

---

```java
DecimalMaxValidatorForCharSequence
```

---

depending on field type.

---

# Internal Validation Logic

Example:

```java
@DecimalMax("100")
```

Internally:

```java
value.compareTo(
    new BigDecimal("100")
) <= 0
```

---

For:

```java
inclusive = false
```

Internally:

```java
value.compareTo(
    new BigDecimal("100")
) < 0
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
DecimalMaxValidator
   │
   ▼
Compare Value
   │
 ┌─Valid────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @DecimalMax to Reject Null

Wrong:

```java
@DecimalMax("100")
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

@DecimalMax("100")
```

---

# Mistake 2

Using Numeric Literal

Wrong:

```java
@DecimalMax(1000.50)
```

---

Correct:

```java
@DecimalMax("1000.50")
```

---

# Mistake 3

Confusing With @Digits

Wrong:

```java
@Digits(
    integer = 5,
    fraction = 2
)
```

---

This validates:

```text
Digit Count
```

---

NOT:

```text
Maximum Value
```

---

Use:

```java
@DecimalMax
```

for maximum value validation.

---

# Common Interview Questions

## What is @DecimalMax?

Validates maximum numeric value.

---

## Why is value specified as String?

To avoid floating-point precision issues.

---

## Does @DecimalMax reject null?

No.

---

## Difference Between @Max and @DecimalMax?

`@Max` is mainly for integer values.

`@DecimalMax` supports decimal values.

---

## What does inclusive=true mean?

```text
value <= maximum
```

---

## What does inclusive=false mean?

```text
value < maximum
```

---

## Can @DecimalMax be used with BigDecimal?

Yes.

Most common usage.

---

# Real-World Production Example

```java
public class ProductRequest {

    @NotNull(
        message = "Price is required"
    )

    @Digits(
        integer = 10,
        fraction = 2
    )

    @DecimalMin(
        value = "1.00",
        message = "Price must be at least 1.00"
    )

    @DecimalMax(
        value = "100000.00",
        message = "Price cannot exceed 100000.00"
    )
    private BigDecimal price;

}
```

---

Request:

```json
{
    "price":150000
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "price":
    "Price cannot exceed 100000.00"
}
```

---

# Key Points To Remember

- `@DecimalMax` validates a maximum numeric value.
- Supports decimal numbers.
- Value is always provided as a String.
- Default behavior is inclusive (`<=`).
- `inclusive=false` makes it strictly less than (`<`).
- Does NOT reject null values.
- Use `@NotNull` if the field is mandatory.
- Frequently used with `BigDecimal`.
- Commonly used for prices, discounts, loan amounts, salaries, taxes, and financial calculations.
- Internally uses `BigDecimal.compareTo()`.
- Frequently asked Spring Boot and Bean Validation interview annotation.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Min` | Minimum Integer Value |
| `@Max` | Maximum Integer Value |
| `@DecimalMin` | Minimum Decimal Value |
| `@DecimalMax` | Maximum Decimal Value |
| `@Digits` | Digit Count Validation |

This comparison is commonly asked in Spring Boot and Hibernate Validator interviews.