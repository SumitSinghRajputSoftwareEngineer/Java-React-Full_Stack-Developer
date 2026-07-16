
## Definition

`@Endpoint` is a Spring Web Services (Spring-WS) annotation used to mark a class as a **SOAP Web Service endpoint**.

It tells Spring:

> This class will handle incoming SOAP XML requests and expose operations as web service methods.

Package:

```java
import org.springframework.ws.server.endpoint.annotation.Endpoint;
```

---

# Simple Understanding

Think of:

```text
@RestController → REST APIs (JSON)
```

---

Similarly:

```text
@Endpoint → SOAP Web Services (XML)
```

---

So:

```text
HTTP + JSON → REST
HTTP + XML (SOAP) → @Endpoint
```

---

# Where is @Endpoint Used?

Used in:

```text
SOAP-based Web Services
Enterprise Integrations
Banking Systems
Legacy Systems
ERP Integrations
```

---

# Real-Life Analogy

REST API:

```text
Fast Food Counter
Order → Get Food Quickly
```

---

SOAP Endpoint:

```text
Formal Restaurant
Structured Menu
Strict Rules
XML-based Communication
```

---

# Why Do We Need @Endpoint?

SOAP services require:

```text
Strict XML Request/Response Format
Contract-Based Communication (WSDL)
Enterprise Security Standards
```

---

`@Endpoint` helps define:

```text
SOAP Operations
Request Handling
Response Mapping
```

---

# Basic Example

```java
@Endpoint
public class EmployeeEndpoint {

    @PayloadRoot(
        namespace = "http://example.com/hr",
        localPart = "GetEmployeeRequest"
    )
    @ResponsePayload
    public GetEmployeeResponse getEmployee(
            @RequestPayload
            GetEmployeeRequest request) {

        GetEmployeeResponse response =
            new GetEmployeeResponse();

        response.setName("Sumit");

        return response;
    }
}
```

---

# What Happens Internally?

Client sends SOAP XML:

```xml
<GetEmployeeRequest>
    <id>101</id>
</GetEmployeeRequest>
```

---

Spring:

```text
Parse XML
      ↓
Match PayloadRoot
      ↓
Call Method
      ↓
Generate XML Response
```

---

Response:

```xml
<GetEmployeeResponse>
    <name>Sumit</name>
</GetEmployeeResponse>
```

---

# Key Annotations Used with @Endpoint

## @PayloadRoot

Maps XML request to method:

```java
@PayloadRoot(
    namespace = "...",
    localPart = "GetEmployeeRequest"
)
```

---

## @RequestPayload

Binds incoming XML to Java object:

```java
@RequestPayload
```

---

## @ResponsePayload

Converts Java object to XML response:

```java
@ResponsePayload
```

---

# Architecture

```text
SOAP Client
     │
     ▼

XML Request

     │
     ▼

@Endpoint Class

     │
     ▼

Business Logic

     │
     ▼

XML Response
```

---

# Example: Banking System

Request:

```xml
<GetBalanceRequest>
    <accountId>123</accountId>
</GetBalanceRequest>
```

---

Endpoint:

```java
@Endpoint
public class BankEndpoint {

    @PayloadRoot(
        namespace="http://bank.com",
        localPart="GetBalanceRequest"
    )
    @ResponsePayload
    public GetBalanceResponse getBalance(
        @RequestPayload
        GetBalanceRequest request) {

        GetBalanceResponse response =
            new GetBalanceResponse();

        response.setBalance(5000);

        return response;
    }
}
```

---

Response:

```xml
<GetBalanceResponse>
    <balance>5000</balance>
</GetBalanceResponse>
```

---

# WSDL Role

SOAP services expose:

```text
WSDL (Web Service Definition Language)
```

---

It defines:

```text
Operations
Request/Response Structure
Namespace
```

---

`@Endpoint` implements WSDL operations.

---

# @Endpoint vs @RestController

| Feature | @RestController | @Endpoint |
|----------|----------------|------------|
| Protocol | REST | SOAP |
| Format | JSON | XML |
| Style | Lightweight | Strict |
| Contract | Optional | Mandatory (WSDL) |
| Usage | Modern APIs | Enterprise legacy systems |

---

# Request Flow

```text
SOAP Client
     │
     ▼

XML Request

     │
     ▼

Spring WS Dispatcher

     │
     ▼

@Endpoint Method

     │
     ▼

XML Response
```

---

# Real Production Use Cases

## Banking

```text
Balance Inquiry
Fund Transfer
Transaction History
```

---

## Insurance

```text
Policy Details
Claims Processing
```

---

## Government Systems

```text
Citizen Services
Tax Systems
Identity Systems
```

---

## Enterprise ERP

```text
SAP Integration
Oracle Systems
```

---

# Common Mistakes

## Mistake 1

Using @RestController instead of @Endpoint

Wrong:

```java
@RestController
```

for SOAP services.

---

# Mistake 2

Missing PayloadRoot Mapping

Result:

```text
Request Not Routed
```

---

# Mistake 3

Wrong Namespace

```java
namespace mismatch
```

leads to:

```text
No handler found
```

---

# Spring WS Dependencies

```xml
<dependency>
    <groupId>
        org.springframework.ws
    </groupId>
    <artifactId>
        spring-ws-core
    </artifactId>
</dependency>
```

---

# Common Interview Questions

## What is @Endpoint?

Marks a class as SOAP web service handler.

---

## Is It REST Or SOAP?

SOAP.

---

## What Does It Replace?

Servlet-based SOAP handling.

---

## What Is Used With It?

- @PayloadRoot  
- @RequestPayload  
- @ResponsePayload  

---

## Does It Use JSON?

No. It uses XML.

---

# Enterprise Best Practice

Use `@Endpoint` when:

```text
Integrating With Legacy Systems
Banking Systems
Enterprise SOAP Services
```

---

Avoid for:

```text
Modern Microservices
Mobile APIs
Frontend APIs
```

Use REST instead.

---

# Key Points To Remember

- `@Endpoint` is used for SOAP Web Services.
- Works with XML-based communication.
- Maps requests using `@PayloadRoot`.
- Uses `@RequestPayload` and `@ResponsePayload`.
- Depends on WSDL contract.
- Used in enterprise legacy systems.
- Not used for REST APIs.
- Handles structured SOAP messages.
- Part of Spring Web Services (Spring-WS).
- Frequently asked integration interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@RestController` | REST APIs (JSON) |
| `@Endpoint` | SOAP APIs (XML) |
| `@PayloadRoot` | Map SOAP request |
| `@RequestPayload` | Read XML request |
| `@ResponsePayload` | Send XML response |

---

# Interview Shortcut

```java
@Endpoint
public class BankEndpoint {

    @PayloadRoot(
        namespace = "http://bank.com",
        localPart = "GetBalanceRequest"
    )
    @ResponsePayload
    public GetBalanceResponse getBalance(
        @RequestPayload
        GetBalanceRequest request) {

        return response;
    }
}
```

Meaning:

```text
Receive SOAP XML Request

Map To Method

Process Business Logic

Return XML Response
```

---

# Real Production Example

```text
Banking System
```

Flow:

```text
SOAP Client Request (XML)
        ↓
Spring WS Dispatcher
        ↓
@Endpoint Method
        ↓
Business Logic
        ↓
SOAP Response (XML)
```

Result:

```text
Strict Contract-Based Communication
Enterprise Integration
Secure and Standardized Messaging
```

`@Endpoint` is a Spring Web Services annotation used to define a SOAP web service handler that processes XML-based requests and returns structured XML responses according to a WSDL contract.