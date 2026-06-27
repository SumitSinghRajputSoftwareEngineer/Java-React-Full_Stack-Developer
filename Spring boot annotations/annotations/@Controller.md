
## Definition

`@Controller` is a specialized Spring stereotype annotation used to mark a class as a Spring MVC Controller.

It tells Spring:

> This class is responsible for handling incoming HTTP requests and returning views (HTML/JSP/Thymeleaf pages) or delegating processing to the Service Layer.

Internally:

```java
@Controller
```

is a specialization of:

```java
@Component
```

Therefore every controller:

- Is automatically discovered by component scanning.
- Becomes a Spring Bean.
- Can participate in Dependency Injection.
- Is managed by Spring IoC Container.

---

# Why Do We Need @Controller?

In a typical web application:

```text
Browser
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

The Controller acts as the:

```text
Entry Point
```

for all HTTP requests.

Its responsibilities include:

- Receiving Requests
- Extracting Parameters
- Calling Services
- Preparing Response Data
- Returning Views

---

# Basic Syntax

```java
@Controller
public class EmployeeController {

}
```

---

# What Happens Internally?

During startup:

```java
@ComponentScan
```

finds:

```java
@Controller
public class EmployeeController {

}
```

Spring:

### Step 1

Creates Bean Definition.

---

### Step 2

Registers Controller Bean.

---

### Step 3

Maps Request Handling Methods.

---

### Step 4

Stores Controller in Application Context.

---

Flow:

```text
@Controller
      │
      ▼
Component Scan
      │
      ▼
Create Bean
      │
      ▼
Register Request Mappings
      │
      ▼
Controller Ready
```

---

# Internal Structure

Simplified version:

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface Controller {

}
```

Because:

```java
@Component
```

is present,

Spring treats controllers as normal beans.

---

# Traditional MVC Example

## Controller

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home() {

        return "home";

    }

}
```

---

Request:

```http
GET /
```

---

Response:

```java
"home"
```

---

Spring looks for:

```text
home.jsp
```

or

```text
home.html
```

depending on view technology.

---

# Understanding View Resolution

Very important.

---

Example:

```java
@Controller
public class EmployeeController {

    @GetMapping("/employees")
    public String employees() {

        return "employees";

    }

}
```

---

Spring does NOT return:

```text
employees
```

to browser.

Instead:

```text
ViewResolver
```

finds:

```text
employees.jsp
```

or

```text
employees.html
```

and renders it.

---

Flow:

```text
Request
   │
   ▼
Controller
   │
   ▼
Return View Name
   │
   ▼
View Resolver
   │
   ▼
HTML Page
```

---

# Passing Data to View

Using:

```java
Model
```

---

Example:

```java
@Controller
public class EmployeeController {

    @GetMapping("/employee")
    public String employee(
            Model model) {

        model.addAttribute(
                "name",
                "Sumit"
        );

        return "employee";

    }

}
```

---

View:

```html
<h1 th:text="${name}"></h1>
```

Output:

```html
<h1>Sumit</h1>
```

---

# Complete Example

## Controller

```java
@Controller
public class EmployeeController {

    @GetMapping("/employee")
    public String employee(
            Model model) {

        model.addAttribute(
                "employee",
                "John"
        );

        return "employee";

    }

}
```

---

## employee.html

```html
<html>
<body>

<h1 th:text="${employee}"></h1>

</body>
</html>
```

---

Output:

```html
John
```

---

# Controller Calling Service Layer

Most common pattern.

---

## Service

```java
@Service
public class EmployeeService {

    public String getEmployee() {

        return "John";

    }

}
```

---

## Controller

```java
@Controller
public class EmployeeController {

    private final EmployeeService service;

    public EmployeeController(
            EmployeeService service) {

        this.service = service;
    }

    @GetMapping("/employee")
    public String employee(
            Model model) {

        model.addAttribute(
                "employee",
                service.getEmployee()
        );

        return "employee";

    }

}
```

---

Architecture:

```text
Browser
   │
   ▼
Controller
   │
   ▼
Service
   │
   ▼
Repository
```

---

# Request Mapping with Controller

Controller methods usually use:

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@RequestMapping
```

---

Example:

```java
@Controller
@RequestMapping("/employees")
public class EmployeeController {

    @GetMapping("/{id}")
    public String getEmployee() {

        return "employee";

    }

}
```

---

URL:

```http
GET /employees/1
```

---

# Handling Form Submission

Traditional MVC use case.

---

## Form

```html
<form action="/save"
      method="post">

    <input name="name">

</form>
```

---

## Controller

```java
@Controller
public class EmployeeController {

    @PostMapping("/save")
    public String save(
            @RequestParam String name) {

        return "success";

    }

}
```

---

# Returning ModelAndView

Another MVC approach.

---

```java
@Controller
public class EmployeeController {

    @GetMapping("/")
    public ModelAndView home() {

        ModelAndView mv =
                new ModelAndView();

        mv.setViewName("home");

        mv.addObject(
                "name",
                "Sumit"
        );

        return mv;

    }

}
```

---

# @Controller vs @RestController

Most Important Interview Question

---

## @Controller

Returns:

```text
View Names
```

Example:

```java
return "home";
```

means:

```text
Render home.html
```

---

## @RestController

Returns:

```text
Response Body
```

Example:

```java
return "home";
```

means:

```http
home
```

directly sent to client.

---

Example:

```java
@RestController
public class TestController {

    @GetMapping("/")
    public String home() {

        return "home";

    }

}
```

Response:

```http
home
```

---

# Internal Difference

```java
@RestController
```

is equivalent to:

```java
@Controller
@ResponseBody
```

---

Meaning:

```java
@RestController
```

automatically serializes return values.

---

# Controller Lifecycle

```text
Application Start
        │
        ▼
Create Controller Bean
        │
        ▼
Register Request Mapping
        │
        ▼
Receive Request
        │
        ▼
Invoke Method
        │
        ▼
Return View Name
        │
        ▼
View Resolver
        │
        ▼
HTML Response
```

---

# Scope of Controller

Default:

```java
Singleton
```

---

```java
@Controller
@Scope("prototype")
public class EmployeeController {

}
```

Possible but rarely used.

---

# Lazy Controller

```java
@Controller
@Lazy
public class EmployeeController {

}
```

Created only when needed.

---

# Profile-Specific Controller

```java
@Controller
@Profile("dev")
public class DevController {

}
```

Only active in:

```text
dev
```

profile.

---

# Common Mistakes

## Mistake 1

Returning JSON from @Controller.

```java
@Controller
public class TestController {

    @GetMapping("/")
    public String home() {

        return "Hello";

    }

}
```

Spring tries to resolve:

```text
Hello.html
```

or

```text
Hello.jsp
```

---

## Solution

Use:

```java
@ResponseBody
```

or

```java
@RestController
```

---

## Mistake 2

Putting business logic inside controller.

Wrong:

```java
@Controller
public class EmployeeController {

    public void calculateSalary() {

    }

}
```

Business logic belongs in:

```java
@Service
```

---

## Mistake 3

Calling Repository directly.

Wrong:

```text
Controller
   ▼
Repository
```

---

Correct:

```text
Controller
   ▼
Service
   ▼
Repository
```

---

# Common Interview Questions

## What is @Controller?

Marks a class as a Spring MVC Controller.

---

## Is @Controller a Component?

Yes.

Internally:

```java
@Controller
```

contains:

```java
@Component
```

---

## What does @Controller return?

Typically:

```text
View Names
```

such as:

```java
return "home";
```

---

## Difference Between @Controller and @RestController?

| @Controller | @RestController |
|-------------|----------------|
| Returns View | Returns Data |
| MVC Apps | REST APIs |
| Uses View Resolver | Uses HttpMessageConverter |

---

## Can @Controller participate in Dependency Injection?

Yes.

Like any Spring Bean.

---

## What is the default scope?

```java
Singleton
```

---

## Can @Controller return JSON?

Yes, using:

```java
@ResponseBody
```

or

```java
@RestController
```

---

# Real-World Significance

`@Controller` is primarily used in:

- Spring MVC Applications
- Thymeleaf Applications
- JSP Applications
- Server-Side Rendered Web Applications
- Traditional Enterprise Web Systems

While modern REST applications often use:

```java
@RestController
```

many enterprise applications still heavily use:

```java
@Controller
```

for rendering dynamic HTML pages.

---

# Key Points To Remember

- Specialized form of `@Component`.
- Represents the Presentation Layer.
- Handles HTTP requests.
- Returns view names by default.
- Works with View Resolvers.
- Supports Model and ModelAndView.
- Typically calls Service Layer.
- Default scope is Singleton.
- Automatically discovered via Component Scan.
- Different from `@RestController`.
- Central entry point in Spring MVC applications.