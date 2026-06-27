
One of Kafka's most powerful features is that messages are not deleted immediately after consumption.

Unlike traditional messaging systems:

```text
Consume Message
      ↓
Delete Message
```

Kafka follows a different approach:

```text
Produce Message
      ↓
Store Message
      ↓
Consume Message
      ↓
Keep Message
      ↓
Delete Based On Retention Policy
```

This enables:

```text
Event Replay

Auditing

Recovery

Multiple Consumers

Event Sourcing
```

Kafka uses **Retention Policies** to determine:

```text
How Long Data Is Kept

How Much Data Is Kept

When Data Is Removed
```

---

# What is Retention?

## Definition

Retention is the mechanism that controls how long Kafka stores data before deleting it.

---

Kafka retains messages regardless of:

```text
Whether Messages Are Consumed

Whether Consumer Offsets Advance
```

---

Important:

```text
Consumers Do Not Control Deletion
```

Retention policies control deletion.

---

# Example

Topic:

```text
orders
```

Message:

```text
Order Created
```

Produced on:

```text
Monday
```

Consumed on:

```text
Tuesday
```

Retention:

```text
7 Days
```

Deleted on:

```text
Next Monday
```

---

# Why Kafka Retains Data?

Kafka was designed as:

```text
Distributed Event Store
```

not merely a messaging queue.

---

Benefits:

```text
Replay Old Events

Recovery From Failures

Audit Trail

Multiple Consumer Groups

Data Reprocessing
```

---

# Example

Consumer crashes.

---

Traditional Queue:

```text
Message Already Gone
```

---

Kafka:

```text
Message Still Available
```

---

Consumer can restart and continue.

---

# Retention Types

Kafka supports two primary retention mechanisms:

```text
Time-Based Retention

Size-Based Retention
```

---

Kafka deletes data when either limit is reached.

---

# Time-Based Retention

Most commonly used retention policy.

---

# What is Time-Based Retention?

Messages are retained for a specific period of time.

---

Example:

```text
Keep Messages For 7 Days
```

---

After:

```text
7 Days
```

Kafka deletes old log segments.

---

# Configuration

```properties
log.retention.hours
```

---

Example:

```properties
log.retention.hours=168
```

---

Meaning:

```text
168 Hours
=
7 Days
```

---

# Other Time Configurations

---

## Minutes

```properties
log.retention.minutes
```

---

Example:

```properties
log.retention.minutes=60
```

---

## Milliseconds

```properties
log.retention.ms
```

---

Example:

```properties
log.retention.ms=86400000
```

---

Represents:

```text
24 Hours
```

---

# Priority Order

Kafka evaluates:

```text
retention.ms
      ↓
retention.minutes
      ↓
retention.hours
```

---

Most specific value wins.

---

# Example

Retention:

```properties
log.retention.hours=168
```

---

Messages:

```text
Monday

Tuesday

Wednesday
```

---

After 7 Days:

```text
Monday Segment Deleted
```

---

# Visualization

```text
Day 1
Day 2
Day 3
Day 4
Day 5
Day 6
Day 7
```

---

Retention Window:

```text
<---- Retained ---->

Old Data Removed
```

---

# Advantages

---

## Predictable Data Lifecycle

---

## Easy To Configure

---

## Common Production Choice

---

## Useful For Replay

---

# Disadvantages

---

## Storage Growth Depends On Traffic

---

High traffic topics may consume significant disk space.

---

# Size-Based Retention

Another important retention mechanism.

---

# What is Size-Based Retention?

Kafka retains data until a maximum storage size is reached.

---

Configuration:

```properties
log.retention.bytes
```

---

Example:

```properties
log.retention.bytes=107374182400
```

---

Meaning:

```text
100 GB
```

---

# Example

Topic Size:

```text
120 GB
```

Retention Limit:

```text
100 GB
```

---

Kafka removes oldest segments.

---

Result:

```text
Storage Reduced To 100 GB
```

---

# Visualization

```text
Segment 1 = 20 GB

Segment 2 = 20 GB

Segment 3 = 20 GB

Segment 4 = 20 GB

Segment 5 = 20 GB

Segment 6 = 20 GB
```

---

Total:

```text
120 GB
```

---

Limit:

```text
100 GB
```

---

Kafka deletes:

```text
Segment 1
```

---

New Total:

```text
100 GB
```

---

# Advantages

---

## Prevents Disk Exhaustion

---

## Controls Storage Costs

---

## Useful For High-Volume Topics

---

# Disadvantages

---

## Retention Period Becomes Unpredictable

---

Example:

```text
High Traffic Day
```

may remove data faster.

---

# Time-Based vs Size-Based Retention

| Feature | Time-Based | Size-Based |
|----------|-----------|------------|
| Based On | Age | Storage Size |
| Predictable Duration | Yes | No |
| Predictable Storage | No | Yes |
| Most Common | Yes | Less Common |
| Controls Disk Usage | Indirectly | Directly |

---

# Combined Retention

Kafka supports both simultaneously.

---

Example:

```properties
log.retention.hours=168

log.retention.bytes=100GB
```

---

Deletion occurs when:

```text
Either Condition Is Met
```

---

Visualization:

```text
Age Limit Reached
OR
Size Limit Reached
```

---

Kafka deletes segments.

---

# Log Cleanup

Retention is implemented through log cleanup.

---

# What is Log Cleanup?

The process of removing old data from Kafka logs.

---

Kafka never deletes individual records.

---

Kafka deletes:

```text
Entire Log Segments
```

---

This makes cleanup extremely efficient.

---

# Example

Partition:

```text
orders-0
```

Segments:

```text
Segment A

Segment B

Segment C
```

---

Retention expired:

```text
Segment A
```

---

Kafka deletes:

```text
Segment A
```

---

Remaining:

```text
Segment B

Segment C
```

---

# Why Segment-Based Cleanup?

Deleting individual messages would require:

```text
File Rewrites

Disk Fragmentation

Poor Performance
```

---

Kafka avoids this by deleting whole segments.

---

# Cleanup Policies

Kafka supports two cleanup modes:

```text
Delete

Compact
```

---

# Delete Policy

Default Kafka behavior.

---

Configuration:

```properties
cleanup.policy=delete
```

---

Behavior:

```text
Delete Old Segments
```

---

Most topics use this policy.

---

# Example

```text
7 Day Retention
```

---

After 7 days:

```text
Old Segments Deleted
```

---

# Compact Policy

Advanced retention mechanism.

---

Configuration:

```properties
cleanup.policy=compact
```

---

Behavior:

```text
Keep Latest Value
For Each Key
```

---

# Example

Messages:

```text
User1 -> A

User1 -> B

User1 -> C
```

---

After compaction:

```text
User1 -> C
```

---

Older values removed.

---

# Use Cases

```text
Customer Profiles

Configuration Data

Reference Data

State Stores
```

---

# Delete + Compact

Kafka supports both:

```properties
cleanup.policy=compact,delete
```

---

Behavior:

```text
Compaction
+
Retention Deletion
```

---

# Retention Configuration

Retention can be configured:

```text
Broker Level

Topic Level
```

---

# Broker-Level Configuration

Applies to all topics.

---

File:

```text
server.properties
```

---

Example:

```properties
log.retention.hours=168
```

---

# Topic-Level Configuration

Overrides broker settings.

---

Example:

```bash
kafka-topics.sh \
--alter \
--topic orders \
--config retention.ms=604800000
```

---

Result:

```text
Only orders Topic Updated
```

---

# Common Retention Configurations

---

## Retention Time

```properties
retention.ms
```

---

## Retention Hours

```properties
retention.hours
```

---

## Retention Bytes

```properties
retention.bytes
```

---

## Cleanup Policy

```properties
cleanup.policy
```

---

## Segment Size

```properties
segment.bytes
```

---

## Segment Roll Time

```properties
segment.ms
```

---

# Retention Deletion Workflow

```text
Message Written
      ↓
Stored In Segment
      ↓
Segment Ages
      ↓
Retention Check
      ↓
Delete Eligible Segment
```

---

# Retention Check Interval

Kafka periodically checks logs.

---

Configuration:

```properties
log.retention.check.interval.ms
```

---

Default:

```text
5 Minutes
```

---

Meaning:

Kafka checks for expired segments every five minutes.

---

# Segment Rolling and Retention

Retention operates on segments.

---

Example:

Retention:

```text
7 Days
```

---

Segment:

```text
Still Active
```

---

Kafka cannot delete it yet.

---

Reason:

```text
Only Closed Segments
Can Be Deleted
```

---

Therefore:

```text
Segment Rolling
```

is important.

---

# Real-World Examples

---

# Example 1: E-Commerce Orders

Topic:

```text
orders
```

Retention:

```text
30 Days
```

---

Reason:

```text
Order Auditing

Replay Capability
```

---

Configuration:

```properties
retention.ms=2592000000
```

---

# Example 2: Application Logs

Topic:

```text
application-logs
```

Retention:

```text
7 Days
```

---

Reason:

```text
Troubleshooting

Monitoring
```

---

# Example 3: Metrics

Topic:

```text
metrics
```

Retention:

```text
24 Hours
```

---

Reason:

```text
High Volume Data
```

---

# Example 4: Customer Profiles

Topic:

```text
customer-profile
```

Policy:

```properties
cleanup.policy=compact
```

---

Reason:

```text
Need Latest State Only
```

---

# Example 5: Financial Transactions

Topic:

```text
transactions
```

Retention:

```text
Several Months
```

or

```text
Several Years
```

depending on compliance requirements.

---

# Retention Best Practices

---

## Use Time-Based Retention For Most Topics

Most common production approach.

---

## Configure Size Limits

Protect disks from exhaustion.

---

Example:

```properties
retention.bytes=100GB
```

---

## Monitor Disk Usage

Track:

```text
Storage Growth

Retention Cleanup

Segment Count
```

---

## Use Compaction For State Data

Examples:

```text
User Profiles

Configuration Topics

Reference Data
```

---

## Keep Retention Aligned With Business Needs

Examples:

```text
Logs → Days

Orders → Weeks

Financial Data → Years
```

---

## Use Topic-Level Retention

Different topics often need different retention periods.

---

## Avoid Infinite Retention Without Planning

Configuration:

```properties
retention.ms=-1
```

---

Risk:

```text
Disk Full
```

---

## Monitor Cleanup Lag

Ensure retention cleanup is functioning correctly.

---

# Common Interview Questions

## What is Kafka Retention?

The mechanism that determines how long Kafka keeps data before deleting it.

---

## Does Kafka delete messages after consumption?

```text
No
```

Messages are deleted based on retention policies, not consumption.

---

## What are the two primary retention types?

```text
Time-Based Retention

Size-Based Retention
```

---

## What is Time-Based Retention?

Messages are retained for a configured duration.

---

## What is Size-Based Retention?

Messages are retained until a storage limit is reached.

---

## What is Log Cleanup?

The process of removing old data from Kafka logs.

---

## Does Kafka delete individual records?

```text
No
```

Kafka deletes entire log segments.

---

## What is cleanup.policy=delete?

The default policy that removes old segments based on retention rules.

---

## What is cleanup.policy=compact?

A policy that retains only the latest record for each key.

---

## Can Kafka use both delete and compact?

```text
Yes
```

Using:

```properties
cleanup.policy=compact,delete
```

---

## Which retention type is most commonly used?

```text
Time-Based Retention
```

---

# Summary

Kafka Retention Policies determine how long data remains available in Kafka.

Core Concepts:

- Time-Based Retention
- Size-Based Retention
- Log Cleanup
- Retention Configuration
- Retention Best Practices

Retention Flow:

```text
Message Produced
       ↓
Stored In Segment
       ↓
Retention Evaluated
       ↓
Expired Segment Deleted
```

Cleanup Policies:

```text
delete
```

Removes old segments.

---

```text
compact
```

Retains latest value per key.

---

Most Common Production Setup:

```properties
retention.ms=604800000
cleanup.policy=delete
```

Advanced State-Based Topics:

```properties
cleanup.policy=compact
```

Kafka's retention architecture enables:

```text
Event Replay

Audit Trails

Disaster Recovery

Event Sourcing

Scalable Storage Management
```

while maintaining high throughput and operational simplicity.