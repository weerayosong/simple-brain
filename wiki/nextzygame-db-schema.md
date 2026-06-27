---
title: "Nextzy Gamification DB Schema"
type: wiki-entry
project: "nextzy-gamification"
createdAt: 2026-06-28T02:16:00
modifiedAt: 2026-06-28T02:16:00
target_agents:
  - backend
  - architect
  - data
related_tools:
  - prisma
  - postgresql
tags:
  - database
  - schema
  - prisma
---

# Nextzy Gamification Database Schema

> **🤖 Agent Routing:** Backend and architect agents should read this to understand the relational structure, indexing strategies, and database-level protections implemented in PostgreSQL via Prisma.

> **🧠 LLM Context:** This defines the core data layer for the gacha/gamification system. It uses `uuid` for primary keys and relies on compound unique indexes to enforce business rules.

## 📌 Overview
The system tracks users, their spin history (points earned), and their claimed reward checkpoints. The database is PostgreSQL, orchestrated through Prisma ORM.

## 🏗️ Core Models

### 1. User
Stores basic player data and their accumulated points.
```prisma
model User {
  id          String        @id @default(dbgenerated("gen_random_uuid()")) @db.Uuid
  nickname    String        @unique
  totalPoints Int           @default(0)
  createdAt   DateTime      @default(now())
  
  spinHistory SpinHistory[]
  rewards     RewardClaim[]

  @@index([nickname])
}
```
* **Performance Note:** `@@index([nickname])` is used to speed up logins/lookups since `nickname` acts as the primary identifier for users logging in.

### 2. SpinHistory
An append-only log of every spin a user makes.
```prisma
model SpinHistory {
  id             String   @id @default(dbgenerated("gen_random_uuid()")) @db.Uuid
  userId         String   @db.Uuid
  pointsReceived Int
  createdAt      DateTime @default(now())

  user           User     @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@index([userId])
}
```
* **Performance Note:** `@@index([userId])` is crucial here. Over time, history tables grow massive. This index ensures that fetching a specific user's spin history remains fast.

### 3. RewardClaim
Tracks which milestone checkpoints (e.g., 500, 1000, 10000 points) a user has successfully claimed.
```prisma
model RewardClaim {
  id         String   @id @default(dbgenerated("gen_random_uuid()")) @db.Uuid
  userId     String   @db.Uuid
  checkpoint Int      // e.g. 500, 1000, 10000
  claimedAt  DateTime @default(now())

  user       User     @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([userId, checkpoint])
}
```

## 🛡️ Database-Level Protections
The `RewardClaim` table employs a powerful constraint: `@@unique([userId, checkpoint])`.
This is a **Compound Unique Constraint**. It physically prevents a race condition (e.g., double-clicking a "Claim" button) from generating two rows for the same user at the same checkpoint. If the application logic fails, the database will throw an error, protecting the integrity of the reward system.

## 🔗 Related Context
- [[nextzygame-architecture]]
- [[nextzygame-database-protection]]
