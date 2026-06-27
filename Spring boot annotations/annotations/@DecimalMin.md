
## Definition

`@DecimalMin` is a Bean Validation annotation used to validate that a numeric value is **greater than or equal to** a specified minimum decimal value.

It tells the validation framework:

> The number must not be less than the configured minimum value.

Package:

```java
import jakarta.validation.constraints.DecimalMin;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.DecimalMin;
```

---

# Why Do We Need @DecimalMin?

Many business rules require a minimum allowed value.

Examples:

```text
Minimum Product Price
Minimum Salary
Minimum Deposit Amount
Minimum Order Quantity
Minimum Interest Rate
Minimum Account Balance
```

---

Example:

Business Rule:

```text
Minimum order amount = 100.50
```

---

Valid:

```java
100.50
```

---

Valid:

```java
150.75
```

---

Invalid:

```java
99.99
```

---

Solution:

```java
@DecimalMin
```

---

# Syntax

```java
@DecimalMin("100.50")
private BigDecimal amount;
```

---

Notice:

```java
"100.50"
```

is a String.

This avoids floating-point precision issues.

---

# Basic Example

```java
@DecimalMin("100.50")
private BigDecimal amount;
```

Meaning:

```text
amount >= 100.50
```

---

Valid:

```java
100.50
```

---

Valid:

```java
500.00
```

---

Invalid:

```java
99.99
```

---

# Important Interview Question

## Why is the value a String?

Wrong:

```java
@DecimalMin(100.50)
```

---

Correct:

```java
@DecimalMin("100.50")
```

---

Reason:

```text
Avoids floating-point precision problems.
```

---

# Important Interview Question

## Does @DecimalMin reject null?

Answer:

```text
NO
```

---

Example:

```java
@DecimalMin("100")
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

@DecimalMin("100")
private BigDecimal amount;
```

---

# Product Price Example

```java
@DecimalMin(
    value = "1.00",
    message = "Price must be at least 1.00"
)
private BigDecimal price;
```

---

Valid:

```java
1.00
```

---

Valid:

```java
999.99
```

---

Invalid:

```java
0.99
```

---

# Salary Example

```java
@DecimalMin("10000.00")
private BigDecimal salary;
```

---

Valid:

```java
10000.00
```

---

Valid:

```java
50000.00
```

---

Invalid:

```java
9999.99
```

---

# Interest Rate Example

```java
@DecimalMin("0.01")
private BigDecimal interestRate;
```

---

Valid:

```java
0.01
```

---

Valid:

```java
5.75
```

---

Invalid:

```java
0.00
```

---

# Minimum Deposit Example

```java
@DecimalMin("500")
private BigDecimal depositAmount;
```

---

Valid:

```java
500
```

---

Valid:

```java
1000
```

---

Invalid:

```java
499
```

---

# Inclusive Attribute

Most important feature.

Default:

```java
inclusive = true
```

Meaning:

```text
>= minimum value
```

---

Example:

```java
@DecimalMin("100")
```

Equivalent to:

```java
@DecimalMin(
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
101
```

---

Invalid:

```java
99
```

---

# Exclusive Minimum

Use:

```java
inclusive = false
```

---

Example:

```java
@DecimalMin(
    value = "100",
    inclusive = false
)
private BigDecimal amount;
```

---

Meaning:

```text
amount > 100
```

---

Valid:

```java
101
```

---

Invalid:

```java
100
```

---

Invalid:

```java
99
```

---

# Custom Message

```java
@DecimalMin(
    value = "100.00",
    message = "Amount must be at least 100.00"
)
private BigDecimal amount;
```

---

Response:

```json
{
    "message":
    "Amount must be at least 100.00"
}
```

---

# Using @DecimalMin with @Valid

DTO:

```java
public class PaymentRequest {

    @DecimalMin("100.00")
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
    "amount":50
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

private BigDecimal price;
```

---

Ensures:

```text
Price Exists
Valid Precision
Minimum Value = 1.00
```

---

# @DecimalMin vs @Min

Most Asked Interview Question.

---

## @Min

Works with:

```java
long values
```

---

Example:

```java
@Min(100)
```

---

Cannot represent:

```java
100.50
```

---

## @DecimalMin

Supports:

```java
Decimal Values
```

---

Example:

```java
@DecimalMin("100.50")
```

---

Comparison:

| Feature | @Min | @DecimalMin |
|----------|--------|------------|
| Integer Values | ✅ | ✅ |
| Decimal Values | ❌ | ✅ |
| BigDecimal | Limited | ✅ |

---

# @DecimalMin vs @Positive

Interview Question.

---

## @Positive

Checks:

```text
value > 0
```

---

Valid:

```java
0.01
```

---

Invalid:

```java
0
```

---

## @DecimalMin("100")

Checks:

```text
value >= 100
```

---

Valid:

```java
100
```

---

Invalid:

```java
99
```

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
@DecimalMin("100")
private String amount;
```

---

Valid:

```java
"150"
```

---

Invalid:

```java
"99"
```

---

# Internal Implementation

Hibernate Validator uses:

```java
DecimalMinValidatorForBigDecimal
```

---

```java
DecimalMinValidatorForBigInteger
```

---

```java
DecimalMinValidatorForCharSequence
```

---

depending on field type.

---

# Internal Validation Logic

Example:

```java
@DecimalMin("100")
```

Internally:

```java
value.compareTo(
    new BigDecimal("100")
) >= 0
```

---

For:

```java
inclusive = false
```

---

Internally:

```java
value.compareTo(
    new BigDecimal("100")
) > 0
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
DecimalMinValidator
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

Expecting @DecimalMin to Reject Null

Wrong:

```java
@DecimalMin("100")
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

@DecimalMin("100")
```

---

# Mistake 2

Using Numeric Literal

Wrong:

```java
@DecimalMin(100.50)
```

---

Correct:

```java
@DecimalMin("100.50")
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
Minimum Value
```

---

Use:

```java
@DecimalMin
```

for minimum value validation.

---

# Common Interview Questions

## What is @DecimalMin?

Validates minimum numeric value.

---

## Why is value specified as String?

To avoid floating-point precision issues.

---

## Does @DecimalMin reject null?

No.

---

## Difference Between @Min and @DecimalMin?

`@Min` is for integer values.

`@DecimalMin` supports decimal values.

---

## What does inclusive=true mean?

```text
value >= minimum
```

---

## What does inclusive=false mean?

```text
value > minimum
```

---

## Can @DecimalMin be used with BigDecimal?

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
    private BigDecimal price;

}
```

---

Request:

```json
{
    "price":0.50
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
    "Price must be at least 1.00"
}
```

---

# Key Points To Remember

- `@DecimalMin` validates a minimum numeric value.
- Supports decimal numbers.
- Value is always provided as a String.
- Default behavior is inclusive (`>=`).
- `inclusive=false` makes it strictly greater (`>`).
- Does NOT reject null values.
- Use `@NotNull` if the field is mandatory.
- Frequently used with `BigDecimal`.
- Commonly used for prices, salaries, deposits, taxes, and financial calculations.
- Internally uses `BigDecimal.compareTo()`.
- Frequently asked Spring Boot and Bean Validation interview annotation.