
## Definition

`@EnableFeignClients` is a Spring Cloud OpenFeign annotation that enables scanning and registration of all Feign client interfaces in the application.

It tells Spring:

> Find all interfaces annotated with `@FeignClient` and create implementations for them automatically at runtime.

Package:

```java
import org.springframework.cloud.openfeign.EnableFeignClients;
```

---

# What is Feign?

Feign is a **declarative HTTP client**.

Without Feign:

```java
RestTemplate
WebClient
HttpURLConnection
```

You write HTTP request code manually.

---

With Feign:

```java
@FeignClient(name = "USER-SERVICE")
public interface UserClient {

    @GetMapping("/users")
    List<User> getUsers();
}
```

Just define an interface.

Spring creates the implementation automatically.

---

# Why Do We Need @EnableFeignClients?

Suppose:

```text
Order Service
```

needs to call:

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

A lot of boilerplate code.

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

Much cleaner.

---

# Required Dependency

Spring Boot 3+

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>
        spring-cloud-starter-openfeign
    </artifactId>
</dependency>
```

---

# Basic Example

Main Class:

```java
@SpringBootApplication
@EnableFeignClients
public class OrderApplication {

    public static void main(
            String[] args) {

        SpringApplication.run(
            OrderApplication.class,
            args
        );
    }
}
```

---

Meaning:

```text
Scan For
@FeignClient Interfaces

Create Beans
Automatically
```

---

# What Happens Internally?

Application Startup:

```text
Spring Starts
      ↓
@EnableFeignClients Activated
      ↓
Searches For @FeignClient
      ↓
Creates Proxy Objects
      ↓
Registers As Beans
```

---

Example:

```java
@FeignClient(name="USER-SERVICE")
public interface UserClient {

}
```

---

Spring creates:

```java
class UserClientProxy
    implements UserClient
{
   ...
}
```

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

Request Flow:

```text
Method Call
      ↓
Feign Proxy
      ↓
HTTP Request
      ↓
Remote Service
```

---

# Creating a Feign Client

```java
@FeignClient(
    name = "USER-SERVICE"
)
public interface UserClient {

    @GetMapping("/users")
    List<User> getUsers();
}
```

---

Usage:

```java
@Service
public class OrderService {

    @Autowired
    private UserClient userClient;

    public List<User> users() {

        return userClient.getUsers();
    }
}
```

---

No HTTP code needed.

---

# Service Discovery Integration

Very common in microservices.

---

Works with:

```text
Eureka
Consul
Kubernetes
```

---

Example:

```java
@FeignClient(
    name = "USER-SERVICE"
)
```

---

Feign asks service registry:

```text
Where Is USER-SERVICE?
```

---

Registry returns:

```text
10.0.0.15:8081
```

---

Feign calls it automatically.

---

# Using URL Instead Of Service Discovery

```java
@FeignClient(
    name = "userClient",
    url = "http://localhost:8081"
)
public interface UserClient {

}
```

---

Useful for:

```text
External APIs
```

---

# Custom Path

```java
@FeignClient(
    name = "USER-SERVICE",
    path = "/api/v1"
)
```

---

Method:

```java
@GetMapping("/users")
```

---

Actual URL:

```text
/api/v1/users
```

---

# Multiple Feign Clients

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

All automatically registered.

---

# Specifying Packages

Default:

```java
@EnableFeignClients
```

---

Spring scans current package and subpackages.

---

Custom:

```java
@EnableFeignClients(
    basePackages = {
        "com.app.clients"
    }
)
```

---

Only scans:

```text
com.app.clients
```

---

# Specifying Classes

```java
@EnableFeignClients(
    clients = {
        UserClient.class,
        PaymentClient.class
    }
)
```

---

Registers only:

```text
UserClient
PaymentClient
```

---

# Feign + Circuit Breaker

Very common production setup.

```java
@FeignClient(
    name = "PAYMENT-SERVICE"
)
public interface PaymentClient {

    @GetMapping("/pay")
    String pay();
}
```

---

Service:

```java
@CircuitBreaker(
    name = "payment",
    fallbackMethod = "fallback"
)
public String process() {

    return paymentClient.pay();
}
```

---

Provides:

```text
Fault Tolerance
```

---

# Feign + Retry

```java
@Retry(name = "payment")
public String pay() {

    return paymentClient.pay();
}
```

---

Useful for:

```text
Temporary Failures
```

---

# Real Production Example

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

---

Clients:

```java
@FeignClient(name="USER-SERVICE")
```

```java
@FeignClient(name="PAYMENT-SERVICE")
```

```java
@FeignClient(name="INVENTORY-SERVICE")
```

---

Communication becomes:

```java
userClient.getUser();

paymentClient.pay();

inventoryClient.stock();
```

---

Very clean.

---

# Without @EnableFeignClients

Suppose:

```java
@FeignClient(name="USER-SERVICE")
public interface UserClient {

}
```

---

But:

```java
@EnableFeignClients
```

missing.

---

Startup:

```text
No Bean Found
```

---

Error:

```text
NoSuchBeanDefinitionException
```

---

Because Spring never scanned the Feign interface.

---

# Common Use Cases

## Microservice Communication

```text
Order → User
```

---

## Service Discovery

```text
Eureka
```

---

## External APIs

```text
Payment Gateway
```

---

## Cloud Applications

```text
AWS
Azure
GCP
```

---

# Common Mistakes

## Mistake 1

Missing Dependency

Without:

```xml
spring-cloud-starter-openfeign
```

---

Feign won't work.

---

# Mistake 2

Missing @EnableFeignClients

Wrong:

```java
@SpringBootApplication
```

Only.

---

Result:

```text
Feign Clients Not Registered
```

---

# Mistake 3

Wrong Package Scanning

Feign interface outside scanned package.

---

Spring can't find it.

---

# Common Interview Questions

## What is @EnableFeignClients?

Enables scanning and registration of Feign clients.

---

## Why Is It Used?

To activate OpenFeign support.

---

## What Does It Scan?

Interfaces annotated with:

```java
@FeignClient
```

---

## What Happens Internally?

Spring creates proxy implementations.

---

## Is It Mandatory?

Yes, unless auto-configuration or explicit configuration is provided.

---

## Difference Between @FeignClient and @EnableFeignClients?

`@FeignClient`

```text
Marks Interface
```

---

`@EnableFeignClients`

```text
Activates Scanning
```

---

# Enterprise Best Practice

Use:

```java
@EnableFeignClients
```

in:

```java
@SpringBootApplication
```

class.

---

Organize clients:

```text
client package
```

Example:

```text
com.company.client
```

---

Combine with:

```java
@CircuitBreaker
@Retry
@TimeLimiter
```

for resilience.

---

# Key Points To Remember

- `@EnableFeignClients` enables OpenFeign support.
- Scans interfaces annotated with `@FeignClient`.
- Creates proxy implementations automatically.
- Simplifies microservice communication.
- Works with Eureka and service discovery.
- Eliminates boilerplate HTTP code.
- Supports package-specific scanning.
- Frequently used in Spring Cloud microservices.
- Required for Feign client registration.
- Frequently asked Spring Cloud interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableFeignClients` | Enable Feign Scanning |
| `@FeignClient` | Define HTTP Client |
| `@EnableDiscoveryClient` | Enable Service Discovery |
| `@EnableEurekaClient` | Eureka Registration |
| `@LoadBalanced` | Client-Side Load Balancing |

---

# Interview Shortcut

```java
@SpringBootApplication
@EnableFeignClients
public class OrderApplication {
}
```

Meaning:

```text
Scan All @FeignClient Interfaces

Create Proxy Implementations

Register Them As Spring Beans
```

---

# Real Production Scenario

```text
ORDER-SERVICE
      ↓
USER-SERVICE

ORDER-SERVICE
      ↓
PAYMENT-SERVICE

ORDER-SERVICE
      ↓
INVENTORY-SERVICE
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

Application:

```java
userClient.getUser();

paymentClient.pay();

inventoryClient.checkStock();
```

Result:

```text
Clean Code
Less Boilerplate
Easy Service Calls
Microservice Friendly
```

`@EnableFeignClients` is a Spring Cloud OpenFeign annotation that activates Feign support by scanning for `@FeignClient` interfaces, generating runtime proxy implementations, and registering them as Spring beans for declarative HTTP communication between services.