
## Definition

`@JdbcTest` is a Spring Boot test annotation used to test the **JDBC layer** of an application.

It tells Spring Boot:

> Load only JDBC-related components such as DataSource, JdbcTemplate, NamedParameterJdbcTemplate, and transaction management for testing database operations.

Package:

```java
import org.springframework.boot.test.autoconfigure.jdbc.JdbcTest;
```

---

# Why Do We Need @JdbcTest?

Suppose your application uses:

```text
Controller
    ↓
Service
    ↓
DAO
    ↓
JdbcTemplate
    ↓
Database
```

---

You only want to test:

```text
SQL Queries
JdbcTemplate
Stored Procedures
ResultSet Mapping
DAO Layer
```

---

Loading:

```java
@SpringBootTest
```

would load:

```text
Controllers
Services
Security
Caching
Messaging
Everything
```

---

Unnecessary.

---

Solution:

```java
@JdbcTest
```

---

# What Does @JdbcTest Load?

Loaded:

```text
DataSource
JdbcTemplate
NamedParameterJdbcTemplate
TransactionManager
Database Configuration
SQL Initialization
```

---

Not Loaded:

```text
Controller
Service
Repository
JPA
Hibernate
Security
Kafka
Redis
```

---

# Internal Workflow

```text
JUnit
   ↓
@JdbcTest
   ↓
Create DataSource
   ↓
Create JdbcTemplate
   ↓
Start Transaction
   ↓
Run Test
   ↓
Rollback
```

---

# Basic Example

## DAO

```java
@Repository
public class ProductDao {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    public int countProducts() {

        return jdbcTemplate.queryForObject(
                "SELECT COUNT(*) FROM PRODUCT",
                Integer.class
        );
    }
}
```

---

## Test

```java
@JdbcTest
class ProductDaoTest {

    @Autowired
    JdbcTemplate jdbcTemplate;

    @Test
    void testCount() {

        Integer count =
            jdbcTemplate.queryForObject(
                "SELECT 1",
                Integer.class
            );

        assertEquals(1, count);
    }
}
```

---

Execution:

```text
Create Test Database
      ↓
Create JdbcTemplate
      ↓
Execute SQL
      ↓
Rollback
```

---

# Default Database

By default Spring Boot uses:

:contentReference[oaicite:0]{index=0}

if present.

---

Dependency:

```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>
```

---

During test:

```text
Temporary In-Memory Database
```

is created.

---

# Testing JdbcTemplate

Example:

```java
@JdbcTest
class JdbcTemplateTest {

    @Autowired
    JdbcTemplate jdbcTemplate;

    @Test
    void testQuery() {

        Integer result =
            jdbcTemplate.queryForObject(
                "SELECT 100",
                Integer.class
            );

        assertEquals(
            100,
            result
        );
    }
}
```

---

# Testing Table Insert

```java
@Test
void testInsert() {

    jdbcTemplate.update(
       """
       INSERT INTO PRODUCT
       VALUES (1,'Laptop')
       """
    );

    Integer count =
        jdbcTemplate.queryForObject(
          "SELECT COUNT(*) FROM PRODUCT",
          Integer.class
        );

    assertEquals(1, count);
}
```

---

# Testing Query Results

Table:

```sql
PRODUCT
```

---

Query:

```java
List<String> names =
    jdbcTemplate.query(
        "SELECT NAME FROM PRODUCT",
        (rs,rowNum)
            -> rs.getString("NAME")
    );
```

---

Verify:

```java
assertEquals(
    "Laptop",
    names.get(0)
);
```

---

# Testing NamedParameterJdbcTemplate

Spring automatically configures:

```java
NamedParameterJdbcTemplate
```

---

Example:

```java
@JdbcTest
class ProductTest {

    @Autowired
    NamedParameterJdbcTemplate
    template;
}
```

---

Query:

```java
Map<String,Object> params =
    Map.of("id",1);

String name =
    template.queryForObject(
      """
      SELECT NAME
      FROM PRODUCT
      WHERE ID=:id
      """,
      params,
      String.class
    );
```

---

# Testing RowMapper

DAO:

```java
public Product getById(
        Long id) {

    return jdbcTemplate.queryForObject(
            """
            SELECT *
            FROM PRODUCT
            WHERE ID=?
            """,
            new ProductMapper(),
            id
    );
}
```

---

Test:

```java
@JdbcTest
```

---

Verifies:

```text
Row Mapping Logic
```

---

# Testing Stored Procedures

```java
SimpleJdbcCall call =
      new SimpleJdbcCall(
          jdbcTemplate
      );
```

---

Test:

```java
@JdbcTest
```

---

Verifies:

```text
Stored Procedure Calls
```

---

# SQL Initialization

Spring automatically executes:

```sql
schema.sql
```

and

```sql
data.sql
```

during tests.

---

Example:

```sql
CREATE TABLE PRODUCT (
  ID BIGINT,
  NAME VARCHAR(100)
);
```

---

Spring loads table automatically.

---

# Example Using schema.sql

schema.sql:

```sql
CREATE TABLE PRODUCT(
   ID BIGINT,
   NAME VARCHAR(100)
);
```

---

data.sql:

```sql
INSERT INTO PRODUCT
VALUES(1,'Laptop');
```

---

Test:

```java
@JdbcTest
```

---

Database already populated.

---

# Transactions

Very important interview topic.

---

Every test is:

```java
@Transactional
```

automatically.

---

Example:

```java
@JdbcTest
class ProductTest {

}
```

---

Internally:

```java
@Transactional
```

applied.

---

# Rollback Behavior

Insert:

```java
jdbcTemplate.update(
      """
      INSERT INTO PRODUCT
      VALUES(1,'Laptop')
      """
);
```

---

After test:

```text
Rollback
```

---

Database remains clean.

---

# Using Real Database

Default:

```text
H2 Database
```

---

Use actual database:

```java
@AutoConfigureTestDatabase(
 replace =
 AutoConfigureTestDatabase.Replace.NONE
)
```

---

Example:

```java
@JdbcTest

@AutoConfigureTestDatabase(
 replace =
 AutoConfigureTestDatabase.Replace.NONE
)
class ProductTest {

}
```

---

Uses:

```text
MySQL
PostgreSQL
Oracle
SQL Server
```

configured in properties.

---

# Internal Components Loaded

```text
DataSource
JdbcTemplate
NamedParameterJdbcTemplate
TransactionManager
SQL Initialization
```

---

Not loaded:

```text
Controller
Service
Repository
JPA
Hibernate
Security
Kafka
Redis
```

---

# @JdbcTest vs @DataJpaTest

Interview Favorite.

---

## @JdbcTest

Tests:

```text
JdbcTemplate
Raw SQL
DAO Layer
```

---

## @DataJpaTest

Tests:

```text
JPA
Hibernate
Repositories
Entities
```

---

Comparison:

| Feature | @JdbcTest | @DataJpaTest |
|----------|------------|-------------|
| JdbcTemplate | ✅ | ❌ |
| NamedParameterJdbcTemplate | ✅ | ❌ |
| JPA | ❌ | ✅ |
| Hibernate | ❌ | ✅ |
| EntityManager | ❌ | ✅ |
| Raw SQL | ✅ | Limited |
| Repository | ❌ | ✅ |

---

# @JdbcTest vs @SpringBootTest

## @JdbcTest

Loads:

```text
Only JDBC Components
```

---

Fast.

---

## @SpringBootTest

Loads:

```text
Entire Application
```

---

Slower.

---

Comparison:

| Feature | @JdbcTest | @SpringBootTest |
|----------|-----------|----------------|
| JDBC | ✅ | ✅ |
| Service | ❌ | ✅ |
| Controller | ❌ | ✅ |
| Security | ❌ | ✅ |
| Fast | ✅ | ❌ |

---

# Common Use Cases

## JdbcTemplate Testing

```java
@JdbcTest
```

---

## SQL Query Validation

```java
@JdbcTest
```

---

## Stored Procedure Testing

```java
@JdbcTest
```

---

## DAO Layer Testing

```java
@JdbcTest
```

---

## RowMapper Testing

```java
@JdbcTest
```

---

## SQL Performance Verification

```java
@JdbcTest
```

---

# Common Mistakes

## Mistake 1

Expecting Controller Beans

Wrong:

```java
@Autowired
ProductController
```

---

Not loaded.

---

# Mistake 2

Expecting JPA Repositories

Wrong:

```java
@Autowired
ProductRepository
```

---

Need:

```java
@DataJpaTest
```

---

# Mistake 3

Forgetting Rollback

Data disappears after test.

---

Because:

```text
Transaction Rollback
```

occurs automatically.

---

# Common Interview Questions

## What is @JdbcTest?

Loads JDBC-related components for testing.

---

## Does It Load Controllers?

No.

---

## Does It Load Services?

No.

---

## Which Database Is Used By Default?

:contentReference[oaicite:1]{index=1}

---

## Is JdbcTemplate Available?

Yes.

---

## Is NamedParameterJdbcTemplate Available?

Yes.

---

## Are Tests Transactional?

Yes.

---

## What Happens After Test?

Rollback.

---

# Enterprise Best Practice

Use:

```java
@JdbcTest
```

for:

```text
JdbcTemplate
DAO Testing
Stored Procedures
Raw SQL Validation
```

---

Use:

```java
@DataJpaTest
```

for:

```text
JPA Repositories
Entity Testing
Hibernate
```

---

Use:

```java
@SpringBootTest
```

for:

```text
Full Integration Tests
```

---

# Key Points To Remember

- `@JdbcTest` tests the JDBC layer only.
- Loads `JdbcTemplate`.
- Loads `NamedParameterJdbcTemplate`.
- Configures an embedded database by default.
- Automatically transactional.
- Automatically rolls back after tests.
- Does not load controllers or services.
- Faster than `@SpringBootTest`.
- Ideal for DAO testing.
- Frequently asked Spring Boot testing interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@JdbcTest` | JDBC Layer Testing |
| `@DataJpaTest` | JPA Repository Testing |
| `@WebMvcTest` | Controller Testing |
| `@SpringBootTest` | Full Integration Testing |
| `@Transactional` | Transaction Management |

---

# Interview Shortcut

```java
@JdbcTest
class ProductDaoTest {

    @Autowired
    JdbcTemplate jdbcTemplate;
}
```

Execution:

```text
Create DataSource
      ↓
Create JdbcTemplate
      ↓
Run SQL Queries
      ↓
Rollback
```

Think of it as:

```text
Testing SQL And JdbcTemplate
Without Loading
Controllers Services Or JPA
```

---

# Real Production Scenario

Application:

```text
Controller
    ↓
Service
    ↓
DAO
    ↓
JdbcTemplate
    ↓
MySQL
```

Test:

```java
@JdbcTest
class ProductDaoTest {

}
```

Loads:

```text
JdbcTemplate
DataSource
Transaction Manager
```

Tests:

```text
SQL Queries
Stored Procedures
Row Mappers
DAO Logic
```

Result:

```text
Fast Tests
Reliable SQL Validation
Database Confidence
```

`@JdbcTest` is Spring Boot's JDBC slice testing annotation that loads only JDBC-related infrastructure, making DAO and SQL testing fast, isolated, and focused on database interaction logic.