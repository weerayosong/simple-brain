---
title: "State Management via Custom Hooks"
type: wiki-entry
project: "typesith"
createdAt: 2026-06-28T01:28:00
modifiedAt: 2026-06-28T01:28:00
target_agents:
  - architect
  - frontend
related_tools:
  - react
tags:
  - architecture-pattern
  - state-management
---

# State Management via Custom Hooks

> **🤖 Agent Routing:** Architect and frontend agents should read this to understand the project's approach to state management without external libraries like Redux or Zustand.

> **🧠 LLM Context:** TypeSith uses a "Custom Hook Controller" pattern to lift complex state logic out of components and into dedicated hooks, providing clean interfaces for the UI.

## 📌 Overview
Instead of cluttering `App.tsx` with dozens of `useState` declarations and data-processing logic, TypeSith delegates all state tracking and derived data calculations to `useForceCodex.ts`.

## 🏗️ Core Details
1. **Facade Pattern (Custom Hook):**
   - The `useForceCodex` acts as a Facade. It hides the complexities of multi-stage data processing (filter -> sort -> paginate) behind a simple interface that just returns `finalData` and a few setters.
   - `App.tsx` doesn't need to know *how* the data is filtered; it just calls `setSearchTerm` and receives the new `finalData`.
2. **Separation of Concerns:**
   - **Data Processing:** Pure functions are kept in `utils/dataProcessor.ts` (e.g., `filterEntities`, `sortEntities`).
   - **State orchestration:** `useForceCodex.ts` holds the state and calls the pure functions inside `useMemo`.
   - **UI Rendering:** `App.tsx` only calls the hook and passes the returned data and setter functions as props to child components (`FilterControls`, `EntityCard`).
3. **Performance:** `useMemo` ensures that sorting and filtering only run when the specific dependent state changes, avoiding expensive recalculations on every render.

## 🔗 Related Context
- [[typesith-useForceCodex]]
- [[typesith-architecture]]
