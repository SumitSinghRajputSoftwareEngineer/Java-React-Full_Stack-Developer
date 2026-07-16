
## Definition

`@FeignClient` is a Spring Cloud OpenFeign annotation used to declare an HTTP client interface for calling another microservice or external REST API.

It tells Spring:

> Generate an implementation of this interface and make HTTP calls automatically whenever its methods are invoked.

Package:

```java
import org.springframework.cloud.openfeign.FeignClient;
```

---

# Why Do We Need @FeignClient?

Suppose:

```text
Order Service
```

needs data from:

```text
User Service
```

---

Without Feign:

```java
@RestController
public class OrderService {

    @Autowired
    private RestTemplate restTemplate;

    public User getUser(Long id) {

        return restTemplate.getForObject(
            "http://USER-SERVICE/users/" + id,
            User.class
        );
    }
}
```

---

Problems:

```text
Boilerplate Code
Manual URL Handling
Manual Error Handling
Harder Maintenance
```

---

With Feign:

```java
@FeignClient(name = "USER-SERVICE")
public interface UserClient {

    @GetMapping("/users/{id}")
    User getUser(
        @PathVariable Long id
    );
}
```

Usage:

```java
userClient.getUser(1L);
```

That's it.

---

# Required Dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>
        spring-cloud-starter-openfeign
    </artifactId>
</dependency>
```

---

Also enable Feign:

```java
@SpringBootApplication
@EnableFeignClients
public class Application {
}
```

---

# Basic Example

```java
@FeignClient(
    name = "USER-SERVICE"
)
public interface UserClient {

    @GetMapping("/users/{id}")
    User getUser(
        @PathVariable Long id
    );
}
```

---

Usage:

```java
@Service
public class OrderService {

    @Autowired
    private UserClient userClient;

    public User fetchUser(
            Long id) {

        return userClient.getUser(id);
    }
}
```

---

# What Happens Internally?

At startup:

```text
@EnableFeignClients
       ↓
Find @FeignClient
       ↓
Create Dynamic Proxy
       ↓
Register Bean
```

---

Spring generates something conceptually like:

```java
class UserClientProxy
       implements UserClient
{
    ...
}
```

---

Method call:

```java
userClient.getUser(1L);
```

becomes:

```http
GET /users/1
```

automatically.

---

# Architecture

```text
Order Service
      │
      ▼

  Feign Client

      │
      ▼

 User Service
```

---

Flow:

```text
Method Call
      ↓
Feign Proxy
      ↓
HTTP Request
      ↓
Response
      ↓
Java Object
```

---

# Using Service Discovery

Very common.

With Eureka:

```java
@FeignClient(
    name = "USER-SERVICE"
)
```

---

Feign asks Eureka:

```text
Where is USER-SERVICE?
```

---

Eureka returns:

```text
10.0.0.5:8081
```

---

Feign calls:

```http
http://10.0.0.5:8081/users/1
```

---

Automatically.

---

# Calling External APIs

Instead of service discovery:

```java
@FeignClient(
    name = "weatherClient",
    url = "https://api.weather.com"
)
public interface WeatherClient {

    @GetMapping("/weather")
    Weather getWeather();
}
```

---

Useful for:

```text
Third Party APIs
```

---

# GET Request Example

```java
@FeignClient(
    name = "USER-SERVICE"
)
public interface UserClient {

    @GetMapping("/users/{id}")
    User getUser(
        @PathVariable Long id
    );
}
```

---

HTTP:

```http
GET /users/1
```

---

# POST Request Example

```java
@PostMapping("/users")
User createUser(
        @RequestBody User user
);
```

---

HTTP:

```http
POST /users
```

---

# PUT Request Example

```java
@PutMapping("/users/{id}")
User updateUser(
    @PathVariable Long id,
    @RequestBody User user
);
```

---

HTTP:

```http
PUT /users/1
```

---

# DELETE Request Example

```java
@DeleteMapping("/users/{id}")
void deleteUser(
    @PathVariable Long id
);
```

---

HTTP:

```http
DELETE /users/1
```

---

# Query Parameters

```java
@GetMapping("/users")
List<User> search(
    @RequestParam String city
);
```

---

Call:

```java
search("Delhi");
```

---

HTTP:

```http
/users?city=Delhi
```

---

# Headers Example

```java
@GetMapping("/users")
List<User> getUsers(
   @RequestHeader("token")
   String token
);
```

---

HTTP:

```http
token: abc123
```

---

# Path Variable Example

```java
@GetMapping("/users/{id}")
User getUser(
    @PathVariable Long id
);
```

---

Call:

```java
getUser(10L);
```

---

HTTP:

```http
/users/10
```

---

# Multiple Clients

```java
@FeignClient(name="USER-SERVICE")
interface UserClient {}
```

```java
@FeignClient(name="PAYMENT-SERVICE")
interface PaymentClient {}
```

```java
@FeignClient(name="ORDER-SERVICE")
interface OrderClient {}
```

---

All become Spring beans.

---

# Feign + Circuit Breaker

Production favorite.

```java
@Service
public class PaymentService {

    @Autowired
    private PaymentClient client;

    @CircuitBreaker(
        name = "payment",
        fallbackMethod = "fallback"
    )
    public String pay() {

        return client.pay();
    }
}
```

---

Protects against:

```text
Service Failure
```

---

# Feign + Retry

```java
@Retry(
    name = "payment"
)
public String pay() {

    return client.pay();
}
```

---

Useful for:

```text
Temporary Failures
```

---

# Feign + TimeLimiter

```java
@TimeLimiter(
    name = "payment"
)
```

---

Protects against:

```text
Slow Responses
```

---

# Feign + Bulkhead

```java
@Bulkhead(
    name = "payment"
)
```

---

Protects against:

```text
Thread Exhaustion
```

---

# Real Production Example

```text
ORDER-SERVICE
      ↓

USER-SERVICE
PAYMENT-SERVICE
INVENTORY-SERVICE
```

---

Clients:

```java
@FeignClient(
    name="USER-SERVICE"
)
```

```java
@FeignClient(
    name="PAYMENT-SERVICE"
)
```

```java
@FeignClient(
    name="INVENTORY-SERVICE"
)
```

---

Usage:

```java
userClient.getUser();

paymentClient.pay();

inventoryClient.stock();
```

---

Very clean.

---

# Common Attributes

## name

```java
@FeignClient(
    name = "USER-SERVICE"
)
```

Service name.

---

## url

```java
@FeignClient(
    url="https://api.xyz.com"
)
```

Direct URL.

---

## path

```java
@FeignClient(
    name="USER",
    path="/api/v1"
)
```

Base path.

---

## configuration

```java
@FeignClient(
    configuration=
      FeignConfig.class
)
```

Custom settings.

---

# Common Mistakes

## Mistake 1

Missing Enable Annotation

Wrong:

```java
@SpringBootApplication
```

Without:

```java
@EnableFeignClients
```

---

Result:

```text
No Bean Found
```

---

# Mistake 2

Using Class Instead Of Interface

Wrong:

```java
@FeignClient
public class UserClient
```

---

Correct:

```java
public interface UserClient
```

---

# Mistake 3

Missing HTTP Mapping

Wrong:

```java
User getUser();
```

---

Need:

```java
@GetMapping
```

---

# Common Interview Questions

## What is @FeignClient?

Declarative HTTP client.

---

## Why Use It?

To call remote services without writing HTTP code.

---

## Does Spring Create Implementation?

Yes.

At runtime.

---

## Can It Work With Eureka?

Yes.

Very commonly.

---

## Difference Between RestTemplate and Feign?

| RestTemplate | Feign |
|-------------|--------|
| Imperative | Declarative |
| More Code | Less Code |
| Manual HTTP Handling | Automatic |
| Harder Maintenance | Easier |

---

## Is @FeignClient An Interface Or Class?

```text
Interface
```

---

# Enterprise Best Practice

Create a dedicated package:

```text
client
```

Example:

```text
com.company.client
```

---

Use:

```java
@FeignClient
```

for:

```text
Internal Microservices
External APIs
Cloud Services
```

---

Combine with:

```java
@CircuitBreaker
@Retry
@TimeLimiter
@Bulkhead
```

for resiliency.

---

# Key Points To Remember

- `@FeignClient` creates declarative HTTP clients.
- Eliminates boilerplate REST code.
- Generates implementations automatically.
- Works with service discovery.
- Supports GET, POST, PUT, DELETE.
- Supports headers, query params, path variables.
- Frequently used in microservices.
- Requires `@EnableFeignClients`.
- Integrates with Resilience4j.
- Frequently asked Spring Cloud interview topic.

---

# Quick Comparison

| Component | Purpose |
|------------|----------|
| `@FeignClient` | Define HTTP Client |
| `@EnableFeignClients` | Enable Feign Scanning |
| `@CircuitBreaker` | Handle Failures |
| `@Retry` | Retry Requests |
| `@TimeLimiter` | Handle Timeouts |

---

# Interview Shortcut

```java
@FeignClient(
    name = "USER-SERVICE"
)
public interface UserClient {

    @GetMapping("/users/{id}")
    User getUser(
        @PathVariable Long id
    );
}
```

Meaning:

```text
Create HTTP Client
For USER-SERVICE

Generate Implementation
Automatically

Allow Direct Method Calls
Instead Of Writing REST Code
```

---

# Real Production Scenario

```text
Order Service
      ↓
User Service

Order Service
      ↓
Payment Service

Order Service
      ↓
Inventory Service
```

Feign Clients:

```java
@FeignClient(name="USER-SERVICE")
```

```java
@FeignClient(name="PAYMENT-SERVICE")
```

```java
@FeignClient(name="INVENTORY-SERVICE")
```

Usage:

```java
userClient.getUser();

paymentClient.pay();

inventoryClient.checkStock();
```

Result:

```text
Cleaner Code
Less Boilerplate
Easy Service Communication
Microservice Friendly
```

`@FeignClient` is a Spring Cloud OpenFeign annotation that declares a type-safe, declarative HTTP client interface, allowing Spring to automatically generate the implementation and handle REST communication with other services or external APIs.