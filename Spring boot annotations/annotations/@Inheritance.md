
## Definition

`@Inheritance` is a JPA annotation used to map an **object-oriented inheritance hierarchy** into relational database tables.

It tells Hibernate/JPA:

> How should parent and child classes be stored in database tables?

Package:

```java
import jakarta.persistence.Inheritance;
import jakarta.persistence.InheritanceType;
```

Prior to Spring Boot 3:

```java
import javax.persistence.Inheritance;
import javax.persistence.InheritanceType;
```

---

# Why Do We Need @Inheritance?

Java supports inheritance:

```java
Vehicle
   ↑
 ┌─┴───────┐
Car      Bike
```

---

Example:

```java
public class Vehicle {

    private Long id;
    private String brand;
}
```

---

```java
public class Car extends Vehicle {

    private int seats;
}
```

---

```java
public class Bike extends Vehicle {

    private boolean sportsBike;
}
```

---

Question:

```text
How should Hibernate store this hierarchy?
```

---

Solution:

```java
@Inheritance
```

---

# Basic Syntax

```java
@Entity

@Inheritance(
    strategy =
       InheritanceType.SINGLE_TABLE
)
public class Vehicle {
}
```

---

JPA provides:

```java
SINGLE_TABLE
JOINED
TABLE_PER_CLASS
```

---

# Strategy 1: SINGLE_TABLE

Most common.

---

```java
@Inheritance(
    strategy =
        InheritanceType.SINGLE_TABLE
)
```

---

# Example

### Parent

```java
@Entity

@Inheritance(
    strategy =
        InheritanceType.SINGLE_TABLE
)
public class Vehicle {

    @Id
    private Long id;

    private String brand;
}
```

---

### Child

```java
@Entity
public class Car extends Vehicle {

    private int seats;
}
```

---

### Child

```java
@Entity
public class Bike extends Vehicle {

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

 DTYPE
)
```

---

Example Data

| ID | BRAND | SEATS | SPORTS_BIKE | DTYPE |
|----|--------|--------|------------|--------|
| 1 | BMW | 5 | NULL | Car |
| 2 | Yamaha | NULL | TRUE | Bike |

---

Notice:

```text
Single Table
```

stores everything.

---

# DTYPE Column

Hibernate automatically creates:

```sql
DTYPE
```

---

Stores:

```text
Car
Bike
Truck
```

---

Identifies child type.

---

# Advantages

```text
Fast Queries
No Joins
Simple Design
```

---

# Disadvantages

```text
Lots of NULL Columns
Poor Normalization
```

---

# Strategy 2: JOINED

Most common in enterprise applications.

---

```java
@Inheritance(
    strategy =
        InheritanceType.JOINED
)
```

---

# Parent

```java
@Entity

@Inheritance(
    strategy =
        InheritanceType.JOINED
)
public class Vehicle {

    @Id
    private Long id;

    private String brand;
}
```

---

# Car

```java
@Entity
public class Car extends Vehicle {

    private int seats;
}
```

---

# Bike

```java
@Entity
public class Bike extends Vehicle {

    private boolean sportsBike;
}
```

---

# Generated Tables

### VEHICLE

```sql
ID
BRAND
```

---

### CAR

```sql
ID
SEATS
```

---

### BIKE

```sql
ID
SPORTS_BIKE
```

---

Data:

### VEHICLE

| ID | BRAND |
|----|--------|
| 1 | BMW |
| 2 | Yamaha |

---

### CAR

| ID | SEATS |
|----|--------|
| 1 | 5 |

---

### BIKE

| ID | SPORTS_BIKE |
|----|-------------|
| 2 | TRUE |

---

# Querying Car

Hibernate executes:

```sql
SELECT *
FROM VEHICLE v

JOIN CAR c
ON v.ID=c.ID
```

---

# Advantages

```text
Normalized Design
No Null Columns
Cleaner Schema
```

---

# Disadvantages

```text
More Joins
Slower Queries
```

---

# Strategy 3: TABLE_PER_CLASS

Each class gets its own table.

---

```java
@Inheritance(
    strategy =
        InheritanceType.TABLE_PER_CLASS
)
```

---

# Generated Tables

### CAR

```sql
ID
BRAND
SEATS
```

---

### BIKE

```sql
ID
BRAND
SPORTS_BIKE
```

---

No Vehicle table.

---

Example:

### CAR

| ID | BRAND | SEATS |
|----|--------|--------|
| 1 | BMW | 5 |

---

### BIKE

| ID | BRAND | SPORTS_BIKE |
|----|--------|-------------|
| 2 | Yamaha | TRUE |

---

# Advantages

```text
No Joins
No Null Columns
```

---

# Disadvantages

```text
Data Duplication
Complex Polymorphic Queries
```

---

Least used strategy.

---

# InheritanceType Comparison

| Feature | SINGLE_TABLE | JOINED | TABLE_PER_CLASS |
|-----------|-------------|---------|----------------|
| Tables | 1 | Multiple | Multiple |
| Joins | ❌ | ✅ | ❌ |
| Null Columns | ✅ | ❌ | ❌ |
| Performance | Fast | Medium | Medium |
| Normalization | Poor | Good | Poor |
| Production Usage | High | Very High | Low |

---

# @DiscriminatorColumn

Used with:

```java
SINGLE_TABLE
```

---

Example:

```java
@Inheritance(
    strategy =
        InheritanceType.SINGLE_TABLE
)

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

# @DiscriminatorValue

Custom child values.

---

Car:

```java
@Entity

@DiscriminatorValue("CAR")
public class Car extends Vehicle {
}
```

---

Bike:

```java
@Entity

@DiscriminatorValue("BIKE")
public class Bike extends Vehicle {
}
```

---

Stored:

| VEHICLE_TYPE |
|--------------|
| CAR |
| BIKE |

---

# Complete Production Example

## Parent

```java
@Entity

@Inheritance(
    strategy =
        InheritanceType.JOINED
)
public abstract class Employee {

    @Id

    @GeneratedValue
    private Long id;

    private String name;
}
```

---

## FullTimeEmployee

```java
@Entity
public class FullTimeEmployee
       extends Employee {

    private Double salary;
}
```

---

## ContractEmployee

```java
@Entity
public class ContractEmployee
       extends Employee {

    private Double hourlyRate;
}
```

---

Generated Tables:

### EMPLOYEE

```sql
ID
NAME
```

---

### FULL_TIME_EMPLOYEE

```sql
ID
SALARY
```

---

### CONTRACT_EMPLOYEE

```sql
ID
HOURLY_RATE
```

---

# Polymorphic Queries

JPA supports:

```java
List<Employee> employees =
    employeeRepository.findAll();
```

---

Returns:

```text
FullTimeEmployee
ContractEmployee
```

automatically.

---

Very powerful feature.

---

# Internal Hibernate Processing

Hibernate stores metadata:

```text
Parent Entity
Child Entities
Inheritance Strategy
Discriminator Information
```

---

Used during:

```text
Insert
Update
Delete
Select
Polymorphic Queries
```

---

# Common Mistakes

## Mistake 1

Using TABLE_PER_CLASS Unnecessarily

Results:

```text
Data Duplication
Complex Queries
```

---

Usually avoid.

---

# Mistake 2

Using SINGLE_TABLE With Huge Hierarchies

Results:

```text
Many Null Columns
```

---

Table becomes massive.

---

# Mistake 3

Forgetting Abstract Parent

Wrong:

```java
@Entity
public class Vehicle
```

---

Sometimes parent should be:

```java
abstract class Vehicle
```

---

Prevents accidental creation.

---

# Common Interview Questions

## What is @Inheritance?

Defines inheritance mapping strategy.

---

## What strategies are available?

```java
SINGLE_TABLE
JOINED
TABLE_PER_CLASS
```

---

## Default strategy?

If not specified:

```java
SINGLE_TABLE
```

---

## Which strategy is fastest?

```java
SINGLE_TABLE
```

---

## Which strategy is most normalized?

```java
JOINED
```

---

## Which strategy is least used?

```java
TABLE_PER_CLASS
```

---

## What is DTYPE?

Discriminator column used in SINGLE_TABLE.

---

## What is @DiscriminatorValue?

Custom child identifier value.

---

# Real Enterprise Recommendation

### Use SINGLE_TABLE When

```text
Small Hierarchy
Performance Critical
```

---

### Use JOINED When

```text
Large Enterprise Systems
Clean Database Design
```

---

### Avoid TABLE_PER_CLASS

Unless specifically needed.

---

# Key Points To Remember

- `@Inheritance` maps Java inheritance to database tables.
- Three strategies: `SINGLE_TABLE`, `JOINED`, `TABLE_PER_CLASS`.
- `SINGLE_TABLE` uses one table and discriminator column.
- `JOINED` creates parent and child tables.
- `TABLE_PER_CLASS` creates one table per child class.
- `@DiscriminatorColumn` customizes type column.
- `@DiscriminatorValue` customizes child values.
- Supports polymorphic queries.
- `JOINED` is common in enterprise applications.
- Important advanced JPA/Hibernate interview topic.

---

# Quick Interview Shortcut

```java
@Entity

@Inheritance(
    strategy =
        InheritanceType.JOINED
)
public abstract class Employee {
}
```

Meaning:

```text
Employee
     ↑
 ┌───┴─────────┐
FullTime   Contract
     ↓
Separate Tables
     ↓
Joined Using Primary Key
```

This is one of the most commonly used inheritance mapping strategies in enterprise Spring Boot and Hibernate applications.