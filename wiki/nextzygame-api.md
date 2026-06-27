---
title: "Nextzy Gamification API Endpoints"
type: wiki-entry
project: "nextzy-gamification"
createdAt: 2026-06-28T02:17:00
modifiedAt: 2026-06-28T02:17:00
target_agents:
  - backend
  - frontend
related_tools:
  - nestjs
tags:
  - api
  - rest
  - controllers
---

# Nextzy Gamification REST API

> **🤖 Agent Routing:** Frontend and backend agents should read this to understand the contract between the Next.js client and the NestJS server.

> **🧠 LLM Context:** This documents the exposed REST endpoints in the NestJS backend. All endpoints respond with JSON and expect JSON payloads.

## 📌 Overview
The API is split into four distinct domains: `Auth`, `Game`, `History`, and `Reward`. It relies on standard HTTP methods and simple JSON body parsing.

## 🏗️ Endpoints

### 1. Auth Controller (`/auth`)
Handles user login and registration seamlessly.

- **`POST /auth/login`**
  - **Body:** `{ "nickname": "string" }`
  - **Behavior:** Checks if the user exists. If yes, returns the user data and their spin history. If no, creates a new user and returns it.
  - **Validation:** Throws `BadRequestException` if nickname is empty.

### 2. Game Controller (`/game`)
Handles the core gacha mechanics.

- **`POST /game/spin`**
  - **Body:** `{ "userId": "uuid" }`
  - **Behavior:** Triggers the RNG algorithm to generate a score (0, 10, 20, ..., 100). Saves the spin to `SpinHistory` and updates the user's `totalPoints`.
  - **Validation:** Throws `BadRequestException` if userId is missing.

### 3. History Controller (`/history`)
Provides paginated feeds for the UI.

- **`GET /history/global`**
  - **Query:** `?page=1&limit=5`
  - **Behavior:** Returns a list of the most recent spins from *all* users.
- **`GET /history/personal/:userId`**
  - **Query:** `?page=1&limit=5`
  - **Behavior:** Returns a list of the most recent spins for a specific user.

### 4. Reward Controller (`/reward`)
Handles the milestone claiming logic.

- **`POST /reward/claim`** *(Inferred from typical structure)*
  - **Body:** `{ "userId": "uuid", "checkpoint": 500 }`
  - **Behavior:** Validates if the user has enough points. Creates a `RewardClaim` record.
  - **Protection:** Relies on DB unique constraint to prevent duplicate claims.

## 🔗 Related Context
- [[nextzygame-db-schema]]
- [[nextzygame-architecture]]
