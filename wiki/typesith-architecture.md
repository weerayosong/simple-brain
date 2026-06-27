---
title: "TypeSith Architecture"
type: wiki-entry
project: "typesith"
createdAt: 2026-06-28T01:28:00
modifiedAt: 2026-06-28T01:28:00
target_agents:
  - architect
  - devops
  - frontend
related_tools:
  - react
  - typescript
  - vite
  - tailwindcss
tags:
  - architecture
  - system-design
  - spa
---

# TypeSith Architecture

> **🤖 Agent Routing:** Architect agents should read this to understand the high-level system design. DevOps agents can reference the related_tools to understand the build requirements (Vite). Frontend agents need this as a starting point.

> **🧠 LLM Context:** This file outlines the system design and tech stack of TypeSith, a React SPA built with TypeScript, Vite, and TailwindCSS.

## 📌 Overview
TypeSith is a frontend-only React SPA functioning as a "Galactic Records" training ground. It uses mock data and pure client-side state management to filter, sort, and display character entities.

## 🏗️ Core Details
- **Build Tool:** Vite
- **Language:** TypeScript (`strict` mode enabled)
- **Styling:** TailwindCSS (v4)
- **Linting:** ESLint with React Hooks and React Refresh plugins.
- **Data Flow:** Top-down data flow from `App.tsx`, which delegates heavy state management to a central custom hook (`useForceCodex`).
- **Logic Separation:** The architecture explicitly separates pure business logic (filtering, sorting, pagination in `src/utils/dataProcessor.ts`) from React component state (in `src/hooks/useForceCodex.ts`). This ensures high testability and prevents UI components from bloating with data manipulation logic.

### Component Hierarchy
```mermaid
graph TD
    App["App.tsx<br/>(State Manager)"]:::core
    Header["Header.tsx"]:::ui
    Controls["FilterControls.tsx"]:::ui
    Content["ContentArea"]:::generic
    Grid["EntityGrid.tsx"]:::ui
    Card["EntityCard.tsx<br/>(Union Type Props)"]:::ui
    Page["Pagination.tsx"]:::ui
    App -->|Props: stats| Header
    App -->|Props: filters & Callbacks| Controls
    App --> Content
    Content --> Grid
    Content --> Page
    Grid -->|Array.map| Card
```

### Data Flow Diagram
```mermaid
graph LR
    Mock[("mockData.ts<br/>(112 Entities)")]:::data
    AppState["App.tsx<br/>(Current State)"]:::state
    Util["dataProcessor.ts<br/>(Pure Functions)"]:::logic
    UI["UI Components<br/>(Rendering)"]:::view
    Mock -->|Step 1: Import Raw Data| AppState
    AppState -->|Step 2: Pass Data + Filter States| Util
    Util -->|Step 3: Return Paginated Result| AppState
    AppState -->|Step 4: Pass Final Data via Props| UI
    UI -.->|Step 5: User Triggers Events<br/>onChange, onClick| AppState
```

## 🔗 Related Context
- [[typesith-components]]
- [[typesith-workflows]]
- [[typesith-state-management]]
