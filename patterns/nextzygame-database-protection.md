---
title: "Database-Level Protection Pattern"
type: wiki-entry
project: "nextzy-gamification"
createdAt: 2026-06-28T02:17:00
modifiedAt: 2026-06-28T02:17:00
target_agents:
  - backend
  - architect
  - data
related_tools:
  - postgresql
  - prisma
tags:
  - architecture-pattern
  - database
  - security
---

# Database-Level Protection Pattern

> **🤖 Agent Routing:** Backend and data agents should read this to understand how to prevent race conditions and duplicate entries using database constraints, rather than relying solely on application-level logic.

> **🧠 LLM Context:** Explores how Nextzy Gamification prevents users from exploiting the reward claim system (e.g., clicking a button twice really fast) by enforcing uniqueness at the PostgreSQL level.

## 📌 The Problem: Race Conditions
If a user hits a milestone (e.g., 500 points) and clicks the "Claim Reward" button rapidly multiple times, the frontend might send multiple concurrent requests to the backend.

If the backend relies purely on:
```typescript
const alreadyClaimed = await prisma.rewardClaim.findFirst({ ... });
if (alreadyClaimed) throw Error();
```
Both concurrent requests might pass the check at the exact same millisecond before either can save to the database, resulting in the user getting double rewards.

## 🛡️ The Solution: Compound Unique Constraints
Instead of trusting the Node.js application layer, the project delegates the final check to the ACID-compliant database (PostgreSQL).

In `schema.prisma`:
```prisma
model RewardClaim {
  id         String   @id @default(dbgenerated("gen_random_uuid()")) @db.Uuid
  userId     String   @db.Uuid
  checkpoint Int      

  @@unique([userId, checkpoint]) // The magic happens here
}
```

### How it works:
1. The first request reaches the database and inserts a row: `userId: 123, checkpoint: 500`.
2. The second concurrent request attempts to insert the same `userId: 123, checkpoint: 500`.
3. PostgreSQL instantaneously rejects the second request, throwing a constraint violation error (Prisma Error code `P2002`).
4. The NestJS backend catches the error and safely returns a `400 Bad Request` to the user, preventing the exploit.

## 🔗 Related Context
- [[nextzygame-db-schema]]
- [[nextzygame-api]]
