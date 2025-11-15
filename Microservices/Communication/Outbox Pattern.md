/// <summary>
/// The Outbox Pattern is a reliable messaging pattern used in distributed systems to ensure that events or messages are not lost during the process of updating a database and publishing messages.
/// </summary>
/// <remarks>
/// This pattern involves storing messages or events in a dedicated "outbox" table within the same transactional boundary as the business data changes.
/// A separate process then reads from the outbox and publishes the events to the message broker, ensuring atomicity and consistency.
/// The Outbox Pattern helps to solve the problem of dual writes and guarantees eventual consistency between the database and the message broker.
/// </remarks>
# Outbox Pattern Documentation

## Overview

The **Outbox Pattern** is a reliable messaging pattern used in microservices architectures to ensure that events are published only if the related database transaction is committed. This pattern helps solve the problem of atomicity between database updates and message/event publishing, especially in distributed systems.

## Why Use the Outbox Pattern?

- Guarantees consistency between your database and message broker.
- Prevents lost or duplicated messages due to failures.
- Enables reliable event-driven communication in microservices.

---

## Outbox Pattern Variants

### 1. In-Memory Outbox Pattern

**Description:**  
Messages/events are temporarily stored in memory during the transaction. After the transaction is committed, the messages are published to the message broker.

**Pros:**
- Simple to implement.
- No additional database tables required.

**Cons:**
- Not resilient to process crashes.
- Messages can be lost if the application crashes before publishing.

**Typical Usage:**
- Suitable for simple scenarios or where durability is not critical.

**Example Flow:**
1. Begin transaction.
2. Save business data.
3. Store event/message in memory.
4. Commit transaction.
5. Publish message from memory.

---

### 2. Transactional Outbox Pattern

**Description:**  
Events/messages are stored in a dedicated "outbox" table within the same database transaction as the business data. A separate process (outbox processor) reads the outbox table and publishes messages to the broker.

**Pros:**
- Guarantees atomicity between data changes and event publishing.
- Durable and resilient to crashes.

**Cons:**
- Requires outbox table management.
- Needs a reliable outbox processor.

**Typical Usage:**
- Recommended for production-grade, distributed systems.

**Example Flow:**
1. Begin transaction.
2. Save business data.
3. Insert event/message into outbox table.
4. Commit transaction.
5. Outbox processor reads and publishes messages, then marks them as processed.

---

## Transactional Outbox with Single Database

### Same Schema

- Outbox table resides in the same schema as business tables.
- Simpler to manage.
- Easier to query and join data.

| Column Name     | Data Type      | Description                       |
|-----------------|---------------|-----------------------------------|
| Id              | GUID/UUID     | Unique identifier for the message |
| AggregateId     | GUID/UUID     | Related business entity ID        |
| Type            | string        | Event/message type                |
| Payload         | string (JSON) | Serialized event data             |
| OccurredOn      | DateTime      | When the event occurred           |
| ProcessedOn     | DateTime?     | When the event was published      |
| Status          | string        | Message status (e.g., Pending, Processed, Failed) |