
## Definition

`@DiscriminatorColumn` is a JPA annotation used with the **SINGLE_TABLE inheritance strategy** to define the column that identifies which subclass a row belongs to.

It tells Hibernate/JPA:

> Store the type of entity in this column so Hibernate knows which child class to create.

Package:

```java
import jakarta.persistence.DiscriminatorColumn;
import jakarta.persistence.DiscriminatorType;
```

Prior to Spring Boot 3:

```java
import javax.persistence.DiscriminatorColumn;
import javax.persistence.DiscriminatorType;
```

---

# Why Do We Need @DiscriminatorColumn?

Suppose we have inheritance:

```text
Vehicle
   ↑
 ┌─┴─────┐
Car    Bike
```

---

Using:

```java
@Inheritance(
    strategy =
      InheritanceType.SINGLE_TABLE
)
```

---

All data goes into:

```sql
VEHICLE
```

table.

---

Question:

```text
How does Hibernate know
whether a row is Car or Bike?
```

---

Answer:

```java
@DiscriminatorColumn
```

---

# Without Discriminator Column

Imagine:

| ID | BRAND |
|----|--------|
| 1 | BMW |
| 2 | Yamaha |

---

Hibernate cannot determine:

```text
Car ?
Bike ?
Truck ?
```

---

Need type information.

---

# With Discriminator Column

Table:

| ID | BRAND | VEHICLE_TYPE |
|----|--------|-------------|
| 1 | BMW | CAR |
| 2 | Yamaha | BIKE |

---

Now Hibernate knows:

```text
CAR → Car Entity
BIKE → Bike Entity
```

---

# Basic Syntax

```java
@DiscriminatorColumn(
    name = "vehicle_type"
)
```

---

Usually placed on parent entity.

---

Example:

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
}
```

---

# Default Behavior

If omitted:

```java
@Inheritance(
    strategy =
        InheritanceType.SINGLE_TABLE
)
```

---

Hibernate automatically creates:

```sql
DTYPE
```

column.

---

Example:

| ID | BRAND | DTYPE |
|----|--------|-------|
| 1 | BMW | Car |
| 2 | Yamaha | Bike |

---

# Custom Column Name

Example:

```java
@DiscriminatorColumn(
    name = "vehicle_type"
)
```

---

Generated:

```sql
VEHICLE_TYPE
```

instead of:

```sql
DTYPE
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
    name = "vehicle_type"
)
public abstract class Vehicle {

    @Id
    private Long id;

    private String brand;
}
```

---

## Car

```java
@Entity
public class Car
       extends Vehicle {

    private int seats;
}
```

---

## Bike

```java
@Entity
public class Bike
       extends Vehicle {

    private boolean sportsBike;
}
```

---

# Generated Table

```sql
VEHICLE
(
 ID,
 BRAND,

 SEATS,
 SPORTS_BIKE,

 VEHICLE_TYPE
)
```

---

Example Data

| ID | BRAND | SEATS | SPORTS_BIKE | VEHICLE_TYPE |
|----|--------|--------|------------|-------------|
| 1 | BMW | 5 | NULL | Car |
| 2 | Yamaha | NULL | TRUE | Bike |

---

# How Hibernate Uses It

When reading:

```sql
SELECT *
FROM VEHICLE
```

---

Hibernate checks:

```sql
VEHICLE_TYPE
```

---

If:

```text
Car
```

creates:

```java
Car object
```

---

If:

```text
Bike
```

creates:

```java
Bike object
```

---

Automatically.

---

# Discriminator Types

Important Interview Topic.

---

Three supported types:

```java
STRING
CHAR
INTEGER
```

---

# STRING (Most Common)

```java
@DiscriminatorColumn(
    name = "vehicle_type",

    discriminatorType =
        DiscriminatorType.STRING
)
```

---

Stored:

```text
CAR
BIKE
TRUCK
```

---

Database:

| VEHICLE_TYPE |
|-------------|
| CAR |
| BIKE |

---

# CHAR

```java
@DiscriminatorColumn(
    name = "type",

    discriminatorType =
        DiscriminatorType.CHAR
)
```

---

Stored:

| TYPE |
|------|
| C |
| B |

---

# INTEGER

```java
@DiscriminatorColumn(
    name = "type_id",

    discriminatorType =
        DiscriminatorType.INTEGER
)
```

---

Stored:

| TYPE_ID |
|---------|
| 1 |
| 2 |

---

Rarely used.

---

# @DiscriminatorValue

Usually paired with:

```java
@DiscriminatorColumn
```

---

Example:

```java
@Entity

@DiscriminatorValue("CAR")
public class Car
       extends Vehicle {
}
```

---

```java
@Entity

@DiscriminatorValue("BIKE")
public class Bike
       extends Vehicle {
}
```

---

Stored:

| VEHICLE_TYPE |
|-------------|
| CAR |
| BIKE |

---

# Full Production Example

## Parent

```java
@Entity

@Inheritance(
    strategy =
        InheritanceType.SINGLE_TABLE
)

@DiscriminatorColumn(
    name = "employee_type",

    discriminatorType =
        DiscriminatorType.STRING
)
public abstract class Employee {

    @Id

    @GeneratedValue
    private Long id;

    private String name;
}
```

---

## Full Time

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

## Contract

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

Generated Table:

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

# Internal SQL

Insert Full Time Employee:

```sql
INSERT INTO EMPLOYEE
(
 NAME,
 SALARY,
 EMPLOYEE_TYPE
)
VALUES
(
 'Sumit',
 120000,
 'FULL_TIME'
)
```

---

Insert Contract Employee:

```sql
INSERT INTO EMPLOYEE
(
 NAME,
 HOURLY_RATE,
 EMPLOYEE_TYPE
)
VALUES
(
 'Raj',
 1000,
 'CONTRACT'
)
```

---

# Internal Hibernate Processing

Hibernate stores metadata:

```text
Parent Class
Child Classes
Discriminator Column
Discriminator Values
```

---

During SELECT:

```text
Read Discriminator
      ↓
Determine Child Class
      ↓
Instantiate Object
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
    name = "employee_type"
)
```

---

Creates:

```sql
EMPLOYEE_TYPE
```

column.

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

inside column.

---

Comparison:

| Annotation | Purpose |
|------------|----------|
| @DiscriminatorColumn | Defines Column |
| @DiscriminatorValue | Defines Value |

---

# Common Mistakes

## Mistake 1

Using @DiscriminatorColumn With JOINED

Usually unnecessary.

---

Best suited for:

```java
InheritanceType.SINGLE_TABLE
```

---

# Mistake 2

Duplicate Discriminator Values

Wrong:

```java
@DiscriminatorValue("EMP")
```

for multiple subclasses.

---

Hibernate cannot distinguish entities.

---

# Mistake 3

Using Integer Type Without Values

Wrong:

```java
DiscriminatorType.INTEGER
```

without proper:

```java
@DiscriminatorValue("1")
```

---

Mapping errors.

---

# Common Interview Questions

## What is @DiscriminatorColumn?

Defines the column used to identify subclass types.

---

## Which inheritance strategy uses it?

Mostly:

```java
InheritanceType.SINGLE_TABLE
```

---

## Default column name?

```sql
DTYPE
```

---

## Default discriminator type?

```java
STRING
```

---

## Supported types?

```java
STRING
CHAR
INTEGER
```

---

## What is @DiscriminatorValue?

Defines the value stored for each subclass.

---

# Real Enterprise Recommendation

For:

```java
SINGLE_TABLE
```

always define:

```java
@DiscriminatorColumn
```

and

```java
@DiscriminatorValue
```

explicitly.

---

Benefits:

```text
Readable Database
Better Maintenance
Clear Mapping
```

---

# Key Points To Remember

- `@DiscriminatorColumn` defines the column that identifies subclass types.
- Mainly used with `InheritanceType.SINGLE_TABLE`.
- Default column name is `DTYPE`.
- Supports `STRING`, `CHAR`, and `INTEGER`.
- Works together with `@DiscriminatorValue`.
- Helps Hibernate instantiate the correct subclass.
- Stored in the parent table.
- Important inheritance-mapping interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Inheritance` | Defines Inheritance Strategy |
| `@DiscriminatorColumn` | Defines Type Column |
| `@DiscriminatorValue` | Defines Stored Value |
| `@MappedSuperclass` | Shares Fields Only |
| `@Entity` | Persistent Class |

---

# Interview Shortcut

```java
@Inheritance(
    strategy =
        InheritanceType.SINGLE_TABLE
)

@DiscriminatorColumn(
    name = "employee_type"
)
```

Meaning:

```text
All Child Entities
Stored In One Table
        ↓
employee_type Column
Stores Entity Type
        ↓
Hibernate Uses It
To Create Correct Subclass
```

Example:

```text
FULL_TIME → FullTimeEmployee
CONTRACT  → ContractEmployee
```

This is the standard way Hibernate distinguishes subclasses when using the SINGLE_TABLE inheritance strategy.