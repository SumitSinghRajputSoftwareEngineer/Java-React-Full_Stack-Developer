# Spring Boot Actuator Logging

> **Goal:** Understand Spring Boot Actuator Logging, Loggers Endpoint, Runtime Log Level Management, Log Monitoring, production use cases, best practices, and interview concepts.

---

# 1. Introduction to Spring Boot Actuator

Spring Boot applications running in production need visibility into:

```text
Application Health

Performance

Metrics

Logs

Beans

Environment Properties
```

---

Without monitoring:

```text
Difficult Troubleshooting

Limited Visibility

Slow Incident Resolution
```

---

# Solution

Use:

```text
Spring Boot Actuator
```

---

# What is Spring Boot Actuator?

## Definition

> Spring Boot Actuator provides production-ready features to monitor, manage, and inspect Spring Boot applications.

---

# Actuator Provides

```text
Health Information

Application Metrics

Environment Details

Beans Information

Loggers Management

Thread Information
```

---

# Common Actuator Endpoints

| Endpoint | Purpose |
|-----------|----------|
| /actuator | Lists Available Endpoints |
| /actuator/health | Application Health |
| /actuator/info | Application Information |
| /actuator/metrics | Application Metrics |
| /actuator/env | Environment Variables |
| /actuator/beans | Spring Beans |
| /actuator/loggers | Logger Management |

---

# Why Use Actuator for Logging?

Allows:

```text
View Loggers

View Log Levels

Change Log Levels

Runtime Troubleshooting
```

---

# Traditional Approach

To change logging level:

```properties
logging.level.root=DEBUG
```

---

Then:

```text
Restart Application
```

---

Problem:

```text
Not Practical In Production
```

---

# Actuator Solution

Change log levels:

```text
Without Restarting
```

---

# 2. Loggers Endpoint

One of the most useful Actuator endpoints.

---

## Endpoint

```text
/actuator/loggers
```

---

# Purpose

Provides information about:

```text
All Available Loggers

Configured Log Levels

Effective Log Levels
```

---

# Example Request

```http
GET /actuator/loggers
```

---

# Example Response

```json
{
  "levels": [
    "OFF",
    "ERROR",
    "WARN",
    "INFO",
    "DEBUG",
    "TRACE"
  ]
}
```

---

# What Can We See?

```text
Logger Names

Current Log Levels

Effective Log Levels
```

---

# Why Is This Useful?

Helps identify:

```text
Current Logging Configuration

Active Debugging Levels

Logger Hierarchy
```

---

# Logger Information Flow

```text
Application

     ↓

Actuator

     ↓

/actuator/loggers

     ↓

Logger Details
```

---

# Benefits

```text
Visibility

Monitoring

Runtime Management
```

---

# 3. Enabling Actuator

Before using logging endpoints, Actuator must be added.

---

# Maven Dependency

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

# Gradle Dependency

```gradle
implementation(
    "org.springframework.boot:
    spring-boot-starter-actuator"
)
```

---

# Exposing Endpoints

By default, many endpoints are not exposed.

---

# application.properties

```properties
management.endpoints.web.exposure.include=*
```

---

# Expose Only Loggers

```properties
management.endpoints.web.exposure.include=loggers
```

---

# Benefits

```text
Security

Controlled Access
```

---

# Example URL

```http
http://localhost:8080/actuator/loggers
```

---

# 4. Viewing Logger Information

Actuator allows viewing all configured loggers.

---

# Request

```http
GET /actuator/loggers
```

---

# Example Response

```json
{
  "loggers": {
    "ROOT": {
      "configuredLevel": "INFO",
      "effectiveLevel": "INFO"
    }
  }
}
```

---

# Important Fields

---

## configuredLevel

Level explicitly configured.

---

Example:

```text
DEBUG
```

---

## effectiveLevel

Level currently being applied.

---

Example:

```text
INFO
```

---

# Why Both Matter?

Because logging follows hierarchy.

---

Example:

```text
Parent Logger

↓

Child Logger
```

---

Child may inherit parent level.

---

# Example

```text
Configured Level:
null

Effective Level:
INFO
```

---

Meaning:

```text
Inherited From Parent Logger
```

---

# Viewing Specific Logger

Request:

```http
GET /actuator/loggers/com.company.service
```

---

Response:

```json
{
  "configuredLevel":"DEBUG",
  "effectiveLevel":"DEBUG"
}
```

---

# Benefits

```text
Logger Inspection

Configuration Validation

Troubleshooting
```

---

# 5. Changing Log Levels at Runtime

One of the most powerful Actuator features.

---

## Problem

Application running in production.

---

Need:

```text
More Logs

For Troubleshooting
```

---

Traditional Approach:

```text
Change Properties

Restart Application
```

---

Not ideal.

---

# Actuator Solution

Change log level dynamically.

---

# Example

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

Use:

```http
POST /actuator/loggers/com.company.service
```

---

Request Body:

```json
{
  "configuredLevel":"DEBUG"
}
```

---

# Result

Logger immediately changes to:

```text
DEBUG
```

---

Without:

```text
Restarting Application
```

---

# Flow

```text
POST Request

      ↓

Actuator

      ↓

Logger Updated

      ↓

New Logs Generated
```

---

# Example Response

```http
204 No Content
```

---

Meaning:

```text
Successfully Updated
```

---

# Benefits

```text
Live Troubleshooting

No Downtime

Fast Diagnosis
```

---

# Returning to INFO

Request:

```json
{
  "configuredLevel":"INFO"
}
```

---

# Disabling Logger

Request:

```json
{
  "configuredLevel":"OFF"
}
```

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

# 6. Runtime Debugging Scenario

Very common production use case.

---

# Problem

Customer reports:

```text
Order Creation Failure
```

---

Current Logging Level:

```text
INFO
```

---

Logs insufficient.

---

# Solution

Temporarily enable:

```text
DEBUG
```

---

Using:

```http
POST /actuator/loggers/com.company.order
```

---

Request:

```json
{
  "configuredLevel":"DEBUG"
}
```

---

# Result

Additional logs appear:

```text
Request Payload

Database Queries

Business Logic Steps
```

---

Issue diagnosed.

---

# After Fix

Reset:

```json
{
  "configuredLevel":"INFO"
}
```

---

# Benefits

```text
No Restart

Faster Investigation

Reduced Downtime
```

---

# 7. Monitoring Logs

Actuator helps monitor logging behavior.

---

# What Can Be Monitored?

```text
Current Log Levels

Logger Configuration

Debug Mode Activation

Runtime Logging Changes
```

---

# Example Monitoring Questions

```text
Which Logger Is DEBUG?

Which Logger Is TRACE?

Which Logger Is OFF?

Which Loggers Inherit Levels?
```

---

# Using /actuator/loggers

Provides answers instantly.

---

# Monitoring Flow

```text
Application

     ↓

Actuator

     ↓

Loggers Endpoint

     ↓

Monitoring Dashboard
```

---

# Benefits

```text
Visibility

Operational Monitoring

Production Support
```

---

# Integration With Monitoring Tools

Can be combined with:

```text
Grafana

ELK Stack

Prometheus

Datadog

Splunk
```

---

# Example Workflow

```text
Actuator

      ↓

Log Monitoring

      ↓

Centralized Logging

      ↓

Alerting
```

---

# 8. Actuator and Logback

Spring Boot typically uses:

```text
Logback
```

---

Actuator works directly with:

```text
Logback Loggers
```

---

Example:

```text
ROOT

com.company

com.company.service
```

---

Runtime changes immediately affect:

```text
Logback Configuration
```

---

Without:

```text
Restarting Application
```

---

# Example

```text
ROOT

↓

INFO
```

---

Change:

```text
ROOT

↓

DEBUG
```

---

Result:

```text
Entire Application
Logs More Information
```

---

# 9. Security Considerations

Actuator endpoints are powerful.

---

# Risk

Unauthorized users may:

```text
View Configuration

Change Log Levels

Enable Debug Logging
```

---

# Best Practice

Secure Actuator endpoints.

---

Example:

```properties
management.endpoints.web.exposure.include=health,info,loggers
```

---

Avoid exposing:

```properties
include=*
```

in production unless properly secured.

---

# Use Spring Security

Protect:

```text
/actuator/loggers
```

---

# Example

Only:

```text
Administrators

Operations Team
```

should have access.

---

# Security Benefits

```text
Controlled Access

Reduced Risk

Compliance
```

---

# 10. Best Practices

---

## Use INFO in Production

Default recommended level.

---

## Enable DEBUG Temporarily

For troubleshooting only.

---

## Reset Levels After Debugging

Avoid excessive logging.

---

## Secure Actuator Endpoints

Prevent unauthorized access.

---

## Monitor Runtime Changes

Track who changed log levels.

---

## Avoid TRACE in Production

Produces huge log volumes.

---

# Recommended Levels

| Environment | Level |
|------------|--------|
| Development | DEBUG |
| Testing | DEBUG |
| Staging | INFO |
| Production | INFO |
| Emergency Debugging | DEBUG (Temporary) |

---

# 11. Common Mistakes

---

## Leaving DEBUG Enabled

Problem:

```text
Huge Log Files

Performance Impact
```

---

## Exposing All Endpoints

Problem:

```text
Security Risks
```

---

## Using TRACE in Production

Problem:

```text
Massive Log Volume
```

---

## Not Monitoring Changes

Problem:

```text
Unknown Logging Configuration
```

---

## Changing ROOT Logger Unnecessarily

Problem:

```text
Entire Application
Produces Excessive Logs
```

---

# 12. Interview Questions

### Q1. What is Spring Boot Actuator?

A production-ready module for monitoring and managing Spring Boot applications.

---

### Q2. Which endpoint manages logging?

```text
/actuator/loggers
```

---

### Q3. What can the Loggers endpoint do?

```text
View Loggers

View Levels

Change Levels
```

---

### Q4. Can log levels be changed without restarting the application?

```text
Yes
```

using:

```text
/actuator/loggers
```

---

### Q5. Which HTTP method is used to update log levels?

```http
POST
```

---

### Q6. What is configuredLevel?

The explicitly configured logging level.

---

### Q7. What is effectiveLevel?

The actual logging level being applied.

---

### Q8. Why is runtime log level change useful?

For production troubleshooting without downtime.

---

### Q9. Why should Actuator endpoints be secured?

To prevent unauthorized access and configuration changes.

---

### Q10. Which logging framework does Spring Boot commonly use with Actuator?

```text
Logback
```

---

# Quick Revision

```text
Spring Boot Actuator Logging
----------------------------

Purpose

✔ Monitor Loggers
✔ View Levels
✔ Change Levels

--------------------------------

Endpoint

/actuator/loggers

--------------------------------

View All Loggers

GET

/actuator/loggers

--------------------------------

View Specific Logger

GET

/actuator/loggers/com.company

--------------------------------

Change Log Level

POST

/actuator/loggers/com.company

--------------------------------

Request Body

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

Key Benefits

✔ Runtime Updates
✔ No Restart Required
✔ Faster Troubleshooting

--------------------------------

Important Fields

configuredLevel

Explicitly Set Level

effectiveLevel

Actually Applied Level

--------------------------------

Security

Protect

/actuator/loggers

Using

Spring Security

--------------------------------

Best Practice

Production

INFO

Temporary Debugging

DEBUG

Avoid

TRACE

--------------------------------

Remember

Spring Boot Actuator

↓

/actuator/loggers

↓

View Loggers

↓

Change Levels

↓

No Restart Required
```