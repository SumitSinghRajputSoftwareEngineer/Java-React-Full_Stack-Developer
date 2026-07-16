
## Definition

`@CacheEvict` is a Spring Cache annotation used to **remove entries from a cache**.

It tells Spring:

> Remove specific cache entries (or the entire cache) when a method is executed.

Package:

```java
import org.springframework.cache.annotation.CacheEvict;
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

`@CacheEvict` does nothing.

---

# Why Do We Need @CacheEvict?

Suppose a product is cached.

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

Cache:

```text
1 → Mobile
```

---

Now product gets deleted.

```java
repository.deleteById(1L);
```

---

Problem:

```text
Database Record Deleted
Cache Still Contains Data
```

---

Result:

```java
getProduct(1L)
```

returns:

```text
Old Cached Data
```

---

Solution:

```java
@CacheEvict
```

---

# What Does @CacheEvict Do?

```text
Removes Cache Entries
```

after (or before) method execution.

---

Typical Use Cases:

```text
Delete Operations
Update Operations
Bulk Data Refresh
Cache Cleanup
```

---

# Basic Example

```java
@Service
public class ProductService {

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

Execution:

```text
Delete Product
      ↓
Remove Cache Entry
```

---

# What Happens Internally?

```text
Method Called
      ↓
Execute Method
      ↓
Remove Cache Entry
      ↓
Return
```

---

Cache becomes consistent.

---

# Complete Example

## Read Method

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

## Delete Method

```java
@CacheEvict(
 value = "products",
 key = "#id"
)
public void deleteProduct(
       Long id) {

    repository.deleteById(id);
}
```

---

Flow:

```text
Read Product
     ↓
Stored In Cache

Delete Product
     ↓
Database Delete
     ↓
Cache Entry Removed

Read Again
     ↓
Cache Miss
     ↓
Database Checked
```

---

# Cache Name

```java
@CacheEvict("products")
```

---

Equivalent:

```java
@CacheEvict(
 value = "products"
)
```

---

Meaning:

```text
Remove Entry
From products Cache
```

---

# Cache Key

Most common usage.

---

```java
@CacheEvict(
 value = "products",
 key = "#id"
)
```

---

Example:

```java
deleteProduct(100L);
```

---

Removed:

```text
Key = 100
```

---

# SpEL Examples

## Parameter

```java
key = "#id"
```

---

## Object Property

```java
key = "#product.id"
```

---

## Multiple Parameters

```java
key =
"#id + '-' + #category"
```

---

Generated:

```text
100-Mobile
```

---

# allEntries Attribute

Interview Favorite.

---

Remove all cache entries.

---

```java
@CacheEvict(
 value = "products",
 allEntries = true
)
```

---

Meaning:

```text
Clear Entire products Cache
```

---

Example:

```java
public void refreshCatalog() {

}
```

---

Execution:

```text
Products Cache Cleared
```

---

# beforeInvocation Attribute

Very important interview topic.

---

Default:

```java
beforeInvocation = false
```

---

Meaning:

```text
Method Executes First
Then Cache Removed
```

---

Example:

```java
@CacheEvict(
 value = "products",
 key = "#id"
)
public void deleteProduct(
       Long id) {

}
```

---

Flow:

```text
Delete Product
      ↓
Success
      ↓
Remove Cache
```

---

# beforeInvocation = true

```java
@CacheEvict(
 value = "products",
 key = "#id",
 beforeInvocation = true
)
```

---

Flow:

```text
Remove Cache
      ↓
Execute Method
```

---

Even if:

```java
Method Throws Exception
```

Cache already removed.

---

# Example

```java
@CacheEvict(
 value = "products",
 key = "#id",
 beforeInvocation = true
)
public void deleteProduct(
      Long id) {

    throw new RuntimeException();
}
```

---

Cache still removed.

---

# Update Scenario

Sometimes used after updates.

---

```java
@CacheEvict(
 value = "products",
 key = "#product.id"
)
public Product updateProduct(
        Product product) {

    return repository.save(product);
}
```

---

Meaning:

```text
Update Database
Remove Old Cache Entry
```

---

Next read:

```text
Loads Fresh Data
```

---

# Delete Example

```java
@CacheEvict(
 value = "users",
 key = "#id"
)
public void deleteUser(
        Long id) {

    repository.deleteById(id);
}
```

---

Flow:

```text
Delete User
      ↓
Remove User Cache
```

---

# Multiple Cache Evictions

Using:

```java
@Caching
```

---

Example:

```java
@Caching(
 evict = {

   @CacheEvict(
      value="products",
      key="#id"
   ),

   @CacheEvict(
      value="inventory",
      key="#id"
   )
 }
)
```

---

Meaning:

```text
Remove From
Products Cache

AND

Inventory Cache
```

---

# @CacheEvict vs @CachePut

Interview Favorite.

---

## @CacheEvict

```text
Remove Cache
```

---

## @CachePut

```text
Update Cache
```

---

Comparison:

| Feature | @CachePut | @CacheEvict |
|----------|-----------|------------|
| Update Cache | ✅ | ❌ |
| Remove Cache | ❌ | ✅ |
| Used For Updates | ✅ | Sometimes |
| Used For Deletes | ❌ | ✅ |

---

# @CacheEvict vs @Cacheable

## @Cacheable

```text
Store Data
```

---

## @CacheEvict

```text
Remove Data
```

---

Comparison:

| Feature | @Cacheable | @CacheEvict |
|----------|-----------|------------|
| Read Cache | ✅ | ❌ |
| Store Cache | ✅ | ❌ |
| Remove Cache | ❌ | ✅ |

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
Method Executes
      ↓
Cache Removed
```

---

# Self Invocation Problem

Interview Favorite.

---

Wrong:

```java
public void process() {

    deleteProduct(1L);
}
```

---

Same class:

```java
@CacheEvict(...)
public void deleteProduct(
       Long id) {

}
```

---

Result:

```text
Cache Not Removed
```

---

Reason:

```text
Proxy Bypassed
```

---

# Common Use Cases

## Product Deletion

```java
@CacheEvict
```

---

## User Deletion

```java
@CacheEvict
```

---

## Refresh Catalog

```java
allEntries=true
```

---

## Configuration Refresh

```java
allEntries=true
```

---

## Cache Cleanup Jobs

```java
@CacheEvict
```

---

# Common Mistakes

## Mistake 1

Forgetting Cache Eviction

```java
repository.delete(...)
```

---

Cache still contains data.

---

# Mistake 2

Wrong Cache Key

Read:

```java
@Cacheable(
 key="#id"
)
```

Delete:

```java
@CacheEvict(
 key="#code"
)
```

---

Cache entry remains.

---

# Mistake 3

Using allEntries=true Unnecessarily

```java
@CacheEvict(
 allEntries=true
)
```

---

Can remove thousands of entries.

---

Use carefully.

---

# Common Interview Questions

## What is @CacheEvict?

Removes cache entries.

---

## When Is It Used?

Mostly after:

```text
Delete
Update
Refresh
```

operations.

---

## What Does allEntries=true Do?

Clears the entire cache.

---

## What Does beforeInvocation=true Do?

Removes cache before method execution.

---

## Difference Between @CachePut and @CacheEvict?

`@CachePut`

updates cache.

---

`@CacheEvict`

removes cache.

---

## Why Doesn't Self Invocation Work?

Spring proxy is bypassed.

---

# Enterprise Best Practice

Typical CRUD pattern:

---

## Read

```java
@Cacheable
```

---

## Update

```java
@CachePut
```

or

```java
@CacheEvict
```

---

## Delete

```java
@CacheEvict
```

---

Example:

```java
GET    -> @Cacheable

PUT    -> @CachePut

DELETE -> @CacheEvict
```

---

Use:

:contentReference[oaicite:0]{index=0}

for production cache storage.

---

Monitor:

```text
Eviction Count
Cache Size
Hit Ratio
Memory Usage
```

---

# Key Points To Remember

- `@CacheEvict` removes cache entries.
- Requires `@EnableCaching`.
- Supports custom keys using SpEL.
- Supports `allEntries=true`.
- Supports `beforeInvocation=true`.
- Commonly used for delete operations.
- Can be used after updates.
- Uses Spring AOP proxies internally.
- Helps prevent stale cache data.
- Frequently asked Spring Cache interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Cacheable` | Read/Store Cache |
| `@CachePut` | Update Cache |
| `@CacheEvict` | Remove Cache |
| `@Caching` | Multiple Cache Operations |
| `@EnableCaching` | Enable Cache Support |

---

# Interview Shortcut

```java
@CacheEvict(
 value = "products",
 key = "#id"
)
public void deleteProduct(
      Long id) {

    repository.deleteById(id);
}
```

Execution:

```text
Delete Product
      ↓
Remove Cache Entry
      ↓
Next Read
      ↓
Fresh Database Query
```

Think of it as:

```java
repository.deleteById(id);

cache.remove(id);
```

implemented automatically by Spring.

---

# Real Production Scenario

```java
@Cacheable(
 value = "users",
 key = "#id"
)
public User getUser(Long id) {

    return repository.findById(id)
                     .orElse(null);
}
```

```java
@CacheEvict(
 value = "users",
 key = "#id"
)
public void deleteUser(Long id) {

    repository.deleteById(id);
}
```

Flow:

```text
User Cached
      ↓
User Deleted
      ↓
Cache Removed
      ↓
Future Reads Use Database
```

Result:

```text
No Stale Data
Consistent Cache
Reliable System
```

`@CacheEvict` is the Spring caching annotation used to remove stale or unnecessary data from a cache, ensuring that future requests retrieve fresh and accurate information.