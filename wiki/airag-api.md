---
title: "AI RAG Agent API Controllers"
type: wiki-entry
project: "ai-rag-agent"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - backend
  - frontend
related_tools:
  - express
  - typescript
tags:
  - api
  - controllers
  - error-handling
---

# AI RAG Agent API Controllers

> **🤖 Agent Routing:** Backend agents should read this to understand how requests are orchestrated between the client, DummyJSON, and Gemini. Frontend agents should read this to understand the custom error messages returned for specific HTTP status codes.

> **🧠 LLM Context:** Details the `chat.controller.ts` which manages an in-memory cache, fetches DummyJSON data, optimizes the payload for Gemini, and maps raw AI errors to Persona-driven Thai error responses.

## 📌 Overview
The AI RAG backend uses a single primary endpoint `POST /api/chat`. Instead of directly feeding user input to an LLM, the `chat.controller.ts` acts as an orchestrator that first gathers structured context from `fetchProducts()` (DummyJSON), optimizes the payload, and then invokes the Gemini Service.

## 🏗️ Core Details

### 1. In-Memory Caching (`responseCache`)
- To save LLM API costs and reduce latency, the controller uses a `Map<string, any>` to cache responses.
- The cache key is simply the `message.trim().toLowerCase()`.
- If a user asks the exact same question twice, the backend responds instantly from the cache without hitting the Gemini API.

### 2. Payload Optimization
- `fetchProducts()` fetches a large JSON from DummyJSON.
- The controller maps the `rawProducts` array into an `optimizedProducts` array, picking only the essential fields (`id`, `title`, `price`, `category`, `thumbnail`, `description`).
- This step is critical for a RAG architecture to **reduce token usage** and prevent the LLM from being overwhelmed by irrelevant metadata (e.g., `stock`, `brand`, `reviews`).

### 3. Persona-Driven Error Handling
If the `gemini.service.ts` throws an error, the controller catches it and translates it into a polite, convenience-store-themed Thai response:
- **Rate Limit (429):** If the error message starts with `RATE_LIMIT:`, the controller extracts the retry seconds and responds with: *"ขออภัยด้วยนะคะคุณลูกค้า ตอนนี้คิวชำระเงินยาวนิดนึง รบกวนรอประมาณ X วินาที..."*
- **Service Unavailable (503):** Responds with: *"ขออภัยค่ะ ตอนนี้ลูกค้าในร้านเยอะมาก คิวชำระเงินยาวเลย..."*
- **General Server Error (500):** Responds with: *"ขออภัยค่ะ ระบบแคชเชียร์มีปัญหาขัดข้องเล็กน้อย..."*

## 🔗 Related Context
- [[airag-architecture]]
- [[airag-gemini-service]]
- [[airag-llm-orchestrator]]
