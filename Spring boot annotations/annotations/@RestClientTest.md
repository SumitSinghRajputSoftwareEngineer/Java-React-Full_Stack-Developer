
## Definition

`@RestClientTest` is a Spring Boot test annotation used to test components that call **external REST APIs** using:

- `RestTemplate`
- `RestClient` (Spring 6+)
- HTTP Interface Clients
- REST client-related beans

It tells Spring Boot:

> Load only REST client-related components and provide a mock HTTP server for testing outbound API calls.

Package:

```java
import org.springframework.boot.test.autoconfigure.web.client.RestClientTest;
```

---

# Why Do We Need @RestClientTest?

Suppose your application calls an external API:

```text
Your Application
       ↓
External API
       ↓
Weather API
```

Example:

```java
@Service
public class WeatherClient {

    private final RestTemplate restTemplate;

    public WeatherClient(
            RestTemplate restTemplate) {

        this.restTemplate = restTemplate;
    }

    public String getWeather() {

        return restTemplate.getForObject(
                "https://weather-api.com/weather",
                String.class
        );
    }
}
```

---

Testing this directly would:

```text
Call Real API
Require Internet
Be Slow
Be Unreliable
Cost Money
```

---

Solution:

```java
@RestClientTest
```

---

# What Does @RestClientTest Load?

Loaded:

```text
RestTemplate
RestClient
Jackson
JSON Converters
HttpMessageConverters
MockRestServiceServer
Target REST Client Bean
```

---

Not Loaded:

```text
Controller
Service Layer
Repository
Database
Security
Kafka
Redis
```

---

# Internal Workflow

```text
JUnit
   ↓
@RestClientTest
   ↓
Create RestTemplate
   ↓
Create Mock Server
   ↓
Intercept HTTP Calls
   ↓
Return Fake Response
   ↓
Run Test
```

---

# Basic Example

## REST Client

```java
@Service
public class UserClient {

    private final RestTemplate restTemplate;

    public UserClient(
            RestTemplate restTemplate) {

        this.restTemplate = restTemplate;
    }

    public String getUser() {

        return restTemplate.getForObject(
            "http://users/api",
            String.class
        );
    }
}
```

---

## Test

```java
@RestClientTest(UserClient.class)
class UserClientTest {

    @Autowired
    private UserClient client;

    @Autowired
    private MockRestServiceServer server;

}
```

---

Spring automatically creates:

```text
UserClient
RestTemplate
MockRestServiceServer
```

---

# What Is MockRestServiceServer?

Most important interview topic.

---

It is:

```text
Fake HTTP Server
```

used to intercept:

```java
RestTemplate
```

requests.

---

Instead of:

```text
Internet
```

request goes to:

```text
MockRestServiceServer
```

---

# Example

```java
server.expect(
      requestTo(
          "http://users/api"
      )
)
.andRespond(
      withSuccess(
          "John",
          MediaType.TEXT_PLAIN
      )
);
```

---

Meaning:

```text
When URL Called
Return "John"
```

---

# Complete Example

Client:

```java
@Service
public class UserClient {

    private final RestTemplate restTemplate;

    public UserClient(
        RestTemplate restTemplate
    ) {
        this.restTemplate = restTemplate;
    }

    public String getUser() {

        return restTemplate.getForObject(
            "http://users/api",
            String.class
        );
    }
}
```

---

Test:

```java
@RestClientTest(UserClient.class)
class UserClientTest {

    @Autowired
    UserClient client;

    @Autowired
    MockRestServiceServer server;

    @Test
    void testClient() {

        server.expect(
            requestTo(
                "http://users/api"
            )
        )
        .andRespond(
            withSuccess(
                "John",
                MediaType.TEXT_PLAIN
            )
        );

        String result =
            client.getUser();

        assertEquals(
            "John",
            result
        );
    }
}
```

---

Execution:

```text
Client Calls URL
       ↓
Mock Server Intercepts
       ↓
Returns "John"
       ↓
Assertion Passes
```

---

# Testing JSON Response

API Response:

```json
{
  "id":1,
  "name":"John"
}
```

---

DTO:

```java
public record User(
       Long id,
       String name
) {
}
```

---

Client:

```java
public User getUser() {

    return restTemplate.getForObject(
        "http://users/api",
        User.class
    );
}
```

---

Test:

```java
server.expect(
      requestTo(
          "http://users/api"
      )
)
.andRespond(
      withSuccess(
          """
          {
            "id":1,
            "name":"John"
          }
          """,
          MediaType.APPLICATION_JSON
      )
);
```

---

Verify:

```java
User user =
      client.getUser();

assertEquals(
      "John",
      user.name()
);
```

---

# Testing POST Request

Client:

```java
public String createUser(
        User user) {

    return restTemplate.postForObject(
            "http://users/api",
            user,
            String.class
    );
}
```

---

Test:

```java
server.expect(
      method(HttpMethod.POST)
)
.andRespond(
      withSuccess()
);
```

---

Verifies:

```text
POST Request Sent
```

---

# Testing Request Headers

Client:

```java
HttpHeaders headers =
       new HttpHeaders();

headers.add(
      "Authorization",
      "Bearer token"
);
```

---

Test:

```java
server.expect(
      header(
          "Authorization",
          "Bearer token"
      )
);
```

---

Verifies:

```text
Header Sent Correctly
```

---

# Testing Query Parameters

Client:

```java
restTemplate.getForObject(
   "http://users/api?id=1",
   String.class
);
```

---

Test:

```java
server.expect(
      requestTo(
          "http://users/api?id=1"
      )
);
```

---

# Testing Error Responses

Mock:

```java
server.andRespond(
      withStatus(
          HttpStatus.NOT_FOUND
      )
);
```

---

Verify:

```java
assertThrows(
      Exception.class,
      () -> client.getUser()
);
```

---

Tests:

```text
404 Handling
500 Handling
Retry Logic
```

---

# Testing RestClient (Spring 6)

Spring 6 introduced:

```java
RestClient
```

---

Client:

```java
public class UserClient {

    private final RestClient client;

}
```

---

`@RestClientTest`

supports:

```java
RestClient
```

automatically.

---

# Internal Components Loaded

```text
RestTemplate
RestClient
MockRestServiceServer
ObjectMapper
Jackson
Converters
```

---

Not loaded:

```text
Controller
Repository
Database
Security
Kafka
Redis
```

---

# @RestClientTest vs @WebMvcTest

Interview Favorite.

---

## @WebMvcTest

Tests:

```text
Incoming HTTP Requests
```

---

Example:

```text
Browser
   ↓
Controller
```

---

## @RestClientTest

Tests:

```text
Outgoing HTTP Requests
```

---

Example:

```text
Application
   ↓
External API
```

---

Comparison:

| Feature | @WebMvcTest | @RestClientTest |
|----------|-------------|----------------|
| Controller Testing | ✅ | ❌ |
| RestTemplate Testing | ❌ | ✅ |
| MockMvc | ✅ | ❌ |
| Mock Server | ❌ | ✅ |
| External API Calls | ❌ | ✅ |

---

# @RestClientTest vs @SpringBootTest

## @RestClientTest

Loads:

```text
REST Client Components Only
```

---

Fast.

---

## @SpringBootTest

Loads:

```text
Entire Application
```

---

Slow.

---

Comparison:

| Feature | @RestClientTest | @SpringBootTest |
|----------|----------------|----------------|
| RestTemplate | ✅ | ✅ |
| MockRestServiceServer | ✅ | ❌ |
| Database | ❌ | ✅ |
| Controller | ❌ | ✅ |
| Fast | ✅ | ❌ |

---

# Common Use Cases

## RestTemplate Testing

```java
@RestClientTest
```

---

## RestClient Testing

```java
@RestClientTest
```

---

## External API Integration

```java
@RestClientTest
```

---

## Header Validation

```java
@RestClientTest
```

---

## Query Parameter Validation

```java
@RestClientTest
```

---

## Error Handling Verification

```java
@RestClientTest
```

---

# Common Mistakes

## Mistake 1

Expecting Controllers

Wrong:

```java
@Autowired
ProductController
```

---

Not loaded.

---

# Mistake 2

Expecting Database

Wrong.

---

Need:

```java
@SpringBootTest
```

---

# Mistake 3

Calling Real APIs

Wrong.

---

Use:

```java
MockRestServiceServer
```

---

# Common Interview Questions

## What is @RestClientTest?

Tests REST client components.

---

## What Is MockRestServiceServer?

Mock server for outbound HTTP requests.

---

## Does It Load Controllers?

No.

---

## Does It Load Database?

No.

---

## Can It Test RestTemplate?

Yes.

---

## Can It Test RestClient?

Yes.

---

## Why Is It Faster Than SpringBootTest?

Loads only REST client infrastructure.

---

# Enterprise Best Practice

Use:

```java
@RestClientTest
```

for:

```text
External API Clients
RestTemplate
RestClient
HTTP Interfaces
```

---

Use:

```java
MockRestServiceServer
```

instead of real APIs.

---

Avoid:

```text
Internet Calls
```

during testing.

---

# Key Points To Remember

- `@RestClientTest` tests outbound REST clients.
- Loads `RestTemplate`.
- Loads `RestClient`.
- Creates `MockRestServiceServer`.
- Supports JSON testing.
- Supports header verification.
- Supports query parameter verification.
- Does not load controllers.
- Faster than `@SpringBootTest`.
- Frequently asked Spring Boot testing interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@RestClientTest` | REST Client Testing |
| `@WebMvcTest` | Controller Testing |
| `@JsonTest` | JSON Testing |
| `@DataJpaTest` | Repository Testing |
| `@SpringBootTest` | Full Integration Testing |

---

# Interview Shortcut

```java
@RestClientTest(UserClient.class)
class UserClientTest {

    @Autowired
    MockRestServiceServer server;
}
```

Execution:

```text
Client Calls API
      ↓
Mock Server Intercepts
      ↓
Fake Response Returned
      ↓
Assertions Verified
```

Think of it as:

```text
Testing Outgoing HTTP Calls
Without Calling Real APIs
```

---

# Real Production Scenario

Application:

```text
Order Service
      ↓
Payment API
```

Client:

```java
PaymentClient
```

Test:

```java
@RestClientTest(
    PaymentClient.class
)
```

Mock:

```text
Payment API Response
```

Verifies:

```text
Request URL
Headers
JSON Parsing
Error Handling
Retry Logic
```

Result:

```text
Fast Tests
No Internet Dependency
Reliable External API Validation
```

`@RestClientTest` is Spring Boot's REST client slice testing annotation that loads only HTTP client infrastructure and provides a mock server, making external API testing fast, isolated, and reliable.