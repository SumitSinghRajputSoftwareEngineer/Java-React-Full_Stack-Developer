
## Definition

`@NegativeOrZero` is a Bean Validation annotation used to ensure that a numeric value is:

```text
Less than zero
OR
Equal to zero
```

It tells the validation framework:

> The value must be <= 0.

Package:

```java
import jakarta.validation.constraints.NegativeOrZero;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.NegativeOrZero;
```

---

# Why Do We Need @NegativeOrZero?

Many business scenarios require that a value must never become positive.

Examples:

```text
Outstanding Debt
Loss Amount
Credit Balance
Temperature Below Freezing
Remaining Liability
Account Deficit
```

---

Example:

```json
{
    "debtAmount": 500
}
```

should be rejected.

---

But:

```json
{
    "debtAmount": 0
}
```

should be accepted.

---

Solution:

```java
@NegativeOrZero
```

---

# What Does @NegativeOrZero Validate?

Internally:

```java
value <= 0
```

---

Valid:

```java
0
-1
-10
-999
-0.01
```

---

Invalid:

```java
1
10
100
```

---

# Supported Types

`@NegativeOrZero` supports:

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
public class FinanceRequest {

    @NegativeOrZero
    private BigDecimal debtAmount;

}
```

---

Valid:

```json
{
    "debtAmount":0
}
```

---

Valid:

```json
{
    "debtAmount":-100
}
```

---

Invalid:

```json
{
    "debtAmount":100
}
```

---

# Important Interview Question

Does @NegativeOrZero reject null?

Answer:

```text
NO
```

---

Example:

```java
@NegativeOrZero
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

Bean Validation specification treats:

```java
null
```

as valid.

---

If null should fail:

```java
@NotNull
@NegativeOrZero
private Integer adjustment;
```

---

# Debt Validation Example

```java
@NotNull

@NegativeOrZero
private BigDecimal debtAmount;
```

---

Valid:

```java
0
-500
-1000
```

---

Invalid:

```java
100
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

# Temperature Example

```java
@NegativeOrZero
private Double temperature;
```

---

Valid:

```java
0
-5.5
-20
```

---

Invalid:

```java
5
```

---

# Credit Balance Example

```java
@NegativeOrZero
private BigDecimal creditBalance;
```

---

Valid:

```java
0
-250.75
```

---

Invalid:

```java
250.75
```

---

# Custom Message

Default:

```text
must be less than or equal to 0
```

---

Custom:

```java
@NegativeOrZero(
    message = "Balance cannot be positive"
)
private BigDecimal balance;
```

---

Error:

```json
{
    "message":
    "Balance cannot be positive"
}
```

---

# Using @NegativeOrZero with @Valid

DTO:

```java
public class LiabilityRequest {

    @NegativeOrZero
    private BigDecimal liability;

}
```

---

Controller:

```java
@PostMapping("/liability")
public String save(

        @Valid
        @RequestBody
        LiabilityRequest request) {

    return "saved";
}
```

---

Request:

```json
{
    "liability":100
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

    public String calculate(

            @NegativeOrZero
            @RequestParam
            Double lossAmount) {

        return "success";
    }

}
```

---

Valid:

```http
?lossAmount=0
```

---

Valid:

```http
?lossAmount=-50
```

---

Invalid:

```http
?lossAmount=50
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

    public void updateLoss(

            @NegativeOrZero
            BigDecimal amount) {

    }

}
```

---

Call:

```java
updateLoss(new BigDecimal("100"));
```

---

Result:

```java
ConstraintViolationException
```

---

# @NegativeOrZero vs @Negative

Most Frequently Asked Interview Question.

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

## @NegativeOrZero

Checks:

```java
value <= 0
```

---

Valid:

```java
0
-1
-10
```

---

Invalid:

```java
1
```

---

Comparison:

| Value | @Negative | @NegativeOrZero |
|---------|-----------|----------------|
| -1 | ✅ | ✅ |
| 0 | ❌ | ✅ |
| 1 | ❌ | ❌ |

---

# @NegativeOrZero vs @Max(0)

Another Interview Question.

---

## @Max(0)

Checks:

```java
value <= 0
```

---

Valid:

```java
0
-1
-10
```

---

Invalid:

```java
1
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
0
-1
```

---

Invalid:

```java
1
```

---

For integer values:

```java
@Max(0)
```

and

```java
@NegativeOrZero
```

behave similarly.

---

# Key Difference

`@NegativeOrZero` works naturally with:

```java
Double
Float
BigDecimal
```

---

Example:

```java
@NegativeOrZero
private BigDecimal amount;
```

Valid:

```java
-0.01
```

---

Using:

```java
@Max(0)
```

is less expressive for decimal-oriented business validations.

---

# Internal Implementation

Hibernate Validator provides:

```java
NegativeOrZeroValidatorForInteger
```

---

```java
NegativeOrZeroValidatorForLong
```

---

```java
NegativeOrZeroValidatorForDouble
```

---

```java
NegativeOrZeroValidatorForBigDecimal
```

---

depending on field type.

---

# Internal Validation Logic

Example:

```java
@NegativeOrZero
private Integer balance;
```

Internally:

```java
return value <= 0;
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
NegativeOrZeroValidator
   │
   ▼
value <= 0 ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @NegativeOrZero to Reject Null

Wrong:

```java
@NegativeOrZero
private Integer balance;
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
@NegativeOrZero
```

---

# Mistake 2

Using @Negative Instead

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

If zero should be accepted:

```java
@NegativeOrZero
```

---

# Mistake 3

Using on String

Wrong:

```java
@NegativeOrZero
private String balance;
```

---

Not supported.

---

Use numeric types.

---

# Common Interview Questions

## What is @NegativeOrZero?

Validates that a numeric value is less than or equal to zero.

---

## Does @NegativeOrZero allow zero?

Yes.

---

## Does @NegativeOrZero allow positive values?

No.

---

## Does @NegativeOrZero reject null?

No.

---

## Difference Between @Negative and @NegativeOrZero?

`@Negative` rejects zero.

`@NegativeOrZero` accepts zero.

---

## Difference Between @NegativeOrZero and @Max(0)?

For integer values they behave similarly.

`@NegativeOrZero` is more expressive and commonly preferred.

---

## Can @NegativeOrZero be used on BigDecimal?

Yes.

---

# Real-World Production Example

```java
public class FinancialRequest {

    @NotNull

    @NegativeOrZero(
        message = "Debt cannot be positive"
    )
    private BigDecimal debtAmount;

    @NotNull

    @NegativeOrZero(
        message = "Loss amount cannot be positive"
    )
    private BigDecimal lossAmount;

}
```

---

Request:

```json
{
    "debtAmount":100,
    "lossAmount":50
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "debtAmount":"Debt cannot be positive",
    "lossAmount":"Loss amount cannot be positive"
}
```

---

# Key Points To Remember

- `@NegativeOrZero` validates that a numeric value is less than or equal to zero.
- Zero is allowed.
- Positive values are not allowed.
- Supports integer and decimal numeric types.
- Does NOT reject null values.
- Use `@NotNull` if null should fail validation.
- Commonly used for debts, liabilities, losses, and negative balances.
- Similar to `@Max(0)` for integer values.
- Uses type-specific `NegativeOrZeroValidator` implementations internally.
- Frequently asked Spring Boot and Bean Validation interview annotation.