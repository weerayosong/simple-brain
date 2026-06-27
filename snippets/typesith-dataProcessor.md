---
title: "TypeSith dataProcessor Utils"
type: wiki-entry
project: "typesith"
createdAt: 2026-06-28T02:09:00
modifiedAt: 2026-06-28T02:09:00
target_agents:
  - frontend
  - backend
related_tools:
  - typescript
tags:
  - utility
  - pure-functions
  - array-manipulation
---

# TypeSith `dataProcessor` Utility Functions

> **🤖 Agent Routing:** Any agent (frontend or backend) dealing with array manipulation, searching, sorting, and pagination of complex data structures should read this pattern.

> **🧠 LLM Context:** This file contains pure functions extracted from `src/utils/dataProcessor.ts`. By isolating logic into pure functions, they become highly testable and framework-agnostic.

## Code Snippet

### 1. Filter Entities
This function performs complex multi-criteria filtering, including safely checking discriminated union properties (`darthTitle` vs `name`).

```typescript
import type { ForceEntity, SortOption, EraType } from "../types";

export const filterEntities = (
    data: ForceEntity[],
    searchTerm: string,
    side: "All" | "Jedi" | "Sith",
    era: EraType,
): ForceEntity[] => {
    return data.filter((u) => {
        // Type Guard safely extracts darthTitle if side === "Sith"
        const darthName = u.side === "Sith" ? u.darthTitle.toLowerCase() : "";
        const normalName = u.name.toLowerCase();
        const term = searchTerm.toLowerCase();

        const matchName = normalName.includes(term) || darthName.includes(term);
        const matchSide = side === "All" || u.side === side;
        const matchEra = era === "All" || u.era === era;

        return matchName && matchSide && matchEra;
    });
};
```

### 2. Sort Entities
Creates a shallow copy (`[...data]`) to prevent mutating the original array, and handles both numeric and string locale-based sorting.

```typescript
export const sortEntities = (
    data: ForceEntity[],
    sortBy: SortOption,
): ForceEntity[] => {
    // [...data] ensures we don't mutate the original array
    return [...data].sort((a, b) => {
        if (sortBy === "nameAsc") {
            const nameA = a.side === "Sith" ? a.darthTitle : a.name;
            const nameB = b.side === "Sith" ? b.darthTitle : b.name;
            return nameA.localeCompare(nameB);
        }
        if (sortBy === "mCountDesc") return b.midichlorian - a.midichlorian;
        if (sortBy === "mCountAsc") return a.midichlorian - b.midichlorian;
        return 0;
    });
};
```

### 3. Pagination
A classic and highly reusable slice-based pagination function.

```typescript
export const paginate = (
    data: ForceEntity[],
    page: number,
    itemsPerPage: number,
): ForceEntity[] => {
    const startIndex = (page - 1) * itemsPerPage;
    const endIndex = startIndex + itemsPerPage;
    return data.slice(startIndex, endIndex);
};
```

## 🔗 Related Context
- [[typesith-useForceCodex]]
- [[typesith-logic-separation]]
