
## Definition

`@RequestPart` is a Spring MVC annotation used to extract and bind a specific part of a **multipart/form-data** request to a method parameter.

It tells Spring:

> Read a particular part of a multipart request, convert it to the required Java type, and inject it into the controller method.

Package:

```java
import org.springframework.web.bind.annotation.RequestPart;
```

---

# Why Do We Need @RequestPart?

In real-world applications, we often need to send:

```text
File + JSON Data
File + Metadata
Multiple Files + DTO
```

Example:

```text
Upload Employee Profile Picture
+
Employee Information
```

---

Request:

```http
POST /employees
Content-Type: multipart/form-data
```

Contains:

```text
Part 1 → employee.json
Part 2 → profile-image.jpg
```

---

Without `@RequestPart`, Spring cannot automatically deserialize the JSON part into a Java object.

---

# What Problem Does It Solve?

Suppose frontend sends:

```text
Employee Details
+
Employee Image
```

in a single request.

---

Example:

```json
{
  "name":"John",
  "salary":90000
}
```

and

```text
profile.jpg
```

---

How do we receive both?

Answer:

```java
@RequestPart
```

---

# What is Multipart/Form-Data?

Normally:

```http
Content-Type: application/json
```

sends only JSON.

---

Multipart allows:

```text
Multiple Independent Parts
```

inside one request.

---

Example:

```http
Content-Type: multipart/form-data
```

---

Request contains:

```text
Part 1 -> JSON
Part 2 -> Image
Part 3 -> PDF
Part 4 -> Text
```

---

# Basic Syntax

```java
@PostMapping("/upload")
public String upload(

        @RequestPart("file")
        MultipartFile file) {

    return file.getOriginalFilename();

}
```

---

Request:

```text
file = resume.pdf
```

---

Response:

```text
resume.pdf
```

---

# What Happens Internally?

Request:

```text
multipart/form-data
```

---

Spring Flow:

```text
HTTP Request
      │
      ▼
DispatcherServlet
      │
      ▼
MultipartResolver
      │
      ▼
Split Multipart Request
      │
      ▼
RequestPart Resolver
      │
      ▼
Convert Part
      │
      ▼
Controller Method
```

---

# Simple File Upload Example

Controller:

```java
@RestController
public class FileController {

    @PostMapping("/upload")
    public String upload(

            @RequestPart("file")
            MultipartFile file) {

        return file.getOriginalFilename();

    }

}
```

---

Request:

```text
file = employee.pdf
```

---

Response:

```text
employee.pdf
```

---

# JSON + File Upload

Most Common Real-World Use Case.

---

DTO:

```java
public class EmployeeRequest {

    private String name;
    private Double salary;

}
```

---

Controller:

```java
@PostMapping("/employees")
public String createEmployee(

        @RequestPart("employee")
        EmployeeRequest employee,

        @RequestPart("file")
        MultipartFile file) {

    return employee.getName();

}
```

---

Request Parts:

```text
employee
```

```json
{
  "name":"John",
  "salary":90000
}
```

---

```text
file
```

```text
profile.jpg
```

---

Spring automatically converts:

```text
JSON
   ↓
EmployeeRequest
```

---

# Why Not Use @RequestBody?

Interview Question.

Suppose request contains:

```text
JSON
+
Image
```

---

`@RequestBody` expects:

```text
Single Request Body
```

---

It cannot handle:

```text
Multiple Multipart Parts
```

---

For multipart requests:

```java
@RequestPart
```

must be used.

---

# JSON Deserialization

Spring uses:

```java
HttpMessageConverter
```

to convert JSON part.

---

Flow:

```text
JSON Part
      │
      ▼
Jackson ObjectMapper
      │
      ▼
EmployeeRequest
```

---

# Multiple Files Upload

---

```java
@PostMapping("/documents")
public String upload(

        @RequestPart("files")
        MultipartFile[] files) {

    return String.valueOf(files.length);

}
```

---

Request:

```text
file1.pdf
file2.pdf
file3.pdf
```

---

Response:

```text
3
```

---

# Uploading List of Files

---

```java
@PostMapping("/documents")
public String upload(

        @RequestPart("files")
        List<MultipartFile> files) {

    return String.valueOf(files.size());

}
```

---

# Uploading Only JSON

Possible but uncommon.

---

```java
@PostMapping("/employee")
public Employee create(

        @RequestPart("employee")
        EmployeeRequest request) {

    return service.save(request);

}
```

---

In practice:

```java
@RequestBody
```

is preferred when no file exists.

---

# Optional Request Part

By default:

```java
@RequestPart
```

is required.

---

Example:

```java
@RequestPart("file")
MultipartFile file
```

---

If file missing:

```java
MissingServletRequestPartException
```

occurs.

---

Optional:

```java
@RequestPart(
    value = "file",
    required = false
)
MultipartFile file
```

---

# Validation with @Valid

Very common.

---

DTO:

```java
public class EmployeeRequest {

    @NotBlank
    private String name;

}
```

---

Controller:

```java
@PostMapping("/employees")
public String create(

        @Valid
        @RequestPart("employee")
        EmployeeRequest request,

        @RequestPart("file")
        MultipartFile file) {

    return request.getName();

}
```

---

Invalid Request:

```json
{
  "name":""
}
```

---

Exception:

```java
MethodArgumentNotValidException
```

---

# Reading Raw String Part

---

```java
@PostMapping("/test")
public String test(

        @RequestPart("message")
        String message) {

    return message;

}
```

---

Request:

```text
message=Hello
```

---

Response:

```text
Hello
```

---

# Consumes Attribute

Most Important Configuration.

---

```java
@PostMapping(
    value = "/upload",
    consumes = MediaType.MULTIPART_FORM_DATA_VALUE
)
```

---

Equivalent:

```java
@PostMapping(
    value = "/upload",
    consumes = "multipart/form-data"
)
```

---

# @RequestPart vs @RequestParam

Very Important Interview Question.

---

## @RequestParam

Reads:

```text
Simple Form Fields
```

Example:

```java
@RequestParam String name
```

---

## @RequestPart

Reads:

```text
Multipart Sections
```

and supports conversion.

---

Example:

```java
@RequestPart EmployeeRequest request
```

---

Comparison:

| Feature | @RequestParam | @RequestPart |
|----------|--------------|--------------|
| Form Field | Yes | Yes |
| File Upload | Yes | Yes |
| JSON Conversion | No | Yes |
| Multipart Support | Limited | Full |
| DTO Binding | No | Yes |

---

# @RequestPart vs @RequestBody

| Feature | @RequestPart | @RequestBody |
|----------|-------------|-------------|
| Multipart Request | Yes | No |
| JSON Request | Yes | Yes |
| File Upload | Yes | No |
| Multiple Parts | Yes | No |
| Single Body | No | Yes |

---

# Internal Architecture

Very Important Interview Topic.

---

Components:

```java
DispatcherServlet
```

↓

```java
MultipartResolver
```

↓

```java
RequestPartMethodArgumentResolver
```

↓

```java
HttpMessageConverter
```

↓

```java
Jackson ObjectMapper
```

↓

```java
Controller Method
```

---

# Internal Classes

Resolver:

```java
RequestPartMethodArgumentResolver
```

---

Multipart Handler:

```java
MultipartResolver
```

---

JSON Converter:

```java
MappingJackson2HttpMessageConverter
```

---

Deserializer:

```java
ObjectMapper
```

---

# Common Mistakes

## Mistake 1

Using @RequestBody with File Upload

Wrong:

```java
@PostMapping
public void save(

        @RequestBody EmployeeRequest request,

        MultipartFile file) {

}
```

---

Correct:

```java
@RequestPart
```

---

# Mistake 2

Missing Multipart Configuration

Wrong Content-Type:

```http
application/json
```

---

Correct:

```http
multipart/form-data
```

---

# Mistake 3

Wrong Part Name

Request:

```text
employee
```

---

Code:

```java
@RequestPart("emp")
```

---

Result:

```java
MissingServletRequestPartException
```

---

# Mistake 4

Not Using consumes

Always specify:

```java
consumes = MediaType.MULTIPART_FORM_DATA_VALUE
```

for clarity.

---

# Common Interview Questions

## What is @RequestPart?

Used to bind a specific part of a multipart request to a method parameter.

---

## When should @RequestPart be used?

When request contains:

```text
JSON + File
JSON + Multiple Files
Multipart Content
```

---

## Which resolver handles @RequestPart?

```java
RequestPartMethodArgumentResolver
```

---

## Can JSON be converted to DTO?

Yes.

Using:

```java
Jackson ObjectMapper
```

---

## Can @RequestPart upload files?

Yes.

Using:

```java
MultipartFile
```

---

## Difference Between @RequestPart and @RequestBody?

`@RequestBody`

```text
Single Request Body
```

---

`@RequestPart`

```text
Multipart Request Sections
```

---

## Is @RequestPart required by default?

Yes.

---

## Can Validation Be Applied?

Yes.

Using:

```java
@Valid
@RequestPart
```

---

# Real-World Example

```java
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {

    @PostMapping(
        consumes =
        MediaType.MULTIPART_FORM_DATA_VALUE
    )
    public ResponseEntity<String>
    createEmployee(

            @Valid
            @RequestPart("employee")
            EmployeeRequest request,

            @RequestPart("image")
            MultipartFile image) {

        service.save(request, image);

        return ResponseEntity
                .status(201)
                .body("Employee Created");

    }

}
```

---

Request:

```text
Part 1:
employee
```

```json
{
  "name":"John",
  "salary":90000
}
```

---

```text
Part 2:
image
```

```text
profile.jpg
```

---

Response:

```http
201 Created
```

---

# Internal Flow Diagram

```text
Client
   │
   ▼
Multipart Request
   │
   ▼
DispatcherServlet
   │
   ▼
MultipartResolver
   │
   ▼
Split Request Parts
   │
   ▼
RequestPartMethodArgumentResolver
   │
   ▼
Jackson ObjectMapper
   │
   ▼
DTO / MultipartFile
   │
   ▼
Controller Method
```

---

# Key Points To Remember

- `@RequestPart` binds a specific multipart request part.
- Primarily used for JSON + File upload scenarios.
- Supports DTO conversion using Jackson.
- Works with `MultipartFile`, DTOs, Strings, Lists, and Arrays.
- Requires `multipart/form-data`.
- Internally resolved by `RequestPartMethodArgumentResolver`.
- Uses `MultipartResolver` to process multipart requests.
- Supports validation using `@Valid`.
- Frequently asked in Spring Boot REST API interviews.
- Preferred whenever a request contains both files and structured JSON data.