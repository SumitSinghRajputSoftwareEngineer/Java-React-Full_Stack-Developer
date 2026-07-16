
---

# 1. Spring Boot Core Annotations


[[@SpringBootApplication]]
[[@EnableAutoConfiguration]]
[[@ComponentScan]]
[[@Configuration]]
[[@Bean]]
[[@ConfigurationProperties]]
[[@EnableConfigurationProperties]]
[[@PropertySource]]
[[@Import]]
[[@ImportResource]]
[[@Profile]]
[[@Lazy]]

---

# 2. Dependency Injection (IoC Container)

[[@Component]]
[[@Service]]
[[@Repository]]
[[@Controller]]
[[@RestController]]

[[@Autowired]]
[[@Qualifier]]
[[@Primary]]
[[@Resource]]
[[@Inject]]
[[@Value]]
[[@Lookup]]

---

# 3. Configuration Annotations


[[@Configuration]]
[[@Bean]]
[[@PropertySource]]
[[@ConfigurationProperties]]
[[@EnableConfigurationProperties]]
[[@ConstructorBinding]]
[[@Import]]
[[@ImportResource]]
[[@Profile]]
[[@DependsOn]]
[[@Scope]]
[[@Lazy]]

---

# 4. Spring MVC / REST API Annotations

## Controller Level


[[@Controller]]
[[@RestController]]
[[@RequestMapping]]
[[@CrossOrigin]]
[[@ControllerAdvice]]
[[@RestControllerAdvice]]

## Request Mapping


[[@RequestMapping]]
[[@GetMapping]]
[[@PostMapping]]
[[@PutMapping]]
[[@DeleteMapping]]
[[@PatchMapping]]

## Method Parameters


[[@PathVariable]]
[[@RequestParam]]
[[@RequestBody]]
[[@RequestHeader]]
[[@RequestPart]]
[[@RequestAttribute]]
[[@CookieValue]]
[[@MatrixVariable]]
[[@ModelAttribute]]

## Response Handling


[[@ResponseBody]]
[[@ResponseStatus]]
[[@ResponseHeader]]

## Exception Handling


[[@ExceptionHandler]]
[[@ControllerAdvice]]
[[@RestControllerAdvice]]

---

# 5. Validation Annotations

## Validation Trigger


[[@Valid]]
[[@Validated]]

## Bean Validation Constraints


[[@NotNull]]
[[@NotEmpty]]
[[@NotBlank]]

[[@Size]]
[[@Min]]
[[@Max]]
[[@Positive]]
[[@PositiveOrZero]]
[[@Negative]]
[[@NegativeOrZero]]

[[@Email]]
[[@Pattern]]

[[@Past]]
[[@PastOrPresent]]
[[@Future]]
[[@FutureOrPresent]]

[[@Digits]]
[[@DecimalMin]]
[[@DecimalMax]]

[[@AssertTrue]]
[[@AssertFalse]]

[[@Null]]

[[@Length]]
[[@Range]]
[[@URL]]
[[@CreditCardNumber]]

---

# 6. Exception Handling

[[@ExceptionHandler]]
[[@ControllerAdvice]]
[[@RestControllerAdvice]]
[[@ResponseStatus]]

---

# 7. Spring Data JPA Annotations

## Entity Mapping


[[@Entity]]
[[@Table]]
[[@Id]]
[[@GeneratedValue]]
[[@Column]]
[[@Transient]]
[[@Lob]]
[[@Version]]

## Relationships


[[@OneToOne]]
[[@OneToMany]]
[[@ManyToOne]]
[[@ManyToMany]]

[[@JoinColumn]]
[[@JoinColumns]]
[[@JoinTable]]

[[@MapsId]]

## Inheritance

[[@Inheritance]]
[[@DiscriminatorColumn]]
[[@DiscriminatorValue]]
[[@MappedSuperclass]]

## Fetching

[[@EntityGraph]]

## Auditing

[[@EnableJpaAuditing]]
[[@CreatedDate]]
[[@LastModifiedDate]]
[[@CreatedBy]]
[[@LastModifiedBy]]
[[@EntityListeners]]

## Query Related

[[@Query]]
[[@Modifying]]
[[@Procedure]]
[[@Lock]]
[[@Param]]

---

# 8. Transaction Management

[[@Transactional]]
[[@EnableTransactionManagement]]

## Transaction Attributes

```java
Propagation
Isolation
rollbackFor
noRollbackFor
timeout
readOnly
```

---

# 9. AOP (Aspect Oriented Programming)

[[@Aspect]]
[[@EnableAspectJAutoProxy]]

## Advice Types

[[@Before]]
[[@After]]
[[@AfterReturning]]
[[@AfterThrowing]]
[[@Around]]

## Pointcuts

[[@Pointcut]]

---

# 10. Spring Security Annotations

## Configuration

[[@EnableWebSecurity]]
[[@EnableMethodSecurity]]

## Authorization

[[@PreAuthorize]]
[[@PostAuthorize]]

[[@PreFilter]]
[[@PostFilter]]

[[@Secured]]
[[@RolesAllowed]]

## Authentication Principal

[[@AuthenticationPrincipal]]

---

# 11. Scheduling


[[@EnableScheduling]]
[[@Scheduled]]

---

# 12. Async Processing

[[@EnableAsync]]
[[@Async]]

---

# 13. Caching

[[@EnableCaching]]

[[@Cacheable]]
[[@CachePut]]
[[@CacheEvict]]
[[@Caching]]
[[@CacheConfig]]

---

# 14. Spring Events

[[@EventListener]]

---

# 15. Testing Annotations

## Spring Boot Testing

[[@SpringBootTest]]
[[@TestConfiguration]]

## MVC Testing

[[@WebMvcTest]]
[[@AutoConfigureMockMvc]]

## JPA Testing

[[@DataJpaTest]]

## JDBC Testing

[[@JdbcTest]]

## JSON Testing

[[@JsonTest]]

## REST Client Testing

[[@RestClientTest]]

## Mocking

[[@MockBean]]
[[@SpyBean]]

## JUnit

[[@Test]]
[[@BeforeEach]]
[[@AfterEach]]
[[@BeforeAll]]
[[@AfterAll]]
[[@DisplayName]]
[[@Nested]]
[[@ParameterizedTest]]

---

# 16. Spring Cloud / Microservices

## Service Discovery

[[@EnableDiscoveryClient]]
[[@EnableEurekaClient]]

## Circuit Breaker

[[@CircuitBreaker]]
[[@Retry]]
[[@RateLimiter]]
[[@Bulkhead]]
[[@TimeLimiter]]

## Feign Client

[[@EnableFeignClients]]
[[@FeignClient]]

## Load Balancing

[[@LoadBalanced]]

---

# 17. Messaging (Kafka / RabbitMQ)

## Kafka

[[@EnableKafka]]
[[@KafkaListener]]
[[@KafkaHandler]]

## RabbitMQ

[[@EnableRabbit]]
[[@RabbitListener]]

---

# 18. WebSocket

## Configuration

[[@EnableWebSocket]]
[[@EnableWebSocketMessageBroker]]

## Messaging

[[@MessageMapping]]
[[@SendTo]]
[[@SendToUser]]

---

# 19. Actuator

[[@Endpoint]]
[[@ReadOperation]]
[[@WriteOperation]]
[[@DeleteOperation]]

---

# 20. Spring Batch

## Configuration

[[@EnableBatchProcessing]]

## Batch Components

[[@StepScope]]
[[@JobScope]]
[[@BeforeJob]]
[[@AfterJob]]
[[@BeforeStep]]
[[@AfterStep]]

---

# 21. Conditional Annotations

[[@Conditional]]

[[@ConditionalOnBean]]
[[@ConditionalOnMissingBean]]

[[@ConditionalOnClass]]
[[@ConditionalOnMissingClass]]

[[@ConditionalOnProperty]]

[[@ConditionalOnResource]]

[[@ConditionalOnJava]]

[[@ConditionalOnExpression]]

[[@ConditionalOnWebApplication]]
[[@ConditionalOnNotWebApplication]]

---

# 22. Servlet & Filter Related

[[@WebServlet]]
[[@WebFilter]]
[[@WebListener]]

[[@ServletComponentScan]]

---

# 23. Lombok Annotations

[[@Getter]]
[[@Setter]]

[[@Data]]

[[@Builder]]

[[@NoArgsConstructor]]
[[@AllArgsConstructor]]
[[@RequiredArgsConstructor]]

[[@ToString]]
[[@EqualsAndHashCode]]

[[@Slf4j]]

[[@Value - lombok]]

[[@SneakyThrows]]

---

# 24. OpenAPI / Swagger

[[@OpenAPIDefinition]]
[[@Tag]]
[[@Operation]]
[[@ApiResponse]]
[[@ApiResponses]]
[[@Parameter]]
[[@Schema]]
[[@SecurityRequirement]]

---

# 25. Most Important Interview Annotations

```java
@SpringBootApplication

@Component
@Service
@Repository
@Controller
@RestController

@Autowired
@Qualifier
@Primary

@Configuration
@Bean

@Value
@ConfigurationProperties

@RequestMapping
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping

@PathVariable
@RequestParam
@RequestBody

@Valid
@Validated

@Entity
@Table
@Id
@GeneratedValue
@Column

@OneToOne
@OneToMany
@ManyToOne
@ManyToMany

@Query
@Transactional

@ControllerAdvice
@ExceptionHandler

@EnableScheduling
@Scheduled

@EnableAsync
@Async

@Cacheable

@SpringBootTest

@PreAuthorize
```

---

# 26. Frequently Forgotten Annotations

```java
@Order
@PostConstruct
@PreDestroy

@PersistenceContext

@EnableConfigurationProperties

@RefreshScope

@SessionAttributes

@SessionAttribute

@RequestScope
@SessionScope
@ApplicationScope

@EnableJpaRepositories

@EntityScan

@EnableMongoRepositories

@EnableRedisRepositories

@Document
@Field

@RedisHash

@EnableBinding

@ConfigurationPropertiesScan

@AliasFor

@Indexed

@Repeatable
```

---
