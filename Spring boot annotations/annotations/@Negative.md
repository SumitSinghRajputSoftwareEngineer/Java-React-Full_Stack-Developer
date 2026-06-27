
## Definition

`@Negative` is a Bean Validation annotation used to ensure that a numeric value is **strictly less than zero**.

It tells the validation framework:

> The value must be < 0.

Package:

```java
import jakarta.validation.constraints.Negative;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Negative;
```

---

# Why Do We Need @Negative?

Some business scenarios require values that must always be negative.

Examples:

```text
Temperature Below Freezing
Debt Amount
Loss Amount
Negative Adjustment
Credit Balance (depending on business rules)
```

---

Example:

```json
{
    "lossAmount": 100
}
```

should be rejected because a loss amount should be represented as a negative value.

---

Solution:

```java
@Negative
```

---

# What Does @Negative Validate?

Internally:

```java
value < 0
```

---

Valid:

```java
-1
-10
-999
-0.01
```

---

Invalid:

```java
0
1
100
```

---

# Supported Types

`@Negative` supports:

Primitive types:

```java
byte
short
int
long
float
double
```

Wrapper types:

```java
Byte
Short
Integer
Long
Float
Double
```

Also:

```java
BigInteger
BigDecimal
```

---

# Basic Example

```java
public class TransactionRequest {

    @Negative
    private Double lossAmount;

}
```

---

Valid:

```json
{
    "lossAmount": -500
}
```

---

Invalid:

```json
{
    "lossAmount": 0
}
```

---

Invalid:

```json
{
    "lossAmount": 500
}
```

---

# Important Interview Question

Does @Negative reject null?

Answer:

```text
NO
```

---

Example:

```java
@Negative
private Integer adjustment;
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

If null should fail:

```java
@NotNull
@Negative
private Integer adjustment;
```

---

# Debt Amount Example

```java
@NotNull

@Negative
private BigDecimal debtAmount;
```

---

Valid:

```java
-1000.50
```

---

Invalid:

```java
0
```

---

Invalid:

```java
1000
```

---

# Temperature Example

```java
@Negative
private Double temperature;
```

---

Valid:

```java
-5.5
```

---

Valid:

```java
-0.1
```

---

Invalid:

```java
0
```

---

Invalid:

```java
5
```

---

# Custom Message

Default:

```text
must be less than 0
```

---

Custom:

```java
@Negative(
    message = "Amount must be negative"
)
private BigDecimal amount;
```

---

Error:

```json
{
    "message":
    "Amount must be negative"
}
```

---

# Using @Negative with @Valid

DTO:

```java
public class AdjustmentRequest {

    @Negative
    private Integer adjustment;

}
```

---

Controller:

```java
@PostMapping("/adjustment")
public String save(

        @Valid
        @RequestBody
        AdjustmentRequest request) {

    return "saved";
}
```

---

Request:

```json
{
    "adjustment": 10
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
public class FinanceController {

    @GetMapping

    public String process(

            @Negative
            @RequestParam
            Double loss) {

        return "success";
    }

}
```

---

Valid:

```http
?loss=-100
```

---

Invalid:

```http
?loss=100
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
public class AccountingService {

    public void recordLoss(

            @Negative
            BigDecimal amount) {

    }

}
```

---

Call:

```java
recordLoss(new BigDecimal("500"));
```

---

Result:

```java
ConstraintViolationException
```

---

# @Negative vs @Max(-1)

Common Interview Question.

---

## @Negative

Checks:

```java
value < 0
```

---

Valid:

```java
-1
-10
```

---

Invalid:

```java
0
1
```

---

## @Max(-1)

Checks:

```java
value <= -1
```

---

Valid:

```java
-1
-10
```

---

Invalid:

```java
0
1
```

---

For integer values:

```java
@Negative
```

and

```java
@Max(-1)
```

behave similarly.

---

# Key Difference

`@Negative` is clearer and works naturally with:

```java
Double
Float
BigDecimal
```

---

Example:

```java
@Negative
private BigDecimal amount;
```

Valid:

```java
-0.01
```

---

Equivalent:

```java
@Max(-1)
```

would incorrectly reject:

```java
-0.01
```

---

# @Negative vs @NegativeOrZero

Very Important Interview Question.

---

## @Negative

Checks:

```java
value < 0
```

---

Valid:

```java
-1
```

---

Invalid:

```java
0
```

---

## @NegativeOrZero

Checks:

```java
value <= 0
```

---

Valid:

```java
-1
0
```

---

Comparison:

| Value | @Negative | @NegativeOrZero |
|---------|-----------|----------------|
| -1 | ✅ | ✅ |
| 0 | ❌ | ✅ |
| 1 | ❌ | ❌ |

---

# Internal Implementation

Hibernate Validator provides:

```java
NegativeValidatorForInteger
```

---

```java
NegativeValidatorForLong
```

---

```java
NegativeValidatorForDouble
```

---

```java
NegativeValidatorForBigDecimal
```

---

depending on the field type.

---

# Internal Validation Logic

Example:

```java
@Negative
private Integer amount;
```

Internally:

```java
return value < 0;
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
NegativeValidator
   │
   ▼
value < 0 ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Negative to Reject Null

Wrong:

```java
@Negative
private Integer amount;
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
@Negative
```

---

# Mistake 2

Expecting Zero to Pass

Wrong:

```java
@Negative
private Integer balance;
```

---

Value:

```java
0
```

fails validation.

---

Use:

```java
@NegativeOrZero
```

if zero should be allowed.

---

# Mistake 3

Using on String

Wrong:

```java
@Negative
private String amount;
```

---

Not supported.

---

Use numeric types.

---

# Common Interview Questions

## What is @Negative?

Validates that a numeric value is strictly less than zero.

---

## Does @Negative allow zero?

No.

---

## Does @Negative allow positive values?

No.

---

## Does @Negative reject null?

No.

---

## Difference Between @Negative and @NegativeOrZero?

`@Negative` rejects zero.

`@NegativeOrZero` accepts zero.

---

## Difference Between @Negative and @Max(-1)?

For integers they are similar.

`@Negative` works better for decimal values.

---

## Can @Negative be used on BigDecimal?

Yes.

---

# Real-World Production Example

```java
public class FinancialAdjustmentRequest {

    @NotNull

    @Negative(
        message = "Adjustment amount must be negative"
    )
    private BigDecimal adjustmentAmount;

}
```

---

Request:

```json
{
    "adjustmentAmount": 100
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "adjustmentAmount":
    "Adjustment amount must be negative"
}
```

---

# Key Points To Remember

- `@Negative` validates that a numeric value is strictly less than zero.
- Zero is NOT allowed.
- Positive values are NOT allowed.
- Supports integer and decimal numeric types.
- Does NOT reject null values.
- Use `@NotNull` when null should fail validation.
- Commonly used for debts, losses, adjustments, and negative balances.
- Better than `@Max(-1)` for decimal values.
- Uses type-specific `NegativeValidator` implementations internally.
- Frequently asked Bean Validation and Spring Boot interview annotation.