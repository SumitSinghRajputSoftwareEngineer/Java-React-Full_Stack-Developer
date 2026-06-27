
## Definition

`@Service` is a specialized Spring stereotype annotation used to mark a class as a Service Layer component.

It tells Spring:

> This class contains business logic and should be managed as a Spring Bean.

Internally:

```java
@Service
```

is a specialization of:

```java
@Component
```

meaning every `@Service` is automatically a Spring-managed bean.

---

# Syntax

```java
@Service
public class EmployeeService {

}
```

---

# Why Do We Need @Service?

In a layered architecture:

```text
Controller Layer
      │
      ▼
Service Layer
      │
      ▼
Repository Layer
      │
      ▼
Database
```

The Service Layer is responsible for:

- Business Logic
- Validation
- Transaction Management
- Orchestration
- External API Calls
- Security Checks
- Data Transformation

Instead of marking such classes with:

```java
@Component
```

Spring provides:

```java
@Service
```

to clearly indicate their purpose.

---

# Real-World Example

Suppose we have an Employee Management System.

---

## Controller

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    private final EmployeeService employeeService;

    public EmployeeController(
            EmployeeService employeeService) {

        this.employeeService = employeeService;
    }

}
```

---

## Service

```java
@Service
public class EmployeeService {

    public Employee getEmployee(Long id) {

        // Business Logic

        return employeeRepository.findById(id)
                .orElseThrow();
    }

}
```

---

## Repository

```java
@Repository
public interface EmployeeRepository
        extends JpaRepository<Employee, Long> {

}
```

---

Service layer sits between:

```text
Controller
and
Repository
```

---

# What Happens Internally?

When Spring starts:

```java
@ComponentScan
```

scans packages.

---

Finds:

```java
@Service
public class EmployeeService {
}
```

---

Since:

```java
@Service
```

contains:

```java
@Component
```

Spring:

### Step 1

Creates Bean Definition.

---

### Step 2

Registers Bean.

---

### Step 3

Instantiates Object.

---

### Step 4

Performs Dependency Injection.

---

### Step 5

Stores Bean in IoC Container.

---

Flow:

```text
@Service
      │
      ▼
Component Scan
      │
      ▼
Bean Definition
      │
      ▼
Bean Creation
      │
      ▼
Dependency Injection
      │
      ▼
Bean Ready
```

---

# Internal Structure of @Service

Simplified version:

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface Service {

}
```

Notice:

```java
@Component
```

inside.

Therefore:

```java
@Service
```

inherits all behavior of:

```java
@Component
```

---

# Example

```java
@Service
public class EmailService {

    public void sendEmail() {

    }

}
```

Injection:

```java
@Autowired
private EmailService emailService;
```

Spring injects automatically.

---

# Bean Naming

Default Bean Name:

```java
@Service
public class EmployeeService {

}
```

Bean Name:

```text
employeeService
```

---

# Custom Bean Name

```java
@Service("empService")
public class EmployeeService {

}
```

Bean Name:

```text
empService
```

---

# Constructor Injection (Recommended)

```java
@Service
public class EmployeeService {

    private final EmployeeRepository repository;

    public EmployeeService(
            EmployeeRepository repository) {

        this.repository = repository;
    }

}
```

Spring automatically injects dependency.

---

# Field Injection

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeRepository repository;

}
```

Works but generally not recommended.

---

# Setter Injection

```java
@Service
public class EmployeeService {

    private EmployeeRepository repository;

    @Autowired
    public void setRepository(
            EmployeeRepository repository) {

        this.repository = repository;
    }

}
```

---

# Business Logic Example

---

## Bad Practice

Controller directly accessing repository.

```java
@RestController
public class EmployeeController {

    @Autowired
    private EmployeeRepository repository;

}
```

Business logic scattered.

---

## Good Practice

```java
@RestController
public class EmployeeController {

    private final EmployeeService service;

}
```

---

```java
@Service
public class EmployeeService {

    private final EmployeeRepository repository;

}
```

Business logic centralized.

---

# Transaction Management

One of the most common usages.

---

```java
@Service
@Transactional
public class EmployeeService {

    public void saveEmployee(
            Employee employee) {

        employeeRepository.save(employee);

    }

}
```

---

Spring creates transactional proxy around service.

---

Flow:

```text
Method Called
      │
      ▼
Start Transaction
      │
      ▼
Execute Business Logic
      │
      ▼
Commit Transaction
```

---

# Multiple Repository Coordination

Very common in enterprise applications.

---

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;
    private final PaymentRepository paymentRepository;

}
```

---

Example:

```java
@Transactional
public void createOrder() {

    orderRepository.save(order);

    paymentRepository.save(payment);

}
```

Single transaction.

---

# Calling External APIs

Service layer usually handles external systems.

---

```java
@Service
public class WeatherService {

    private final RestTemplate restTemplate;

}
```

---

```java
public WeatherResponse getWeather() {

    return restTemplate.getForObject(
            url,
            WeatherResponse.class);

}
```

---

# Validation Logic

```java
@Service
public class EmployeeService {

    public void createEmployee(
            Employee employee) {

        if(employee.getSalary() < 0) {

            throw new RuntimeException(
                    "Salary cannot be negative"
            );
        }

    }

}
```

Business validation belongs in service layer.

---

# Service Layer Responsibilities

Typical responsibilities:

```text
Business Rules
Validation
Transactions
External APIs
Data Transformation
Caching
Security Checks
Event Publishing
Workflow Coordination
```

---

# @Service vs @Component

Most common interview question.

---

## @Component

Generic Spring Bean.

```java
@Component
public class EmployeeService {

}
```

---

## @Service

Business Layer Bean.

```java
@Service
public class EmployeeService {

}
```

---

Technically:

```java
Both create Spring Beans.
```

---

Difference:

```text
@Service provides semantic meaning.
```

---

| @Component | @Service |
|------------|-----------|
| Generic Bean | Business Layer Bean |
| No Layer Meaning | Service Layer Meaning |
| Base Annotation | Specialized Annotation |

---

# @Service vs @Repository

---

## @Service

Business Logic.

```java
@Service
public class EmployeeService {

}
```

---

## @Repository

Data Access Logic.

```java
@Repository
public class EmployeeRepository {

}
```

---

Responsibilities:

```text
@Service
    ↓
Business Logic

@Repository
    ↓
Database Operations
```

---

# @Service vs @Controller

---

## Controller

```java
@RestController
```

Handles:

```text
HTTP Requests
HTTP Responses
```

---

## Service

Handles:

```text
Business Processing
```

---

Architecture:

```text
Client
   │
   ▼
Controller
   │
   ▼
Service
   │
   ▼
Repository
   │
   ▼
Database
```

---

# Lazy Service

```java
@Service
@Lazy
public class ReportService {

}
```

Created only when first used.

---

# Profile Specific Service

```java
@Service
@Profile("dev")
public class MockPaymentService
        implements PaymentService {

}
```

Only active in:

```text
dev
```

profile.

---

# Scope on Service

Default:

```java
Singleton
```

---

```java
@Service
@Scope("prototype")
public class EmployeeService {

}
```

Creates new instance every request.

---

# Internal Flow

```text
Application Start
        │
        ▼
@ComponentScan
        │
        ▼
Find @Service
        │
        ▼
Create Bean Definition
        │
        ▼
Instantiate Service
        │
        ▼
Inject Dependencies
        │
        ▼
Store In IoC Container
```

---

# Common Mistakes

## Mistake 1

Putting database logic inside controller.

Wrong:

```java
@RestController
public class EmployeeController {

    @Autowired
    EmployeeRepository repository;

}
```

---

Correct:

```java
Controller
   ↓
Service
   ↓
Repository
```

---

## Mistake 2

Using @Component instead of @Service.

Works technically.

But:

```java
@Service
```

improves readability and architecture.

---

## Mistake 3

Putting business logic in repository.

Repositories should focus on:

```text
Data Access
```

not business rules.

---

# Common Interview Questions

## What is @Service?

Marks a class as a Service Layer component and Spring-managed bean.

---

## Is @Service a Component?

Yes.

Internally:

```java
@Service
```

contains:

```java
@Component
```

---

## Why use @Service instead of @Component?

Provides semantic meaning that the class contains business logic.

---

## Can @Service participate in Dependency Injection?

Yes.

```java
@Autowired
private EmployeeService service;
```

---

## What is the default scope of a Service Bean?

```java
Singleton
```

---

## Is @Transactional commonly used with @Service?

Yes.

Service layer is the most common place for transaction management.

---

## Can a Service call multiple repositories?

Yes.

This is one of its primary responsibilities.

---

# Real-World Significance

In enterprise applications, the Service Layer is the heart of the application.

Typical responsibilities include:

- Business Rules
- Workflow Processing
- Database Coordination
- External Integrations
- Event Handling
- Transactions
- Security Checks
- Caching

Most production Spring Boot applications contain dozens or hundreds of service classes.

---

# Key Points To Remember

- Specialized form of `@Component`.
- Represents the Service Layer.
- Used for business logic.
- Automatically discovered through component scanning.
- Supports Dependency Injection.
- Commonly combined with `@Transactional`.
- Default scope is Singleton.
- Improves architectural clarity.
- Sits between Controller and Repository.
- Central place for business rules and orchestration.
- One of the most commonly used annotations in Spring Boot applications.