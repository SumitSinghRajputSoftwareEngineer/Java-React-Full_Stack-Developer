
## Definition

`@DeleteMapping` is a Spring MVC annotation used to map HTTP **DELETE** requests to a controller method.

It tells Spring:

> When an HTTP DELETE request arrives for a specific URL, execute this method.

Package:

```java
import org.springframework.web.bind.annotation.DeleteMapping;
```

---

# Why Do We Need @DeleteMapping?

In REST APIs:

| HTTP Method | Purpose |
|------------|----------|
| GET | Read Data |
| POST | Create Data |
| PUT | Update Entire Resource |
| PATCH | Partial Update Resource |
| DELETE | Delete Resource |

---

DELETE is used when:

```text
An existing resource must be removed.
```

Example:

```http
DELETE /employees/101
```

means:

```text
Delete Employee with ID 101
```

---

# What is @DeleteMapping Internally?

Very Important Interview Question.

Internally:

```java
@DeleteMapping
```

is a shortcut for:

```java
@RequestMapping(
    method = RequestMethod.DELETE
)
```

---

Equivalent:

```java
@DeleteMapping("/employees/{id}")
```

and

```java
@RequestMapping(
    value = "/employees/{id}",
    method = RequestMethod.DELETE
)
```

are exactly the same.

---

# Basic Syntax

```java
@DeleteMapping("/employees/{id}")
```

---

Example

```java
@RestController
public class EmployeeController {

    @DeleteMapping("/employees/{id}")
    public String deleteEmployee(
            @PathVariable Long id) {

        return "Employee Deleted";

    }

}
```

---

Request:

```http
DELETE /employees/101
```

---

Response:

```text
Employee Deleted
```

---

# What Happens Internally?

Suppose:

```java
@DeleteMapping("/employees/{id}")
```

---

Application Startup:

### Step 1

Spring scans controllers.

---

### Step 2

Finds:

```java
@DeleteMapping("/employees/{id}")
```

---

### Step 3

Registers URL mapping.

---

Stored in:

```java
RequestMappingHandlerMapping
```

---

Runtime Flow:

```text
HTTP DELETE Request
         │
         ▼
DispatcherServlet
         │
         ▼
RequestMappingHandlerMapping
         │
         ▼
Find Controller Method
         │
         ▼
Execute Method
         │
         ▼
Return Response
```

---

# Most Common Usage

## Delete Existing Resource

---

Controller:

```java
@DeleteMapping("/employees/{id}")
public String deleteEmployee(
        @PathVariable Long id) {

    service.delete(id);

    return "Deleted";

}
```

---

Request:

```http
DELETE /employees/101
```

---

Result:

```text
Employee 101 Removed
```

---

# Using ResponseEntity

Production-grade APIs usually use:

```java
ResponseEntity
```

---

```java
@DeleteMapping("/employees/{id}")
public ResponseEntity<Void>
deleteEmployee(
        @PathVariable Long id) {

    service.delete(id);

    return ResponseEntity.noContent()
            .build();

}
```

---

Response:

```http
204 No Content
```

---

No response body.

---

# Why 204 No Content?

REST Best Practice:

| Operation | Status |
|------------|---------|
| GET | 200 OK |
| POST | 201 Created |
| PUT | 200 OK |
| DELETE | 204 No Content |

---

Because:

```text
Resource Removed Successfully
No Data Returned
```

---

# Handling Resource Not Found

---

```java
@DeleteMapping("/employees/{id}")
public ResponseEntity<Void>
deleteEmployee(
        @PathVariable Long id) {

    boolean deleted =
            service.delete(id);

    if(!deleted) {

        return ResponseEntity
                .notFound()
                .build();

    }

    return ResponseEntity
            .noContent()
            .build();

}
```

---

Response:

```http
404 Not Found
```

if employee doesn't exist.

---

# Using Path Variables

Most common pattern.

---

```java
@DeleteMapping("/employees/{id}")
public void delete(
        @PathVariable Long id) {

}
```

---

Request:

```http
DELETE /employees/100
```

---

Spring injects:

```java
id = 100
```

---

# Using Query Parameters

Possible but less common.

---

```java
@DeleteMapping("/employees")
public String delete(
        @RequestParam Long id) {

    return "Deleted";

}
```

---

Request:

```http
DELETE /employees?id=100
```

---

# Class-Level + Method-Level Mapping

---

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @DeleteMapping("/{id}")
    public void delete(
            @PathVariable Long id) {

    }

}
```

---

Final URL:

```http
DELETE /employees/101
```

---

# Returning Custom Response

---

```java
@DeleteMapping("/{id}")
public ResponseEntity<String>
delete(
        @PathVariable Long id) {

    service.delete(id);

    return ResponseEntity.ok(
            "Employee Deleted");

}
```

---

Response:

```http
200 OK
```

```text
Employee Deleted
```

---

# DELETE Request Body

Technically allowed by HTTP specification.

Example:

```http
DELETE /employees
```

```json
{
  "id": 101
}
```

---

Controller:

```java
@DeleteMapping("/employees")
public void delete(
        @RequestBody Employee employee) {

}
```

---

However:

```text
Not Recommended
```

Most REST APIs use:

```java
@PathVariable
```

instead.

---

# Idempotency

Very Important Interview Topic.

DELETE is generally:

```text
Idempotent
```

---

Meaning:

```text
Multiple identical DELETE requests
produce the same final state.
```

---

Example:

```http
DELETE /employees/101
```

Run:

```text
1 Time
10 Times
100 Times
```

Final Result:

```text
Employee 101 Does Not Exist
```

Same final state.

---

Therefore:

```text
DELETE is Idempotent
```

---

# DELETE vs POST

| Feature | POST | DELETE |
|----------|--------|---------|
| Create Resource | Yes | No |
| Remove Resource | No | Yes |
| Idempotent | No | Yes |
| Typical Status | 201 | 204 |

---

# DELETE vs PUT

| Feature | PUT | DELETE |
|----------|------|--------|
| Update Resource | Yes | No |
| Remove Resource | No | Yes |
| Idempotent | Yes | Yes |

---

# Soft Delete vs Hard Delete

Very Important Real-World Topic.

---

## Hard Delete

Physically remove row.

---

```sql
DELETE FROM employee
WHERE id = 101;
```

---

Record disappears permanently.

---

## Soft Delete

Mark record as deleted.

---

```sql
UPDATE employee
SET deleted = true
WHERE id = 101;
```

---

Record remains in database.

---

Common in:

```text
Banking Systems
E-Commerce
Healthcare
ERP Systems
```

---

# Real-World Service Example

---

```java
@Service
public class EmployeeService {

    public void delete(Long id) {

        Employee employee =
                repository.findById(id)
                          .orElseThrow(
                              EmployeeNotFoundException::new
                          );

        repository.delete(employee);

    }

}
```

---

Controller:

```java
@DeleteMapping("/{id}")
public ResponseEntity<Void>
delete(
        @PathVariable Long id) {

    service.delete(id);

    return ResponseEntity
            .noContent()
            .build();

}
```

---

# Internal Architecture

Mappings stored in:

```java
RequestMappingHandlerMapping
```

---

Request Flow:

```text
DELETE Request
       │
       ▼
DispatcherServlet
       │
       ▼
RequestMappingHandlerMapping
       │
       ▼
@DeleteMapping Method
       │
       ▼
Business Logic
       │
       ▼
Response
```

---

# Common Mistakes

## Mistake 1

Using GET for Delete Operations

Wrong:

```java
@GetMapping("/delete/{id}")
```

---

Correct:

```java
@DeleteMapping("/{id}")
```

---

# Mistake 2

Returning 200 Without Need

---

Preferred:

```java
204 No Content
```

for successful deletion.

---

# Mistake 3

Ignoring Missing Resources

---

Always handle:

```http
404 Not Found
```

---

# Mistake 4

Using Request Body for DELETE

Technically possible.

Usually avoided.

Prefer:

```java
@PathVariable
```

---

# Common Interview Questions

## What is @DeleteMapping?

Maps HTTP DELETE requests to controller methods.

---

## What is @DeleteMapping internally?

```java
@RequestMapping(
    method = RequestMethod.DELETE
)
```

---

## What is the primary use case?

```text
Delete Existing Resource
```

---

## Is DELETE idempotent?

Yes.

---

## Which status code is commonly returned?

```http
204 No Content
```

---

## Can @DeleteMapping use Path Variables?

Yes.

Most commonly:

```java
@DeleteMapping("/{id}")
```

---

## Can DELETE have a request body?

Yes.

But generally avoided.

---

## Difference Between DELETE and POST?

POST:

```text
Create Resource
```

DELETE:

```text
Remove Resource
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {

    @DeleteMapping("/{id}")
    public ResponseEntity<Void>
    deleteEmployee(
            @PathVariable Long id) {

        service.delete(id);

        return ResponseEntity
                .noContent()
                .build();

    }

}
```

---

Request:

```http
DELETE /api/employees/101
```

---

Response:

```http
204 No Content
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
DELETE Request
   │
   ▼
DispatcherServlet
   │
   ▼
RequestMappingHandlerMapping
   │
   ▼
@DeleteMapping Method
   │
   ▼
Business Logic
   │
   ▼
Delete Resource
   │
   ▼
204 No Content
```

---

# Key Points To Remember

- `@DeleteMapping` handles HTTP DELETE requests.
- Internally equals `@RequestMapping(method = RequestMethod.DELETE)`.
- Primarily used for deleting resources.
- Most commonly uses `@PathVariable`.
- DELETE operations should be idempotent.
- Common success response is `204 No Content`.
- Missing resources typically return `404 Not Found`.
- Supports `ResponseEntity`.
- Mappings are stored in `RequestMappingHandlerMapping`.
- Often used with soft delete or hard delete strategies.
- One of the most important REST API annotations in Spring Boot.