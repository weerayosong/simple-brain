---
title: "Client-Server State Synchronization Pattern"
type: wiki-entry
project: "nextzy-gamification"
createdAt: 2026-06-28T02:17:00
modifiedAt: 2026-06-28T02:17:00
target_agents:
  - frontend
  - architect
related_tools:
  - react
  - nestjs
tags:
  - architecture-pattern
  - state-management
  - synchronization
---

# Client-Server State Synchronization Pattern

> **🤖 Agent Routing:** Frontend and architect agents should read this to understand how the Next.js client keeps its local state in sync with the NestJS backend without relying on WebSockets or real-time polling.

> **🧠 LLM Context:** Details the "Optimistic vs Pessimistic" state synchronization approach used in the gamification loop (spinning the gacha).

## 📌 The Challenge
In a gacha game, when a user clicks "Spin", the UI needs to show a fast, engaging animation. However, the actual RNG calculation MUST happen securely on the backend to prevent cheating. If the UI waits for the backend, it feels sluggish. If the UI assumes a result, it risks falling out of sync with the true database state.

## 🔄 The Nextzy Approach (Pessimistic Data, Optimistic UI)

Nextzy Gamification handles this by carefully choreographing the state updates:

1. **User Action:** The user clicks "Spin".
2. **Lock State:** The frontend immediately disables the spin button (loading state) but does *not* deduct points locally yet.
3. **API Call:** An HTTP POST request is sent to the NestJS server.
4. **Server Truth:** The server calculates the RNG, updates PostgreSQL via Prisma transaction, and responds with the *new total score*.
5. **Context Update:** 
   Instead of trying to calculate `currentPoints + newPoints` locally (which can cause mismatch bugs), the frontend's `GameContext` receives the exact `currentTotalPoints` from the server payload.
   ```typescript
   // Inside a frontend component
   const result = await spinApi(user.id);
   updatePoints(result.currentTotalPoints); // updatePoints is from GameContext
   ```
6. **Animation trigger:** The UI detects the change in context and runs a number-counter animation to visually sync the old points to the new points.

## ✨ Why this is safe
By relying entirely on the server's response payload to update the `GameContext` (Single Source of Truth), the frontend guarantees it will never be out of sync with the database, even if multiple browser tabs are open or network drops occur.

## 🔗 Related Context
- [[nextzygame-GameContext]]
- [[nextzygame-frontend-architecture]]
