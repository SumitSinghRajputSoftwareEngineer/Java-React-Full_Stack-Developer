
## Definition

`@Cacheable` is a Spring Cache annotation used to **store a method's return value in a cache** and return the cached value for future calls with the same key.

It tells Spring:

> Before executing the method, check the cache. If data exists, return it. Otherwise execute the method, store the result, and return it.

Package:

```java
import org.springframework.cache.annotation.Cacheable;
```

---

# Prerequisite

Caching must be enabled.

```java
@Configuration

@EnableCaching
public class CacheConfig {
}
```

Without:

```java
@EnableCaching
```

`@Cacheable` does nothing.

---

# Why Do We Need @Cacheable?

Consider:

```java
public Product getProduct(
       Long id) {

    return repository
           .findById(id)
           .orElse(null);
}
```

---

Every call:

```text
Application
    ↓
Database
    ↓
Return Result
```

---

Example:

```java
getProduct(1L);
getProduct(1L);
getProduct(1L);
```

---

Database executes:

```sql
SELECT * FROM product
WHERE id = 1
```

three times.

---

Problem:

```text
High Latency
Increased DB Load
Poor Performance
```

---

Solution:

```java
@Cacheable
```

---

# Basic Example

```java
@Service
public class ProductService {

    @Cacheable("products")
    public Product getProduct(
            Long id) {

        System.out.println(
           "DB Call"
        );

        return repository
                .findById(id)
                .orElse(null);
    }
}
```

---

Call:

```java
service.getProduct(1L);
service.getProduct(1L);
service.getProduct(1L);
```

---

Output:

```text
DB Call
```

Only once.

---

Subsequent calls:

```text
Returned From Cache
```

---

# What Happens Internally?

First Request:

```text
Method Called
      ↓
Check Cache
      ↓
Not Found
      ↓
Execute Method
      ↓
Store Result In Cache
      ↓
Return Result
```

---

Second Request:

```text
Method Called
      ↓
Check Cache
      ↓
Found
      ↓
Return Cached Result
```

---

Method is not executed.

---

# Internal Workflow

```text
Method Call
      ↓
Spring Proxy
      ↓
Cache Interceptor
      ↓
Cache Manager
      ↓
Cache Lookup
      ↓
Return Cached Value
```

---

# Cache Name

Basic syntax:

```java
@Cacheable("products")
```

---

Equivalent:

```java
@Cacheable(
 value = "products"
)
```

---

Meaning:

```text
Store Data In
products Cache
```

---

# Example

```java
@Cacheable("employees")
public Employee
getEmployee(Long id) {

}
```

---

Cache Name:

```text
employees
```

---

# Default Cache Key

Method:

```java
@Cacheable("products")
public Product getProduct(
      Long id) {

}
```

---

Call:

```java
getProduct(1L)
```

---

Generated Key:

```text
1
```

---

Call:

```java
getProduct(2L)
```

---

Generated Key:

```text
2
```

---

# Multiple Parameters

```java
@Cacheable("products")
public Product getProduct(
     Long id,
     String category
) {

}
```

---

Call:

```java
getProduct(
  1L,
  "Mobile"
)
```

---

Generated Key:

```text
[1, Mobile]
```

---

# Custom Key

Most common interview topic.

---

```java
@Cacheable(
 value = "products",
 key = "#id"
)
```

---

Meaning:

```text
Use id As Cache Key
```

---

# SpEL Examples

## Parameter

```java
@Cacheable(
 key = "#id"
)
```

---

## Object Property

```java
@Cacheable(
 key = "#product.id"
)
```

---

## Multiple Values

```java
@Cacheable(
 key = "#id + '-' + #category"
)
```

---

Generated:

```text
1-Mobile
```

---

# Complete Example

```java
@Cacheable(
 value = "products",
 key = "#id"
)
public Product getProduct(
       Long id) {

    return repository
          .findById(id)
          .orElse(null);
}
```

---

Call:

```java
getProduct(100L)
```

---

Cache:

```text
Key = 100
Value = Product
```

---

# Condition Attribute

Cache only when condition is true.

---

Example:

```java
@Cacheable(
 value = "products",
 condition = "#id > 100"
)
```

---

Meaning:

```text
Cache Only IDs Greater Than 100
```

---

Examples:

```java
getProduct(50)
```

Not cached.

---

```java
getProduct(200)
```

Cached.

---

# Unless Attribute

Opposite of condition.

---

Example:

```java
@Cacheable(
 value = "products",
 unless = "#result == null"
)
```

---

Meaning:

```text
Do Not Cache Null Values
```

---

# Example

```java
@Cacheable(
 value = "products",
 unless = "#result == null"
)
public Product getProduct(
      Long id) {

}
```

---

If:

```java
null
```

returned:

```text
Nothing Stored
```

---

# Sync Attribute

Useful for preventing cache stampede.

---

```java
@Cacheable(
 value = "products",
 sync = true
)
```

---

Meaning:

```text
Only One Thread Loads Data
Others Wait
```

---

Common interview topic.

---

# Real Example

Without sync:

```text
100 Requests
Cache Miss
```

---

Result:

```text
100 Database Calls
```

---

With:

```java
sync=true
```

---

Result:

```text
1 Database Call
99 Threads Wait
```

---

# @Cacheable With Redis

Configuration:

```properties
spring.data.redis.host=localhost
spring.data.redis.port=6379
```

---

Method:

```java
@Cacheable("products")
public Product getProduct(
       Long id) {

}
```

---

Storage:

```text
Redis
```

instead of memory.

---

# Common Use Cases

## Product Catalog

```java
@Cacheable
```

---

## Country List

```java
@Cacheable
```

---

## Configuration Data

```java
@Cacheable
```

---

## User Profile

```java
@Cacheable
```

---

## Currency Data

```java
@Cacheable
```

---

# Self Invocation Problem

Interview Favorite.

---

Wrong:

```java
@Service
public class ProductService {

    public Product test() {

        return getProduct(1L);
    }

    @Cacheable("products")
    public Product getProduct(
           Long id) {

        return repository
             .findById(id)
             .orElse(null);
    }
}
```

---

Result:

```text
Cache Not Used
```

---

Reason:

```text
Proxy Bypassed
```

---

Same issue occurs with:

```java
@Transactional
@Async
```

---

# Internal Components

Uses:

```text
CacheInterceptor
CacheManager
CacheResolver
Spring AOP Proxy
```

---

Execution:

```text
Method Call
      ↓
Proxy Intercepts
      ↓
Cache Lookup
      ↓
Cache Hit?
      ↓
Return Cached Value
```

---

# @Cacheable vs @CachePut

Interview Favorite.

---

## @Cacheable

```text
May Skip Method Execution
```

---

If cache exists:

```text
Method Not Called
```

---

# @CachePut

```text
Always Executes Method
```

---

Updates cache afterwards.

---

Comparison:

| Feature | @Cacheable | @CachePut |
|----------|-----------|-----------|
| Checks Cache First | ✅ | ❌ |
| Skips Method | ✅ | ❌ |
| Updates Cache | On Miss | Always |
| Best For | Reads | Updates |

---

# @Cacheable vs @CacheEvict

## @Cacheable

```text
Stores Data
```

---

## @CacheEvict

```text
Removes Data
```

---

Example:

```java
@CacheEvict(
 value="products",
 key="#id"
)
```

---

Used after updates/deletes.

---

# Common Mistakes

## Mistake 1

Missing EnableCaching

```java
@EnableCaching
```

required.

---

# Mistake 2

Caching Frequently Changing Data

Example:

```text
Live Stock Prices
Sports Scores
```

---

Cache becomes stale.

---

# Mistake 3

Caching Large Objects

Can cause:

```text
Memory Problems
```

---

# Mistake 4

Not Evicting Cache

Update DB:

```java
repository.save(...)
```

---

But cache unchanged.

---

Result:

```text
Stale Data
```

---

# Common Interview Questions

## What is @Cacheable?

Stores method result in cache and reuses it.

---

## Does Method Execute Every Time?

No.

Only on cache miss.

---

## What Is Default Cache Key?

Method parameters.

---

## What Is sync=true?

Prevents multiple threads from loading same data simultaneously.

---

## Difference Between @Cacheable and @CachePut?

`@Cacheable`

may skip method execution.

---

`@CachePut`

always executes method.

---

## Why Doesn't Self Invocation Work?

Spring proxy is bypassed.

---

# Enterprise Best Practice

Use:

```java
@Cacheable
```

for:

```text
Products
Configurations
Country Lists
User Preferences
Reference Data
```

---

Use:

:contentReference[oaicite:0]{index=0}

for production systems.

---

Avoid caching:

```text
Highly Dynamic Data
Sensitive Data
Frequently Updated Data
```

---

Monitor:

```text
Hit Ratio
Memory Usage
Latency
Evictions
```

---

# Key Points To Remember

- `@Cacheable` stores method results in cache.
- Requires `@EnableCaching`.
- Uses Spring AOP proxies.
- Skips method execution on cache hits.
- Supports custom keys using SpEL.
- Supports conditions and unless clauses.
- Supports `sync=true`.
- Works with Redis, Caffeine, Ehcache, etc.
- Improves performance and scalability.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Cacheable` | Read From Cache |
| `@CachePut` | Update Cache |
| `@CacheEvict` | Remove Cache |
| `@Caching` | Multiple Cache Operations |
| `@EnableCaching` | Enable Cache Support |

---

# Interview Shortcut

```java
@Cacheable(
 value = "products",
 key = "#id"
)
public Product getProduct(
       Long id) {

    return repository
           .findById(id)
           .orElse(null);
}
```

Execution:

```text
First Call
     ↓
Cache Miss
     ↓
Database
     ↓
Store Result

Second Call
     ↓
Cache Hit
     ↓
Return Cached Result
```

Result:

```text
Faster Response
Less Database Load
Better Scalability
```

Think of `@Cacheable` as:

```java
if(cache.contains(id)) {
    return cache.get(id);
}

Product product =
      repository.findById(id);

cache.put(id, product);

return product;
```

implemented automatically by Spring.

`@Cacheable` is the most commonly used Spring caching annotation for improving application performance by storing and reusing frequently requested data instead of repeatedly executing expensive operations.