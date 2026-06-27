---
title: "TypeSith Workflows"
type: wiki-entry
project: "typesith"
createdAt: 2026-06-27T16:51:35
modifiedAt: 2026-06-28T00:10:00
target_agents:
  - frontend
  - backend
  - architect
related_tools:
  - react
  - typescript
tags:
  - workflows
  - state-management
  - hooks
  - custom-hooks
---

# TypeSith Workflows

> **🤖 Agent Routing:** Frontend agents must read this to understand state management and hook logic. Backend and Architect agents should read this to understand how data models (`types`) are structured and processed on the client side before designing API payloads.

> **🧠 LLM Context:** This file explains the data flow, state management, and TypeScript typing strategies used in TypeSith, focusing heavily on the `useForceCodex` custom hook.

## 📌 Overview
The core business logic of TypeSith is strictly decoupled from the UI and encapsulated within custom hooks. This approach ensures high performance through memoization and robust data safety through strict TypeScript typings.

## 🏗️ Core Details
### Data Flow & State Management
The logic is driven by the `useForceCodex` custom hook (`src/hooks/useForceCodex.ts`).
1. **State Initialization**: The hook initializes states for the search term, side filter, era filter, sorting preferences, current page, and the currently selected entity (for the modal).
2. **Memoized Data Processing**: To optimize performance and prevent unnecessary re-renders, `useMemo` is heavily utilized to process the data in stages:
   - `filteredData`: Filters the initial dataset based on search term, side, and era.
   - `sortedData`: Sorts the filtered results based on selected criteria (e.g., midichlorian count descending).
   - `finalData`: Paginates the sorted results to display a maximum of 8 entities per page.
3. **Prop Drilling**: The `App` component extracts these states and setter functions from `useForceCodex` and passes them down as props to the respective child components (`FilterControls`, `Pagination`, etc.).

### TypeScript Typings Strategy
The application enforces strict data types using interfaces defined in `src/types/index.ts`:
- **`BaseEntity`**: Defines core properties shared by everyone (id, name, era, midichlorian).
- **`JediEntity` & `SithEntity`**: Extend `BaseEntity` by adding side-specific properties (e.g., `lightsaberColor` for Jedi, `darthTitle` for Sith).
- **`ForceEntity`**: A union type (`JediEntity | SithEntity`) used throughout the app to ensure safe handling of characters regardless of their affiliation.

## 🔗 Related Context
- [[typesith-architecture]]
- [[typesith-components]]
