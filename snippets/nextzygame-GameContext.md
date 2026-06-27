---
title: "Nextzy Gamification GameContext"
type: wiki-entry
project: "nextzy-gamification"
createdAt: 2026-06-28T02:17:00
modifiedAt: 2026-06-28T02:17:00
target_agents:
  - frontend
related_tools:
  - react
  - nextjs
tags:
  - snippet
  - context-api
  - state-management
---

# Nextzy Gamification `GameContext.tsx`

> **🤖 Agent Routing:** Frontend agents should read this to see how global state is implemented in Next.js without third-party libraries.

> **🧠 LLM Context:** Extracts the React Context provider that manages user sessions and scores. It demonstrates handling `localStorage` inside Next.js safely (avoiding hydration mismatches).

## Code Snippet

```tsx
"use client";

import React, { createContext, useContext, useState, useEffect } from "react";
import { User } from "@/types";

type GameContextType = {
    user: User | null;
    setUser: (user: User | null) => void;
    updatePoints: (newTotal: number) => void;
    logout: () => void;
};

const GameContext = createContext<GameContextType | undefined>(undefined);

export function GameProvider({ children }: { children: React.ReactNode }) {
    const [user, setUser] = useState<User | null>(null);

    // Fetch user from localStorage safely (avoiding hydration errors in Next.js)
    useEffect(() => {
        const storedUser = localStorage.getItem("nextzy_user");
        if (storedUser) {
            setTimeout(() => {
                setUser(JSON.parse(storedUser));
            }, 0);
        }
    }, []);

    const handleSetUser = (newUser: User | null) => {
        setUser(newUser);
        if (newUser) {
            localStorage.setItem("nextzy_user", JSON.stringify(newUser));
        } else {
            localStorage.removeItem("nextzy_user");
        }
    };

    // Update only the total points (called after a successful spin)
    const updatePoints = (newTotal: number) => {
        if (user) {
            const updatedUser = { ...user, totalPoints: newTotal };
            handleSetUser(updatedUser);
        }
    };

    const logout = () => {
        setUser(null);
        localStorage.removeItem("nextzy_user");
        window.location.href = "/";
    };

    return (
        <GameContext.Provider value={{ user, setUser: handleSetUser, updatePoints, logout }}>
            {children}
        </GameContext.Provider>
    );
}

export function useGame() {
    const context = useContext(GameContext);
    if (context === undefined) {
        throw new Error("useGame must be used within a GameProvider");
    }
    return context;
}
```

## 🔗 Related Context
- [[nextzygame-frontend-architecture]]
- [[nextzygame-state-sync]]
