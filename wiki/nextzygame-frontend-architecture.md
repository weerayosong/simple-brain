---
title: "Nextzy Gamification Frontend Architecture"
type: wiki-entry
project: "nextzy-gamification"
createdAt: 2026-06-28T02:17:00
modifiedAt: 2026-06-28T02:17:00
target_agents:
  - frontend
related_tools:
  - nextjs
  - react
tags:
  - frontend
  - architecture
  - app-router
---

# Nextzy Gamification Frontend Architecture

> **🤖 Agent Routing:** Frontend agents should read this to understand how the Next.js App Router is structured and how global state is provided to the component tree.

> **🧠 LLM Context:** This covers the `nextzy-game-client` directory. It uses Next.js 16+ App Router, Tailwind CSS 4, and a custom React Context for state management.

## 📌 Overview
The frontend is a lightweight client designed to interact with the gamification backend. It prioritizes fast rendering and simple state management over heavy client-side libraries.

## 🏗️ Core Structure

- `src/app/`: The Next.js App Router directory. Contains `layout.tsx` (the root layout) and `page.tsx` (the main game interface).
- `src/components/`: Reusable UI components (e.g., spin wheel, score board, reward cards).
- `src/contexts/`: Contains `GameContext.tsx`, which holds the global state (user data, current points, spin history) and provides it to the rest of the application.
- `src/services/`: API wrapper functions (using `fetch`) to communicate with the NestJS backend cleanly, hiding HTTP headers and URL parsing from the UI components.
- `src/types/`: TypeScript interfaces shared across the frontend.

## 🔄 State Management (GameContext)
Instead of installing Redux or Zustand, the app wraps the main `layout.tsx` or `page.tsx` with `<GameProvider>`.

The `GameContext` is responsible for:
1. Storing the currently logged-in user profile.
2. Tracking the user's `totalPoints`.
3. Maintaining the `SpinHistory` array to render the recent activity feed.
4. Exposing functions like `login()`, `spin()`, and `claimReward()` which components can call.

## 🔗 Related Context
- [[nextzygame-architecture]]
- [[nextzygame-GameContext]]
- [[nextzygame-state-sync]]
