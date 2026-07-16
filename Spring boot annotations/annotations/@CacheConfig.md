
## Definition

`@CacheConfig` is a Spring Cache annotation used to define **common cache configuration at the class level**.

It tells Spring:

> Use these cache names, key generators, cache managers, or cache resolvers as defaults for all cache operations in this class.

Package:

```java
import org.springframework.cache.annotation.CacheConfig;
```

---

# Why Do We Need @CacheConfig?

Without `@CacheConfig`:

```java
@Service
public class ProductService {

    @Cacheable(
        value = "products"
    )
    public Product getProduct(Long id) {
    }

    @CachePut(
        value = "products"
    )
    public Product updateProduct(Product p) {
    }

    @CacheEvict(
        value = "products"
    )
    public void deleteProduct(Long id) {
    }
}
```

---

Problem:

```text
Repeated Cache Names
Repeated Configuration
More Boilerplate Code
```

---

Solution:

```java
@CacheConfig
```

---

# Basic Example

```java
@Service

@CacheConfig(
    cacheNames = "products"
)
public class ProductService {

    @Cacheable
    public Product getProduct(
            Long id) {

        return repository
                .findById(id)
                .orElse(null);
    }

    @CachePut
    public Product updateProduct(
            Product product) {

        return repository.save(product);
    }

    @CacheEvict
    public void deleteProduct(
            Long id) {

        repository.deleteById(id);
    }
}
```

---

Meaning:

```text
All Cache Operations
Use products Cache
By Default
```

---

# What Problem Does It Solve?

Without:

```java
@Cacheable("products")
@CachePut("products")
@CacheEvict("products")
```

everywhere.

---

With:

```java
@CacheConfig(
 cacheNames="products"
)
```

---

Methods become cleaner.

---

# What Can Be Configured?

`@CacheConfig` supports:

```java
cacheNames
cacheManager
cacheResolver
keyGenerator
```

---

# cacheNames

Most common usage.

---

```java
@CacheConfig(
 cacheNames = "products"
)
```

---

Methods:

```java
@Cacheable
@CachePut
@CacheEvict
```

---

Automatically use:

```text
products
```

cache.

---

# Example

```java
@Service

@CacheConfig(
 cacheNames = "employees"
)
public class EmployeeService {

    @Cacheable
    public Employee getEmployee(
            Long id) {

        return repository
                .findById(id)
                .orElse(null);
    }
}
```

---

Equivalent to:

```java
@Cacheable("employees")
```

---

# Overriding Class-Level Cache Name

Method-level configuration wins.

---

Example:

```java
@CacheConfig(
 cacheNames = "products"
)
public class ProductService {

    @Cacheable(
      "inventory"
    )
    public Product inventory(
           Long id) {

        return null;
    }
}
```

---

Uses:

```text
inventory
```

not:

```text
products
```

---

Method-level has higher priority.

---

# keyGenerator

Used when custom key generation is required.

---

Custom Generator:

```java
@Component("customKeyGenerator")
public class CustomKeyGenerator
implements KeyGenerator {

    @Override
    public Object generate(
            Object target,
            Method method,
            Object... params) {

        return method.getName()
               + "_"
               + params[0];
    }
}
```

---

Configuration:

```java
@CacheConfig(
 keyGenerator =
 "customKeyGenerator"
)
```

---

Meaning:

```text
All Cache Methods
Use Custom Key Generator
```

---

# cacheManager

Useful when multiple cache managers exist.

---

Example:

```java
@Bean
public CacheManager redisCacheManager() {

}
```

---

```java
@Bean
public CacheManager caffeineCacheManager() {

}
```

---

Choose one:

```java
@CacheConfig(
 cacheManager =
 "redisCacheManager"
)
```

---

All methods use:

```text
Redis Cache Manager
```

---

# cacheResolver

Advanced feature.

---

```java
@CacheConfig(
 cacheResolver =
 "customCacheResolver"
)
```

---

Allows dynamic cache selection.

---

Rarely used in interviews.

---

# Complete Example

```java
@Service

@CacheConfig(
 cacheNames = "products"
)
public class ProductService {

    @Cacheable(
      key = "#id"
    )
    public Product getProduct(
            Long id) {

        return repository
                .findById(id)
                .orElse(null);
    }

    @CachePut(
      key = "#product.id"
    )
    public Product updateProduct(
            Product product) {

        return repository.save(product);
    }

    @CacheEvict(
      key = "#id"
    )
    public void deleteProduct(
            Long id) {

        repository.deleteById(id);
    }
}
```

---

Result:

```text
Single Cache Configuration
Cleaner Code
```

---

# Internal Workflow

```text
Method Called
      ↓
Spring Proxy
      ↓
Read CacheConfig Defaults
      ↓
Apply Cache Operation
```

---

# What Happens Internally?

```text
Class Loaded
      ↓
Read @CacheConfig
      ↓
Store Defaults
      ↓
Method Called
      ↓
Apply Defaults
      ↓
Execute Cache Logic
```

---

# Real-World Example

Suppose:

```text
User Service
```

uses:

```text
users Cache
```

---

Without CacheConfig:

```java
@Cacheable("users")
@CachePut("users")
@CacheEvict("users")
```

everywhere.

---

With:

```java
@CacheConfig(
 cacheNames="users"
)
```

---

Cleaner:

```java
@Cacheable
@CachePut
@CacheEvict
```

---

# Example With Redis

```java
@Service

@CacheConfig(
 cacheNames="products",
 cacheManager=
 "redisCacheManager"
)
public class ProductService {

}
```

---

Meaning:

```text
Use Products Cache
Stored In Redis
```

---

Most common production scenario.

---

Uses:

:contentReference[oaicite:0]{index=0}

---

# Example With Caffeine

```java
@Service

@CacheConfig(
 cacheNames="products",
 cacheManager=
 "caffeineCacheManager"
)
public class ProductService {

}
```

---

Uses:

:contentReference[oaicite:1]{index=1}

---

# Internal Components

Uses:

```text
CacheInterceptor
CacheManager
CacheResolver
KeyGenerator
Spring AOP Proxy
```

---

Execution:

```text
Method Call
      ↓
Read CacheConfig Defaults
      ↓
Apply Cache Annotation
      ↓
Perform Cache Operation
```

---

# @CacheConfig vs @Cacheable

Interview Favorite.

---

## @CacheConfig

Class-level defaults.

---

```java
@CacheConfig(
 cacheNames="products"
)
```

---

## @Cacheable

Method-level cache operation.

---

```java
@Cacheable
```

---

Comparison:

| Feature | @CacheConfig | @Cacheable |
|----------|-------------|------------|
| Class Level | ✅ | ❌ |
| Method Level | ❌ | ✅ |
| Defines Defaults | ✅ | ❌ |
| Stores Data | ❌ | ✅ |

---

# Common Use Cases

## Product Service

```java
@CacheConfig(
 cacheNames="products"
)
```

---

## User Service

```java
@CacheConfig(
 cacheNames="users"
)
```

---

## Order Service

```java
@CacheConfig(
 cacheNames="orders"
)
```

---

## Shared Cache Manager

```java
@CacheConfig(
 cacheManager="redisCacheManager"
)
```

---

# Common Mistakes

## Mistake 1

Assuming It Performs Caching

Wrong:

```java
@CacheConfig
```

alone.

---

It only defines defaults.

---

Need:

```java
@Cacheable
@CachePut
@CacheEvict
```

---

# Mistake 2

Using Wrong Cache Name

```java
@CacheConfig(
 cacheNames="users"
)
```

inside:

```java
ProductService
```

---

Can create confusion.

---

# Mistake 3

Forgetting Method-Level Override

Method-level settings always win.

---

# Common Interview Questions

## What is @CacheConfig?

Defines common cache configuration for a class.

---

## Does It Perform Caching?

No.

It only provides defaults.

---

## What Can It Configure?

```text
cacheNames
cacheManager
cacheResolver
keyGenerator
```

---

## Can Method-Level Configuration Override It?

Yes.

Method-level has higher priority.

---

## Why Use It?

To reduce duplicate cache configuration.

---

# Enterprise Best Practice

Use:

```java
@CacheConfig(
 cacheNames="products"
)
```

for service-level cache configuration.

---

Combine with:

```java
@Cacheable
@CachePut
@CacheEvict
```

---

Example:

```java
@Service

@CacheConfig(
 cacheNames="products"
)
public class ProductService {

}
```

---

Cleaner:

```java
@Cacheable
@CachePut
@CacheEvict
```

without repeating cache names.

---

# Key Points To Remember

- `@CacheConfig` provides class-level cache defaults.
- Does not perform caching itself.
- Supports `cacheNames`.
- Supports `cacheManager`.
- Supports `cacheResolver`.
- Supports `keyGenerator`.
- Reduces duplicate configuration.
- Method-level annotations override it.
- Uses Spring Cache infrastructure internally.
- Frequently asked Spring Cache interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@CacheConfig` | Class-Level Cache Defaults |
| `@Cacheable` | Read/Store Cache |
| `@CachePut` | Update Cache |
| `@CacheEvict` | Remove Cache |
| `@EnableCaching` | Enable Cache Infrastructure |

---

# Interview Shortcut

```java
@CacheConfig(
 cacheNames = "products"
)
```

Meaning:

```text
All Cache Methods
Use products Cache
Unless Overridden
```

Example:

```java
@Service

@CacheConfig(
 cacheNames="products"
)
public class ProductService {

    @Cacheable
    public Product get(Long id) {
    }

    @CachePut
    public Product update(Product p) {
    }

    @CacheEvict
    public void delete(Long id) {
    }
}
```

Equivalent to:

```java
@Cacheable("products")
@CachePut("products")
@CacheEvict("products")
```

on every method.

---

# Real Production Scenario

```java
@Service

@CacheConfig(
 cacheNames="users",
 cacheManager=
 "redisCacheManager"
)
public class UserService {

    @Cacheable
    public User getUser(
          Long id) {

        return repository
                .findById(id)
                .orElse(null);
    }
}
```

Flow:

```text
Read User
      ↓
Check Redis Cache
      ↓
Cache Hit?
      ↓
Return Cached User
```

Result:

```text
Cleaner Code
Centralized Cache Configuration
Easy Maintenance
```

`@CacheConfig` is Spring's class-level cache configuration annotation that allows common cache settings such as cache names, cache managers, key generators, and cache resolvers to be defined once and reused across all cache operations within the class.