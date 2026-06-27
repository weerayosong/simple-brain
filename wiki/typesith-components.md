---
title: "TypeSith Components"
type: wiki-entry
project: "typesith"
createdAt: 2026-06-27T16:51:35
modifiedAt: 2026-06-28T00:10:00
target_agents:
  - frontend
related_tools:
  - react
  - tailwindcss
tags:
  - components
  - ui
  - react
---

# TypeSith Components

> **🤖 Agent Routing:** Frontend agents should read this file to understand the UI component tree, styling logic, and how props are passed down from the main App component.

> **🧠 LLM Context:** This file details the React component structure of the TypeSith project, explaining the roles of the main UI building blocks like EntityCard, EntityModal, and FilterControls.

## 📌 Overview
The UI of TypeSith is divided into several reusable React components located in `src/components/`, ensuring a clean separation of concerns and maintainability.

## 🏗️ Core Details
### Main Components & Props

- **`App`** (`src/App.tsx`): 
  The root component. It initializes the `useForceCodex` hook and distributes state via props to child components. It acts as the single source of truth for the UI state.

- **`FilterControls`** (`src/components/FilterControls.tsx`): 
  A "Controlled Component" that receives state and `setState` functions from `App.tsx`.
  - **Props:** `searchTerm`, `setSearchTerm`, `sideFilter`, `setSideFilter`, `eraFilter`, `setEraFilter`, `sortBy`, `setSortBy`, `onFilterChange`
  - **Behavior:** Whenever a filter changes, it calls `onFilterChange` (which resets the pagination to page 1).

- **`EntityCard`** (`src/components/EntityCard.tsx`): 
  A highly reusable presentation component.
  - **Props:** `entity: ForceEntity` (Discriminated Union), `onClick: () => void`.
  - **Behavior:** Renders different UI elements based on the `entity.side` discriminator (e.g., Sith gets red themes and Darth Titles, Jedi gets blue/green themes and Lightsaber Colors).

- **`EntityModal`** (`src/components/EntityModal.tsx`): 
  A detailed split-layout modal that appears when an entity is clicked. 
  - **Props:** `entity: ForceEntity | null`, `onClose: () => void`.
  - **Behavior:** Uses conditional styling based on the character's dark/light side affiliation. Renders nothing if `entity` is null.

- **`Pagination`** (`src/components/Pagination.tsx`): 
  Manages the current page state to navigate through the dataset.
  - **Props:** `currentPage: number`, `totalPages: number`, `onPageChange: (page: number) => void`.

## 🔗 Related Context
- [[typesith-architecture]]
- [[typesith-workflows]]
- [[typesith-data-model]]
