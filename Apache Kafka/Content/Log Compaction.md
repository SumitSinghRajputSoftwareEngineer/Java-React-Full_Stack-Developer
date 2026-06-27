
Log Compaction is one of Kafka's most powerful storage features.

While normal retention deletes data based on:

```text
Time

Size
```

Log Compaction works differently.

Instead of deleting old records based on age, Kafka keeps the **latest value for each key** and removes older versions.

This makes Kafka suitable for:

```text
State Management

Event Sourcing

Caching

Database Change Logs

Distributed Systems
```

---

# Why Log Compaction?

Consider a topic storing user profile updates.

Messages:

```text
Key=User1 Value=John

Key=User1 Value=John Smith

Key=User1 Value=John Smith Rajput
```

---

Without compaction:

```text
All Records Remain
```

---

Log:

```text
Offset 0 -> User1 -> John

Offset 1 -> User1 -> John Smith

Offset 2 -> User1 -> John Smith Rajput
```

---

Problem:

```text
Storage Growth

Duplicate Historical States
```

---

Most applications only need:

```text
Latest State
```

---

Solution:

```text
Log Compaction
```

---

After compaction:

```text
Offset 2 -> User1 -> John Smith Rajput
```

---

Older values removed.

---

# What is Log Compaction?

## Definition

Log Compaction is a cleanup mechanism that retains the latest record for each unique key while removing older records for the same key.

---

Important:

```text
Latest Value Per Key Is Preserved
```

---

Unlike retention:

```text
Time-Based

Size-Based
```

compaction is:

```text
Key-Based
```

---

# Traditional Retention vs Compaction

## Retention

Deletes data based on:

```text
Age

Size
```

---

Example:

```text
Offset 0

Offset 1

Offset 2

Offset 3
```

---

After retention:

```text
Old Offsets Removed
```

---

## Compaction

Deletes data based on:

```text
Duplicate Keys
```

---

Example:

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

# Key Requirement

Compaction only works for records that contain:

```text
Message Key
```

---

Example:

```text
Key=User1
Value=John
```

---

Without keys:

```text
Compaction Not Possible
```

---

# How Log Compaction Works?

Kafka continuously scans log segments and identifies obsolete records.

---

Goal:

```text
Keep Latest Record
For Each Key
```

---

# Example

Topic:

```text
customer-profile
```

Messages:

```text
Offset 0

User1 -> John
```

---

```text
Offset 1

User2 -> Alice
```

---

```text
Offset 2

User1 -> John Smith
```

---

```text
Offset 3

User2 -> Alice Cooper
```

---

Current State:

```text
User1 -> John Smith

User2 -> Alice Cooper
```

---

Compaction Result:

```text
Offset 2

User1 -> John Smith
```

---

```text
Offset 3

User2 -> Alice Cooper
```

---

Offsets 0 and 1 become obsolete.

---

# Visualization

Before:

```text
Offset 0 -> User1 -> A

Offset 1 -> User2 -> B

Offset 2 -> User1 -> C

Offset 3 -> User2 -> D
```

---

After Compaction:

```text
Offset 2 -> User1 -> C

Offset 3 -> User2 -> D
```

---

# Important Observation

Kafka does NOT immediately compact records.

---

Compaction occurs:

```text
Asynchronously
```

in the background.

---

Therefore:

```text
Old Records May Exist Temporarily
```

---

Until compaction runs.

---

# Compaction Workflow

```text
Write Records
       ↓
Store In Log
       ↓
Background Cleaner Runs
       ↓
Identify Latest Keys
       ↓
Remove Older Records
       ↓
Write Compacted Segment
```

---

# Log Cleaner

Compaction is performed by Kafka's:

```text
Log Cleaner
```

thread.

---

Responsibilities:

```text
Scan Segments

Find Duplicate Keys

Remove Obsolete Records

Create Compacted Segments
```

---

# Log Cleaner Architecture

```text
Partition Log
        ↓
Log Cleaner
        ↓
Compacted Log
```

---

# Example

Original Log:

```text
User1 -> A

User1 -> B

User1 -> C
```

---

Cleaner:

```text
Keep Latest
```

---

Compacted Log:

```text
User1 -> C
```

---

# Compaction Configuration

Enable compaction:

```properties
cleanup.policy=compact
```

---

Topic Example:

```bash
kafka-topics.sh \
--create \
--topic customer-profile \
--config cleanup.policy=compact
```

---

# Tombstone Records

One of the most important log compaction concepts.

---

# Problem

Suppose:

```text
User1 Exists
```

Current Record:

```text
User1 -> John
```

---

How do we delete:

```text
User1
```

from a compacted topic?

---

Simply removing records would cause issues because compaction keeps the latest value.

---

Solution:

```text
Tombstone Record
```

---

# What is a Tombstone Record?

A record with:

```text
Key Present

Value = null
```

---

Example:

```text
User1 -> null
```

---

Meaning:

```text
Delete User1
```

---

# Example

Messages:

```text
Offset 0

User1 -> John
```

---

```text
Offset 1

User1 -> Smith
```

---

Delete:

```text
Offset 2

User1 -> null
```

---

Compaction:

```text
User1 Removed
```

---

# Visualization

Before:

```text
User1 -> John

User1 -> Smith

User1 -> null
```

---

After Compaction:

```text
User1 Deleted
```

---

# Why Tombstones Matter?

Without tombstones:

```text
Deleted Data Could Reappear
```

during recovery.

---

Tombstones ensure:

```text
Deletion Is Preserved
```

across replicas and consumers.

---

# Tombstone Retention

Kafka does not remove tombstones immediately.

---

Configuration:

```properties
delete.retention.ms
```

---

Default:

```text
24 Hours
```

---

Purpose:

Allow consumers time to observe deletion events.

---

# Compacted Topics

Topics configured for compaction are called:

```text
Compacted Topics
```

---

Configuration:

```properties
cleanup.policy=compact
```

---

# Characteristics

---

## Latest State Always Available

---

## Supports Recovery

---

## Storage Efficient

---

## Suitable For State Data

---

# Example

Topic:

```text
customer-profile
```

---

Final Log:

```text
User1 -> John

User2 -> Alice

User3 -> Bob
```

---

Represents:

```text
Current Database State
```

---

# Compacted Topic Recovery

New consumer starts.

---

Consumer reads:

```text
Entire Compacted Topic
```

---

Result:

```text
Rebuild Current State
```

---

Very useful for:

```text
Caches

State Stores

Materialized Views
```

---

# Compact vs Delete Policy

| Feature | Delete | Compact |
|----------|---------|----------|
| Based On | Time/Size | Key |
| Removes Old Data | Yes | Only Old Versions |
| Latest Record Preserved | No | Yes |
| State Recovery | Limited | Excellent |
| Storage Usage | Moderate | Efficient |
| Event Replay | Limited By Retention | Current State Available |

---

# Compact + Delete Together

Kafka supports both policies.

---

Configuration:

```properties
cleanup.policy=compact,delete
```

---

Behavior:

```text
Keep Latest Record Per Key
```

and

```text
Delete Very Old Segments
```

---

Used in many production systems.

---

# Compaction Use Cases

---

# 1. User Profile Service

Messages:

```text
User1 -> John

User1 -> John Smith

User1 -> John Smith Rajput
```

---

Need:

```text
Latest User Profile
```

---

Perfect for compaction.

---

# 2. Customer Database Snapshot

Topic mirrors database state.

---

Messages:

```text
Customer1 -> Updated

Customer2 -> Updated

Customer3 -> Updated
```

---

Compaction keeps:

```text
Current Database Snapshot
```

---

# 3. Configuration Management

Topic:

```text
application-config
```

---

Messages:

```text
db.url -> localhost

db.url -> production-server
```

---

Need:

```text
Latest Configuration
```

---

Compaction ideal.

---

# 4. Distributed Cache

Applications rebuild cache from Kafka.

---

Consumer startup:

```text
Read Compacted Topic
```

---

Result:

```text
Cache Restored
```

---

# 5. Kafka Streams State Stores

Kafka Streams heavily uses compacted topics.

---

Reason:

```text
Recover Application State
```

after failures.

---

# 6. CDC (Change Data Capture)

Tools such as:

```text
Debezium
```

capture database changes.

---

Events:

```text
Customer Updated

Product Updated

Order Updated
```

---

Compaction maintains:

```text
Latest Row State
```

---

# 7. Event Sourcing Snapshots

Instead of replaying millions of events:

```text
Load Current Snapshot
```

from compacted topic.

---

Faster recovery.

---

# Internal Compaction Example

Original Log:

```text
Offset 0

User1 -> A
```

---

```text
Offset 1

User2 -> B
```

---

```text
Offset 2

User1 -> C
```

---

```text
Offset 3

User3 -> D
```

---

```text
Offset 4

User2 -> E
```

---

Compacted Result:

```text
User1 -> C

User2 -> E

User3 -> D
```

---

# Important Compaction Configurations

---

## Enable Compaction

```properties
cleanup.policy=compact
```

---

## Cleaner Threads

```properties
log.cleaner.threads
```

---

## Minimum Dirty Ratio

```properties
min.cleanable.dirty.ratio
```

---

Determines when cleaning should start.

---

## Tombstone Retention

```properties
delete.retention.ms
```

---

## Segment Size

```properties
segment.bytes
```

---

# Best Practices

---

## Always Use Keys

Compaction requires keys.

---

## Use Tombstones For Deletes

```text
Key + null Value
```

---

## Monitor Log Cleaner

---

## Use For State Data

Examples:

```text
Profiles

Configurations

Caches

Reference Data
```

---

## Avoid Using Compaction For Pure Event Streams

Examples:

```text
Transaction Logs

Audit Logs

Order Events
```

---

Historical events may be important.

---

## Consider Compact + Delete

Often the best production combination.

---

# Common Interview Questions

## What is Log Compaction?

A cleanup mechanism that keeps only the latest record for each key.

---

## How is Log Compaction different from Retention?

Retention removes data based on time or size.

Compaction removes older versions of the same key.

---

## Does Compaction require keys?

```text
Yes
```

Without keys, Kafka cannot determine duplicates.

---

## What is a Tombstone Record?

A record with:

```text
Value = null
```

used to indicate deletion.

---

## Why are Tombstone Records needed?

To preserve delete operations in compacted topics.

---

## What configuration enables compaction?

```properties
cleanup.policy=compact
```

---

## Can Kafka use both compaction and deletion?

```text
Yes
```

Using:

```properties
cleanup.policy=compact,delete
```

---

## Is compaction immediate?

```text
No
```

It runs asynchronously in the background.

---

## What performs compaction?

```text
Log Cleaner
```

---

## What are common use cases for compaction?

```text
User Profiles

Caches

Configuration Data

Kafka Streams State Stores

CDC Systems
```

---

# Summary

Log Compaction is Kafka's mechanism for retaining the latest value for each key while removing older versions.

Core Concepts:

- What is Log Compaction?
- How Log Compaction Works
- Tombstone Records
- Compacted Topics
- Compaction Use Cases

Compaction Flow:

```text
Write Records
      ↓
Store In Log
      ↓
Log Cleaner Runs
      ↓
Identify Latest Key Versions
      ↓
Remove Older Versions
      ↓
Create Compacted Log
```

Key Configuration:

```properties
cleanup.policy=compact
```

Deletion Mechanism:

```text
Key Present
Value = null
```

(Tombstone Record)

Compaction is ideal for:

```text
State Management

Configuration Data

Caches

CDC

Kafka Streams

Event Sourcing Snapshots
```

and is one of the reasons Kafka is widely used as both a messaging platform and a distributed state storage system.