
## Definition

`@EnableCaching` is a Spring annotation used to enable **Spring Cache Abstraction**.

It tells Spring:

> Activate cache-related annotations such as `@Cacheable`, `@CachePut`, and `@CacheEvict`.

Package:

```java
import org.springframework.cache.annotation.EnableCaching;
```

---

# Why Do We Need Caching?

Suppose a method fetches data from a database:

```java
public Product getProduct(Long id) {

    return repository.findById(id)
                     .orElse(null);
}
```

---

Every request:

```text
Client
   ↓
Application
   ↓
Database
   ↓
Return Data
```

---

Problem:

```text
Repeated Database Calls
Higher Latency
More Resource Usage
```

---

Example:

```java
getProduct(1);
getProduct(1);
getProduct(1);
getProduct(1);
```

---

Same database query executed repeatedly.

---

Solution:

```text
Store Result In Cache
```

---

Flow:

```text
First Request
      ↓
Database
      ↓
Cache Result

Next Requests
      ↓
Read From Cache
      ↓
Skip Database
```

---

# Basic Setup

Enable caching:

```java
@SpringBootApplication

@EnableCaching
public class Application {

    public static void main(
      String[] args
    ) {

        SpringApplication.run(
           Application.class,
           args
        );
    }
}
```

---

Meaning:

```text
Enable Spring Cache Support
```

---

# First Cache Example

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
Return Cached Value
```

---

Database not hit.

---

# Internal Workflow

```text
Method Call
      ↓
Cache Interceptor
      ↓
Cache Manager
      ↓
Cache Store
      ↓
Return Cached Value
```

---

# Cache Abstraction

`@EnableCaching` activates:

```java
@Cacheable
@CachePut
@CacheEvict
@Caching
@CacheConfig
```

---

Without:

```java
@EnableCaching
```

these annotations do nothing.

---

# Default Cache Provider

Spring Boot automatically configures:

```text
ConcurrentMapCacheManager
```

---

Stores cache in memory.

---

Example:

```text
HashMap-Based Cache
```

---

Good for:

```text
Development
Testing
Learning
```

---

Not ideal for:

```text
Distributed Systems
Microservices
```

---

# Production Cache Providers

Popular options:

```text
Redis
Ehcache
Hazelcast
Caffeine
Infinispan
Apache Ignite
```

---

Most common:

:contentReference[oaicite:0]{index=0}

---

# Using Redis Cache

Dependency:

```xml
<dependency>
    <groupId>
      org.springframework.boot
    </groupId>

    <artifactId>
      spring-boot-starter-data-redis
    </artifactId>
</dependency>
```

---

Configuration:

```properties
spring.data.redis.host=localhost
spring.data.redis.port=6379
```

---

Enable:

```java
@EnableCaching
```

---

Now cache data stored in Redis.

---

# Common Cache Annotations

## @Cacheable

Read from cache.

---

```java
@Cacheable("products")
```

---

Meaning:

```text
If Data Exists In Cache
Return It
Else Execute Method
```

---

# @CachePut

Always execute method.

---

Update cache afterwards.

---

```java
@CachePut("products")
```

---

# @CacheEvict

Remove cache entries.

---

```java
@CacheEvict(
 value = "products",
 key = "#id"
)
```

---

Used when data changes.

---

# Example

```java
@CacheEvict(
 value="products",
 key="#id"
)
public void deleteProduct(
        Long id) {

    repository.deleteById(id);
}
```

---

Meaning:

```text
Delete DB Record
Remove Cache Entry
```

---

# Cache Key Generation

Method:

```java
@Cacheable("products")
public Product getProduct(
      Long id) {

}
```

---

Key:

```text
1
```

for:

```java
getProduct(1L)
```

---

Multiple Parameters:

```java
getProduct(
   1L,
   "mobile"
)
```

---

Generated key:

```text
[1, mobile]
```

---

# Custom Cache Key

```java
@Cacheable(
 value = "products",
 key = "#id"
)
```

---

Or:

```java
@Cacheable(
 value = "products",
 key = "#product.id"
)
```

---

Uses:

```text
SpEL
```

(Spring Expression Language)

---

# Conditional Caching

```java
@Cacheable(
 value = "products",
 condition = "#id > 100"
)
```

---

Meaning:

```text
Cache Only If ID > 100
```

---

# Unless Condition

```java
@Cacheable(
 value = "products",
 unless = "#result == null"
)
```

---

Meaning:

```text
Do Not Cache Null Results
```

---

# Complete Example

```java
@Service
public class ProductService {

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

    @CacheEvict(
      value = "products",
      key = "#id"
    )
    public void deleteProduct(
            Long id) {

        repository.deleteById(id);
    }
}
```

---

Flow:

```text
Read
 ↓
Cache

Delete
 ↓
Remove Cache Entry
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
Cache Not Applied
```

---

Why?

```text
Proxy Bypassed
```

---

Same issue as:

```java
@Transactional
@Async
```

---

# Internal Components

`@EnableCaching` activates:

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
Check Cache
      ↓
Cache Hit?
      ↓
Return Cached Value
```

---

# Real-World Use Cases

## Product Catalog

```java
@Cacheable
```

---

Frequently read.

Rarely updated.

---

## User Profile

```java
@Cacheable
```

---

Avoid repeated DB calls.

---

## Country List

```java
@Cacheable
```

---

Almost never changes.

---

## Configuration Data

```java
@Cacheable
```

---

Read many times.

---

# Common Mistakes

## Mistake 1

Forgetting EnableCaching

Wrong:

```java
@Cacheable
```

without:

```java
@EnableCaching
```

---

Cache won't work.

---

# Mistake 2

Caching Frequently Changing Data

Example:

```text
Stock Prices
Live Scores
```

---

Cache becomes stale.

---

# Mistake 3

Ignoring Cache Eviction

Update DB:

```java
repository.save(...)
```

---

But cache not cleared.

---

Result:

```text
Old Data Returned
```

---

# Common Interview Questions

## What is @EnableCaching?

Enables Spring Cache Abstraction.

---

## Which Annotations Does It Activate?

```java
@Cacheable
@CachePut
@CacheEvict
```

---

## Does Spring Use AOP?

Yes.

Caching is implemented using:

```text
Spring AOP Proxies
```

---

## What Is Default Cache Manager?

```java
ConcurrentMapCacheManager
```

---

## Most Common Production Cache?

:contentReference[oaicite:1]{index=1}

---

## Why Doesn't Self Invocation Work?

Because Spring proxy is bypassed.

---

# Enterprise Best Practice

Use:

```java
@EnableCaching
```

with:

:contentReference[oaicite:2]{index=2}

for production systems.

---

Cache:

```text
Products
Configurations
User Profiles
Reference Data
```

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
Evictions
Latency
```

---

# Key Points To Remember

- `@EnableCaching` enables Spring Cache support.
- Activates `@Cacheable`, `@CachePut`, and `@CacheEvict`.
- Uses Spring AOP proxies internally.
- Improves application performance.
- Reduces database load.
- Default cache manager is `ConcurrentMapCacheManager`.
- Most production systems use Redis.
- Self-invocation bypasses caching.
- Supports SpEL-based keys and conditions.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableCaching` | Enable Cache Support |
| `@Cacheable` | Read From Cache |
| `@CachePut` | Update Cache |
| `@CacheEvict` | Remove Cache |
| `@Caching` | Combine Cache Operations |

---

# Interview Shortcut

```java
@EnableCaching
```

Meaning:

```text
Enable Spring Cache Infrastructure
        ↓
Scan Cache Annotations
        ↓
Create Cache Proxies
        ↓
Store/Re-use Results
```

Example:

```java
@Cacheable("products")
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
Database
     ↓
Store In Cache

Second Call
     ↓
Cache
     ↓
Return Result
```

Result:

```text
Faster Response
Reduced DB Load
Better Scalability
```

`@EnableCaching` is the annotation that activates Spring's caching infrastructure, allowing applications to store and reuse frequently accessed data instead of repeatedly executing expensive operations such as database queries or API calls.