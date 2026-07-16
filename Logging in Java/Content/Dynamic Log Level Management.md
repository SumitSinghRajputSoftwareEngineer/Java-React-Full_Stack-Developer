# Dynamic Log Level Management

> **Goal:** Understand Dynamic Log Level Management, Runtime Log Level Changes, Spring Boot Actuator integration, production troubleshooting, real-world use cases, best practices, and interview concepts.

---

# 1. Introduction to Dynamic Log Level Management

Logging is one of the most important tools for monitoring and troubleshooting applications.

---

In Development:

```text
DEBUG Logs

TRACE Logs

Detailed Information
```

are usually enabled.

---

In Production:

```text
INFO Logs

WARN Logs

ERROR Logs
```

are typically used.

---

# Problem

Suppose a production issue occurs.

---

Current Logging Level:

```text
INFO
```

---

Available Logs:

```text
Application Started

Request Received

Response Sent
```

---

Not enough information to identify the problem.

---

Traditional Solution:

```text
Change Configuration

Restart Application
```

---

Problems:

```text
Downtime

User Impact

Operational Risk
```

---

# Modern Solution

Use:

```text
Dynamic Log Level Management
```

---

# What is Dynamic Log Level Management?

## Definition

> Dynamic Log Level Management is the ability to change logging levels while the application is running without restarting the application.

---

# Example

Current Level:

```text
INFO
```

---

Temporarily change to:

```text
DEBUG
```

---

Collect detailed logs.

---

After investigation:

```text
DEBUG

↓

INFO
```

---

All without restarting.

---

# Benefits

```text
Faster Troubleshooting

No Downtime

Live Debugging

Production Support
```

---

# Dynamic Logging Flow

```text
Application Running

        ↓

Issue Detected

        ↓

Change Log Level

        ↓

Collect Logs

        ↓

Diagnose Issue

        ↓

Restore Level
```

---

# 2. Why Dynamic Log Level Management?

Production issues are often difficult to reproduce.

---

# Example

Customer reports:

```text
Payment Failure
```

---

Logs show:

```text
INFO

Payment Failed
```

---

Question:

```text
Why Did It Fail?
```

---

Need:

```text
More Detailed Logs
```

---

Instead of:

```text
Restart Application
```

---

Use:

```text
Runtime Log Level Change
```

---

Enable:

```text
DEBUG
```

---

Gather information.

---

# Advantages

```text
No Restart

No Service Interruption

Immediate Visibility
```

---

# Common Situations

```text
API Failures

Database Issues

Authentication Problems

Performance Bottlenecks

Third-Party Integration Failures
```

---

# 3. Runtime Log Level Changes

Core feature of Dynamic Log Level Management.

---

## What is a Runtime Log Level Change?

Changing the logger level while the application is actively running.

---

# Example

Current Configuration:

```text
ROOT

↓

INFO
```

---

Need More Details:

```text
ROOT

↓

DEBUG
```

---

Change occurs:

```text
Immediately
```

---

Without:

```text
Restart
```

---

# Spring Boot Support

Provided through:

```text
Spring Boot Actuator
```

---

# Endpoint

```http
POST /actuator/loggers
```

---

Typically:

```http
POST /actuator/loggers/{logger-name}
```

---

Example:

```http
POST /actuator/loggers/com.company.service
```

---

# Request Flow

```text
Application Running

       ↓

Actuator Request

       ↓

Logger Updated

       ↓

New Level Active
```

---

# Benefits

```text
Real-Time Changes

Operational Flexibility

Faster Diagnostics
```

---

# 4. Using Spring Boot Actuator

Dynamic logging is commonly implemented using:

```text
Spring Boot Actuator
```

---

# Dependency

```xml
<dependency>
    <groupId>
        org.springframework.boot
    </groupId>

    <artifactId>
        spring-boot-starter-actuator
    </artifactId>
</dependency>
```

---

# Expose Endpoint

```properties
management.endpoints.web.exposure.include=loggers
```

---

# Endpoint

```http
/actuator/loggers
```

---

Provides:

```text
Logger Information

Current Levels

Runtime Updates
```

---

# View Logger

```http
GET /actuator/loggers/com.company
```

---

Example Response

```json
{
  "configuredLevel":"INFO",
  "effectiveLevel":"INFO"
}
```

---

# Change Logger Level

```http
POST /actuator/loggers/com.company
```

---

Request Body

```json
{
  "configuredLevel":"DEBUG"
}
```

---

# Result

```text
DEBUG Logging Enabled
```

---

Immediately.

---

# Supported Levels

```text
TRACE

DEBUG

INFO

WARN

ERROR

OFF
```

---

# 5. Changing Log Levels Dynamically

Most common runtime operation.

---

# Example 1

Current Level:

```text
INFO
```

---

Need:

```text
DEBUG
```

---

Request

```http
POST /actuator/loggers/com.company.service
```

---

Body

```json
{
  "configuredLevel":"DEBUG"
}
```

---

# Result

Additional logs appear:

```text
Method Entry

Method Exit

SQL Queries

Request Details
```

---

# Example 2

Enable TRACE

```json
{
  "configuredLevel":"TRACE"
}
```

---

Provides:

```text
Maximum Logging Detail
```

---

# Example 3

Disable Logging

```json
{
  "configuredLevel":"OFF"
}
```

---

Result:

```text
Logger Disabled
```

---

# Example 4

Restore INFO

```json
{
  "configuredLevel":"INFO"
}
```

---

# Recommended Workflow

```text
INFO

↓

DEBUG

↓

Investigate

↓

INFO
```

---

# 6. Logger Hierarchy and Dynamic Changes

Spring logging follows hierarchy.

---

# Example

```text
ROOT

↓

com.company

↓

com.company.service

↓

com.company.service.employee
```

---

# Scenario

ROOT:

```text
INFO
```

---

Service Logger:

```text
DEBUG
```

---

Result:

```text
Only Service Logs
Use DEBUG
```

---

# Advantage

Targeted troubleshooting.

---

Instead of:

```text
Entire Application
```

log only:

```text
Specific Package
```

---

# Example

```http
POST /actuator/loggers/com.company.payment
```

---

Result:

```text
Payment Service

↓

DEBUG
```

---

Other modules remain:

```text
INFO
```

---

# 7. Production Use Cases

Most important real-world topic.

---

# Use Case 1

## API Failure Investigation

Problem:

```text
Customer Reports Error
```

---

Current Logs:

```text
INFO
```

---

Solution:

```text
Enable DEBUG
```

for:

```text
API Package
```

---

Collect:

```text
Request Data

Business Logic Steps

Response Details
```

---

# Benefit

```text
Quick Root Cause Analysis
```

---

# Use Case 2

## Database Troubleshooting

Problem:

```text
Slow Queries
```

---

Enable:

```text
DEBUG
```

for:

```text
Hibernate

JPA

Repository Layer
```

---

Example

```http
POST /actuator/loggers/org.hibernate
```

---

Request

```json
{
  "configuredLevel":"DEBUG"
}
```

---

Result

```text
SQL Queries Logged
```

---

# Use Case 3

## Third-Party Integration Issues

Problem:

```text
External Payment API Failing
```

---

Enable:

```text
DEBUG
```

for:

```text
Integration Package
```

---

Monitor:

```text
Requests

Responses

Errors
```

---

# Use Case 4

## Authentication Problems

Problem:

```text
Login Failure
```

---

Enable:

```text
DEBUG
```

for:

```text
Spring Security
```

---

Example

```http
POST /actuator/loggers/org.springframework.security
```

---

# Result

Detailed authentication logs.

---

# Use Case 5

## Performance Investigation

Problem:

```text
Slow Response Time
```

---

Enable:

```text
DEBUG
```

for:

```text
Service Layer

Database Layer
```

---

Identify:

```text
Slow Method

Slow Query

Slow API
```

---

# Use Case 6

## Microservices Troubleshooting

Problem:

```text
Inter-Service Communication Failure
```

---

Enable:

```text
DEBUG
```

for:

```text
Feign Clients

RestTemplate

WebClient
```

---

Capture:

```text
Request Flow

Response Flow

Failures
```

---

# 8. Dynamic Logging in Microservices

Very common enterprise use case.

---

# Scenario

Services:

```text
User Service

Order Service

Payment Service

Inventory Service
```

---

Issue:

```text
Payment Failure
```

---

Instead of:

```text
Enable DEBUG Everywhere
```

---

Enable:

```text
DEBUG

Only For

Payment Service
```

---

Benefits:

```text
Targeted Logging

Lower Storage

Better Performance
```

---

# Microservice Flow

```text
Issue

↓

Identify Service

↓

Enable DEBUG

↓

Collect Logs

↓

Fix Issue

↓

Restore INFO
```

---

# 9. Monitoring Runtime Changes

Dynamic changes should be monitored.

---

# Why?

To avoid:

```text
Unexpected Logging

Performance Issues

Storage Problems
```

---

# Example

Someone enables:

```text
TRACE
```

for:

```text
ROOT Logger
```

---

Result:

```text
Huge Log Volume
```

---

Monitoring helps detect:

```text
Who Changed

What Changed

When Changed
```

---

# Recommended

Audit:

```text
Log Level Changes
```

---

# 10. Best Practices

---

## Use INFO in Production

Recommended default.

---

## Enable DEBUG Temporarily

Only during investigation.

---

## Restore Original Levels

After issue resolution.

---

## Avoid TRACE in Production

Generates excessive logs.

---

## Change Specific Loggers

Prefer:

```text
Package Level
```

---

instead of:

```text
ROOT Logger
```

---

## Secure Actuator Endpoint

Prevent unauthorized changes.

---

# Recommended Strategy

```text
INFO

↓

Temporary DEBUG

↓

Investigate

↓

Back To INFO
```

---

# 11. Common Mistakes

---

## Enabling DEBUG For Entire Application

Problem:

```text
Huge Log Volume
```

---

## Forgetting To Reset Log Level

Problem:

```text
Long-Term Performance Impact
```

---

## Using TRACE In Production

Problem:

```text
Storage Explosion
```

---

## Exposing Actuator Publicly

Problem:

```text
Security Risk
```

---

## No Audit Trail

Problem:

```text
Unknown Configuration Changes
```

---

# 12. Interview Questions

### Q1. What is Dynamic Log Level Management?

Changing log levels while the application is running without restarting it.

---

### Q2. Why is Dynamic Logging useful?

For troubleshooting production issues without downtime.

---

### Q3. Which Spring Boot component enables runtime log level changes?

```text
Spring Boot Actuator
```

---

### Q4. Which endpoint is used?

```text
/actuator/loggers
```

---

### Q5. Which HTTP method changes log levels?

```http
POST
```

---

### Q6. Can log levels be changed without restarting the application?

```text
Yes
```

---

### Q7. Which log levels can be configured dynamically?

```text
TRACE

DEBUG

INFO

WARN

ERROR

OFF
```

---

### Q8. Why should DEBUG be temporary in production?

Because it increases log volume and may affect performance.

---

### Q9. What is the best practice for production debugging?

Enable DEBUG only for the affected package or service.

---

### Q10. Why should Actuator endpoints be secured?

To prevent unauthorized runtime configuration changes.

---

# Quick Revision

```text
Dynamic Log Level Management
----------------------------

Definition

Change Log Levels

Without Restarting

--------------------------------

Benefits

✔ Live Debugging
✔ No Downtime
✔ Faster Troubleshooting

--------------------------------

Spring Boot Support

Actuator

--------------------------------

Endpoint

/actuator/loggers

--------------------------------

Update Logger

POST

/actuator/loggers/{logger}

--------------------------------

Example

{
  "configuredLevel":"DEBUG"
}

--------------------------------

Supported Levels

TRACE
DEBUG
INFO
WARN
ERROR
OFF

--------------------------------

Production Use Cases

✔ API Failures
✔ Database Issues
✔ Authentication Problems
✔ Performance Analysis
✔ Microservices Troubleshooting

--------------------------------

Best Practice

INFO

↓

DEBUG

↓

Investigate

↓

INFO

--------------------------------

Avoid

✘ TRACE In Production
✘ DEBUG For Entire Application
✘ Unsecured Actuator Endpoints

--------------------------------

Remember

Dynamic Logging

↓

Runtime Log Level Change

↓

No Application Restart

↓

Production Troubleshooting
```