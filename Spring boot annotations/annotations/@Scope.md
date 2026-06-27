
## Definition

`@Scope` is a Spring annotation used to define the lifecycle and visibility of a bean inside the Spring IoC Container.

It tells Spring:

> How many instances of a bean should be created and how long those instances should live.

Package:

```java
import org.springframework.context.annotation.Scope;
```

---

# Why Do We Need @Scope?

By default, Spring creates only one object of a bean.

Example:

```java
@Service
public class EmployeeService {

}
```

Spring creates:

```text
One Object
```

for the entire application.

This is called:

```text
Singleton Scope
```

---

However, some applications require:

```text
One Object Per Request
```

or

```text
New Object Every Time
```

or

```text
One Object Per Session
```

To support these requirements Spring provides:

```java
@Scope
```

---

# Default Scope

If no scope is specified:

```java
@Component
public class EmployeeService {

}
```

Spring automatically uses:

```java
@Scope("singleton")
```

---

Equivalent to:

```java
@Component
@Scope("singleton")
public class EmployeeService {

}
```

---

# Available Scopes

Spring supports:

```text
singleton
prototype
request
session
application
websocket
```

---

# 1. Singleton Scope

## Definition

Only one instance of the bean exists in the entire Spring Container.

---

```java
@Component
@Scope("singleton")
public class EmployeeService {

}
```

---

Spring creates:

```text
Only One Object
```

---

Diagram:

```text
Spring Container
        │
        ▼
 EmployeeService
        │
        ▼
 Single Instance
```

---

# Example

```java
@Component
@Scope("singleton")
public class EmployeeService {

}
```

---

```java
@Autowired
private EmployeeService service1;

@Autowired
private EmployeeService service2;
```

---

Result:

```java
service1 == service2
```

Output:

```text
true
```

---

# Internal Working

Application Startup:

```text
Create Bean
      │
      ▼
Store In Container
      │
      ▼
Reuse Same Instance
```

---

# Use Cases

- Services
- Repositories
- Configuration Classes
- Utility Components

---

# 2. Prototype Scope

## Definition

A new bean instance is created every time it is requested.

---

```java
@Component
@Scope("prototype")
public class Employee {

}
```

---

Diagram:

```text
Request Bean
     │
     ▼
New Object

Request Again
     │
     ▼
New Object
```

---

# Example

```java
@Component
@Scope("prototype")
public class Employee {

}
```

---

```java
Employee e1 =
        context.getBean(Employee.class);

Employee e2 =
        context.getBean(Employee.class);
```

---

Result:

```java
e1 == e2
```

Output:

```text
false
```

---

# Internal Working

```text
Request Bean
      │
      ▼
Create Object
      │
      ▼
Return Object
```

Repeated every time.

---

# Use Cases

- Report Objects
- DTO Builders
- Workflow Instances
- Job Execution Context

---

# Singleton vs Prototype

| Feature | Singleton | Prototype |
|----------|-----------|-----------|
| Instances | One | Many |
| Creation | Startup | On Demand |
| Memory Usage | Low | Higher |
| Performance | Faster | Slightly Slower |
| Default Scope | Yes | No |

---

# Important Interview Scenario

Singleton Bean:

```java
@Service
public class EmployeeService {

    @Autowired
    private Employee employee;

}
```

---

Prototype Bean:

```java
@Component
@Scope("prototype")
public class Employee {

}
```

---

Question:

Will Spring create a new Employee every time?

---

Answer:

```text
No
```

Only one Employee instance gets injected during singleton creation.

---

Solution:

Use:

```java
@Lookup
```

or

```java
ObjectProvider
```

---

# 3. Request Scope

## Definition

One bean instance is created per HTTP request.

---

```java
@Component
@Scope(
    value = WebApplicationContext.SCOPE_REQUEST
)
public class RequestData {

}
```

---

Equivalent:

```java
@RequestScope
```

---

# Example

Request 1:

```text
Create Object A
```

---

Request 2:

```text
Create Object B
```

---

Result:

```java
A != B
```

---

# Use Cases

- Request Metadata
- User Input
- Request Context

---

# Internal Working

```text
HTTP Request
      │
      ▼
Create Bean
      │
      ▼
Process Request
      │
      ▼
Destroy Bean
```

---

# 4. Session Scope

## Definition

One bean instance per HTTP session.

---

```java
@Component
@Scope(
    value = WebApplicationContext.SCOPE_SESSION
)
public class UserSession {

}
```

---

Equivalent:

```java
@SessionScope
```

---

# Example

User A:

```text
Session Bean A
```

---

User B:

```text
Session Bean B
```

---

Same user:

```text
Reuse Session Bean
```

---

# Use Cases

- Shopping Cart
- Logged-In User Data
- Session Preferences

---

# Internal Working

```text
User Login
      │
      ▼
Create Session Bean
      │
      ▼
Reuse During Session
      │
      ▼
Session Expired
      │
      ▼
Destroy Bean
```

---

# 5. Application Scope

## Definition

One bean per ServletContext.

---

```java
@Component
@Scope(
    value = WebApplicationContext.SCOPE_APPLICATION
)
public class ApplicationData {

}
```

---

Equivalent:

```java
@ApplicationScope
```

---

# Use Cases

- Shared Application Settings
- Application Cache
- Shared Counters

---

Diagram:

```text
Entire Web Application
          │
          ▼
Single Shared Bean
```

---

# 6. WebSocket Scope

## Definition

One bean instance per WebSocket session.

---

```java
@Component
@Scope(
    value = "websocket"
)
public class ChatSession {

}
```

---

# Use Cases

- Chat Applications
- Live Notifications
- Real-Time Messaging

---

# Scope Constants

Instead of Strings:

---

```java
@Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
```

---

```java
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
```

---

Preferred because:

- Compile-Time Safety
- No Typo Risk

---

# Scope on @Bean Methods

---

```java
@Configuration
public class AppConfig {

    @Bean
    @Scope("prototype")
    public Employee employee() {

        return new Employee();

    }

}
```

---

Spring creates new Employee each request.

---

# Scoped Proxy

Very Important Interview Topic

Suppose:

```java
@Service
public class EmployeeService {

    @Autowired
    private RequestData requestData;

}
```

---

RequestData:

```java
@RequestScope
```

---

Problem:

```text
Singleton
       ↓
Request Scope
```

Spring cannot inject request-scoped bean directly.

---

Solution:

```java
@Scope(
    value = WebApplicationContext.SCOPE_REQUEST,
    proxyMode = ScopedProxyMode.TARGET_CLASS
)
```

---

Spring injects a proxy instead.

---

# @Scope vs @Lazy

## @Scope

Controls:

```text
Number Of Instances
```

---

## @Lazy

Controls:

```text
Creation Time
```

---

Example:

```java
@Lazy
@Scope("singleton")
```

---

Meaning:

```text
Single Instance
Created On Demand
```

---

# Bean Lifecycle by Scope

## Singleton

```text
Container Start
      │
      ▼
Create Bean
      │
      ▼
Destroy On Shutdown
```

---

## Prototype

```text
Request Bean
      │
      ▼
Create Bean
      │
      ▼
Spring Stops Managing
```

Important:

Spring does NOT call destroy methods automatically for prototype beans.

---

# Common Mistakes

## Mistake 1

Expecting Prototype Inside Singleton

---

```java
@Autowired
private PrototypeBean bean;
```

---

Only one prototype instance injected.

---

# Mistake 2

Using Request Scope Outside Web Application

---

```java
@RequestScope
```

requires:

```text
Web Context
```

---

Otherwise startup fails.

---

# Mistake 3

Using Session Scope in REST APIs

REST APIs should generally remain stateless.

---

# Mistake 4

Ignoring Scoped Proxies

---

```text
Singleton
      ↓
Request Bean
```

Requires proxy.

---

# Common Interview Questions

## What is @Scope?

Defines bean lifecycle and visibility inside Spring Container.

---

## What is the default scope?

```text
singleton
```

---

## How many instances does singleton create?

```text
One
```

---

## How many instances does prototype create?

```text
One Per Request From Container
```

---

## Does Spring manage prototype destruction?

```text
No
```

---

## Difference Between Singleton and Prototype?

| Singleton | Prototype |
|------------|-----------|
| One Object | Multiple Objects |
| Startup Creation | On Demand |
| Managed Entire Lifecycle | Partial Lifecycle |

---

## Which scope is used most?

```text
Singleton
```

---

## What annotation is equivalent to request scope?

```java
@RequestScope
```

---

## What annotation is equivalent to session scope?

```java
@SessionScope
```

---

# Real-World Use Cases

## Singleton

```text
Service Layer
Repository Layer
Utility Classes
```

---

## Prototype

```text
Workflow Objects
Report Objects
Processing Contexts
```

---

## Request

```text
Request Metadata
Audit Context
Request Tracking
```

---

## Session

```text
Shopping Cart
User Preferences
Authentication Context
```

---

## Application

```text
Shared Cache
Application Statistics
```

---

## WebSocket

```text
Chat Sessions
Live Dashboards
Real-Time Messaging
```

---

# Internal Scope Resolution Flow

```text
Bean Requested
      │
      ▼
Check Scope
      │
      ├── Singleton → Reuse Existing
      │
      ├── Prototype → Create New
      │
      ├── Request → One Per Request
      │
      ├── Session → One Per Session
      │
      ├── Application → One Per App
      │
      └── WebSocket → One Per Socket
```

---

# Key Points To Remember

- `@Scope` controls bean lifecycle and visibility.
- Default scope is `singleton`.
- `prototype` creates a new instance every lookup.
- `request` creates one bean per HTTP request.
- `session` creates one bean per user session.
- `application` creates one bean per ServletContext.
- `websocket` creates one bean per WebSocket session.
- Prototype beans are not fully managed after creation.
- Scoped proxies are required when injecting shorter-lived beans into longer-lived beans.
- One of the most important Spring Bean Lifecycle and Dependency Injection interview topics.