---
title: "useForceCodex Custom Hook"
type: wiki-entry
project: "typesith"
createdAt: 2026-06-28T01:28:00
modifiedAt: 2026-06-28T01:28:00
target_agents:
  - frontend
related_tools:
  - react
  - typescript
tags:
  - custom-hook
  - state-management
  - usememo
---

# useForceCodex Custom Hook

> **🤖 Agent Routing:** Frontend agents should read this to see how complex filtering, sorting, and pagination state is encapsulated inside a reusable custom hook.

> **🧠 LLM Context:** This hook manages UI state (search, filters, pagination) and uses `useMemo` for performance optimization when transforming lists of entities.

## Code Snippet

```typescript
import { useState, useMemo } from "react";
import { filterEntities, sortEntities, paginate } from "../utils/dataProcessor";
import type { ForceEntity, EraType, ForceSide, SortOption } from "../types";

export function useForceCodex(initialData: ForceEntity[]) {
    const [searchTerm, setSearchTerm] = useState<string>("");
    const [sideFilter, setSideFilter] = useState<"All" | ForceSide>("All");
    const [eraFilter, setEraFilter] = useState<EraType>("All");
    const [sortBy, setSortBy] = useState<SortOption>("mCountDesc");
    const [currentPage, setCurrentPage] = useState<number>(1);
    const [selectedEntity, setSelectedEntity] = useState<ForceEntity | null>(null);

    const filteredData = useMemo(
        () => filterEntities(initialData, searchTerm, sideFilter, eraFilter),
        [initialData, searchTerm, sideFilter, eraFilter],
    );

    const sortedData = useMemo(
        () => sortEntities(filteredData, sortBy),
        [filteredData, sortBy],
    );

    const finalData = useMemo(
        () => paginate(sortedData, currentPage, 8),
        [sortedData, currentPage],
    );

    const totalPages = Math.ceil(filteredData.length / 8) || 1;

    return {
        searchTerm, sideFilter, eraFilter, sortBy, currentPage, selectedEntity,
        setSearchTerm, setSideFilter, setEraFilter, setSortBy, setCurrentPage, setSelectedEntity,
        finalData, totalPages, totalFound: filteredData.length,
    };
}
```
