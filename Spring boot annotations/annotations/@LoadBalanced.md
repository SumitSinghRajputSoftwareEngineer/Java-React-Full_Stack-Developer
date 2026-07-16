
## Definition

`@LoadBalanced` is a Spring Cloud annotation used to enable **client-side load balancing** for `RestTemplate`, `RestClient`, or `WebClient`.

It tells Spring:

> When a service name is used instead of an IP address or URL, automatically resolve it through the service registry and distribute requests across multiple instances.

Package:

```java
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
```

---

# Why Do We Need @LoadBalanced?

Suppose:

```text
ORDER-SERVICE
```

needs to call:

```text
USER-SERVICE
```

---

There are multiple instances:

```text
USER-SERVICE-1 → 10.0.0.1

USER-SERVICE-2 → 10.0.0.2

USER-SERVICE-3 → 10.0.0.3
```

---

Without Load Balancing:

```java
http://10.0.0.1:8080/users
```

---

Problems:

```text
Single Instance Overloaded
No Fault Tolerance
No Traffic Distribution
```

---

Solution:

```java
@LoadBalanced
```

---

# Real-Life Analogy

Imagine 3 billing counters.

```text
Counter 1
Counter 2
Counter 3
```

---

Customers arrive:

```text
Customer 1 → Counter 1

Customer 2 → Counter 2

Customer 3 → Counter 3
```

---

Load gets distributed.

---

Exactly what Load Balancer does.

---

# Problem Without @LoadBalanced

Application:

```java
restTemplate.getForObject(
   "http://USER-SERVICE/users",
   String.class
);
```

---

Without `@LoadBalanced`:

```text
UnknownHostException
```

Because:

```text
USER-SERVICE
```

is not a real hostname.

---

Spring cannot resolve it.

---

# Solution

```java
@Bean
@LoadBalanced
public RestTemplate restTemplate() {

    return new RestTemplate();
}
```

---

Now Spring can resolve:

```text
USER-SERVICE
```

through:

```text
Eureka
Consul
Kubernetes
```

---

# Required Dependency

Modern Spring Cloud:

```xml
<dependency>
    <groupId>
        org.springframework.cloud
    </groupId>
    <artifactId>
        spring-cloud-starter-loadbalancer
    </artifactId>
</dependency>
```

---

# Basic Example

Configuration:

```java
@Configuration
public class Config {

    @Bean
    @LoadBalanced
    public RestTemplate
    restTemplate() {

        return new RestTemplate();
    }
}
```

---

Usage:

```java
@Service
public class OrderService {

    @Autowired
    private RestTemplate restTemplate;

    public String getUsers() {

        return restTemplate
                .getForObject(
                    "http://USER-SERVICE/users",
                    String.class
                );
    }
}
```

---

# What Happens Internally?

Request:

```java
http://USER-SERVICE/users
```

---

Spring:

```text
Ask Service Registry
```

---

Registry:

```text
USER-SERVICE

10.0.0.1
10.0.0.2
10.0.0.3
```

---

LoadBalancer chooses:

```text
10.0.0.2
```

---

Final Request:

```http
http://10.0.0.2/users
```

---

# Architecture

```text
Order Service
      │
      ▼

 Spring LoadBalancer

      │
      ▼

 USER-SERVICE
   │   │   │
   ▼   ▼   ▼

 Inst1 Inst2 Inst3
```

---

# Request Distribution

Example:

```text
Request 1 → Instance 1

Request 2 → Instance 2

Request 3 → Instance 3

Request 4 → Instance 1

Request 5 → Instance 2
```

---

Usually:

```text
Round Robin
```

---

# Without @LoadBalanced

```java
@Bean
public RestTemplate
restTemplate() {

    return new RestTemplate();
}
```

---

Call:

```java
http://USER-SERVICE/users
```

---

Result:

```text
UnknownHostException
```

---

Because no service resolution exists.

---

# With Eureka

Service Registration:

```text
USER-SERVICE
```

Registers itself.

---

Order Service:

```java
restTemplate.getForObject(
    "http://USER-SERVICE/users",
    String.class
);
```

---

Spring:

```text
Queries Eureka
```

---

Gets:

```text
Instance List
```

---

Performs:

```text
Client-Side Load Balancing
```

---

# Client-Side vs Server-Side Load Balancing

Most Important Interview Question.

---

## Client-Side Load Balancing

Example:

```java
@LoadBalanced
RestTemplate
```

---

Flow:

```text
Client Chooses Instance
```

---

Examples:

```text
Spring Cloud LoadBalancer
Ribbon (Old)
```

---

## Server-Side Load Balancing

Example:

```text
Nginx
HAProxy
AWS ELB
Azure Load Balancer
```

---

Flow:

```text
Load Balancer Chooses Instance
```

---

Comparison:

| Feature | Client Side | Server Side |
|----------|------------|------------|
| Selection Done By | Client | Load Balancer |
| Extra Network Hop | No | Yes |
| Faster | Yes | Slightly Slower |
| Spring Cloud | Yes | No |

---

# Using @LoadBalanced With WebClient

Configuration:

```java
@Bean
@LoadBalanced
public WebClient.Builder
webClientBuilder() {

    return WebClient.builder();
}
```

---

Usage:

```java
webClientBuilder.build()
        .get()
        .uri(
          "http://USER-SERVICE/users"
        )
        .retrieve()
        .bodyToMono(String.class);
```

---

# Using @LoadBalanced With RestClient

Spring Boot 3:

```java
@Bean
@LoadBalanced
RestClient.Builder
restClientBuilder() {

    return RestClient.builder();
}
```

---

Modern alternative to:

```java
RestTemplate
```

---

# Load Balancing Algorithms

Spring Cloud LoadBalancer supports:

---

## Round Robin

Default.

```text
1 → Instance A

2 → Instance B

3 → Instance C

4 → Instance A
```

---

## Random

```text
Random Instance
```

---

## Custom

Implement your own strategy.

---

# Real Production Example

Instances:

```text
USER-SERVICE-1

USER-SERVICE-2

USER-SERVICE-3
```

---

Traffic:

```text
3000 Requests
```

---

Distribution:

```text
1000 → Instance 1

1000 → Instance 2

1000 → Instance 3
```

---

Balanced load.

---

# @LoadBalanced vs Feign

Interview Favorite.

---

## Feign

```java
@FeignClient(
   name="USER-SERVICE"
)
```

---

Provides:

```text
Declarative HTTP Client
```

---

## @LoadBalanced

```java
@LoadBalanced
RestTemplate
```

---

Provides:

```text
Load Balancing
```

---

Comparison:

| Feature | Feign | LoadBalanced |
|----------|-------|-------------|
| HTTP Client | ✅ | ❌ |
| Load Balancing | ✅ (internally) | ✅ |
| Declarative | ✅ | ❌ |
| Manual Calls | ❌ | ✅ |

---

Important:

Feign internally uses:

```text
Spring Cloud LoadBalancer
```

---

# Common Use Cases

## Eureka-Based Microservices

```text
Order → User
```

---

## Kubernetes Services

```text
Pod Discovery
```

---

## High Availability Systems

```text
Multiple Instances
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

```xml
spring-cloud-starter-loadbalancer
```

---

Without it:

```text
Load Balancing Won't Work
```

---

# Mistake 2

Missing Annotation

Wrong:

```java
@Bean
RestTemplate
```

---

Correct:

```java
@Bean
@LoadBalanced
RestTemplate
```

---

# Mistake 3

Using Service Name Without Registry

Wrong:

```java
http://USER-SERVICE
```

without:

```text
Eureka
Consul
Kubernetes
```

---

Cannot resolve service.

---

# Common Interview Questions

## What is @LoadBalanced?

Enables client-side load balancing.

---

## Where Is It Used?

```java
RestTemplate
WebClient
RestClient
```

---

## Why Is It Needed?

To resolve service names and distribute traffic.

---

## Does Feign Need It?

No.

Feign already integrates with Spring Cloud LoadBalancer.

---

## Difference Between Client-Side And Server-Side Load Balancing?

Client chooses instance vs load balancer chooses instance.

---

## What Algorithm Is Default?

```text
Round Robin
```

---

# Enterprise Best Practice

Prefer:

```java
Feign Client
```

for microservice communication.

---

Use:

```java
@LoadBalanced
```

when using:

```java
RestTemplate
RestClient
WebClient
```

---

Integrate with:

```text
Eureka
Kubernetes
Consul
```

for service discovery.

---

# Key Points To Remember

- `@LoadBalanced` enables client-side load balancing.
- Used with RestTemplate, RestClient, and WebClient.
- Resolves service names like `USER-SERVICE`.
- Works with Eureka, Consul, Kubernetes.
- Uses Spring Cloud LoadBalancer.
- Default algorithm is Round Robin.
- Helps distribute traffic across service instances.
- Provides fault tolerance and scalability.
- Not needed for Feign clients.
- Frequently asked Spring Cloud interview topic.

---

# Quick Comparison

| Component | Purpose |
|------------|----------|
| `@LoadBalanced` | Client-Side Load Balancing |
| `@FeignClient` | Declarative HTTP Client |
| `@EnableFeignClients` | Enable Feign Support |
| `@EnableDiscoveryClient` | Enable Service Discovery |
| `@EnableEurekaClient` | Register With Eureka |

---

# Interview Shortcut

```java
@Bean
@LoadBalanced
public RestTemplate
restTemplate() {

    return new RestTemplate();
}
```

Meaning:

```text
Resolve Service Names

Discover Instances

Distribute Requests

Perform Client-Side
Load Balancing
```

---

# Real Production Scenario

```text
ORDER-SERVICE
       ↓

USER-SERVICE
  │    │    │

  ▼    ▼    ▼

INST1 INST2 INST3
```

Request Flow:

```text
Order Service
      ↓
Spring LoadBalancer
      ↓
Select Instance
      ↓
Call User Service
```

Result:

```text
Traffic Distribution
High Availability
Scalability
Fault Tolerance
```

`@LoadBalanced` is a Spring Cloud annotation that enables client-side load balancing by allowing RestTemplate, RestClient, or WebClient to resolve service names through service discovery and distribute requests across multiple service instances automatically.