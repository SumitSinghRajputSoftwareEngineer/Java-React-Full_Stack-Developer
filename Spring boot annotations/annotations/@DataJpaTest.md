
## Definition

`@DataJpaTest` is a Spring Boot test annotation used to test the **JPA persistence layer** (Repositories, Entities, JPA mappings, and database interactions) without loading the entire application.

It tells Spring Boot:

> Load only JPA-related components and configure an in-memory database for repository testing.

Package:

```java
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
```

---

# Why Do We Need @DataJpaTest?

Suppose the application contains:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

---

You only want to test:

```text
Repository
Entity Mapping
JPQL Queries
Native Queries
Database Constraints
```

---

Loading the entire application using:

```java
@SpringBootTest
```

is unnecessary.

---

Solution:

```java
@DataJpaTest
```

---

# What Does @DataJpaTest Load?

Loaded:

```text
@Entity
@Repository
JpaRepository
CrudRepository
EntityManager
Hibernate
DataSource
Transaction Manager
```

---

Not Loaded:

```text
@Controller
@RestController
@Service
Security
Kafka
Redis
Cache
Scheduling
```

---

# Internal Workflow

```text
JUnit
   ↓
@DataJpaTest
   ↓
Create DataSource
   ↓
Configure Hibernate
   ↓
Create EntityManager
   ↓
Create Repository Beans
   ↓
Run Test
```

---

# Basic Example

## Entity

```java
@Entity
public class Product {

    @Id
    private Long id;

    private String name;
}
```

---

## Repository

```java
public interface ProductRepository
       extends JpaRepository<Product, Long> {

}
```

---

## Test

```java
@DataJpaTest
class ProductRepositoryTest {

    @Autowired
    private ProductRepository repository;

    @Test
    void saveProduct() {

        Product p = new Product();
        p.setId(1L);
        p.setName("Laptop");

        repository.save(p);

        assertEquals(
            1,
            repository.count()
        );
    }
}
```

---

Execution:

```text
Create In-Memory DB
      ↓
Create Table
      ↓
Save Entity
      ↓
Verify Data
```

---

# Default Database

By default Spring Boot uses:

:contentReference[oaicite:0]{index=0}

when available.

---

Example:

```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>
```

---

During test:

```text
Temporary Database
```

is created.

---

After test:

```text
Database Removed
```

---

# Transaction Support

Very important interview topic.

---

Every test runs inside:

```java
@Transactional
```

automatically.

---

Example:

```java
@DataJpaTest
class ProductTest {

}
```

---

Internally:

```java
@Transactional
```

is applied.

---

# What Happens After Test?

Spring automatically performs:

```java
ROLLBACK
```

---

Flow:

```text
Insert Data
      ↓
Test Executes
      ↓
Rollback
```

---

Database remains clean.

---

# Example

```java
@Test
void testSave() {

    repository.save(
        new Product(
            1L,
            "Laptop"
        )
    );
}
```

---

After test:

```text
No Data Remains
```

---

# Testing Custom Query

Repository:

```java
public interface ProductRepository
extends JpaRepository<Product, Long> {

    Product findByName(
           String name);
}
```

---

Test:

```java
@DataJpaTest
class ProductTest {

    @Autowired
    ProductRepository repository;

    @Test
    void testFindByName() {

        Product p =
            new Product();

        p.setName("Laptop");

        repository.save(p);

        Product result =
            repository.findByName(
                "Laptop"
            );

        assertNotNull(result);
    }
}
```

---

Verifies:

```text
Derived Query Works
```

---

# Testing JPQL Query

Repository:

```java
@Query("""
       select p
       from Product p
       where p.name = :name
       """)
Product getByName(
      String name);
```

---

Test:

```java
@DataJpaTest
void testJPQL() {

}
```

---

Verifies:

```text
JPQL Query
```

works correctly.

---

# Testing Native Query

Repository:

```java
@Query(
 value =
 "SELECT * FROM product WHERE name=?1",
 nativeQuery = true
)
Product getByName(
      String name);
```

---

Test:

```java
@DataJpaTest
```

---

Verifies:

```text
Native SQL
```

works correctly.

---

# Testing Entity Relationships

## One-To-Many

Entity:

```java
@Entity
public class Department {

    @OneToMany
    private List<Employee> employees;
}
```

---

Test:

```java
@DataJpaTest
```

---

Verifies:

```text
Relationship Mapping
```

---

# Many-To-One

```java
@ManyToOne
private Department department;
```

---

Test ensures:

```text
Foreign Keys
Join Queries
```

work correctly.

---

# Using TestEntityManager

Very common interview topic.

---

Spring provides:

```java
TestEntityManager
```

---

Example:

```java
@DataJpaTest
class ProductTest {

    @Autowired
    TestEntityManager entityManager;
}
```

---

Useful for:

```text
Persisting Entities
Flushing
Clearing Persistence Context
```

---

Example:

```java
Product p =
   entityManager.persistAndFlush(
       product
   );
```

---

# Testing Constraints

Entity:

```java
@Column(
 unique = true
)
private String email;
```

---

Test:

```java
assertThrows(
    Exception.class,
    () -> {
       repository.save(user1);
       repository.save(user2);
    }
);
```

---

Verifies:

```text
Unique Constraint
```

---

# Testing Lazy Loading

Entity:

```java
@OneToMany(
 fetch = FetchType.LAZY
)
```

---

Test:

```java
@DataJpaTest
```

---

Verifies:

```text
Lazy Loading
```

behavior.

---

# Use Real Database Instead Of H2

Default:

```text
H2
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
@DataJpaTest

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

# SQL Logging

Enable:

```properties
spring.jpa.show-sql=true
```

---

Output:

```sql
select *
from product
```

---

Helpful for debugging.

---

# Internal Components Loaded

```text
DataSource
Hibernate
JPA
Repositories
EntityManager
Transaction Manager
```

---

Not loaded:

```text
Controller
Service
Security
Kafka
Redis
Cache
```

---

# @DataJpaTest vs @SpringBootTest

Interview Favorite.

---

## @DataJpaTest

Loads:

```text
JPA Layer Only
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

| Feature | @DataJpaTest | @SpringBootTest |
|----------|-------------|----------------|
| Repository | ✅ | ✅ |
| Entity | ✅ | ✅ |
| Database | ✅ | ✅ |
| Service | ❌ | ✅ |
| Controller | ❌ | ✅ |
| Security | ❌ | ✅ |
| Fast | ✅ | ❌ |

---

# @DataJpaTest vs @WebMvcTest

## @DataJpaTest

Tests:

```text
Repository Layer
```

---

## @WebMvcTest

Tests:

```text
Controller Layer
```

---

# Common Use Cases

## Repository Testing

```java
@DataJpaTest
```

---

## Entity Mapping Testing

```java
@DataJpaTest
```

---

## JPQL Validation

```java
@DataJpaTest
```

---

## Native Query Testing

```java
@DataJpaTest
```

---

## Relationship Testing

```java
@DataJpaTest
```

---

## Constraint Testing

```java
@DataJpaTest
```

---

# Common Mistakes

## Mistake 1

Expecting Service Bean

Wrong:

```java
@Autowired
ProductService service;
```

---

Not loaded.

---

# Mistake 2

Expecting Controller Bean

Not loaded.

---

Need:

```java
@SpringBootTest
```

---

# Mistake 3

Forgetting Rollback

Every test:

```text
Automatically Rolled Back
```

unless explicitly changed.

---

# Common Interview Questions

## What is @DataJpaTest?

Loads only JPA components for testing.

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

## Are Tests Transactional?

Yes.

---

## What Happens After Test Execution?

Rollback.

---

## What Is TestEntityManager?

Testing utility around JPA EntityManager.

---

# Enterprise Best Practice

Use:

```java
@DataJpaTest
```

for:

```text
Repository Testing
JPQL Testing
Entity Mapping
Database Constraints
```

---

Use:

```java
@SpringBootTest
```

for:

```text
Integration Testing
```

---

Use:

```java
@WebMvcTest
```

for:

```text
Controller Testing
```

---

# Key Points To Remember

- `@DataJpaTest` loads only JPA-related components.
- Ideal for repository testing.
- Uses an embedded database by default.
- Automatically transactional.
- Automatically rolls back changes.
- Supports JPQL and native query testing.
- Supports relationship testing.
- Provides `TestEntityManager`.
- Faster than `@SpringBootTest`.
- Frequently asked Spring Boot testing interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@DataJpaTest` | Repository Testing |
| `@WebMvcTest` | Controller Testing |
| `@SpringBootTest` | Full Integration Testing |
| `@AutoConfigureMockMvc` | MockMvc Configuration |
| `@Transactional` | Transaction Management |

---

# Interview Shortcut

```java
@DataJpaTest
class ProductRepositoryTest {

    @Autowired
    ProductRepository repository;
}
```

Execution:

```text
Create H2 Database
      ↓
Configure Hibernate
      ↓
Create Repository Beans
      ↓
Execute Queries
      ↓
Rollback Transaction
```

Think of it as:

```text
Repository Testing
Without Loading
Controllers And Services
```

---

# Real Production Scenario

Application:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
MySQL
```

Repository Test:

```java
@DataJpaTest
class OrderRepositoryTest {

}
```

Loads:

```text
Repository
Entity
Hibernate
Database
```

Tests:

```text
JPQL Queries
Native Queries
Mappings
Constraints
Relationships
```

Result:

```text
Fast Tests
Reliable Persistence Validation
Database Confidence
```

`@DataJpaTest` is Spring Boot's JPA slice testing annotation that loads only the persistence layer, making repository and database testing fast, isolated, and focused on JPA functionality.