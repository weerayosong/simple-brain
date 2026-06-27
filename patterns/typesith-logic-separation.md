---
title: "Pure Logic Separation Pattern"
type: wiki-entry
project: "typesith"
createdAt: 2026-06-28T02:10:00
modifiedAt: 2026-06-28T02:10:00
target_agents:
  - architect
  - frontend
related_tools:
  - react
  - typescript
tags:
  - architecture-pattern
  - pure-functions
  - testability
---

# Pure Logic Separation Pattern

> **🤖 Agent Routing:** Architect and frontend agents should read this to understand how business logic is cleanly decoupled from React hooks and components for maximum testability.

> **🧠 LLM Context:** TypeSith enforces a strict boundary between React's render cycle (hooks/components) and raw data manipulation (sorting, filtering).

## 📌 Overview
It is common in React applications to write array filters and sorts directly inside a component or a custom hook. TypeSith deliberately extracts this logic into pure TypeScript functions located in `src/utils/dataProcessor.ts`.

## 🏗️ Core Details
1. **Framework Agnostic:**
   Functions like `filterEntities` or `sortEntities` do not import anything from React. They simply take inputs and return outputs. This means if the project migrates away from React, this logic remains 100% usable.
2. **High Testability:**
   Because these functions do not rely on React state, contexts, or the DOM, they can be tested trivially using standard unit test runners like Vitest or Jest without needing `react-testing-library` or complex mocking.
3. **Immutability Guarantee:**
   The pure functions are designed to never mutate the input data. For example, `sortEntities` uses `[...data].sort(...)` to return a fresh array, preventing insidious bugs where the original state array is modified in place.

## 🔗 Related Context
- [[typesith-dataProcessor]]
- [[typesith-state-management]]
