
## Definition

`@JsonTest` is a Spring Boot test annotation used to test **JSON serialization and deserialization** independently of the rest of the application.

It tells Spring Boot:

> Load only JSON-related components such as Jackson ObjectMapper, JSON serializers, JSON deserializers, and JSON testers.

Package:

```java
import org.springframework.boot.test.autoconfigure.json.JsonTest;
```

---

# Why Do We Need @JsonTest?

Suppose we have:

```java
public class Product {

    private Long id;
    private String name;
}
```

---

When an API returns:

```json
{
  "id": 1,
  "name": "Laptop"
}
```

---

We need to verify:

```text
Object → JSON
JSON → Object
```

works correctly.

---

Without loading:

```text
Controllers
Services
Repositories
Database
Security
```

---

Solution:

```java
@JsonTest
```

---

# What Does @JsonTest Load?

Loaded:

```text
Jackson ObjectMapper
@JsonComponent
Custom Serializers
Custom Deserializers
Jackson Modules
JsonTester Utilities
```

---

Not Loaded:

```text
Controllers
Services
Repositories
Database
JPA
Security
Kafka
Redis
```

---

# Internal Workflow

```text
JUnit
   ↓
@JsonTest
   ↓
Configure Jackson
   ↓
Create ObjectMapper
   ↓
Create JsonTesters
   ↓
Run Tests
```

---

# Basic Example

## Model

```java
public class Product {

    private Long id;
    private String name;

    // getters setters
}
```

---

## Test

```java
@JsonTest
class ProductJsonTest {

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    void serialize()
    throws Exception {

        Product product =
                new Product();

        product.setId(1L);
        product.setName("Laptop");

        String json =
            objectMapper.writeValueAsString(
                product
            );

        assertTrue(
            json.contains("Laptop")
        );
    }
}
```

---

Execution:

```text
Product Object
      ↓
ObjectMapper
      ↓
JSON
      ↓
Verify Output
```

---

# Serialization Testing

Object:

```java
Product p =
    new Product(
        1L,
        "Laptop"
    );
```

---

JSON:

```json
{
  "id":1,
  "name":"Laptop"
}
```

---

Test:

```java
String json =
    objectMapper.writeValueAsString(
        p
    );
```

---

Verifies:

```text
Java Object → JSON
```

---

# Deserialization Testing

JSON:

```json
{
  "id":1,
  "name":"Laptop"
}
```

---

Convert:

```java
Product p =
 objectMapper.readValue(
      json,
      Product.class
 );
```

---

Verify:

```java
assertEquals(
    "Laptop",
    p.getName()
);
```

---

Verifies:

```text
JSON → Java Object
```

---

# Using JacksonTester

Very common interview topic.

---

Spring Boot provides:

```java
JacksonTester<T>
```

---

Example:

```java
@JsonTest
class ProductJsonTest {

    @Autowired
    private JacksonTester<Product>
            json;
}
```

---

# Serialize Using JacksonTester

```java
Product product =
    new Product(
        1L,
        "Laptop"
    );

JsonContent<Product> result =
        json.write(product);
```

---

Verify:

```java
assertThat(result)
      .extractingJsonPathStringValue(
          "@.name"
      )
      .isEqualTo("Laptop");
```

---

# Deserialize Using JacksonTester

```java
String content =
"""
{
 "id":1,
 "name":"Laptop"
}
""";
```

---

Convert:

```java
Product product =
    json.parseObject(content);
```

---

Verify:

```java
assertEquals(
    "Laptop",
    product.getName()
);
```

---

# Testing @JsonProperty

Model:

```java
public class Product {

    @JsonProperty("product_name")
    private String name;
}
```

---

Serialization:

```json
{
 "product_name":"Laptop"
}
```

---

Test:

```java
assertThat(result)
      .hasJsonPathValue(
           "@.product_name"
      );
```

---

Verifies:

```text
Custom JSON Field Names
```

---

# Testing @JsonIgnore

Model:

```java
public class User {

    private String name;

    @JsonIgnore
    private String password;
}
```

---

Serialization:

```json
{
  "name":"John"
}
```

---

Password removed.

---

Test:

```java
assertThat(result)
      .doesNotHaveJsonPath(
          "@.password"
      );
```

---

# Testing Date Formatting

Model:

```java
@JsonFormat(
 shape = STRING,
 pattern = "yyyy-MM-dd"
)
private LocalDate dob;
```

---

Object:

```java
2025-01-01
```

---

JSON:

```json
{
  "dob":"2025-01-01"
}
```

---

Verify:

```java
assertThat(result)
      .extractingJsonPathStringValue(
          "@.dob"
      );
```

---

# Testing Custom Serializer

Serializer:

```java
public class PriceSerializer
extends JsonSerializer<Double> {

}
```

---

Register:

```java
@JsonComponent
public class PriceSerializer {

}
```

---

Test:

```java
@JsonTest
```

---

Verifies:

```text
Custom Serialization Logic
```

---

# Testing Custom Deserializer

```java
@JsonComponent
public class ProductDeserializer {

}
```

---

Test:

```java
@JsonTest
```

---

Verifies:

```text
Custom Deserialization Logic
```

---

# Example With Records

```java
public record Product(
      Long id,
      String name
) {
}
```

---

Test:

```java
@JsonTest
```

---

Verifies:

```text
Record Serialization
```

---

# Internal Components Loaded

```text
ObjectMapper
JacksonTester
JsonComponent
Jackson Modules
Serializer
Deserializer
```

---

Not loaded:

```text
Controller
Service
Repository
Database
JPA
Security
```

---

# @JsonTest vs @WebMvcTest

Interview Favorite.

---

## @JsonTest

Tests:

```text
JSON Conversion Only
```

---

## @WebMvcTest

Tests:

```text
Controllers + JSON
```

---

Comparison:

| Feature | @JsonTest | @WebMvcTest |
|----------|-----------|-------------|
| ObjectMapper | ✅ | ✅ |
| Controller | ❌ | ✅ |
| MockMvc | ❌ | ✅ |
| JSON Validation | ✅ | ✅ |
| Service | ❌ | ❌ |

---

# @JsonTest vs @SpringBootTest

## @JsonTest

Loads:

```text
JSON Components Only
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

| Feature | @JsonTest | @SpringBootTest |
|----------|-----------|----------------|
| Jackson | ✅ | ✅ |
| Controller | ❌ | ✅ |
| Service | ❌ | ✅ |
| Repository | ❌ | ✅ |
| Database | ❌ | ✅ |
| Fast | ✅ | ❌ |

---

# Common Use Cases

## Serialization Testing

```java
@JsonTest
```

---

## Deserialization Testing

```java
@JsonTest
```

---

## Jackson Configuration Testing

```java
@JsonTest
```

---

## Custom Serializer Testing

```java
@JsonTest
```

---

## Custom Deserializer Testing

```java
@JsonTest
```

---

## JSON Property Validation

```java
@JsonTest
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

Expecting Database Access

Not loaded.

---

Need:

```java
@SpringBootTest
```

---

# Mistake 3

Testing REST APIs

Wrong tool.

---

Use:

```java
@WebMvcTest
```

instead.

---

# Common Interview Questions

## What is @JsonTest?

Tests JSON serialization/deserialization.

---

## Does It Load Controllers?

No.

---

## Does It Load Services?

No.

---

## What Is JacksonTester?

Spring Boot helper for JSON testing.

---

## Is ObjectMapper Available?

Yes.

---

## Can It Test Custom Serializers?

Yes.

---

## Can It Test Custom Deserializers?

Yes.

---

# Enterprise Best Practice

Use:

```java
@JsonTest
```

for:

```text
DTO Testing
JSON Mapping
Custom Serializers
Custom Deserializers
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

Use:

```java
@SpringBootTest
```

for:

```text
Full Integration Testing
```

---

# Key Points To Remember

- `@JsonTest` loads only JSON-related components.
- Provides `ObjectMapper`.
- Provides `JacksonTester`.
- Tests serialization and deserialization.
- Supports custom serializers.
- Supports custom deserializers.
- Does not load controllers.
- Does not load databases.
- Faster than `@SpringBootTest`.
- Frequently asked Spring Boot testing interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@JsonTest` | JSON Testing |
| `@WebMvcTest` | Controller Testing |
| `@DataJpaTest` | Repository Testing |
| `@JdbcTest` | JDBC Testing |
| `@SpringBootTest` | Full Integration Testing |

---

# Interview Shortcut

```java
@JsonTest
class ProductJsonTest {

    @Autowired
    ObjectMapper objectMapper;
}
```

Execution:

```text
Create ObjectMapper
      ↓
Serialize Object
      ↓
Deserialize JSON
      ↓
Verify Mapping
```

Think of it as:

```text
Testing JSON Conversion
Without Loading
Controllers Services Or Database
```

---

# Real Production Scenario

API Response:

```json
{
  "id": 1,
  "product_name": "Laptop"
}
```

DTO:

```java
@JsonProperty("product_name")
private String name;
```

Test:

```java
@JsonTest
class ProductJsonTest {

}
```

Verifies:

```text
Object → JSON
JSON → Object
Field Mapping
Date Formatting
Custom Serialization
```

Result:

```text
Reliable API Payloads
Correct JSON Structure
Fast Execution
```

`@JsonTest` is Spring Boot's JSON slice testing annotation that loads only Jackson-related infrastructure, making serialization and deserialization testing fast, isolated, and focused entirely on JSON behavior.