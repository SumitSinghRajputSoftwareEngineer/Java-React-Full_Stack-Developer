
After a client successfully authenticates with Kafka, the next question Kafka asks is:

```text
What Are You Allowed To Do?
```

This is handled by:

```text
Authorization
```

Authorization controls access to Kafka resources and ensures that authenticated users can only perform operations they are permitted to perform.

Without authorization:

```text
Any Authenticated User Could:

Read Any Topic

Delete Topics

Modify Configurations

Consume Sensitive Data

Produce Unauthorized Messages
```

---

# What is Authorization?

## Definition

Authorization is the process of determining what actions an authenticated user, application, or service is allowed to perform on Kafka resources.

---

Authentication answers:

```text
Who Are You?
```

Authorization answers:

```text
What Can You Do?
```

---

# Authentication vs Authorization

| Authentication | Authorization |
|---------------|---------------|
| Verifies identity | Verifies permissions |
| Who are you? | What can you do? |
| Happens first | Happens after authentication |
| Uses credentials/certificates | Uses ACLs and policies |

---

# Authorization Workflow

```text
Client Connects
       ↓
Authentication
       ↓
Identity Verified
       ↓
Authorization Check
       ↓
Allow / Deny Request
```

---

# Example

User:

```text
order-service
```

---

Allowed:

```text
Write To Orders Topic
```

---

Not Allowed:

```text
Delete Orders Topic
```

---

Kafka Authorization:

```text
Allow Write

Deny Delete
```

---

# Why Authorization is Important?

Without authorization:

```text
Any User Could:

Read Customer Data

Delete Topics

Consume Payment Events

Modify Cluster Settings
```

---

Potential Risks:

```text
Data Breach

Financial Loss

Compliance Violations

Operational Disruptions
```

---

# Kafka Authorization Architecture

```text
Producer / Consumer
          ↓
Authentication
          ↓
Authorization (ACL Check)
          ↓
Kafka Resource
```

---

# Kafka Authorization Mechanism

Kafka primarily uses:

```text
ACLs
```

---

ACL stands for:

```text
Access Control List
```

---

# ACLs (Access Control Lists)

ACLs are the foundation of Kafka authorization.

---

# What is an ACL?

An ACL is a set of rules that define:

```text
Who Can Access What

What Operations Are Allowed
```

---

# ACL Structure

An ACL contains:

```text
Principal

Resource

Operation

Permission
```

---

# Example ACL

```text
User: order-service

Resource: orders-topic

Operation: WRITE

Permission: ALLOW
```

---

Meaning:

```text
order-service

Can Write To orders-topic
```

---

# ACL Architecture

```text
User
  ↓
ACL Evaluation
  ↓
Kafka Resource
```

---

# ACL Components

---

## Principal

Identity of user/application.

Example:

```text
User:payment-service
```

---

## Resource

Kafka object being accessed.

Examples:

```text
Topic

Consumer Group

Cluster

Transactional ID
```

---

## Operation

Action being performed.

Examples:

```text
READ

WRITE

DELETE

ALTER

DESCRIBE
```

---

## Permission

Result:

```text
ALLOW

DENY
```

---

# ACL Evaluation Flow

```text
User Requests Operation
           ↓
Broker Reads ACL
           ↓
Permission Match
           ↓
Allow / Deny
```

---

# Kafka Resources Protected By ACLs

ACLs can secure:

```text
Topics

Consumer Groups

Clusters

Transactional IDs

Delegation Tokens
```

---

# Resource Hierarchy

```text
Kafka Cluster
     ↓
Topics
     ↓
Partitions
```

---

ACLs can be applied at multiple levels.

---

# Common ACL Operations

| Operation | Purpose |
|------------|---------|
| READ | Consume messages |
| WRITE | Produce messages |
| CREATE | Create topics |
| DELETE | Delete topics |
| ALTER | Modify configuration |
| DESCRIBE | View metadata |
| CLUSTER_ACTION | Internal cluster actions |
| ALTER_CONFIGS | Change configs |
| DESCRIBE_CONFIGS | View configs |

---

# Role-Based Access

Role-Based Access Control (RBAC) is a higher-level authorization model.

---

# What is Role-Based Access?

Instead of assigning permissions directly to users:

```text
Permissions
      ↓
Assigned To Roles
      ↓
Roles Assigned To Users
```

---

# Traditional ACL Approach

```text
User A → Permissions

User B → Permissions

User C → Permissions
```

---

Management becomes difficult.

---

# RBAC Approach

```text
Admin Role
      ↓
Multiple Permissions
      ↓
Assigned To Users
```

---

Much easier to manage.

---

# Example Roles

---

## Administrator

Permissions:

```text
Full Cluster Access
```

---

## Producer Role

Permissions:

```text
Write Topics

Describe Topics
```

---

## Consumer Role

Permissions:

```text
Read Topics

Read Consumer Groups
```

---

## Auditor Role

Permissions:

```text
Read Only Access
```

---

# RBAC Example

```text
Role: Order Producer
```

Permissions:

```text
WRITE orders-topic

DESCRIBE orders-topic
```

---

Assigned to:

```text
order-service
```

---

# Benefits of RBAC

---

## Easier Permission Management

---

## Scalable Security

---

## Reduced Administrative Effort

---

## Better Compliance

---

# Topic Permissions

Topic permissions control access to Kafka topics.

---

# Why Topic Permissions?

Not all users should access all topics.

---

Example:

```text
payments-topic
```

contains sensitive data.

---

Only payment services should access it.

---

# Topic Authorization Architecture

```text
User
   ↓
Topic ACL
   ↓
Kafka Topic
```

---

# Common Topic Permissions

---

## READ

Consume messages.

---

## WRITE

Produce messages.

---

## CREATE

Create topics.

---

## DELETE

Delete topics.

---

## ALTER

Modify topic settings.

---

## DESCRIBE

View metadata.

---

# Topic Permission Example

Topic:

```text
orders-topic
```

---

User:

```text
order-service
```

---

Permissions:

```text
READ

WRITE
```

---

Allowed:

```text
Produce Orders

Consume Orders
```

---

Denied:

```text
Delete Topic
```

---

# Topic ACL Example

```text
User:order-service

Resource:orders-topic

Operation:WRITE

Permission:ALLOW
```

---

# Sensitive Topic Example

```text
payments-topic
```

---

Permissions:

```text
payment-service → READ/WRITE

order-service → DENY
```

---

Result:

```text
Strong Data Isolation
```

---

# Consumer Permissions

Consumer permissions control message consumption.

---

# Why Consumer Permissions?

Consumers often access sensitive business information.

---

Examples:

```text
Customer Data

Payment Data

Order History
```

---

Only authorized consumers should read them.

---

# Consumer Authorization Flow

```text
Consumer
    ↓
ACL Check
    ↓
Topic Read
```

---

# Required Consumer Permissions

A consumer usually needs:

```text
READ On Topic

READ On Consumer Group
```

---

# Example

Consumer:

```text
inventory-service
```

---

Needs:

```text
READ inventory-topic

READ inventory-group
```

---

Without group permission:

```text
Consumer Cannot Join Group
```

---

# Consumer ACL Example

```text
User:inventory-service

Resource:inventory-topic

Operation:READ
```

---

Additional ACL:

```text
User:inventory-service

Resource:inventory-group

Operation:READ
```

---

# Consumer Operations

| Operation | Purpose |
|------------|----------|
| READ | Consume messages |
| DESCRIBE | View metadata |
| READ Consumer Group | Join group |

---

# Consumer Security Example

```text
analytics-service
```

---

Allowed:

```text
READ analytics-topic
```

---

Denied:

```text
READ payments-topic
```

---

# Producer Permissions

Producer permissions control message publishing.

---

# Why Producer Permissions?

Without controls:

```text
Any Producer Could:

Publish Fake Orders

Publish Fraudulent Payments

Overwrite Business Events
```

---

# Producer Authorization Flow

```text
Producer
    ↓
ACL Check
    ↓
Write Topic
```

---

# Required Producer Permissions

A producer generally needs:

```text
WRITE

DESCRIBE
```

on a topic.

---

# Producer ACL Example

```text
User:order-service

Resource:orders-topic

Operation:WRITE
```

---

Result:

```text
Can Publish Orders
```

---

# Example

Producer:

```text
payment-service
```

---

Allowed:

```text
WRITE payments-topic
```

---

Denied:

```text
WRITE inventory-topic
```

---

# Producer Operations

| Operation | Purpose |
|------------|----------|
| WRITE | Publish messages |
| DESCRIBE | View metadata |
| CREATE | Create topics (if allowed) |

---

# Authorization Decision Example

Producer:

```text
order-service
```

tries:

```text
WRITE orders-topic
```

---

ACL Exists:

```text
ALLOW
```

---

Result:

```text
Success
```

---

Producer:

```text
order-service
```

tries:

```text
DELETE orders-topic
```

---

ACL Missing:

```text
Denied
```

---

# Kafka Super Users

Kafka supports privileged users.

---

# What is a Super User?

A user with unrestricted access.

---

Example:

```text
Kafka Administrator
```

---

Capabilities:

```text
Manage Cluster

Delete Topics

Create Topics

Modify Configurations
```

---

# Example Configuration

```properties
super.users=User:admin
```

---

# Principle of Least Privilege

A critical authorization principle.

---

# Definition

Grant only the permissions required to perform a task.

---

Bad:

```text
All Users → Full Access
```

---

Good:

```text
Order Service
     ↓
Only Orders Topic Access
```

---

# Benefits

---

## Reduced Attack Surface

---

## Improved Security

---

## Better Compliance

---

## Easier Auditing

---

# Authorization Best Practices

---

## Enable ACLs

---

## Follow Least Privilege

---

## Use RBAC

---

## Separate Producer And Consumer Roles

---

## Restrict Sensitive Topics

---

## Minimize Super Users

---

## Audit Authorization Failures

---

## Regularly Review Permissions

---

## Protect Administrative Accounts

---

# Real-World Authorization Design

## Order Service

```text
WRITE orders-topic
READ order-status-topic
```

---

## Payment Service

```text
READ payments-topic
WRITE payment-status-topic
```

---

## Analytics Service

```text
READ analytics-topic
```

---

## Administrator

```text
Full Cluster Access
```

---

# Common Authorization Failures

---

# Missing ACL

Result:

```text
Access Denied
```

---

# Missing Consumer Group Permission

Result:

```text
Cannot Join Consumer Group
```

---

# Incorrect Topic Permission

Result:

```text
Cannot Read/Write Topic
```

---

# Unauthorized Cluster Operation

Result:

```text
Cluster Authorization Exception
```

---

# Common Interview Questions

## What is Authorization in Kafka?

Authorization determines what actions an authenticated user can perform on Kafka resources.

---

## What is an ACL?

Access Control List used to define permissions on Kafka resources.

---

## What are the main components of an ACL?

```text
Principal

Resource

Operation

Permission
```

---

## What resources can ACLs protect?

```text
Topics

Consumer Groups

Clusters

Transactional IDs
```

---

## What permissions does a producer usually need?

```text
WRITE

DESCRIBE
```

---

## What permissions does a consumer usually need?

```text
READ Topic

READ Consumer Group
```

---

## What is RBAC?

Role-Based Access Control, where permissions are assigned to roles instead of directly to users.

---

## What is a Super User?

A privileged Kafka user with unrestricted access to cluster resources.

---

## What is the Principle of Least Privilege?

Grant only the minimum permissions necessary to perform a task.

---

## Why are ACLs important?

They prevent unauthorized access to Kafka resources and protect sensitive data.

---

# Summary

Kafka Authorization controls what authenticated users can do within a Kafka cluster.

Core Topics:

- ACLs
- Role-Based Access
- Topic Permissions
- Consumer Permissions
- Producer Permissions

Authorization Flow:

```text
Authentication
      ↓
Authorization
      ↓
Kafka Resource
```

Main Mechanism:

```text
ACLs (Access Control Lists)
```

ACL Components:

```text
Principal

Resource

Operation

Permission
```

Protected Resources:

```text
Topics

Consumer Groups

Clusters

Transactional IDs
```

Producer Permissions:

```text
WRITE

DESCRIBE
```

Consumer Permissions:

```text
READ Topic

READ Consumer Group
```

Security Principles:

```text
Least Privilege

RBAC

Separation Of Duties
```

Benefits:

```text
Access Control

Data Protection

Compliance

Operational Security

Controlled Resource Access
```

Kafka Authorization ensures that authenticated users and applications only have access to the resources and operations required for their responsibilities, providing strong security and governance across the Kafka ecosystem.