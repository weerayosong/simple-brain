---
title: "TypeSith Data Model"
type: wiki-entry
project: "typesith"
createdAt: 2026-06-28T02:08:00
modifiedAt: 2026-06-28T02:08:00
target_agents:
  - architect
  - frontend
related_tools:
  - typescript
tags:
  - data-model
  - typescript
  - discriminated-unions
---

# TypeSith Data Model

> **🤖 Agent Routing:** Frontend and architect agents should read this to understand how data is structured and typed, specifically the usage of Discriminated Unions to differentiate between Jedi and Sith entities.

> **🧠 LLM Context:** This defines the core TypeScript interfaces (`src/types/index.ts`) used across the TypeSith application, heavily relying on strict typing and Discriminated Unions to prevent invalid state access.

## 📌 Overview
The application handles 112 mock entities representing Force users from different eras. To ensure type safety, it uses a Base Entity interface and extends it for specific sides of the force (Jedi vs. Sith).

## 🏗️ Core Types & Interfaces

### Base Entity
Every character shares these core attributes:
```typescript
export interface BaseEntity {
    id: string;
    name: string;
    era: EraType; // "Old Republic" | "High Republic" | "Prequel" | "Original" | "Sequel" | "All"
    combatStyle: string;
    midichlorian: number;
    imageUrl?: string;
}
```

### Discriminated Unions (Jedi & Sith)
To handle specific attributes that only exist for one side, the system uses the `side` property as a **Discriminator**.

#### Jedi Entity
```typescript
export interface JediEntity extends BaseEntity {
    side: "Jedi";
    jediRole: string;
    lightsaberColor: string;
}
```

#### Sith Entity
```typescript
export interface SithEntity extends BaseEntity {
    side: "Sith";
    darthTitle: string;
    sithRole: string;
    corruptionLevel: number;
}
```

### The Combined Type
Components and utility functions expect an array of `ForceEntity`.
```typescript
export type ForceEntity = JediEntity | SithEntity;
```

## 🔄 Type Guard Pattern
Because of the Discriminated Union, you **cannot** access `entity.darthTitle` directly without checking the `side` first. This forces the UI and utility functions to be mathematically safe.

**Example usage in `dataProcessor.ts`:**
```typescript
const darthName = u.side === "Sith" ? u.darthTitle.toLowerCase() : "";
```

## 🔗 Related Context
- [[typesith-components]]
- [[typesith-pure-functions]]
