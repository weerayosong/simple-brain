---
title: "TypeSith EntityCard"
type: wiki-entry
project: "typesith"
createdAt: 2026-06-28T02:09:00
modifiedAt: 2026-06-28T02:09:00
target_agents:
  - frontend
related_tools:
  - react
  - tailwindcss
  - typescript
tags:
  - component
  - discriminated-unions
  - ui
---

# TypeSith EntityCard

> **🤖 Agent Routing:** Frontend agents should read this to understand how to build visually rich, interactive cards using Tailwind CSS and safely render properties based on TypeScript Discriminated Unions.

> **🧠 LLM Context:** This component extracts `EntityCard.tsx`. It demonstrates conditional rendering and styling based on whether the `entity.side` is "Sith" or "Jedi", completely avoiding TypeScript errors by using type guards.

## Code Snippet

```tsx
import type { ForceEntity } from "../types";

interface EntityCardProps {
    entity: ForceEntity;
    onClick: () => void;
}

export default function EntityCard({ entity, onClick }: EntityCardProps) {
    // Type Guard for Discriminated Union
    const isSith = entity.side === "Sith";
    const displayName = isSith ? entity.darthTitle : entity.name;

    // Conditional Styling
    const sideColorClass = isSith ? "bg-red-600" : "bg-slate-400";
    const badgeClass = isSith
        ? "bg-red-50 text-red-700 border border-red-200"
        : "bg-slate-100 text-slate-600 border border-slate-200";

    const defaultJediImage = "https://images.unsplash.com/photo-1478479405421-ce83c92fb3ba?q=80&w=400&auto=format&fit=crop";
    const defaultSithImage = "https://images.unsplash.com/photo-1505567745926-ba89000d255a?q=80&w=400&auto=format&fit=crop";

    const imageToDisplay = entity.imageUrl ? entity.imageUrl : isSith ? defaultSithImage : defaultJediImage;

    return (
        <div
            onClick={onClick}
            className="border border-slate-700 rounded-md relative overflow-hidden group hover:border-slate-400 hover:shadow-lg hover:shadow-slate-900/20 transition-all cursor-pointer flex flex-col h-full min-h-70"
        >
            <img src={imageToDisplay} alt={displayName} className="absolute inset-0 w-full h-full object-cover transition-transform duration-700 group-hover:scale-110" />
            <div className="absolute inset-0 bg-slate-950/45 group-hover:bg-slate-950/5 transition-colors duration-300"></div>
            <div className={`absolute top-0 left-0 bottom-0 w-1 z-10 ${sideColorClass}`}></div>

            <div className="relative z-10 p-4 pl-5 flex-1 flex flex-col text-slate-200">
                <div className="mb-2">
                    <div className="flex justify-between items-start">
                        <h3 className="font-bold text-white text-lg line-clamp-1 leading-tight drop-shadow-md">
                            {displayName}
                        </h3>
                        <span className={`text-[10px] font-bold px-2 py-1 rounded uppercase backdrop-blur-sm ${badgeClass}`}>
                            {entity.side}
                        </span>
                    </div>
                </div>

                <div className="text-xs space-y-1.5 mt-auto bg-slate-900/60 p-3 rounded border border-slate-700/50 backdrop-blur-sm">
                    {/* Conditionally rendering based on union type */}
                    {entity.side === "Jedi" ? (
                        <>
                            <div className="flex justify-between items-center">
                                <span className="text-slate-400">Role</span>
                                <span className="font-semibold text-slate-200">{entity.jediRole}</span>
                            </div>
                        </>
                    ) : (
                        <>
                            <div className="flex justify-between items-center">
                                <span className="text-slate-400">Dark Side</span>
                                <span className="font-semibold text-red-400">{entity.corruptionLevel}%</span>
                            </div>
                        </>
                    )}
                </div>
            </div>
        </div>
    );
}
```

## 🔗 Related Context
- [[typesith-components]]
- [[typesith-data-model]]
