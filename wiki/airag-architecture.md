---
title: "AI RAG Agent Architecture"
type: wiki-entry
project: "ai-rag-agent"
createdAt: 2026-06-28T01:37:00
modifiedAt: 2026-06-28T01:37:00
target_agents:
  - architect
  - backend
  - frontend
related_tools:
  - react
  - express
  - dummyjson
  - gemini
tags:
  - architecture
  - rag
  - monorepo
---

# AI RAG Agent Architecture

> **🤖 Agent Routing:** Architect agents should read this for full-stack RAG context. Frontend/Backend agents can use this to understand the Client/Server split.

> **🧠 LLM Context:** This document details the system design of the AI RAG Agent, which uses a React frontend, Express backend, DummyJSON for data, and Gemini Flash 3.5 for Semantic Search and Q&A.

## 📌 Overview
The system is built as a monorepo separated into `client` (React + Vite) and `server` (Express). The app allows users to perform semantic product searches and Q&A using a Retrieval-Augmented Generation (RAG) approach to prevent AI hallucination.

## 🏗️ Core Architecture & CI/CD

```mermaid
graph TD
    Repo["GitHub Repository<br/>(Monorepo)"]

    subgraph CI/CD Pipeline
        Repo -->|Git Push| Action["GitHub Actions<br/>(Type Check & Build)"]
        Action -->|Trigger Deploy| Vercel["Vercel<br/>(Hosts /client)"]
        Action -->|Trigger Deploy| Render["Render.com<br/>(Hosts /server)"]
    end

    subgraph Client ["Frontend (React + Vite + Tailwind v4)"]
        UI["Chat UI Components"]
        Vercel -.-> UI
    end

    subgraph Server ["Backend (Express + Node + TS)"]
        API["Express API"]
        RAG["Agent Orchestrator"]
        Render -.-> API
        API --> RAG
    end

    subgraph External ["External Services"]
        DummyJSON[("DummyJSON")]
        Gemini["Gemini Flash 3.5"]
    end

    UI <-->|Strict JSON Payload| API
    RAG <-->|Fetch Data| DummyJSON
    RAG <-->|Prompt Construction| Gemini
```

## 🔄 RAG Data Flow Sequence

```mermaid
sequenceDiagram
    autonumber
    participant User
    participant React as Client UI
    participant Express as API Server
    participant DB as DummyJSON
    participant AI as Gemini Flash

    User->>React: Submit Query
    React->>Express: POST /api/chat
    
    rect rgb(239, 246, 255)
        Note over Express,DB: Phase 1: Retrieval
        Express->>DB: Fetch products category
        DB-->>Express: Return raw JSON array
    end

    rect rgb(243, 232, 255)
        Note over Express,AI: Phase 2: Augmentation & Generation
        Express->>Express: Build Prompt + Product Data
        Express->>AI: Send System Instructions + Payload
        AI-->>Express: Return AIResponseSchema
    end

    Express-->>React: Structured Data (Text & Array)
    React->>User: Render Chat & Product Cards
```

## 🛠️ Core Features
- **Semantic Product Search:** AI analyzes intent, bypassing basic keyword matching.
- **RAG-Powered Q&A:** Grounded exclusively in DummyJSON data.
- **Structured JSON Rendering:** Gemini returns structured JSON (`AIResponse`), which the frontend renders as Product Cards.
- **Context-Aware Memory:** Session history is maintained to allow continuous, contextual prompting.

## 🔗 Related Context
- `shared/types.ts` contains shared types `Product`, `ChatRequest`, and `AIResponse`.
