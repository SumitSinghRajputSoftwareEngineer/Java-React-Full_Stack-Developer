
## Definition

`@DiscriminatorValue` is a JPA annotation used to specify the **value stored in the discriminator column** for a particular subclass in an inheritance hierarchy.

It tells Hibernate/JPA:

> When you see this value in the discriminator column, create an instance of this subclass.

Package:

```java
import jakarta.persistence.DiscriminatorValue;
```

Prior to Spring Boot 3:

```java
import javax.persistence.DiscriminatorValue;
```

---

# Why Do We Need @DiscriminatorValue?

Consider inheritance:

```text
Employee
    ↑
 ┌──┴──────────┐
FullTime   Contract
```

Using:

```java
@Inheritance(
    strategy =
        InheritanceType.SINGLE_TABLE
)
```

---

All data is stored in:

```sql
EMPLOYEE
```

table.

---

Question:

```text
How does Hibernate know
which row belongs to
FullTimeEmployee
or
ContractEmployee?
```

---

Answer:

```java
@DiscriminatorColumn
```

stores the type.

---

Question:

```text
What value should be stored
for each subclass?
```

---

Answer:

```java
@DiscriminatorValue
```

---

# Relationship With @DiscriminatorColumn

Parent:

```java
@DiscriminatorColumn(
    name = "employee_type"
)
```

creates:

```sql
EMPLOYEE_TYPE
```

column.

---

Child:

```java
@DiscriminatorValue(
    "FULL_TIME"
)
```

stores:

```text
FULL_TIME
```

inside that column.

---

# Basic Syntax

```java
@DiscriminatorValue(
    "FULL_TIME"
)
```

---

Applied on:

```java
Subclass
```

---

Example:

```java
@Entity

@DiscriminatorValue(
    "FULL_TIME"
)
public class FullTimeEmployee
       extends Employee {
}
```

---

# Complete Example

## Parent

```java
@Entity

@Inheritance(
    strategy =
        InheritanceType.SINGLE_TABLE
)

@DiscriminatorColumn(
    name = "employee_type"
)
public abstract class Employee {

    @Id

    @GeneratedValue
    private Long id;

    private String name;
}
```

---

## Child 1

```java
@Entity

@DiscriminatorValue(
    "FULL_TIME"
)
public class FullTimeEmployee
       extends Employee {

    private Double salary;
}
```

---

## Child 2

```java
@Entity

@DiscriminatorValue(
    "CONTRACT"
)
public class ContractEmployee
       extends Employee {

    private Double hourlyRate;
}
```

---

# Generated Table

```sql
EMPLOYEE
(
 ID,
 NAME,

 SALARY,
 HOURLY_RATE,

 EMPLOYEE_TYPE
)
```

---

Example Data

| ID | NAME | SALARY | HOURLY_RATE | EMPLOYEE_TYPE |
|----|------|---------|------------|---------------|
| 1 | Sumit | 120000 | NULL | FULL_TIME |
| 2 | Raj | NULL | 1000 | CONTRACT |

---

# How Hibernate Uses It

When Hibernate reads:

```sql
EMPLOYEE_TYPE='FULL_TIME'
```

---

It creates:

```java
FullTimeEmployee
```

object.

---

When Hibernate reads:

```sql
EMPLOYEE_TYPE='CONTRACT'
```

---

It creates:

```java
ContractEmployee
```

object.

---

Automatically.

---

# What Happens Without @DiscriminatorValue?

Example:

```java
@Entity
public class Car
       extends Vehicle {
}
```

---

Hibernate uses:

```text
Entity Name
```

by default.

---

Stored:

```text
Car
```

---

Example:

| VEHICLE_TYPE |
|-------------|
| Car |
| Bike |

---

Works fine.

---

But production systems usually define values explicitly.

---

# Why Explicit Values Are Better

Instead of:

```text
FullTimeEmployee
```

store:

```text
FULL_TIME
```

---

Benefits:

```text
Readable
Stable
Independent Of Class Names
```

---

If class renamed:

```java
FullTimeEmployee
```

↓

```java
PermanentEmployee
```

---

Database remains:

```text
FULL_TIME
```

---

No migration needed.

---

# String Example (Most Common)

```java
@DiscriminatorValue(
    "CAR"
)
```

---

Database:

| VEHICLE_TYPE |
|-------------|
| CAR |

---

# Integer Example

Parent:

```java
@DiscriminatorColumn(
    name = "type_id",

    discriminatorType =
        DiscriminatorType.INTEGER
)
```

---

Child:

```java
@DiscriminatorValue("1")
```

---

Child:

```java
@DiscriminatorValue("2")
```

---

Database:

| TYPE_ID |
|---------|
| 1 |
| 2 |

---

Rarely used.

---

# Char Example

Parent:

```java
@DiscriminatorColumn(
    discriminatorType =
        DiscriminatorType.CHAR
)
```

---

Child:

```java
@DiscriminatorValue("C")
```

---

Child:

```java
@DiscriminatorValue("B")
```

---

Database:

| TYPE |
|------|
| C |
| B |

---

Rarely used.

---

# Real Production Example

## Vehicle

```java
@Entity

@Inheritance(
    strategy =
        InheritanceType.SINGLE_TABLE
)

@DiscriminatorColumn(
    name = "vehicle_type"
)
public abstract class Vehicle {

    @Id

    @GeneratedValue
    private Long id;

    private String brand;
}
```

---

## Car

```java
@Entity

@DiscriminatorValue(
    "CAR"
)
public class Car
       extends Vehicle {

    private Integer seats;
}
```

---

## Bike

```java
@Entity

@DiscriminatorValue(
    "BIKE"
)
public class Bike
       extends Vehicle {

    private Boolean sportsBike;
}
```

---

Database:

| ID | BRAND | VEHICLE_TYPE |
|----|--------|-------------|
| 1 | BMW | CAR |
| 2 | Yamaha | BIKE |

---

# Internal SQL

Insert Car:

```sql
INSERT INTO VEHICLE
(
 BRAND,
 SEATS,
 VEHICLE_TYPE
)
VALUES
(
 'BMW',
 5,
 'CAR'
)
```

---

Insert Bike:

```sql
INSERT INTO VEHICLE
(
 BRAND,
 SPORTS_BIKE,
 VEHICLE_TYPE
)
VALUES
(
 'Yamaha',
 TRUE,
 'BIKE'
)
```

---

# Internal Hibernate Processing

Metadata stored:

```text
Subclass
        ↓
Discriminator Value
```

Example:

```text
CAR
 ↓
Car.class
```

---

```text
BIKE
 ↓
Bike.class
```

---

When reading rows:

```text
Read discriminator value
         ↓
Lookup subclass
         ↓
Instantiate object
```

---

# @DiscriminatorColumn vs @DiscriminatorValue

Interview Favorite.

---

## @DiscriminatorColumn

Defines:

```text
Column Name
Column Type
```

---

Example:

```java
@DiscriminatorColumn(
    name="employee_type"
)
```

---

Creates:

```sql
EMPLOYEE_TYPE
```

---

## @DiscriminatorValue

Defines:

```text
Stored Value
```

---

Example:

```java
@DiscriminatorValue(
    "FULL_TIME"
)
```

---

Stored:

```text
FULL_TIME
```

---

Comparison:

| Annotation | Purpose |
|------------|----------|
| @DiscriminatorColumn | Creates Type Column |
| @DiscriminatorValue | Stores Type Value |

---

# Common Mistakes

## Mistake 1

Duplicate Values

Wrong:

```java
@DiscriminatorValue("EMP")
```

for multiple subclasses.

---

Hibernate cannot distinguish rows.

---

# Mistake 2

Using Integer Type Incorrectly

Wrong:

```java
DiscriminatorType.INTEGER
```

and

```java
@DiscriminatorValue("CAR")
```

---

Type mismatch.

---

# Mistake 3

Changing Existing Values

Example:

```java
FULL_TIME
```

changed to:

```java
FT
```

---

Existing database rows break.

---

Plan migrations carefully.

---

# Common Interview Questions

## What is @DiscriminatorValue?

Specifies the value stored in discriminator column for a subclass.

---

## Where is it used?

Inheritance hierarchies.

Mostly:

```java
InheritanceType.SINGLE_TABLE
```

---

## Is it mandatory?

No.

Hibernate uses class name by default.

---

## Why define it explicitly?

Readable and stable database values.

---

## Can two subclasses have same discriminator value?

No.

Must be unique.

---

## Does it work without @DiscriminatorColumn?

Yes.

Hibernate creates default:

```sql
DTYPE
```

column.

---

# Enterprise Best Practice

Always define:

```java
@DiscriminatorColumn
```

and

```java
@DiscriminatorValue
```

explicitly.

---

Example:

```java
@DiscriminatorColumn(
    name = "employee_type"
)
```

---

```java
@DiscriminatorValue(
    "FULL_TIME"
)
```

---

Advantages:

```text
Readable Schema
Maintainability
Class Renaming Safety
Better Database Design
```

---

# Key Points To Remember

- `@DiscriminatorValue` defines the value stored for a subclass.
- Used with inheritance mappings.
- Most common with `InheritanceType.SINGLE_TABLE`.
- Works together with `@DiscriminatorColumn`.
- Hibernate uses it to determine which subclass to instantiate.
- Default value is the entity class name.
- Must be unique across subclasses.
- Supports STRING, CHAR, and INTEGER discriminator types.
- Important Hibernate inheritance interview topic.

---

# Quick Interview Shortcut

```java
@DiscriminatorColumn(
    name = "employee_type"
)
```

creates:

```sql
EMPLOYEE_TYPE
```

---

```java
@DiscriminatorValue(
    "FULL_TIME"
)
```

stores:

```text
FULL_TIME
```

---

Meaning:

```text
EMPLOYEE_TYPE = FULL_TIME
          ↓
Create FullTimeEmployee

EMPLOYEE_TYPE = CONTRACT
          ↓
Create ContractEmployee
```

This is how Hibernate identifies and instantiates the correct subclass when using inheritance mapping.