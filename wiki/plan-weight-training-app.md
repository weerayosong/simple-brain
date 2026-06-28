---
title: "Project Plan: Weight Training Tracker (IronSith)"
type: wiki-entry
project: "ironsith"
createdAt: 2026-06-28T01:25:00
modifiedAt: 2026-06-28T01:25:00
target_agents:
  - architect
  - planning
related_tools:
  - react
  - typescript
  - vite
  - tailwindcss
  - supabase
tags:
  - ideation
  - project-plan
  - health-tech
---

# Project Plan: Weight Training Tracker (IronSith)

> **🤖 Agent Routing:** Architect and Planning agents should read this to understand the core requirements, tech stack, and phase-by-phase implementation plan before breaking down tasks for Frontend/Backend agents.

## 🎯 1. Project Goals & Vision
เป้าหมายของแอปนี้คือการสร้างแอปพลิเคชันจัดการตารางเวทเทรนนิ่งที่รวดเร็ว ไม่ซับซ้อน และปรับแต่งได้สูง ออกแบบมาเพื่อคนที่ต้องการจดบันทึก Sets, Reps, และ Weight โดยเฉพาะ ไม่ต้องมีฟีเจอร์รกๆ 

**ชื่อโปรเจกต์ (ชั่วคราว):** IronSith (ล้อไปกับ TypeSith) หรือ LiftLog

## 🌟 2. MVP Features (ฟีเจอร์เริ่มต้น)
- **Routine Builder:** สร้างตารางฝึกของตัวเองได้ (เช่น PPL: Push, Pull, Legs หรือ Upper/Lower)
- **Exercise Library:** ฐานข้อมูลท่าออกกำลังกายเบื้องต้น (เพิ่มท่าเองได้)
- **Workout Logger:** หน้าจอบันทึกผลระหว่างออกกำลังกาย (จดน้ำหนัก, จำนวนครั้ง, และเวลาพัก)
- **Progress Tracking:** ดูประวัติน้ำหนักที่ยกได้แบบกราฟ (Progressive Overload)
- **Offline-First:** ใช้งานในยิมที่เน็ตไม่ดีได้ ข้อมูลเก็บลงเครื่องก่อนแล้วค่อยซิงค์

## 💻 3. Proposed Tech Stack
อิงจาก `CONTEXT.md` ที่คุณเชี่ยวชาญ React และ TypeScript สแต็คที่เหมาะสมที่สุดสำหรับความรวดเร็วคือ:
- **Frontend:** React + Vite + TypeScript (ความเร็วสูง แข็งแกร่งเรื่อง Type)
- **Styling:** TailwindCSS + Shadcn UI (เพื่อ UI ที่ดูคลีนและ Modern)
- **State Management:** Zustand (เบากว่า Redux เหมาะกับการเก็บ State ตารางออกกำลังกาย)
- **Database / Backend:** 
  - *MVP:* `localStorage` (Offline first แบบง่ายที่สุด)
  - *Phase 2:* **Supabase** (มีฐานข้อมูล Postgres และระบบ Login ที่ต่อกับ React ง่ายมาก)

## 🏗️ 4. High-level Architecture
แอปจะทำงานแบบ Single Page Application (SPA) โดยมีโครงสร้าง State ดังนี้:
- **Data Models (TypeScript Interfaces):**
  - `WorkoutRoutine` (ชื่อตาราง, วันที่ฝึก)
  - `Exercise` (ชื่อท่า, กล้ามเนื้อที่ได้)
  - `WorkoutSession` (ประวัติการยก 1 วัน ประกอบด้วยหลายๆ `ExerciseSet`)
- **Custom Hooks:** 
  - `useWorkoutSession`: จัดการ State ระหว่างที่กำลังยกเหล็กอยู่ (จับเวลาพัก, บันทึกเซ็ต)
  - `useProgress`: ดึงข้อมูลจากฐานข้อมูลมาคำนวณกราฟพัฒนาการ

## 📋 5. Step-by-step Implementation Tasks

### Phase 1: Foundation (สัปดาห์ที่ 1)
- [x] Setup โปรเจกต์ด้วย Vite + React + TS
- [x] ติดตั้ง TailwindCSS และวางโครงสร้างโฟลเดอร์
- [ ] ร่าง TypeScript Interfaces (`types.ts`) สำหรับข้อมูลการออกกำลังกาย

### Phase 2: Core Core Features (สัปดาห์ที่ 1-2)
- [x] สร้างหน้าจอ Routine Builder (เพิ่มท่าลงในวันต่างๆ)
- [x] สร้างระบบ State ด้วย Zustand เพื่อเก็บข้อมูลตาราง
- [ ] ทำระบบ Persistence เซฟลง LocalStorage

### Phase 3: Workout Execution (สัปดาห์ที่ 2)
- [ ] สร้างหน้าจอกำลังออกกำลังกาย (Active Workout View)
- [ ] ใส่ช่อง Input สำหรับกรอก Reps และ Weight
- [ ] ใส่ปุ่ม Finish Workout เพื่อบันทึกประวัติ

### Phase 4: Data Visualization & Cloud (อนาคต)
- [ ] สร้าง Dashboard ดูกราฟความก้าวหน้า
- [ ] เชื่อมต่อ Supabase เพื่อระบบ Login และ Cloud Sync
