
## Definition

`@Caching` is a Spring Cache annotation used to **combine multiple cache operations on a single method**.

It tells Spring:

> Apply multiple `@Cacheable`, `@CachePut`, and/or `@CacheEvict` annotations together on the same method.

Package:

```java
import org.springframework.cache.annotation.Caching;
```

---

# Why Do We Need @Caching?

Java does not allow multiple annotations of the same type on a method in a clean way.

Suppose we want:

```java
@CacheEvict("products")
@CacheEvict("inventory")
@CacheEvict("categories")
```

on the same method.

---

Instead Spring provides:

```java
@Caching
```

to group cache operations.

---

# Basic Syntax

```java
@Caching(
    cacheable = {},
    put = {},
    evict = {}
)
```

---

Three groups available:

```text
cacheable
put
evict
```

---

# What Can Be Combined?

`@Caching` can contain:

```java
@Cacheable
@CachePut
@CacheEvict
```

together.

---

Example:

```java
@Caching(
   evict = {
      @CacheEvict("products"),
      @CacheEvict("inventory")
   }
)
```

---

Meaning:

```text
Remove Data
From Multiple Caches
```

---

# Most Common Use Case

## Multiple Cache Evictions

```java
@Service
public class ProductService {

    @Caching(
      evict = {

        @CacheEvict(
          value = "products",
          key = "#id"
        ),

        @CacheEvict(
          value = "inventory",
          key = "#id"
        )
      }
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
Remove From Products Cache
      ↓
Remove From Inventory Cache
```

---

# What Happens Internally?

```text
Method Called
      ↓
Execute Method
      ↓
Execute Cache Operations
      ↓
Return Result
```

---

# Example 1

## Product Delete

Caches:

```text
products
inventory
price
```

---

Method:

```java
@Caching(
 evict = {

   @CacheEvict(
     value = "products",
     key = "#id"
   ),

   @CacheEvict(
     value = "inventory",
     key = "#id"
   ),

   @CacheEvict(
     value = "price",
     key = "#id"
   )
 }
)
public void deleteProduct(
       Long id) {

}
```

---

Result:

```text
All Related Cache Entries Removed
```

---

# Example 2

## Multiple Cache Updates

```java
@Caching(
 put = {

   @CachePut(
      value="products",
      key="#result.id"
   ),

   @CachePut(
      value="inventory",
      key="#result.id"
   )
 }
)
public Product updateProduct(
       Product product) {

    return repository.save(product);
}
```

---

Execution:

```text
Database Updated
      ↓
Products Cache Updated
      ↓
Inventory Cache Updated
```

---

# Example 3

## Evict + Put Together

Interview Favorite.

---

```java
@Caching(

   put = {

      @CachePut(
          value="products",
          key="#result.id"
      )
   },

   evict = {

      @CacheEvict(
          value="productList",
          allEntries=true
      )
   }
)
public Product updateProduct(
        Product product) {

    return repository.save(product);
}
```

---

Flow:

```text
Update Product
      ↓
Update Product Cache
      ↓
Clear Product List Cache
```

---

Very common in enterprise systems.

---

# Example 4

## Multiple Cacheable

Less common.

---

```java
@Caching(
 cacheable = {

   @Cacheable(
      value="products",
      key="#id"
   ),

   @Cacheable(
      value="productBackup",
      key="#id"
   )
 }
)
public Product getProduct(
      Long id) {

}
```

---

Meaning:

```text
Check Multiple Caches
```

---

Usually avoided because it becomes confusing.

---

# Real Enterprise Example

Suppose:

```text
User Cache
Role Cache
Permission Cache
```

---

Update user:

```java
@Caching(
 evict = {

   @CacheEvict(
      value="users",
      key="#user.id"
   ),

   @CacheEvict(
      value="roles",
      key="#user.id"
   ),

   @CacheEvict(
      value="permissions",
      key="#user.id"
   )
 }
)
public void updateUser(
      User user) {

}
```

---

Result:

```text
All Related Caches Refreshed
```

---

# Using allEntries

```java
@Caching(
 evict = {

   @CacheEvict(
      value="products",
      allEntries=true
   ),

   @CacheEvict(
      value="inventory",
      allEntries=true
   )
 }
)
```

---

Meaning:

```text
Clear Entire Products Cache
Clear Entire Inventory Cache
```

---

# Using beforeInvocation

```java
@Caching(
 evict = {

   @CacheEvict(
      value="products",
      key="#id",
      beforeInvocation=true
   ),

   @CacheEvict(
      value="inventory",
      key="#id",
      beforeInvocation=true
   )
 }
)
```

---

Meaning:

```text
Evict Cache
Before Method Execution
```

---

# Using SpEL

```java
@Caching(
 evict = {

   @CacheEvict(
      value="products",
      key="#product.id"
   )
 }
)
```

---

Supports:

```text
#id
#product.id
#result
```

---

same as other cache annotations.

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
All Cache Operations Applied
```

---

# @Caching vs Individual Annotations

Without:

```java
@Caching
```

you may need:

```java
@CacheEvict(...)
@CacheEvict(...)
@CachePut(...)
```

---

With:

```java
@Caching
```

everything grouped together.

---

Cleaner and more maintainable.

---

# Common Use Cases

## Product Update

```java
@Caching
```

Update one cache and clear another.

---

## User Update

```java
@Caching
```

Evict user, role, permission caches.

---

## Catalog Refresh

```java
@Caching
```

Clear multiple caches.

---

## Inventory Changes

```java
@Caching
```

Update inventory and product caches together.

---

# Common Mistakes

## Mistake 1

Using @Caching Unnecessarily

Wrong:

```java
@Caching(
 evict = {
   @CacheEvict(
      value="products"
   )
 }
)
```

---

Use:

```java
@CacheEvict
```

directly.

---

# Mistake 2

Mixing Wrong Cache Keys

```java
@CacheEvict(
 key="#id"
)
```

---

while cache created with:

```java
@Cacheable(
 key="#product.id"
)
```

---

Can cause stale cache.

---

# Mistake 3

Excessive allEntries=true

```java
allEntries=true
```

on large caches can cause:

```text
Cache Miss Storm
Performance Issues
```

---

# Common Interview Questions

## What is @Caching?

A wrapper annotation for multiple cache operations.

---

## Which Annotations Can It Contain?

```java
@Cacheable
@CachePut
@CacheEvict
```

---

## Why Use @Caching?

To perform multiple cache operations on a single method.

---

## Can We Use Evict and Put Together?

Yes.

Very common.

---

## Most Common Enterprise Use?

Evicting multiple related caches.

---

# Enterprise Best Practice

Typical update flow:

```java
@Caching(

 put = {
   @CachePut(...)
 },

 evict = {
   @CacheEvict(...)
 }
)
```

---

Example:

```text
Update Product Cache
Clear Product List Cache
```

---

Typical delete flow:

```java
@Caching(
 evict = {
   @CacheEvict(...),
   @CacheEvict(...)
 }
)
```

---

Use with:

:contentReference[oaicite:0]{index=0}

or

:contentReference[oaicite:1]{index=1}

in production systems.

---

# Key Points To Remember

- `@Caching` groups multiple cache annotations.
- Can contain `@Cacheable`, `@CachePut`, and `@CacheEvict`.
- Useful when multiple cache operations are needed.
- Commonly used for cache synchronization.
- Supports SpEL expressions.
- Supports `allEntries`.
- Supports `beforeInvocation`.
- Uses Spring AOP proxies internally.
- Helps maintain cache consistency.
- Frequently asked Spring Cache interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Cacheable` | Read/Store Cache |
| `@CachePut` | Update Cache |
| `@CacheEvict` | Remove Cache |
| `@Caching` | Combine Cache Operations |
| `@EnableCaching` | Enable Cache Support |

---

# Interview Shortcut

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
public void deleteProduct(
       Long id) {

    repository.deleteById(id);
}
```

Execution:

```text
Delete Product
      ↓
Remove Product Cache
      ↓
Remove Inventory Cache
      ↓
Return
```

Think of `@Caching` as:

```java
cache1.remove(id);

cache2.remove(id);

cache3.remove(id);
```

or

```java
cache1.put(...);

cache2.put(...);

cache3.remove(...);
```

executed automatically by Spring.

---

# Real Production Scenario

```java
@Caching(

 put = {

   @CachePut(
      value="products",
      key="#result.id"
   )
 },

 evict = {

   @CacheEvict(
      value="productList",
      allEntries=true
   )
 }
)
public Product updateProduct(
      Product product) {

    return repository.save(product);
}
```

Flow:

```text
Update Database
      ↓
Update Single Product Cache
      ↓
Clear Cached Product List
      ↓
Future Reads Get Fresh Data
```

Result:

```text
Consistent Cache
Fresh Data
Better Performance
```

`@Caching` is Spring's composite caching annotation that allows multiple cache operations (`@Cacheable`, `@CachePut`, and `@CacheEvict`) to be applied together on a single method, making cache management cleaner and more maintainable.